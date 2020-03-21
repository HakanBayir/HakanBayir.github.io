---
layout: post
title: Django Giriş [ 2 ]
categories: tr
---





<center><h3>Django'da Modeller</h3></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Django'da modeller, verilerin kaynağıdır. Burada verilerin özellikleri tutulur ve onlara işaret edilir. Her bir veri tablosu için ayrı bir class tanımlanır. Tabloya ait üyeler ilgili class'ın içerisinde yer alır. Django'da bir model class'ı oluşturduktan sonra buraya dışarıdan erişebilmemiz için bir API sağlanır. Bu API ile veritabanı sorgularımızı kullanabiliriz. Bir önceki yazıda oluşturduğumuz uygulama üzerinde `practice/settings.py` dosyasına giderek varsayılan veritabanı bilgisini görebilirsiniz. Aşağıdaki gibi bir çıktı olacaktır. Django default olarak sqlite3 veritabanını kullanır. 

	#practice/settings.py
	DATABASES = {
	    'default': {
		'ENGINE': 'django.db.backends.sqlite3',
		'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
	    }
	}


<center><h5>Model Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir önceki yazıda oluşturduğumuz projenin üzerine yeni bir uygulama oluşturalım ve modeller üzerinde temel işlemler gerçekleştirelim. Aynı zamanda bu uygulama için kendine ait bir yol belirtelim. Bir önceki projede uygulama kök dizin üzerinden hizmet veriyordu. Bu uygulama için kendine ait bir `urls.py` dosyası oluşturalım ve farklı bir dizin üzerinden hizmet vermesini sağlayalım. Öncelikle aşağıdaki gibi yeni uygulamamızı oluşturuyoruz.

	
	python3 manage.py startapp databaseOperations


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Yeni uygulamamızı da `practice/settings.py` dosyasına aşağıdaki gibi bildirmemiz gerekiyor. 

	#practice/settings.py
	INSTALLED_APPS = [
	    'django.contrib.admin',
	    'django.contrib.auth',
	    'django.contrib.contenttypes',
	    'django.contrib.sessions',
	    'django.contrib.messages',
	    'django.contrib.staticfiles',
	    'reverseString',
	    'databaseOperations',
	]
	

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Şimdi yeni uygulamamız için bir `urls.py` dosyası tanımlayalım. Bu dosya varsayılan olarak gelmeyecektir. Kendimiz oluşturmamız gerekiyor.

	touch databaseOperations/urls.py



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemi gerçekleştirmek bir dizi operasyon gerektiriyor. Öncelikle yeni uygulamamızın içerisinde oluşturduğumuz `databaseOperations/urls.py` aşağıdaki gibi düzenleyeceğiz. Bu sayede tarayıcı üzerinde `/databaseOperations` sorgusu yapıldığında hangi fonksiyonun çalışacağı belirlenecek. 

	#databaseOperations/urls.py
	from django.contrib import admin
	from django.conf.urls import url
	from databaseOperations.views import *

	urlpatterns = [
	    url(r'^$', databaseOperationsHomepage),

	]



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Şimdi de `databaseOperations/views.py` üzerinde ilgili metodun ne işlem yapacağını tanımlayacağız. URL dosyasında karşılığı bulunan fonksiyonu views bölümüne gelip ne yapması gerektiğini arayacak.


	#databaseOperations/views.py
	from django.shortcuts import render,HttpResponse

	def databaseOperationsHomepage(request):
	    return HttpResponse('Database Operations Homepage')


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Son olarak `practice/urls.py` dosyasına yeni uygulamamızdaki url'leri eklememiz gerekiyor. Çünkü sorgu kök dizinden başlayacağı için önce buradan nereye bakılması gerektiğine karar verilecek. 

	#practice/urls.py
	from django.contrib import admin
	from django.conf.urls import url, include
	from reverseString.views import *
	from databaseOperations.views import *

	urlpatterns = [
	    url('admin/', admin.site.urls),
	    url(r'^$', homepage),
	    url(r'^get_string/$', get_string),

	    url(r'^databaseOperations/', include('databaseOperations.urls')),

	]



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu işlemlerin ardından tarayıcıdan `127.0.0.1:8000/databaseOperations` sayfasına gildiğinde `Database Operations Homepage` mesajı alınacaktır. 


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Şimdi `databaseOperations/models.py` dosyasına giderek ilk model sınıfımızı oluşturalım. Burada her sınıf bir tabloya işaret ediyor.

	#databaseOperations/models.py
	from django.db import models

	class OwaspTopTen(models.Model):
	    number = models.IntegerField(max_length=20)
	    vulnerability_name = models.TextField(max_length=50)
	    vulnerability_description = models.TextField(max_length=200)




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Değişikliklerin kayıt edilmesi ve model sınıfının oluşturulması için aşağıdaki adımları izliyoruz. Migration, Django'da modeller ile ilgili yaptığımız değişiklikleri duyurma yöntemidir. Migration işlemlerini uygulamak veya kaldırmak için `migrate`, yeni migration oluşturmak için de `makemigration` parametresini kullanırız. 

	python3 manage.py makemigrations
	python3 manage.py migrate



