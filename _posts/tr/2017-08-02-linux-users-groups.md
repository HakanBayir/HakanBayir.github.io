---
layout: post
title: Linux Sistemde Kullanıcılar ve Gruplar
categories: tr
---



<h4>Kullanıcılar</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Linux sisteminde tüm kullanıcı hesaplarına ait bilgiler /etc/passwd dosyasında bulunur. Buradaki bilgilerin zorunlu formatı şu şekildedir : 

`kullanıcı adı : şifre : hesap no (UID) : grup hesap no (GID) : info : ana dizin (home) : shell`

<br>

`Kullanıcı adı(Username)` : Kullanıcı için eşsiz olan isimdir.
<br>
`Şifre (Password)` : Kullanıcıya ait olan şifredir.
<br>
`Hesap No. (UID)` : Sistemin kullanıcıyı tanımasını sağlayan ve eşsiz olan numaradır. Her kullanıcı için farklı olması gereken bir tamsayıdır. Sistem, bir kullanıcı ile ilgili süreçleri veya dosya izinlerini takip ederken o kullanıcıya ait hesap numarası ile işlem yapar.
<br>
`Grup Hesap No. (GID)` : Kullanıcının ait olduğu grubun eşsiz olan numarasıdır. Dosyalar üzerindeki izinler belirlenirken önem kazanır. Sistemdeki gruplar /etc/group dosyası içinde yer alır. Gruplar belirli dosyalara belirli yetkilerle erişime izin verilen kullanıcı kümeleridir. 
<br>
`Info `: Genellikle kullanıcının gerçek adı gibi bilgiler olabilir.
<br>
`Ana dizin (home) `: Kullanıcının sisteme bağlandığı anda kendinin içeride bulduğu aktif dizinin adıdır.
<br>
`Shell `: Kullanıcı sisteme girdiği zaman çalışacak komut.(shell)
<br>



<h5>Sistemde bulunan bazı kullanıcılar </h5>

adm : Kullanıcı hesapları ve log dosyalarının sahibidir.
<br>
bin : Executable dosyaların sahibidir.
<br>
daemon : Sadece bazı sistem süreçlerinin ve onlara verilecek izinlerin yönetimi için kullanılır.
<br>
root : Kullanıcı hesap numarası 0 olan süper kullanıcıdır. Sisteme sınırsız erişim hakkı bulunur.
<br>
userctrl:x:700:800::/home/:
<br>
ctrl:x:1000:1000:Ctrl:/home/ctrl:/bin/bash
<br>
root:x:0:0:root:/root:/bin/bash
<br>

<h4>Gruplar</h4>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Her kullanıcı bir ya da birden fazla grubun üyesidir. Belirli bir işlevi yerine getirecek olan kullanıcılar aynı grupta bulunabilir. Gruplar ile ilgili bilgiler /etc/group dosyasında saklanır. Buradaki grupların zorunlu formatı şu şekildedir : 

`grup adı : grup şifresi : grup no (GID) : kullanıcılar`

<h5>Sistemde bulunan bazı gruplar </h5>

root::0:root
<br>
bin::1:root,bin,daemon
<br>
daemon::2:root,bin,daemon
<br>
sys::3:root,bin,adm
<br>



<h5>su Komutu </h5> 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sistemde bir kullanıcı , başka bir kullanıcı yerine giriş yaparken su komutunu kulllanır.

	su root
	su ctrl



<h4>Kullanıcı ve Grup İşlemleri</h4>

<h5>useradd</h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sisteme kullanıcı eklemek için kullanılır.

	useradd userctrl
	passwd userctrl
	id userctrl

<h5>userdel</h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut ile hedef kullanıcıya ait tüm bilgiler /etc/passwd, /etc/shadow ve /etc/group dosyalarından silinir. -r parametresi ile kullanıcıya ait tüm dosyalar sistemden silinir.

	userdel userctrl
	userdel -r userctrl

<h5>usermod </h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Hedef kullanıcı ile ilgili bilgileri değiştirmek için kullanılır.
-d parametresi kullanıcının home dizinini değiştirir.
-l parametresi kullanıcı adını değiştirir.
-s parametresi shell bilgisini değiştirmek için kullanılır.
-u parametresi user id (UID) değiştirmek için kullanılır.
-g parametresi group id (GID) değiştirmek için kullanılır. 

	usermod -d /home/user1 userctrl
	usermod -l newuserctrl userctrl
	usermod -u 100 userctrl
	usermod -g 800 userctrl




<h5>groupadd</h5> 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu komut /etc/group dosyasına yeni bir grup ekler. 
-g parametresi group id (GID) belirlemek için kullanılır.

	groupadd -g 600 groupctrl

<h5>groupdel </h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Hedef grubu /etc/group dosyasından siler.

	groupdel groupctrl

<h5>groupmod </h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Hedef grubun bilgilerini değiştirmek için kullanılır.

	groupmod -g 700 -n newgroupctrl groupctrl
	groupmod -n newgroupctrl groupctrl




















































