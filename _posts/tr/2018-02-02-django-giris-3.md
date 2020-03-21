---
layout: post
title: Django Giriş [ 3 ]
categories: tr
---


<center><h3>Django'da Formlar</h3></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bilindiği üzere HTML formları, kullanıcı ile veritabanı arasında bir iletim görevi yapmaktadır. GET veya POST metodları ile kullanıcıdan gelen verileri sunucu tarafına iletebilirler. Django'da bu işlemleri daha pratik yapabilmemiz için form denilen yapılar bulunur. Bir form sınıfı oluşturarak, HTML ile yapacağımız formu hazır hale getirebiliriz. Django'da formlar daha hızlı ve yönetilmesi daha kolay sistemler oluşturmamızı sağlar. Bunun yanı sıra manuel form oluştururken ortaya çıkabilecek güvenlik zafiyetlerinin de önüne geçilmesini sağlar. Bu yazıda hem Django'nun form sınıfı ile hemde manuel bir şekilde form oluştururak çeşitli uygulamalar gerçekleştireceğiz. 

<center><h5>Uygulama Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bir önceki yazıda oluşturulan proje üzerinden devam ederek yeni bir uygulama oluşturalım.

	python3 manage.py startapp formOperations

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu uygulamanın erişilebilir olması için aşağıdaki gibi `practice/settings.py` dosyasına ekliyoruz.

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
	    'formOperations',
	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulamayı, projeye ekledikten sonra url'leri düzenleyceğiz. Uygulamamızın `127.0.0.1:8000/formOperations` üzerinden hizmet vermesini istiyoruz. Buna göre önce `formOperations` dizini altına uygulamaya ait `urls.py` dosyasını oluşturuyoruz.

	touch formOperations/urls.py

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tarayıcıdan `127.0.0.1:8000/formOperations` araması yapıldığında buraya bakılacak ve karşılık gelen metoda göre işlemler gerçekleştirilecek. Dosyayı aşağıdaki gibi düzenliyoruz.

	#formOperations/urls.py
	from django.contrib import admin
	from django.conf.urls import url
	from formOperations.views import *

	urlpatterns = [
	    url(r'^$', formOperationsHomepage),

	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Görüldüğü gibi uygulamamızın view kısmını buraya import ettik. Çünkü hatırlayacağımız üzere buradaki `formOperationsHomepage()` fonksiyonu orada yer alacaktır. Bu yüzden `formOperations/views.py` dosyasını buna uygun düzenliyoruz.
	
	#formOperations/views.py
	from django.shortcuts import render,HttpResponse

	def formOperationsHomepage(request):
	    return HttpResponse('Form Operations Homepage')




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Buradaki son adım olarak proje dizininde yer alan url dosyasına uygulamamızı eklememiz gerekiyor. Sorgu kök dizinden başladığı için ilk buraya bakılıyor. Son olarak aşağıdaki hali alacaktır. 

	#practice/urls.py
	from django.contrib import admin
	from django.conf.urls import url, include
	from reverseString.views import *
	from databaseOperations.views import *
	from formOperations.views import *


	urlpatterns = [
	    url('admin/', admin.site.urls),
	    url(r'^$', homepage),
	    url(r'^get_string/$', get_string),

	    url(r'^databaseOperations/', include('databaseOperations.urls')),

	    url(r'^formOperations/', include('formOperations.urls')),

	    url(r'^add_function/$', add_function),
	    url(r'^add_manuel_function/$', add_manuel_function),
	]



<center><h5>Model Oluşturma</h5></center>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulamamız artık tarayıcıdan erişilebilir durumdadır. Bu aşamadan sonra form uygulamamız için öncelikle bir model oluşturalım. 

	#formOperations/models.py
	from django.db import models

	class EmergencyNumbers(models.Model):
	    name = models.TextField(max_length=50)
	    number = models.TextField(max_length=10)



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Model dosyamızı işlemek için aşağıdaki adımları gerçekleştiriyoruz.

	python3 manage.py makemigrations
	python3 manage.py migrate





<center><h5>Form Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulamamızın altına gelip `formOperations/forms.py` dosyası oluşturuyoruz. Bu dosyanın içeriği model dosyasına uygun bir şekilde oluşturulmalıdır. Kullanıcıdan veriler alındığı zaman bu form üzerinden sunucu tarafına iletilecektir. Model yapısında olduğu gibi burada da her bir form, bir sınıftan oluştur. Altındaki üyeler bu sınfın birer parçasıdır. 

	#formOperations/forms.py
	from django import forms
	from django.forms import ModelForm
	from .models import formOperations

	class EmergencyNumbersForm(forms.ModelForm):
	    class Meta:
		model = formOperations
		fields = [
		    'name',
		    'number',
		]





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Şimdi bu formu çağırabileceğimiz bir HTML sayfası oluşturacağız. Bunun için `formOperations` uygulaması altında bir dizin oluşturup içerisine de ilgili HTML sayfasını ekleyeceğiz.

	mkdir formOperations/templates
	touch formOperations/templates/add.html


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Yeni bir sayfa demek, yeni bir url düzenleme demektir. Bu yüzden uygulamamız için `formOperations/urls.py` dosyasını düzenleyeceğiz.

	#formOperations/urls.py
	from django.contrib import admin
	from django.conf.urls import url
	from formOperations.views import *

	urlpatterns = [
	    url(r'^$', formOperationsHomepage),
	    url(r'^add/$', add),
	    url(r'^add_function/$', add_function),

	]



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;View bölümüne gidip formu HTML'e gönderme işlemini gerçekleştiriyoruz. Bunun yanı sıra formdan gelen verileri model dosyasına kaydetmek için `add_function` adında bir fonksiyon oluşturup, bu fonksiyonu yukarıdaki gibi  `formOperations/urls.py` dosyasına ekliyoruz. 

	#formOperations/views.py
	from django.shortcuts import render,HttpResponse
	from .models import EmergencyNumbers
	from .forms import EmergencyNumbersForm

	def formOperationsHomepage(request):
	    data = EmergencyNumbers.objects.all()
	    return render(request,'emergencynumber.html',{'data': data})

	def add(request):
	    form = EmergencyNumbersForm()
	    return render(request,'add.html',{'form':form})


	def add_function(request):
	    name = request.POST['name']
	    number = request.POST['number']

	    EmergencyNumbers.objects.create(name=name,number=number)
	    message = name + "<br>" + number + "<br>" + "is added!"
	    return HttpResponse(message)


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Tarayıcıdan `127.0.0.01:8008/formOperations/add` sayfasına gidildiğinde karşımıza oluşturduğumuz form çıkacaktır. Bu form'a veri girildiğinde, veriler `add_function` fonksiyonuna post metodu ile gönderilecektir. Gönderilen veriler veritabanına burada kayıt edilecektir.


	#formOperations/templates/add.html
	<html>
	<head>
	    <title>Add Emergency Number</title>
	</head>
	<center><h3>Add Emergency Number</h3></center>
	<hr>
	<body>
	  <form action="/add_function/" method="post">
	  {'% csrf_token %}
	  {'{ form }}
	  <input type="submit" value="Send">
	  </form>
	</body>
	</html>




<center><h5>Manuel Form Oluşturma</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Django'nun form özelliğini kullandıktan sonra şimdi de manuel bir şekilde form oluşturup, oradan gelen verileri veritabanına ekleyeceğiz. Öncelikle url dosyamızı düzenleyelim. Son hali aşağıdaki gibi olacaktır. 



	#formOperations/urls.py
	from django.contrib import admin
	from django.conf.urls import url
	from formOperations.views import *

	urlpatterns = [
	    url(r'^$', formOperationsHomepage),
	    url(r'^add/$', add),
	    url(r'^add_function/$', add_function),
	    url(r'^add_manuel/$', add_manuel),
	    url(r'^add_manuel_function/$', add_manuel_function),


	]


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Devamında views dosyamızı ilgili fonksiyonları ekleyerek düzenliyoruz. Tarayıcıdan `127.0.0.1:8008/formOperations/add_manuel/` çağrıldığı zaman karşımıza `add_manuel.html` dosyası çıkacaktır. Bunun içerisinde manuel bir form oluşturacağız. Bu form dosyasından gelen veriler `add_manuel_function` fonksiyonuna gönderilecektir. Böylece ekleme işlemi gerçekleştirilecektir. 



	#formOperations/views.py
	from django.shortcuts import render,HttpResponse
	from .models import EmergencyNumbers
	from .forms import EmergencyNumbersForm

	def formOperationsHomepage(request):
	    data = EmergencyNumbers.objects.all()
	    return render(request,'emergencynumber.html',{'data': data})

	def add(request):
	    form = EmergencyNumbersForm()
	    return render(request,'add.html',{'form':form})


	def add_function(request):
	    name = request.POST['name']
	    number = request.POST['number']

	    EmergencyNumbers.objects.create(name=name,number=number)
	    message = name + "<br>" + number + "<br>" + "is added!"
	    return HttpResponse(message)

	def add_manuel(request):
	    return render(request,'add_manuel.html')


	def add_manuel_function(request):
	    name = request.POST['name']
	    number = request.POST['number']

	    EmergencyNumbers.objects.create(name=name,number=number)
	    message = name + "<br>" + number + "<br>" + "is added!"
	    return HttpResponse(message)



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Manuel HTML formumuz aşağıdaki gibi olacaktır. 

	#formOperations/templates/add_manuel.html
	<html>
	<head>
	    <title>Add Emergency Number</title>
	</head>
	<center><h3>Add Emergency Number</h3></center>
	<hr>
	<body>
	    <form action="/add_manuel_function/" method="post">
		{'% csrf_token %}
		Name     : <input type="text" name="name"><br><br>
		Number  : <input type="text" name="number"><br><br>
		<input type="submit" value="Send"><br><br>
	    </form>
	</body>
	</html>





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Son olarak veritabanına eklenen verilerin listelenmesi için aşağıdaki gibi bir anasayfa oluşturalım. Anasayfayı çağırdığımızda `formOperationsHomepage` fonksiyonu çalışacak ve model dosyasında bulunan tüm verileri getirip ekranda listeleyecektir. 

	#formOperations/templates/emergencynumber.html
	<html>
	<head>
	    <title>Emergency Numbers</title>
	</head>
	<center><h3>Emergency Numbers</h3></center>
	<hr>
	<body>

	  {'% for emergency in data %}
	  {'{emergency.name}} :
	  {'{emergency.number}}<br>
	  {'% endfor %}


	</body>
	</html>



<img src="/img/django/django-emergency.png">



<center><h3>Sonuç</h3></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu yazıda Django üzerinde hem form metodu ile hemde manuel olarak form oluşturduk. Veritabanı ile bağlantı kurarak POST metodu ile verileri kullanıcıdan alıp sunucuya gönderdik. Not olarak şunu ekleyebilirim. Django `CSRF Token` olmadan POST metodunun kullanılmasına izin vermiyor. Uygulamanızın CSRF saldırılarına maruz kalmaması için sizin yerinize bir çok önlemi kendisi alıyor ya da aldırıyor. Bir sonraki yazı da modeller ile ilgili çeşitli işlemler üzerinde duracağız.







