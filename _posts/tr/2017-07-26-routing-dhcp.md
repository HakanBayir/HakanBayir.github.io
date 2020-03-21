---
layout: post
title: Routing ve DHCP Konfigürasyonu
categories: tr
---



&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Stajın belli bir döneminde ödev olarak verilen network topolojilerinden birini anlatacağım. Staj raporumdan alıntı yapıyorum. Bu yüzden anlatım şekli diğer yazılarıma göre farklı(kasıntı) oldu.


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Projede dört router kullanılarak dört farklı network oluşturuldu. Aynı zamanda bu routerların arasında da ayrı networkler oluşturuldu. Toplamda dört router, dört switch, sekiz client cihazı ve üç tane de server kullanıldı. Routing protokolü olarak `EIGRP 20` tercih edildi. IP adresleri dağıtımı `DHCP server` tarafından yapıldı. Bunun yanı sıra İzmir network için router üzerinden DHCP konfigürasyonu yapıldı. Copper Straight-Through ve Copper Cross-Over kablo tipleri kullanıldı.
<img src="/img/routing-dhcp/topology.png">



<h4>Konfigürasyonlar:</h4> 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cisco Packet tracer üzerindeki topolojide kullanılacak router  2911 modeli olarak seçildi. Switch için ise 2960-24TT modeli kullanıldı.Önce tüm network cihazlarını yerleştirip kablo bağlantılarını yaptıktan sonra konfigürasyon işlemi gerçekleştirildi. Her bir router'ın arkasına birer switch eklendi ve diğer network cihazlarını bu switch'e bağlayıp iletişimi sağlandı. Router ve switch arası bağlantıyı sağlamak için copper-cross-over tipi kablo kullanıldı. Routerlar için extra bir düzenleme yapılması gerekiyor. Buradaki routerlar için default olarak serial kablo girişi bulunmamaktadır. O yüzden kart eklemesi yapılması gerekir. Router'a tıklıyoruz ve Physical bölümüne geliyoruz. Router'ı off yapıp `HWIC-2T` kartı ekliyoruz. Sonra bu işlem bitince tekrar router'ı On yapıp işlemlere devam ediyoruz. Router-Router arası ise yine copper-cross-over kablo kullanıldı. Switch-Cihaz arası da copper-straight-through kablo ile bağlantı gerçekleştirildi.

<h6>Bağlantı için seçilen Interface : </h6>

	İstanbul - Ankara : Gig 0/0 - Gig 0/0 
	İstanbul - Bursa  : Gig 0/1 - Gig 0/1	
	Ankara   - İzmir  : Gig 0/1 - Gig 0/1
	Bursa    - İzmir  : Gig 0/0 - Gig 0/0

<h6>Router-Switch arası seçilen Interface : </h6>

	İstanbul - Switch : Gig 0/2 - Gig 0/2
	Ankara   - Switch : Gig 0/2 - Gig 0/2
	Bursa    - Switch : Gig 0/2 - Gig 0/2
	İzmir    - Switch : Gig 0/2 - Gig 0/2

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu switchlerin her birine bağlı ikişer adet End-Device eklendi. Bu end-device'lar için bağlantı copper-straight-through kablo ile sağlandı.

<h6>Switch - Cihaz Arası seçilen Interface : </h6>

	Switch - Laptop : Fa 0/1
	Switch - PC     : Fa 0/2

olarak tüm cihazlar için aynı şekilde seçildi.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamadan sonra Packet Tracer üzerinde Switch-Cihaz arası bağlantıların yeşil ışık yaktığını fakat Router ile olan bağlantılar arası kırmızı durumda olduğunu görüyoruz. Bunun nedeni routerlar için bacaklar default olarak down şeklindedir. Bu bacakları konfigüre etmeye başlarken up durumuna getiriyoruz.

	
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu aşamadan sonra routerlar için konfigürasyon işlemine başlandı. Bacaklara ip adresi verildi ve arada dummy network kurma işlemi gerçekleştirildi.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet tracer üzerinde `CLI (Command Line Interface)` bölümüne gidildi ve aşağıdaki aşamaları izlendi :

<h5>Ankara Router : </h5>

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/0
	Router(config-if)#ip address 192.168.1.1 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
	Router(config-if)#exit
	Router(config)#interface Gig 0/1
	Router(config-if)#ip address 192.168.2.1 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
	Router(config-if)#exit
	Router(config)#exit

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/2
	Router(config-if)#ip address 172.18.0.1 255.255.0.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
	Router(config-if)#exit
	Router(config)#

	Router(config)#router eigrp 20
	Router(config-router)#no aut
	Router(config-router)#network 192.168.2.0
	Router(config-router)#network 192.168.1.0
	Router(config-router)#network 172.18.0.0
	Router(config-router)#exit
	Router(config)#





