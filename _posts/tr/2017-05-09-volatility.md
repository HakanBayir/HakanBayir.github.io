---
layout: post
title: Volatility
categories: tr
---






<h4>Ram Capturing</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Windows 7 makine de memory(RAM) dump etmek için <strong>DumpIt</strong> aracını kullanacağım. 
Aracı <a href ="https://www.downloadcrew.com/article/23854-dumpit">buradan</a> indirebilirsiniz.
Windows7 makinede cmd açarak bulunduğu dizine gelip DumpIt.exe'yi çalıştırıyoruz. Memory
Dump işlemi başlıyor ve belli bir süre sonra kapatıp durduruyoruz. DumpIt.exe'nin bulunduğu dizinde
oluşan <strong>RAW</strong> dosyasını görüyoruz ve bu bizim Volatility ile inceleyeceğimiz <strong>Memory Dump File</strong>'mız olacak.



<h4>Volatility Analiz</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İlk olarak volatility ile RAW bilgilerine bakmak için şu komutu kullanıyoruz. 

	volatility -f ZAFIYETLI-PC.raw imageinfo

<img src="/img/volatility/imageinfo.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonrasında profile göre <strong>KDBGSCAN</strong> yapıyoruz. Kdbgscan Olası KDBG değerlerini bulmamızı sağlar.
KDBG, hata ayıklama amacıyla Windows çekirdeği tarafından tutulan bir yapıdır. 
Çalışmakta olan süreçlerin ve yüklenen çekirdek modüllerinin bir listesini içerir. 
Ayrıca hangi Hizmet Paketi'nin yüklendiğini ve bellek modelini (32-bit vs 64-bit) 
belirleyebilmenizi sağlayan bazı sürüm bilgilerini de içerir.



	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 kdbgscan
	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 pslist

<img src="/img/volatility/kdbgscan.png">
<img src="/img/volatility/pslist1.png">
<img src="/img/volatility/pslist2.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;KDBG değerinide belirledikten sonra o anda çalışan processleri görüntülemek için <strong>PSLIST</strong> komutunu
ekledim. Burada en altta <strong>DumpIt.exe</strong> görünüyor, o an kullandığımız DUMP aracı olarak. Bunun yanında 
tarayıcılar veya diğer çeşitli programları görüyoruz. 


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;32 ve 64 bit Windows Vista, Windows 2008 Server ve Windows 7 RAM imajlarında ağ programlarını
taramak için <strong>NETSCAN</strong> komutunu kullanırız. Bu tarama, TCP bitiş noktalarını, TCP dinleyicilerini, 
UDP uç noktalarını ve UDP dinleyicilerini bulur. IPv4 ve IPv6 arasında ayrım yapar, yerel ve
uzak IP'yi (varsa), yerel ve uzaktaki bağlantı noktasını (varsa), soketin bağlı olduğu veya
bağlantı kurulduğu zamanı ve geçerli durumunu (TCP bağlantıları için)  gösterir. 


	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 netscan

<img src="/img/volatility/netscan.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fiziksel bellekteki FILE_OBJECT'leri bulmak için <strong>FILESCAN</strong>
komutunu kullanırız. 


	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 filescan

<img src="/img/volatility/filescan.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir sonraki taramamız <strong>PSSCAN</strong> taraması. Bu daha önce sona eren işlemleri (etkin olmayan) ve 
bir rootkit tarafından gizlenmiş veya bağlantısız olan işlemleri bulabilir. Diğer yandan da
işlem listesini bir ağaç yapısında görüntülemek için, <strong>PSTREE</strong> komutunu kullanırız. Bu süreçleri pslist ile 
aynı tekniği kullanarak numaralandırır, bu nedenle de gizli veya bağlantısız süreçler gösterilmez.
Child process'ler, girinti ve süreler kullanılarak belirtilir


	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 psscan
	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 pstree


<img src="/img/volatility/pstree.png">


<h4>Uygulama</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bellekte kayıt defterinin sanal adreslerini ve karşılık gelen disk üzerindeki 
tam yolları bulmak için, <strong>HIVELIST</strong> komutunu kullanırız.

	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 hivelist

<img src="/img/volatility/hivelist.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada bir uygulama gerçekleştireceğim. Windows makinenin kullanıcı parolasını dump edip 
kırmaya çalışacağız.

	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 
hashdump -s 0xfffff8a000aa2010 -y 0xfffff8a000023010 > hash.txt

<img src="/img/volatility/hashdump.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada -s parametresi \SystemRoot\System32\Config\SAM kısmını, -y parametresi de 
\REGISTRY\MACHINE\SYSTEM kısmını alıyor . Hash değerlerini aldıktan sonra <strong>OPHCRACK</strong>
aracını kullanarak parolayı buluyoruz. 