<center><h5>Database API</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Şimdi API üzerinden tablomuza bilgiler ekleyelim ve bu bilgileri bir HTML sayfası oluşturarak görüntüleyelim. API'yi başlatmak ve veri eklemek için aşağıdaki işlemleri gerçekleştiriyoruz. 


	python3 manage.py shell
	
	>>>from databaseOperations.models import OwaspTopTen
	>>>OwaspTopTen.objects.create(number=1,vulnerability_name='Injection',vulnerability_description='Injection flaws, such as SQL, NoSQL, OS, and LDAP injection, occur when untrusted data is sent to an interpreter as part of a command or query.')
	>>>OwaspTopTen.objects.create(number=2,vulnerability_name='Broken Authentication',vulnerability_description='Application functions related to authentication and session management are often implemented incorrectly, allowing attackers to compromise passwords, keys, or session tokens, or to exploit other implementation flaws to assume other users identities temporarily or permanently.'
	>>>OwaspTopTen.objects.create(number=3,vulnerability_name='Sensitive Data Exposure',vulnerability_description='Many web applications and APIs do not properly protect sensitive data, such as financial, healthcare, and PII.')

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Database API üzerinden oluşturduğumuz bilgileri bir HTML sayfasına çağırıp listeleyim. Bunun için önce bir `databaseOperations` dizini altına bir  `templates` dizini oluşturacağız. Onun altına da anasayfamız olmasını istediğimiz bir görüntüleme sayfası oluşturacağız. 

	mkdir databaseOperations/templates
	touch databaseOperations/templates/owasptop10.html


<center><h5>Owasp Top 10 Uygulaması</h5></center>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bunun yanı sıra `databaseOperations/views.py` dosyasını da oluşturduğumuz HTML sayfasını çağıracağı şekilde aşağıdaki gibi düzenliyoruz. Bu adımdan sonra veritabanı sorguları ile tablomuzdaki değerleri aşağıdaki gibi çağıracağız ve HTML sayfasına göndereceğiz.

	#databaseOperations/views.py
	from django.shortcuts import render,HttpResponse
	from .models import OwaspTopTen

	def databaseOperationsHomepage(request):
	    data = OwaspTopTen.objects.all()
	    return render(request,'owasptop10.html',{'info': data})


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`databaseOperations/templates/owasptop10.html` sayfasını da aşağıdaki gibi düzenleyerek verileri buraya aktarıp görüntülüyoruz. Kodu çalıştırırken  `'`
 işaretini kaldırmayı unutmayın. 

	#databaseOperations/templates/owasptop10.html
	<html>
	<head>
	    <title>Owasp Top 10</title>
	</head>
	<center><h3>Owasp Top 10</h3></center>
	<hr>
	<body>
	{'% for vuln in info %} 
	{'{vuln.number}} : 
	{'{vuln.vulnerability_name}}
	<br>	
	{'{vuln.vulnerability_description}}
	<br>
	{'% endfor %}

	</body>
	</html>	

	


<br>
<img src="/img/django/django-owasp.png">
<br>

<center><h3>Sonuç</h3></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu yazıda Django'da model oluşturup shell API ile bu model sınıflarına veri ekledik. Bunun yanı sıra template üzerine veri aktarımı gerçekleştirdik. Bir sonraki yazı da Django'da formlar ile ilgili işlemler üzerinde duracağız.












