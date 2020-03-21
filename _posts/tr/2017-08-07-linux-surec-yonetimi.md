---
layout: post
title: Linux Süreç Yönetimi
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sistemde çalışan her program en az bir süreçten(`process`) oluşur. Bir süreç ön planda veya arka planda çalışarak sistemin kaynaklarını kullanabilir. Bir sistemden en iyi şekilde yararlanabilmek için `sistem yönetimi` iyi yapılmalıdır. Aksi takdirde bir süreç sistem kaynaklarını gereksiz yere kullanarak sistem yavaşlamasına veya çeşitli güvenlik zafiyetlerine neden olabilir. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Her süreç diğerlerinden farklı unique bir `PID(process-identifier)` değerine sahiptir. Bazı komutlar sayesinde PID değerini bulduğumuz süreçleri sonlandırarak, arka planda gereksiz çalışan süreçlerin önüne geçebilmekteyiz. 

<h4>ps komutu</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut sistemde aktif olan süreçler ile ilgili durum bilgisi, boyut, isim, sahiplik, CPU zamanı vb. bilgileri görmemizi sağlar. Bazı parametreler ;

	-A : Sistemdeki bütün süreçleri listeler.
	-a : Belirli bir terminal kontrölündeki o an çalışan süreçleri listeler
	-r : Sadece çalışmakta olan süreçleri listeler.
	-x : Bir terminal kontrolünde olmayan süreçleri listeler.
	-u : Süreç sahiplerini listeler.
	-f : Süreçler arasındaki parent-child ilişkilerini gösterir.
	-l : Uzun formatlı bir liste üretir.
	-w : Bir sürecin komut satırı parametrelerini gösterir.
	-C : Bir süreç ile birlikte o sürecin alt süreçlerini listeler.
	T  : Komutun girildiği terminalde başlatılan süreçleri listeler.
	p  : PID değeri girilen sürecin bilgilerini gösterir.


<img src="/img/surec/ps-aux.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ps komutu kullanıldığında ortaya çıkan sütun başlıkları neyi ifade ediyor ? 

	USER : Sürecin sahibi olan kullanıcı.
	PID  : Sürecin unique değeri.
	%CPU : CPU kullanımının yüzde kaçının ilgili sürece ait olduğu.
	%MEM : Sürecin belleğin yüzde kaçını kullandığı bilgisi.
	VSZ  : Sürecin kullandığı sanal bellek bilgisi.
	RSS  : Sürecin kullandığı fiziksel bellek bilgisi.
	TTY  : Süreci kontrol eden terminal.
	STAT : Sürecin o anki durumu. Bazı STAT değerlerinin anlamları ;
	    -S : Sürecin beklemede olduğunu gösterir.
	    -R : Sürecin o an çalıştığını,CPU'da işlem gördüğünü belirtir.
	    -D : Süreç kesilemez bir şekilde uykuda.
	    -T : Sürecin debugger'da incelendiğini ya da durdurulduğunu gösterir.
	    -Z : Süreç zombi durumunda.
	START : Sürecin başlangıç zamanı.
	TIME : Sürecin CPU'da harcadığı süre.
	COMMAND :  Sürecin adı ve komut satırı parametreleri.



<h4>top komutu </h4> 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ps komutu kullanım anında süreçleri statik bir biçimde sağlayan bir komuttur. top komutu ise ps'den farklı olarak süreçlerdeki ve sistemdeki en ufak değişiklikleri `dinamik` bir şekilde listeler. Süreçleri listeledikten sonra her 2-3 saniyede bu ekranı yeniler. 

<img src="/img/surec/top.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;top komutu kullanıldığında ortaya çıkan sütun başlıkları neyi ifade ediyor ? 

	PID  : Sürecin unique değeri.
	USER : Süreci başlatan sistem kullancısı.
	PR   : Sürecin öncelik değeri.
	NI   : Kullanıcı tarafından belirtilen öncelik değeri.
	VIRT : Sürecin kullandığı sanal bellek.
	RES  : Sürecin kullandığı fiziksel bellek.
	SHR  : VIRT boyutunun ne kadarının paylaşılabilir olduğu bilgisi.
	S    : Sürecin o anki durumu. Bazı S değerlerinin anlamları ;
	    -S : Sürecin beklemede olduğunu gösterir.
	    -R : Sürecin o an çalıştığını,CPU'da işlem gördüğünü belirtir.
	    -X : Sürecin öldürüldüğünü gösterir. 
	    -T : Sürecin durdurulduğunu gösterir.
	    -Z : Süreç zombi durumunda.
	%CPU : Sürecin işlemcide kullandığı alan.
	%MEM : Sürecin bellekte kullandığı alan.
	TIME+ : Sürecin işlemcide çalıştığı süre.
	COMMAND : Sürecin adı.


<h3>Süreçler ile ilgili Bazı İşlemler</h3>

<h4>kill komutu </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut herhangi bir nedenden ötürü bitirmek istediğimiz bir süreci sonlandırmayı sağlar. kill komutunu kullanan kullanıcı sadece kendine ait süreçleri sonlandırabilir. Bunun yanı sıra root kullanıcısı tüm süreçleri sonlandırabilir.  kill komutu kullanımı şu şekildedir ;

	kill -n PID 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada n değeri 9 olarak girildiğinde süreç kesin olarak sonlandırılır. parametre girilmezse kill komutu varsayılan olarak 1 sinyalini yollar.


	kill -9 11951



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bazı durumlarda sisteme kaldırabileceğinden fazla yük binerse bir süreci durdurmak ya da sonlandırmak gerekebilir. kill komutunun `-STOP` parametresiyle bir süreç durdurulabilir. Sonrasında sürece devam edilmek istenirse `-CONT` parametresiyle sürece devam edilebilir. pidof komutu ile istenen sürecin PID değeri bulunabilir. 

	pidof firefox
	kill -STOP 2568
	kill -CONT 2568


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sistem üzerinde bazı süreçlerin önceliğinin diğer süreçlere göre fazla olmasını isteyebiliriz. Bir sürecin önceliğini artırarak işlemci tarafında daha önce işleme alınmasını sağlayabiliriz. 


<h4>nice komutu </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut ile bir sürecin öncelik değerini -20 ve 20 arasında değiştirebiliriz. -20 değeri en yüksek öncelik anlamına gelir. 20 ise en düşük öncelik değeridir. Bu komutun varsayılan değeri 10'dur. Bu komut kullanılmadan çalışan süreçler için  varsayılan öncelik değeri 0'dır. 

	nice -n -10 gimp 
	top | grep gimp

<h4>renice komutu </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut nice komutu ile önceliği değiştirilen veya varsayılan öncelikli herhangi bir sürecin önceliğini değiştirmek için kullanılır.

	renice 10 -p gimp
	top | grep gimp

