<img src="/img/volatility/ophcrack.png">



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir işlemin yüklenmiş DLL'lerini görüntülemek için <strong>DLLLIST</strong> komutunu kullanırız.
Bir süreç LoadLibrary (veya LdrLoadDll gibi bazı türevleri) çağırdığında DLL'ler otomatik 
olarak bu listeye eklenir.

	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 dlllist
	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 dlllist -p 2624
	volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 dlldump 
--pid 2356 --dump-dir=.


<img src="/img/volatility/dlllist.png">




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir işleme ait açık handles'ları görüntülemek için, <strong>HANDLES</strong> komutunu kullanırız. Bu, dosyalar, 
kayıt defteri anahtarları, muteksler, adlandırılmış kanallar, olaylar, pencere istasyonları, 
masaüstleri, iş parçacıkları ve diğer tüm yürütülebilir nesne türleri için geçerlidir. 

	volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 handles -p 2624


<img src="/img/volatility/handles.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;UserAssist yardımcı programı, bir Windows makinesinde çalıştırılan programların sayısını,son yürütme 
tarihini ve saatini içeren bir program tablosunu görüntüler. Windows Gezgini, bu bilgileri UserAssist kayıt defteri girdileri içinde tutar.

	volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 userassist


<img src="/img/volatility/userassist.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>CMDSCAN</strong> eklentisi, bir konsol kabuğu (cmd.exe) üzerinden girilen komutlar için
XP / 2003 / Vista / 2008'de csrss.exe ve Windows 7'de conhost.exe belleklerini arar. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MaxHistory değerinin cmd.exe penceresinin sol 
üst köşesinde sağ tıklayarak Özellikler'e gelerek değiştirilebilir. 
Windows sistemlerinde varsayılan değer 50'dir, yani en son 50 komut kaydedilir.


      volatility -f ZAFIYETLI-PC.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 cmdscan
	volatility -f ZAFIYETLI-PC3.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 consoles

<img src="/img/volatility/consoles.png">



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>IEHISTORY</strong> eklentisi IE geçmişi önbellek dosyalarının parçalarını kurtarır. 
Erişilen temel bağlantıları (FTP veya HTTP üzerinden), bulabilirsiniz.

	volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 iehistory

<img src="/img/volatility/iehistory.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Çalışan Windows Servislerini görmek için <strong>SVCSCAN</strong> taraması yapıyoruz. 

	volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 svcscan

<img src="/img/volatility/svcscan.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fiziksel bellekteki DRIVER_OBJECT'leri bulmak için, <strong>DRIVERSCAN</strong> komutunu kullanırız. 


      volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 driverscan


<img src="/img/volatility/driverscan.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir işlemle ilişkili SID'leri (Güvenlik Tanımlayıcıları) görüntülemek için, <strong>GETSIDS</strong> komutunu 
kullanırız. Diğer şeylerin yanı sıra, bu, kötü niyetle  ayrıcalıklara ve hangi süreçlerin 
belirli kullanıcılara ait olduğunu belirlemenize yardımcı olabilir.

	volatility -f ZAFIYETLI-PC2.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 getsids

<img src="/img/volatility/getsids.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tek bir dosyaya bir süreçte yerleşik tüm sayfaları ayıklamak için , <strong>MEMDUMP</strong> komutunu kullanırız. 
Çıkış dizinini -D veya --dump-dir = DIR ile sağlarız.

	volatility -f ZAFIYETLI-PC3.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 memdump -p 
1804 -D RECOVER



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir işlemin yürütülebilir dosyasını çıkarmak için <strong>PROCDUMP</strong> komutunu kullanırız.

	volatility -f ZAFIYETLI-PC3.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 procdump -p 
1804 -D RECOVER

<img src="/img/volatility/procdump.png">



<h4>Uygulama</h4>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ram imajı alınırken arkada <strong>paint.exe</strong> açacağız ve bunun RAM'e yansımasını inceleyeceğiz.
Aldığımız RAM imajından <strong>mspaint.exe</strong> için memory dump yapıyoruz. Paint için <strong>PID</strong> numarasını 
buluyoruz.


	volatility -f ZAFIYETLI-PC5.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 psxview


	volatility -f ZAFIYETLI-PC5.raw --profile=Win7SP1x64 --kdbg=0xf80001a570a0 memdump -p 
2724 -D RECOVER



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonrasında burada çıkan <strong>.dmp</strong> uzantılı dump dosyasını <strong>.data</strong> dosyasını çeviriyoruz. Ve GIMP'te açıyoruz.

NOT: mspaint.exe için çalışırken görev yöneticisine giderek <strong>Applications</strong> bölümüne gelip
sağ tıklayarak <strong>CREATE DUMP FILE</strong> diyerekte dump dosyası alabiliriz.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>GIMP</strong> 'te offset değerleriyle oynayarak anlamlı bir şey bulmaya çalışırken <strong>test_FLAG</strong>'i bulmuş oluyoruz.





<img src="/img/volatility/flag.png">
