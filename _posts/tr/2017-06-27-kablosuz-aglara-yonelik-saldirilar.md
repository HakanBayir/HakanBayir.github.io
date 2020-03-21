---
layout: post
title: WPA/WPA2 Ağlarına Yönelik Saldırılar
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;WPA(`Wi-Fi Protected Access`) ve WPA2(`Wi-Fi Protected Access II`) kablosuz ağlarda güvenli giriş sağlamak için kullanılan şifreleme yöntemleridir. Daha öncesinde kullanılan WEP(`Wired Equivalent Privacy`) şifreleme yöntemi yetersiz kaldığı için bu sistemler geliştirilmiştir. WPA ve WPA2 metotlarıyla oluşturulan ağlarda, bağlanmak isteyen cihazlar ve bağlantı noktası arasında her bir oturum için PTK (`Pairwise Transient Key`) oluşturulur. Bu anahtarların eşleşmesiyle el sıkışması(`handshake`) yapılmış olur. 



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada WPA/WPA2 koruması olan bir ağa müdahelede bulunup Handshake(kimlik doğrulama paketi) yakalayacağız ve bu paketin içeriğine brute-force saldırısı düzenleyerek kablosuz ağın şifresini öğrenmeye çalışacağız.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İlk önce `airmon-ng` aracını kullanacağız. Normalde ağ kartımız sadece bulunduğu ağdaki bilgileri ve paketleri alabilir. Bu araç sayesinde monitor moda geçerek ağ kartımızın gücü yettiğince etrafımızda yayın yapan tüm ağ bilgilerini gözlemleyebileceğiz.


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İkinci aşama olarak `airodump-ng` aracı ile handshake dinlemeye başlayacağız. Eş zamanlı olarak `aireplay-ng` aracı ile paket bombardımanı yaparak servisi ya da bağlı olan cihazları o ağdan düşürüp tekrar bağlanmasını bekleyeceğiz. Tekrar bağlanma esnasında el sıkışması sağlanırken ilgili handshake paketini yakalamış olacağız.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Handshake paketini yakaladıktan sonra `aircrack-ng` ile brute-force yaparak parolayı elde etme işlemini gerçekleştireceğiz.

<br>


`1`. Terminalden ağ arayüzüne bakmak(mevcut arayüze göre komut dizisi değişecektir) ve monitor moda geçiş yapmak için aşağıdaki adımları izliyoruz.

	airmon-ng
	airmon-ng start wlan0

`2`. Sonrasında etrafta yayın yapan ağlara ait bilgileri görmek için aşağıdaki komutu yazıyoruz.

	airodump-ng wlan0mon

<img src="/img/wpa-wpa2/tarama.png">



`3`. Burada yayın yaptığı `kanal` bilgisi ve `MAC` adresi işimize yarayacak. Bu işlem ile handshake yakalamaya çalışacağız. 

	airodump-ng -c 10 -w ctrl --bssid Target_MAC_ADDRESS wlan0mon

<img src="/img/wpa-wpa2/handshake.png">

`4`. Bir önceki aşama ile eş zamanlı olarak paket bombardımanı başlatıyoruz. Hedefe bağlı cihaz ağdan düşüp tekrar bağlantı kurmaya çalışınca aradaki handshake paketini yakalıyoruz.

	aireplay-ng -0 0 -a Target_MAC_ADDRESS wlan0mon

<img src="/img/wpa-wpa2/aireplay.png">

`5`. Yakaladığımız handshake paketini aircrack aracı ile brute-force saldırısına maruz bırakıyoruz.

	aircrack-ng -w wordlist.txt ctrl-01.cap 

<img src="/img/wpa-wpa2/aircrack.png">

<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Böylece WPA/WPA2 koruması olan bir ağın parolasını elde etmiş olduk. Buradaki zayıf nokta parolanın yetersizliğidir. Uzun ve güçlü parolalar için brute-force atağı günlerce, aylarca sürebilmektedir. Parolamızı çok kullanılan kelimelerden seçmemeliyiz. Bunun yanı sıra belli bir uzunluğu da olmalıdır. `Uzun ve karmaşık parolalar` bu atağa yönelik ciddi güvenlik önlemi sağlar. Bunun yanı sıra modem arayüzünden `ağımızın bilgilerini gizlemekte` alabileceğimiz diğer önlemlerden biridir. 






















	





