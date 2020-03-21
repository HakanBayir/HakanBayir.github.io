---
layout: post
title: Client-Server Örnekleri (Perl - Python)
categories: tr
---

<h3>Perl Client-Server</h3>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Burada Perl dilini kullanarak bir <strong>Client-Server</strong> uygulaması gerçekleştirmeye çalıştım. 
Tabiki olmazsa olmaz diyerek <strong>Socket</strong> kütüphanesini import ediyoruz. 
<strong>IO::Socket::INET</strong> ,AF_INET (ARPA Internet protocols) domain socketleri için nesne arayüzüdür.
Bu sınıfı kullanarak bir nesne oluşturacağız. Bu nesne constractoru içerisinde aşağıdaki
değişkenler bulunabilir.

<ul>
<li>1. PeerAddr </li>
<li>2. PeerHost </li>
<li>3. PeerPort </li>
<li>4. LocalAddr </li>
<li>5. LocalHost </li>
<li>6. LocalPort </li>
<li>7. Proto </li>
<li>8. Type </li>
<li>9. Listen </li>
<li>10. ReuseAddr </li>
<li>11. Reuse </li>
<li>12. ReusePort </li>
<li>13. Broadcast </li>
<li>14. Timeout </li>
<li>15. MultiHomed </li>
<li>16. Blocking </li>
</ul>


Biz buradan :

><strong>LocalHost</strong> : Yerel makina bağlantı adresi

><strong>LocalPort</strong> : Yerel makina bağlantı portu

><strong>Proto</strong>     : Bağlanti Protolü 

><strong>Listen</strong>   :	Dinlemek için queue boyutu

><strong>PeerAddr</strong>  : Uzak makina bağlantı adresi

><strong>PeerPort</strong>  : Uzak makine bağlantı portu

değişkenlerini kullanacağız.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bağlantı kurulacak port numarasını dışarıdan input olarak alınmasını belirledik fakat 
genelde yüksek numaralı portlar tercih edilir.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Uygulama basit bir yazışma uygulaması olarak düşünülebilir. Karşı taraf için bir socket
tanımlayıp  bağlantı beklenmeye başlanır . Bu socket karşı tarafın adresini ve port numarasını 
içerir. Bağlantı sağlanınca devamında 'basit' yazışma kanalına girilir. Burada önce server 
tarafı ve sonra client tarafı yazmak koşuluyla iletişim sağlanır.

<strong>Server Kısmı</strong>

	#!/usr/bin/perl

	use IO::Socket;
	$| = 1;

	print "Port numarası giriniz...\n";
	my $port=<STDIN>;

	$socket = new IO::Socket::INET (
	LocalHost => '127.0.0.1',
	LocalPort => $port,
	Proto => 'tcp',
	Listen => 5,
    
	);

	die "Port açılamadı!" unless $socket;
	print "Bağlantı bekleniyor...Port : $port\n ";

	while(1)
	{
        	$client_socket = "";
        	$client_socket = $socket->accept();
        	$peer_address = $client_socket->peerhost();
        	$peer_port = $client_socket->peerport();

        	print "\n CTRL Chat Server'a  Bağlantı Kuruldu. ( $peer_address , $peer_port ) \n";
        	
	while (1)
	{
        	$giden_veri = <STDIN>;
		my $user1 = "Hakan: ";
		$client_socket->send($user1);
        	$client_socket->send($giden_veri);
        	$client_socket->recv($gelen_veri,30);
        	print $gelen_veri;
        	$client_socket->autoflush(); 

        }

	}


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>Client</strong> tarafı için yine aynı şekilde bir socket nesnesi oluşturuyoruz. <strong>PeerAddr</strong> olarak görünen yer
server adresidir. <strong>PeerPort</strong> ise server'ın hangi portundan bağlantı sağlanacağıdır. Burada yine
dışarıdan port numarasını input alarak ilerledik.Aynı port numarası girildiğinde 
bağlantı sağlanır. Bağlantı kurulunca yazışma kanalına geçilir ve belirtilen 
kurallar dahilinde  iletişim sağlanabilir.

<strong>Client Kısmı</strong>

	#!/usr/bin/perl

	use IO::Socket;

	print "Port numarası giriniz...\n";
	my $port=<STDIN>;


	$socket = new IO::Socket::INET (
    	PeerAddr  => '127.0.0.1',
    	PeerPort  =>  $port,
    	Proto => 'tcp',
	)

	or die "Bağlantı Kurulamadı !\n";

	while (1)
	{
        	$socket->recv($gelen_veri,30);
        	print $gelen_veri."\n";
        	$giden_veri = <STDIN>;
		my $user2 = "Kullanici : ";
		$socket->send($user2);
        	$socket->send($giden_veri);
	}


<h3>Python Client-Server</h3>

<strong>Server Kısmı</strong>


	import socket
	import socketserver
	import threading 

	class threadexample (threading.Thread): 
   		def __init__(self, threadID, name):
       			threading.Thread.__init__(self)
       			self.threadID=threadID
       			self.name = name
   		def run(self):
       			while True:
         		a=baglanti.recv(1000)
         		print(a.decode())
       
	class threadexample2 (threading.Thread):
   		def __init__(self, threadID, name):        
        		threading.Thread.__init__(self)
        		self.threadID=threadID
        		self.name = name
   		def run(self): 
       			while True:
           		a=input("Server: ") 
           		baglanti.send(a.encode())


	myserver=socket.socket()
	mylocal=socket.gethostname()

	myserver.bind((mylocal,3969))
	myserver.listen(5)

	while True:
    		baglanti, adress = myserver.accept()
    		print("Bağlantı Sağlandi..."  )
    		print(adress)
    		thread1=threadexample(1,"recv")
    		thread1.start()
    		thread1=threadexample2(2,"send")
    		thread1.start()



<strong>Client Kısmı</strong>

	import socket
	import sys
	import threading

	class threadexample (threading.Thread):
    		def __init__(self, threadID, name):        
        		threading.Thread.__init__(self)
        		self.threadID=threadID
        		self.name = name
    		def run(self):
        		while True:
          		a=mysocket.recv(1000)
          		print(a.decode())
        
	class threadexample2 (threading.Thread):
    		def __init__(self, threadID, name):    
         		threading.Thread.__init__(self)
         		self.threadID=threadID
         		self.name = name
    		def run(self):        
        		while True:
            		a=input("Client: ")
            		mysocket.send(a.encode())


	mysocket=socket.socket()

	#host="0x6cac60.github.io"
	#addres=socket.gethostbyname(host)
	mylocal=socket.gethostname()
	port=3969

	mysocket.connect((mylocal,port))
	a="selam".encode()
	mysocket.send(a)
	#while True: 
	thread1=threadexample(1,"recv")
	thread2=threadexample2(3,"sxend")
	thread1.start()
	thread2.start()





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu konu literatürde <strong>socket programlama</strong> olarakta geçer.Bu yazışma programı Socket programlama 
kapsamında basit bir <strong>Client-Server</strong> uygulamasıdır. 










