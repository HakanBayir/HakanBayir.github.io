---
layout: post
title: Temel Linux Komutları
categories: tr
---




`pwd` : Kullanıcının o anda hangi dizinde bulunduğunu gösterir. <br>
`cd`  : Aktif dizinin değiştirilmesini sağlar.<br>
`cat` : Dosyanın içeriğini görmemizi sağlar.<br>
`mkdir` : Yeni dizin oluşturmayı sağlar. <br>
`rmdir` : Dizin silmeyi sağlar.<br>
`mv`  : Bir dosyayı ya da dosyaları bulunduğu dizinden başka bir dizine taşımak için kullanılır.<br>
`cp`  : Bir dosyayı ya da dosyaları bulunduğu dizinden başka bir dizine kopyalamak için kullanılır.<br>
`ls`  : Aktif dizinin içerisindeki dosya ve alt dizinleri listeler.<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`ls -a` : Gizli dosyalarla birlikte aktif dizinin içeriğini listeler.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`ls -l` : Aktif dizindeki içeriği boyut ve izin bilgileriyle birlikte listeler.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`ls -R` : Bulunduğumuz dizin içerisindeki tüm alt dizinleri listeler.<br>

`rm` : Bulunduğumuz dizindeki bir dosyayı silmek için kullanılır.<br>
`chown` : Dosya ya da dosyaların sahibinin değiştirilmesini sağlar.<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chown -R` : Hedef dizindeki tüm dosyaların ve alt dizinlerin sahibinin değiştirilmesini sağlar.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chown -v` : Chown komutunun yaptığı tüm işlemleri ve komutun işlemesi sonucunda sahibi değişen ,değişmeyen dosyaları gösterir.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chown -c` : Sadece sahipliği değişen dosyaları listeler.<br>

`chgrp` : Dosya ya da dosyaların gruplarını değiştirmeyi sağlar.<br>
`chmod` : Dosya ya da dizinler için erişim izinlerini değiştirmeyi sağlar.<br>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chmod +r` : Okuma izni verir.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chmod -w` : Yazma izni alır.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`chmod +x` : Execute izni verir.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>not</strong>: `+` ifadesi izni aktif eder, `-` ifadesi izni iptal eder.<br>

`lsmod` : Kernel mödüllerini gösterir.<br>
`lspci` : PCI cihazlarını ve özelliklerini gösterir.<br>
`lsusb` : USB cihazlarını ve özelliklerini gösterir.<br>
`grep`  : Bir dosyada kelime, karakter, rakam, sembol aramak için kullanılır.<br>
`stat`  : Bir dosya hakkında detaylı bilgi verir.<br>
`file`  : Dosya tipini gösterir.<br>
`find`  : Dosya arama için kullanılır.<br>
`locate`: Dosyanın bulunduğu dizini gösterir.<br>
`ps -aux` : Sistemde çalışan süreçleri gösterir.<br>
`uptime`  : Sistemin çalışma süresini gösterir.<br>
`free` : Hafıza kullanım durumunu gösterir.<br>
`fdisk -l` : Sabit disk bölümlerini gösterir.<br>
`netstat -tuna` :  Sistemde açık olan portları ve bağlantıları gösterir.<br>
`uname` : Kernel bilgisini gösterir.<br>




