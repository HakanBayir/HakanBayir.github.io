---
layout: post
title: HTTP Restful API ve Virus Total API (Python)
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HTPP bir istek-yanıt protokolüdür. İnternet üzerinde bir uygulamaya gitmek için bir HTTP istek yaparız ve HTTP yanıt alırız. Bu yazıda Restful API üzerinde duracağız. Restful API, HTTP protokolünün bir formudur. Öncelikle API kavramından bahsedelim. API (Application Programming Interface), bir uygulama için oluşturulmuş bazı fonksiyonların, başka uygulamalar tarafından çağrılıp, kullanılabilmesine imkan veren, destekleyen yapılardır. Yazılımların birbirleriyle iletişim kurması için belirlenmiş kod dizisidir. API'leri bu yüzden arayüz gibi düşünebiliriz. Farklı yazılımların etkileşimini ve birbirine bağlanabilmesini sağlar. Peki bu Restful API nedir ? 


<center><h3>Restful API</h3></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Restful API , internet üzerindeki uygulamalarda ve bulut iletişiminde yaygın olarak kullanılan, uygulama düzeyinde bir protokoldür. Stateless ve cacheless bir yapıdadır. Yapılan her istek benzersizdir. Gelen önceki ve gelebilecek sonraki istekten etkilenmez ve bağlantısı bulunmaz. İstemci-Sunucu bağımsızlığından ve HTTP ile çalıştığından dolayı uygun herhangi bir programlama dili ile test edilebilmektedir. Restful API'nin yapısı aşağıdaki bileşenlerden oluşur. 


`1. URL`<br>
`2. HTTP METHODS`<br>
`3. HEADERS`<br>
`4. PARAMETERS`<br>
`5. DATA`<br>


<center><h5>URL</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uniform Resource Locator, kaynağın bulunduğu ve üzerinden işlemlerin gerçekleştirildiği yoldur. URL, kaynağa doğrudan işaret edebiliyorken, aynı zamanda farklı bir kaynağa veya işleme de yönlendirebilir. 

<center><h5>HTTP METHODS</h5></center>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;GET, POST, PUT, DELETE gibi metodlar ile gerçekleştirilen isteklerdir. Daha geniş bilgi için <a href="http://127.0.0.1:4000/tr/2017/07/14/http.html">buraya</a>
göz atabilirsiniz. 

<h6>GET</h6>
Basit anlamda, kaynaktan veri talep ederken yapılan istektir. 
	
	requests.get(url)

<h6>POST</h6>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sunucuya veri gönderip kaynak oluşturmayı hedeflerken kullandığımız HTTP metodudur.

	requests.post(url, data=data)	

<h6>PUT</h6>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sunucu üzerinde bulunan kaynağı güncellemeyi sağlayan HTTP metodudur. 

	requests.put(url, params=params)
	
<h6>DELETE</h6>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sunucu üzerinden kaynak silmeyi destekleyen yöntemdir. 

	requests.delete(url)



<center><h5>HEADERS</h5></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İstek ve yanıtların içeriklerini işlemek için kullanılan bilgileri içerir. WEB servisi tarafından desteklenen veri tipleri ile ilgili bilgiler burada yer alır. 

	{ 'Content-type' : 'text/html' }

<center><h5>PARAMETERS</h5></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL üzerinde parametre olarak değerlerin gönderildiği durumlar için, yapılan HTTP isteğinin içinde bu bölüm yer alır. 

	{ 'apikey': api_key, 'resource': hash }

<center><h5>DATA</h5></center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İsteğin içinde gönderilecek veriler burada bulunur. 

	{ 'username' : user, 'password' : passwd }

<center><h3>Virus Total API - Python ile Hash ve URL Arama Örneği </h3></center>
	
	
		import requests

		api_key = ''
		hash = '7657fcb7d772448a6d8504e4b20168b8'
		target_url = 'http://www.hakanbayir.com'

		def hash_search():
		    url = 'https://www.virustotal.com/vtapi/v2/file/report'
		    parameters = {'apikey': api_key, 'resource': hash}
		    headers = {"Accept-Encoding": "gzip, deflate"}

		    r = requests.post(url, headers=headers, params=parameters)

		    result = r.json()

		    scanner = result['scans']

		    scanner_count = 1
		    for i in scanner:
			     print scanner_count , " : ", i, " - " , scanner[i] , '\n'
			     scanner_count = scanner_count + 1


		def url_search():
		    url = 'http://www.virustotal.com/vtapi/v2/url/report'
		    parameters = {'apikey' : api_key , 'resource' : target_url}
		    headers = {"Accept-Encoding": "gzip, deflate"}

		    r = requests.post(url, headers=headers, params=parameters)

		    result = r.json()

		    scanner = result['scans']

		    scanner_count = 1
		    for i in scanner:
			     print scanner_count , " : ", i, " - " , scanner[i] , '\n'
			     scanner_count = scanner_count + 1


		if __name__ == '__main__':
		    print "[1]. Hash Search" , "\n"
		    print "[2]. URL Search" , "\n"

		    print "Choose Search Options : "
		    option = raw_input()

		    if option == '1':
			     hash_search()
		    elif option == '2':
			     url_search()
	
	
	
	
<center><h5>Hash Arama Çıktısı</h5></center>
<img src="/img/virustotal/hash_search.png"><br><br>
<center><h5>URL Arama Çıktısı</h5></center>
<img src="/img/virustotal/url_search.png"><br>





















