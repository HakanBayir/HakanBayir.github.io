---
layout: post
title: Jekyll İle Blog Oluşturma
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Jekyll , Ruby kullanılarak yazılmış bir statik site üreticisidir. Jekyll, markdown ve scss formatında yazdığımız yazıları html ve css kodlarına çevirerek  sitemizi üretir. Bilgisayarımıza 'RubyGems' ile jekyll kurmak için aşağıdaki komutu terminale yazabilirsiniz. 

	gem install jekyll 



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Jekyll artık kuruldu. Şimdi statik blog sitemizi oluşturma aşamasına geçebiliriz. Site klasörümüzü 
oluşturmak istediğimiz dizine gidip aşağıdaki komutları girerek site klasörünü meydana getirebiliriz.

	jekyll new klasor_adi



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Klasörümüz oluşturuldu. İlk komut ile dosyaların olduğu dizine gidip sonrasında ikinci komut ile sitemizi
localhost'umuzda yayınlayabiliriz. 

	cd klasor_adi

	jekyll serve 



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Çalışan servisi tarayıcımızdan http://localhost:4000 adresine giderek görebiliriz. Burada Jekyll' ın bize 
default olarak oluşturduğu siteyi göreceğiz. <a href ="http://jekyllthemes.org/">http://jekyllthemes.org/</a> adresini ziyaret ederek buradan çevrimiçi tema edinip ,doğrudan  veya üzerinde değişiklikler yaparak kendi blogumuzu düzenleyebiliriz.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Oluşturduğumuz siteyi github üzerinden yayınlamak için öncelikle bir github hesabımız olması gerekiyor.
<a href="https://github.com/">https://github.com/</a> adresini ziyaret ederek bir github hesabi açabilirsiniz. Oturum açıp profile girdikten sonra Repository bölümüne gelip buradan da new butonuna tıklayarak yeni bir repository oluşturabiliriz.
Bu repository bizim blogumuzdaki tüm dosyaları tutacak yer olacak. Repository 'nin adı, <strong>githubkullaniciadi.github.io</strong> şeklinde olmalıdır. Böylece blog sayfamız için gereken repository 'i
 oluşturmuş olduk.  Şimdi sıra sitemizi bu repository 'e yollamaya geldi. Sitemizi localhost'dan çıkarıp tüm ağlarda tanınır hale getirecek olduğumuz için site klasörümüz içerisindeki <strong>_config.yml</strong> dosyasında bir değişiklik yapmamız gerekiyor. Buradaki 

 - url : ""

kısmını
 
 - url : "https://githubkullaniciadi.github.io"

olarak düzenliyoruz.



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Şimdi sıra geldi dosyalarımızı github repository' imize yollamaya. Bunun için terminale gelip aşağıdaki komutları yazabiliriz ve dosyalarımızı gönderebiliriz.

	cd klasor_adi

	git init

	git remote add origin https://github.com/githubkullaniciadi/githubkullaniciadi.github.io.git

	git add -A

	git commit -m "gönderi"

	git push origin master




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Jekyll, kullanımı kolay ve github üzerinden çalışabilen güncel bir servistir. Değişiklikler anlamında daha fazla hareket edebilmek için ve kullanışlı olması açısından <strong>Markdown</strong> syntax yapısı araştırılabilir. 
















