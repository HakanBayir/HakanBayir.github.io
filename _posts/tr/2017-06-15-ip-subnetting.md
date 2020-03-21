---
layout: post
title: Ip-Subnetting
categories: tr
---




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Yerel ağlarda kullandığımız ip adreslerine private ip adresi denir. Bu ip adresi aralığı bölgelere
göre değişiklik gösterir ve A, B, C sınıfları olarak 3'e ayrılır. Bu aralıklar şöyledir : 

 A Sınıfı  | 10.0.0.0-10.255.255.255 
 B Sınıfı  | 172.16.0.0-172.31.255.255 
 C Sınıfı  | 192.168.0.0-192.168.255.255 

Türkiye C sınıfı IP aralığını kullanır. Bilgisayarınızda Terminale Windows işletim sistemleri için

    ipconfig

Linux tabanlı işletim sistemleri için 

    ifconfig

yazdığınızda bağlı olduğunuz ağ arayüzünden size verilen ip adresini 192.168.... şeklinde devam 
ettiğini göreceksiniz. Bu sizin private ip adresinizdir.

Önemli kavramlara göz atalım : 

<strong>Default Gateway</strong> : Bu yerel ağımızın internete çıkış noktasının ip adresidir. Bu bir deyişle
router'ımızın adresidir.

<strong>Netmask</strong> : Ağ maskesi, bir ağı alt ağlara bölmek ve ağın mevcut ana 
bilgisayarlarını belirlemek için kullanılan 32 bitlik bir maskedir. 

<strong>Broadcast</strong> : Ağdaki bütün adresleri temsil eden ve bulunduğu ağın son adresi olan ip adresidir. 


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ağda bulunan bir cihazın ip adresi ile alt ağ adresini `AND` işlemine tabii tuttuğumuzda 
ağ adresini buluruz. Örneğin bir ağa bağlı cihazın ip adresi 192.168.0.24 ve netmask'ı 
255.255.255.0 olsun. 

<br>
`IP Adresi`: 11000000 10101000 00000000 0001000
<br>
`Netmask`  : 11111111 11111111 11111111 0000000
<br>
AND -------------------------------------------------------------------->
<br>
`Ağ Adresi`: 11000000 10101000 00000000 0000000
<br>

Buradan Ağ adresini 192.168.0.0 olarak görüyoruz. Bu da aslında bizim tüm ağımızın adresi oluyor.

<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir maskede '1' bitlerinin sayısı ağ adresi kısmını gösterir. Bu ağ için

    nmap -sP -T4 192.168.0.0/24 

şeklinde tarama yaparak aynı ağdaki cihazları görüntüleyebiliriz.


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir maskede '0' bitleri ağdaki host sayısını gösterir. Bunu `2^n` formülüyle hesaplarız. 
Yukarıdaki örnek için düşünecek olursak 255.255.255.0 maskesini 32 bite ayırarak baktığımızda
8 tane 0 olduğunu görürüz. Bu da bize o ağda 256 tane host bulunabileceğini gösterir.
İstemci sayısını ifade ederken de Gateway ve Broadcast adreslerini çıkarıp "`(2^n-2)`" 254 olarak 
söyleriz.

<br>
<strong>Örnek</strong> : 178.125.40.0 ip adresli ağı,içerisinde 32 host bulunacak alt ağlara bölelim.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;32 tane hostumuz olacaksa 2^n = 32 eşitliğinden n değerini yani host bitlerinin sayısını
5 olarak buluruz. Hatırlarsak '0' bitleri host bitleri anlamına geliyordu. 
Buradan ağ maskesinin 27 tane '1' bit'inden ve 5 tane '0' bit'inden oluştuğunu anlarız.

`Ağ Maskesi` : 11111111.1111111.1111111.11100000
	   : 255.255.255.224

`Ağ Adresi`       : 178.125.40.0/27<br>
`Default Gateway` : 178.125.40.1<br>
`Broadcast`      : 178.125.40.31<br>

`Ağ Adresi`     : 178.125.40.32/27<br>
`Ağ Adresi`      : 178.125.40.64/27<br>
`Ağ Adresi`       : 178.125.40.96/27<br>



Kontrolü sağlayalım.  
	`Gateway`             : 10110010 01111101 00101000 00000001 <br>
	`Netmask`             : 11111111 11111111 11111111 11100000 <br>
	AND --------------------------------------------------------------------><br>
	`Ağ Adresi`           : 10110010 01111101 00101000 00000000 <br>
				178   .  125   .  40    .   0                 <br>

olarak ağ adresini bulmuş oluruz. 

    route -n 

komutunu çalıştırarak Default Gateway, Ağ Adresi ve Network Mask değerlerinizi görebilirsiniz.

<h4>Neden Subnetting ? </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir ağı alt ağlara bölme olayı ağ performansını optimize eder ve ağın yönetimini kolaylaştırır.
Farklı işlemler için yönetilen kurumsal bir ağ düşünelim. Kurumun farklı bölümleri için farklı işlemler 
dönüyor,dosya erişimleri ve paylaşımları oluyor. Bu bölümleri birbirinden bağımsız bir hale getirme ve yetkileri
sınırlandırma adına subnetting uygulanır. Bu sayede her birim kendi ağı içerisinde hareket eder.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aynı zamanda ağ güvenliği içinde önem arz eder. Örneğin ağımızı yerel ağ ve hosting ağı şeklinde
ayırdığımızda host kullanıcılarının dosya sunucularımıza,veri tabanımıza erişeme durumu olmaz. Aynı
zamanda kendimizi ağ içerisinde sadece kendi bulunduğumuz bir alt ağa alarak belli tipteki ataklara
karşı basit bir önlem almış olabiliriz.









