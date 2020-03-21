---
layout: post
title: 3-Way Handshake
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3-Way Handshake TCP üzerinde çalışan bir kontrol mekanizmasıdır. Bu 
mekanizma sayesinde verinin karşı tarafa gidip gitmediği kontrol edilir.
Bu bir anlamda TCP'yi güvenli yapan bir mekanizmadır. Kastettiğim
güvenlik herhangi bir şifreleme durumu değil de veri iletimi sırasında yapılan
ilgili kontrolün yapılmasıdır. Kontrol yapıldıktan sonra veri iletimi
gerçekleşir.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İletişime geçmek isteyen istemcinin hedef sunucunun bağlantı noktasına 
`SYN(Synchronize)` paketi göndermesiyle ilk adım başlatılır. Hedef sunucunun
bu bağlantı noktası aktif durumda ise `SYN + ACK(Acknowledge)` paketleriyle 
dönüş yapar ve açık durumda olduğunu bildirir. Bu paketi alan istemci 
`ACK` paketi ile yanıt verir ve iletişim sağlanmış olur. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aşağıda örnek bir `wireshark` yakalaması var. Bu çıktı üzerinde 
`hakanbayir.com` adresine yaptığım GET isteği sonucu ortaya çıkan
 3-way handshake mekanizmasını görüyoruz. 

<img src="/img/3-way-handshake/wireshark.png">


