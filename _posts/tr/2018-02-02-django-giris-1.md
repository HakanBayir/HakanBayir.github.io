---
layout: post
title: Django Giriş [ 1 ]
categories: tr
---





<center><h3>Django Nedir ?</h3></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Django, Python ile yazılmış, ücretsiz ve açık kaynak kodlu web uygulama geliştirme ortamıdır. Uygulama geliştirmeyi hızlandırmak için gereken bazı ayarlamaları ve dosyaları sizin için otomatik hazır hale getirebilir. Aynı zamanda uygulamanızı ayaklandırmanız için apache, Ngnix gibi farklı bir web server kurmanıza gerek kalmadan, kendi sunucusu ile bu işlemleri gerçekleştirebilir.Bu ortam MVC (Model - View - Controller) yapısına oldukça benzeyen `MVT (Model - View - Template)` mimarisi ile çalışmaktadır.


<center><h5>Model</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burası, uygulamanın veri ile iletişimde bulunduğu bölümdür. Veritabanı,tablolar, sütunlar için bir temsilci diyebiliriz. Tam olarak veriler burada bulunur diyemesekte buradan temsil edildiğini söyleyebiliriz. Model dosyası içerisinde verilerimizin abstract halleri bulunur. Bu yüzden veritabanı kısmına karşılık gelen yer burasıdır.

<center><h5>View</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;View bölümü için yönetim kısmı diyebiliriz. Burada uygulamanın işlevlerini belirleyecek metodlar/fonksiyonlar yer alır. Modeller ve Template'ler arasında veriyi taşıyabilir ve kullanabilir. Uygulamayı tarayıcıdan çağıracağımız zaman, view kısmında karşılığı olan bir metod bulunmalıdır.Bu metod sayesinde yapılan çağrıya nasıl bir yanıt verileceği belirlenir. İstek yapılan sayfa tarayıcıya buradan gönderilir. Veritabanında yani model kısmında  bulunan veriler, buradaki metodlar ile alınıp istemci tarafına gönderilir.


<center><h5>Template</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burası, uygulamanın sunumuyla ilgili yapıların bulunduğu bölümdür. İstemcinin göreceği, front-end işlemlerinin gerçekleştirildiği yerdir. HTML dosyalarımız burada bulunacaktır.

<center><h3>Proje ve Uygulama Oluşturma</h3></center>
<center><h5>Proje Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Django-admin, django'nun temel operasyonel işlemlerini yürütmek için geliştirilmiş yardımcı komut satırı uygulamasıdır. Bu uyguluma ile projemizi aşağıdaki gibi oluşturuyoruz. 

	django-admin startproject practice
	cd practice


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemi yaptığımızda bu dizin içerisinde `manage.py` isimli bir dosya ve `practice` isimli bir dizin oluşur. manage.py dosyası çeşitli parametreler alarak sunucu başlatma, uygulama oluşturma, uygulama için ayarları tanımlama vb. gibi işlemleri yerine getirmemizi sağlar. Örnek dosya yapısı aşağıdaki gibi olacaktır. 

	---practice
	   ---practice
	     ---__init__.py
	     ---settings.py
	     ---urls.py
	     ---wsgi.py
	   ---manage.py

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Projeyi ayaklandırmak için aşağıdaki gibi `manage.py` dosyasını çalıştırmamız gerekiyor. Varsayılan olarak port numarası 8000 olarak belirlenmiştir. Aşağıdaki gibi başlatıldığında 8000 portu üzerinde çalışacaktır. Devamına eklediğiniz numaraya göre port değiştirilebilir.


	python3 manage.py runserver