<h5>İstanbul Router :</h5>

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/0
	Router(config-if)#ip address 192.168.1.2 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
	Router(config-if)#exit
	Router(config)#interface Gig 0/1
	Router(config-if)#ip address 192.168.3.2 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
	Router(config-if)#exit
	Router(config)#

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/2
	Router(config-if)#ip address 172.15.0.1 255.255.0.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
	Router(config-if)#exit
	Router(config)#exit

	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#router eigrp 20
	Router(config-router)#no aut
	Router(config-router)#network 192.168.1.0
	Router(config-router)#
	%DUAL-5-NBRCHANGE: IP-EIGRP 20: Neighbor 192.168.1.1 (GigabitEthernet0/0) is up: new adjacency
	Router(config-router)#network 192.168.3.0
	Router(config-router)#network 172.15.0.0
	Router(config-router)#exit
	Router(config)#




<h5>Bursa Router : </h5>

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/1
	Router(config-if)#ip address 192.168.3.1 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
	Router(config-if)#exit
	Router(config)#interface Gig 0/0
	Router(config-if)#ip address 192.168.4.1 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
	Router(config-if)#exit
	Router(config)#

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/2 
	Router(config-if)#ip address 172.16.0.1 255.255.0.0
	Router(config-if)#no sh
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2,	changed state to up
	Router(config-if)#exit
	Router(config)#

	Router(config)#router eigrp 20
	Router(config-router)#no aut
	Router(config-router)#network 192.168.4.0
	Router(config-router)#
	%DUAL-5-NBRCHANGE: IP-EIGRP 20: Neighbor 192.168.4.2 (GigabitEthernet0/0) is up: new adjacency
	Router(config-router)#network 192.168.3.0
	Router(config-router)#
	%DUAL-5-NBRCHANGE: IP-EIGRP 20: Neighbor 192.168.3.2 (GigabitEthernet0/1) is up: new adjacency
	Router(config-router)#network 172.16.0.0
	Router(config-router)#exit
	Router(config)#



<h5>İzmir Router : </h5>

	Router>enable
	Router#configure terminal
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#interface Gig 0/0
	Router(config-if)#ip address 192.168.4.2 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
	Router(config-if)#exit
	Router(config)#interface Gig 0/1
	Router(config-if)#ip address 192.168.2.2 255.255.255.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
	Router(config-if)#exit
	Router(config)#

	Router(config)#interface Gig 0/2
	Router(config-if)#ip address 172.17.0.1 255.255.0.0
	Router(config-if)#no shutdown
	Router(config-if)#
	%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to up
	%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/2, changed state to up
	Router(config-if)#exit
	Router(config)#

	Router(config)#router eigrp 20
	Router(config-router)#no aut
	Router(config-router)#network 192.168.2.0
	Router(config-router)#
	%DUAL-5-NBRCHANGE: IP-EIGRP 20: Neighbor 192.168.2.1 (GigabitEthernet0/1) is up: new adjacency
	Router(config-router)#network 192.168.4.0
	Router(config-router)#network 172.17.0.0
	Router(config-router)#exit
	Router(config)#







&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bacaklara ip verip up yapma aşamasından sonra tüm routerlar için birbiri arası bağlantı yeşil ışık yaktı . Bu aşamadan sonra o routerlar için kendi iç network için konfigurasyon olayına başlandı. Bu işlem bittikten sonra EIGRP 20 routing gerçekleştirildi.


<h4>DHCP</h4>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;İstanbul, Ankara ve Bursa Networkleri için birer DHCP-Server konfigürasyonu yapıp ip adreslerini bu server üzerinden dağıtılsı. İzmir için ise Router üzerinden DHCP konfigurasyonu gerçekleştirildi.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Packet Tracer Üzerinde DHCP Server Kurulumu : Server nesnesine tıklandığında  arayüze girilir. Sonra Services -> DHCP adımını izliyoruz.Service ON yaptıktan sonra Default Gateway,Start IP Address ve Subnet Mask bölümlerini doldurulur ve kayıt edilir. Devamında  Desktop -> IP konfigürasyon bölümünden statik ip yapılandırması gerçekleştirilir.

<h5>İzmir DHCP (Router)</h5>

	Router>enable
	Router#conf t
	Enter configuration commands, one per line.  End with CNTL/Z.
	Router(config)#ip dhcp pool ip10
	Router(dhcp-config)#net 172.17.0.0 255.255.0.0
	Router(dhcp-config)#default 172.17.0.1
	Router(dhcp-config)#ip dhcp exc 172.17.0.1 172.17.0.10
	Router(config)#





















