---
layout: post
title: WEB For Pentester 1 - Commands Injections Bölümü Çözümleri
categories: tr
---

<h4>Example 1 </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Arkada çalışan kod : 
	
	`system("ping -c 2 ".$_GET['ip']);` şeklinde devam ediyor.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Buraya müdahele için kodun şöyle çalışıp çalışmayacağını düşünebiliriz. 

	`system("ping -c 2 127.0.0.1;	id);` 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ping -c 2 127.0.0.1; id komutunu terminalde denediğimizde çalıştığını göreceğiz.
	
	`http://192.168.0.28/commandexec/example1.php?ip=127.0.0.1;id`

<h4>Example 2 </h4>
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada kodda bir regex ifade var ve input'u IP formatının dışında kabul etmeyeceğini söylüyor. Fakat devamında `/m` ifadesi var. İnce nokta burada başlıyor. Bu ifade gelen inputu tek bir satır şeklinde kabul ediyor fakat altında başka satırlar olsa bile böyle kabul ediyor. Yani alt satıra geçip inject ifademizi bıraksak bunu okumayıp execute edecek. 

	`http://192.168.0.28/commandexec/example2.php?ip=127.0.0.1%0Aid`

<h4>Example 3 </h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu kısımda eğer regex'e uygun bir ifade girmezsek bizi aynı sayfaya tekrar yönlendireceğini söylüyor. Fakat devamında bizim girdiğimiz parametreye göre işlemini yapacak. Ama bizi anasayfaya yönlendirdiği için sonuç basmayacak ekrana. Peki sonuçları nasıl görürüz ?
	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Parametre olarak  `/commandexec/example3.php?ip=127.0.0.1;id` şeklinde bir giriş yaptığımızda bir yandan da `Burp Suite` açıyoruz. Repeater'a gönderdiğimiz isteğin response'una baktığımızda aşağıdaki gibi bir sonuç ile karşılaşıyoruz ve buradan komutlarımızın çalıştığını anlıyoruz.


<img src="/img/cmdexec/example3.png">