<center><h5>Uygulama Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Proje oluşturduğumuzda otomatik oluşan `manage.py` dosyası django-admin gibi temel işlemlerimizi yerine getirmenin yanı sıra aşağıdaki gibi uygulama oluşturmamızı da sağlar. 

	python3 manage.py startapp reverseString

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemden sonra dosya yapısı aşağıdaki hali almıştır. 

	
	---practice
	   ---practice
	     ---__init__.py
	     ---__pycache__
	     ---settings.py
	     ---urls.py
	     ---wsgi.py
	   ---manage.py
	   ---reverseString
	     ---admin.py
	     ---apps.py
	     ---__init__.py
	     ---migrations
	         ---__init__.py
	     ---models.py
	     ---tests.py
	     ---views.py





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulama oluşturduktan sonra uygulamayı projeye bildirmemiz gerekiyor. Bunu `practice/settings.py` dosyası üzerinde yapıyoruz. Burada hazır olarak yüklenmiş diğer uygulamalarda yer alıyor. Kendi uygulamamızın ismini de buraya ekliyoruz.

	#practice/settings.py
	INSTALLED_APPS = [
	    'django.contrib.admin',
	    'django.contrib.auth',
	    'django.contrib.contenttypes',
	    'django.contrib.sessions',
	    'django.contrib.messages',
	    'django.contrib.staticfiles',
	    'reverseString',
	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulamamızı kök dizine getirmek için bazı işlemler yapmamız gerekiyor. View kısmında bahsettiğim gibi tarayıcıdan herhangi bir dizine gidilirken bir istek yapılmış olur. Bu isteğe karşılık gelen fonksiyon aranır ve içeriğinde yazan işlemler uygulanır. Django ortamında `urls.py` dosyaları bulunur ve bu dosyalar bizim url'ler ile ilgili işlemlerimizi düzenler ve yönetir. Tarayıcıdan yazdığımız değer ilk önce buraya düşer. Burada karşılık gelen fonksiyon ismini bulur. O ismi view bölümünde arar ve gereken işlemleri gerçekleştirir. İlk düzenleyeceğimiz dosya `practice/urls.py` dosyasıdır. Burada `/admin` sayfası için daha önceden tanımlanmış bir ifade bulunmaktadır. Bunun yanına `kök(/)` dizin için kendi sayfamızı ekliyoruz.

	#practice/urls.py
	from django.contrib import admin
	from django.conf.urls import url, include
	from reverseString.views import *

	urlpatterns = [
	    url('admin/', admin.site.urls),
	    url(r'^$', homepage),

	]




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu ekleme işleminin ardından, tarayıcıdan kök dizin isteği yapıldığında `homepage` adlı fonksiyon aranacaktır. Bu fonksiyonu `reverseString/views.py` dosyasına ekleyeceğiz. Yukarıda görüldüğü `from reverseString.views import *` yazarak oradaki tüm fonksiyonları buraya import ettik. Şimdi `reverseString/views.py` dosyasını aşağıdaki gibi düzenliyoruz.
 
	#reverseString/views.py
	from django.shortcuts import render,HttpResponse

	def homepage(request):
	    return HttpResponse('Hello from Homepage')



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemin ardından sunucuyu tekrar çalıştırdığımızda ve kök dizine gittiğimizde `Hello from Homepage` mesajı ile karşılaşacağız. Şimdi bir HTML sayfası oluşturalım ve `render()` fonksiyonu ile istemci tarafına oluşturduğumuz bu sayfayı getirelim. Öncelikle `reverseString` dizini altına `templates` adlı bir dizin oluşturalım. Buranın altına da HTML sayfamızı ekleyelim.

	mkdir reverseString/templates
	touch reverseString/templates/home.html


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Oluşturduğumuz templates dizini için yol bildirmemiz gerekiyor. Bunun için `practice/settings.py` dosyasına gidiyoruz. Dosyayı aşağıdaki gibi düzenledikten sonra templates dizinimiz ulaşılabilir durumda olacaktır. 


	#practice/settings.py
	TEMPLATES = [
	    {
		'BACKEND': 'django.template.backends.django.DjangoTemplates',
		'DIRS': [os.path.join(BASE_DIR,'templates')],
		'APP_DIRS': True,
		'OPTIONS': {
		    'context_processors': [
		        'django.template.context_processors.debug',
		        'django.template.context_processors.request',
		        'django.contrib.auth.context_processors.auth',
		        'django.contrib.messages.context_processors.messages',
		    ],
		},
	    },
	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemi tamamladıktan sonra `reverseString/views.py` dosyasına gelip aşağıdaki gibi düzenleyebiliriz. Sunucuyu başlatıp tarayıcıdan tekrar kök dizine gittiğimizde `home.html` sayfası ile karşılaşacağız.

	#reverseString/views.py
	from django.shortcuts import render,HttpResponse

	def homepage(request):
	    return render(request,'home.html')



<center><h5>Reverse String Uygulaması</h5></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Yazıyı basit bir uygulama ile sonlandırmak istiyorum. Anasayfa üzerinde input alıp , aldığı inputu tersten yazan basit bir uygulama yazalım.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Kullanıcı kısmı için `reverseString/templates/home.html` dosyasını aşağıdaki gibi düzenliyoruz. 

	#reverseString/templates/home.html
	<html>
	<head>
	    <title>Reverse String Application</title>
	</head>
	<center><h3>Reverse String Application</h3></center>
	<hr>
	<body>
	    <form action="/get_string/" method="GET">
		String    : <input type="text" name="mystring"><br><br>
		<input type="submit" value="Send"><br><br>
	    </form>
	</body>
	</html>




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Buradan girilen değer GET metodu ile `/get_string` dizinine yani `get_string()` fonksiyonuna gönderilecek. Bu yüzden `practice/urls.py` ve `reverseString/views.py` altında düzenlemeler yapmamız gerekiyor. HTTP GET isteği yapıldığında `/get_string` url'i biliniyor olmalı, bu yüzden aşağıdaki gibi url'i ekliyoruz.


	#practice/urls.py
	from django.contrib import admin
	from django.conf.urls import url, include
	from reverseString.views import *

	urlpatterns = [
	    url('admin/', admin.site.urls),
	    url(r'^$', homepage),
	    url(r'^get_string/$', get_string),

	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL dosyasına bakılıp karşılık gelen fonksiyonun `get_string()` olduğu görüldüğünde view bölümünde aranacaktır. Buraya ilgili fonksiyonu ekledikten sonra string'imizi `reverse_string()` fonksiyonuna yollayarak ters çevirme işlemini gerçekleştiriyoruz.


	#reverseString/views.py
	from django.shortcuts import render,HttpResponse

	def homepage(request):
	    return render(request,'home.html')


	def get_string(request):
	    text = request.GET['mystring']
	    text = reverse_string(text)
	    return HttpResponse(text)

	def reverse_string(text):
	    return text[::-1]


<center><h3>Sonuç</h3></center>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Girişin ilk aşamasında url oluşturma, view üzerinde metod tanımlama, template üzerinden gelen veriyi işleyerek geri gönderme ve temel konfigürasyonlar gibi işlemleri gerçekleştirmiş olduk. Bir sonraki django yazısında veritabanı ile ilgili işlemlerden bahsedeceğim. 



















