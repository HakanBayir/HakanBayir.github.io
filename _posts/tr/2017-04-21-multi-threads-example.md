---
layout: post
title: Thread-MultiThreading Örneği (C)
categories: tr
---







<h4>Thread nedir ?</h4> 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sistemimizde çalışan her programa process(<strong>süreç</strong>) denir. Bu processler
de kendi içerisinde dallanıp <strong>alt süreçler</strong>i oluşturabilir. Bu alt süreçler aynı anda çalışabilir ve aynı kaynakları kullanabilir. Eş zamanlı çalışmaya yardımcı olan ve aynı process'in işlerini yürüten bu süreçlere <strong>Thread</strong> denir. 
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu yazımda <strong>C</strong> programlama diliyle yazdığım bir <strong>thread</strong> örneğini inceleyeceğiz. Programımız
temel işlev olarak herhangi bir text dosyası içerisinde istenen kelimenin olup olmadığını
kontrol ediyor ve buna göre bir sonuç dönüyor. Fonksiyonumuz normalde tek bir dosya içerisinde arama 
yapacakken thread kullanarak birden fazla dosya için arama mekanizması oluşturduk. Oluşacak thread
sayımı , okunacak dosya sayısı kadar belirliyorum ve oluşturduğum fonksiyonu bu threadlerle
bağlıyorum.

	pthread_create(&tid[sayac], NULL, &arama, (void *)kelime);

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu kodu yazarak threadi oluşturup fonksiyon ile ilişkilendiriyoruz. Bundan sonra thread oluştukça bu
fonksiyona gidecek. Fonksiyona gidiyor ve yanında parametre olarak aldığı <strong>aranan kelimeyi</strong> de götürüyor. <strong>input_sayisi</strong> değişkeni okunan dosya sayisini temsil ediyor. İlk başta değeri '<strong>0</strong>'
olduğu için önce belirlediğimiz text dosyalarından birincisine gidiyor. Burada string kütüphanesi 
fonksiyonlarından  '<strong>strstr</strong>' kullandım ve  bu sayede arama işlemini gerçekleştirdim.  Burada tuttuğum
<strong>kontrol</strong> değişkeni ,aranan kelimenin bulunma durumunu simgeliyor. Eğer aradığımız kelime içeride 
varsa bu değişkeni <strong>1</strong>, yoksa <strong>2</strong> yapıyoruz. Bu sayede ana fonksiyon üzerinde bu kontrolden yola çıkarak
sonucu göstereceğim. Burada okunan dosya sayısını da <strong>dosya_sayisi</strong> değişkeni ile tutuyorum , bunu da 
input dosyalarımızı isimlendirirken kullanacağım. Programımım bu işlemleri oluşturulacak thread 
sayısı, yani dosya sayısı kadar tekrarlayacaktır. 


<img src="/img/thread/compile.png">
<img src="/img/thread/result.png">


	#include<pthread.h>
	#include<stdio.h>
	#include<string.h>
	#include<stdlib.h>
	#include<unistd.h>

	void* arama();
	int kontrol;
	int dosya_sayisi;

	int main()
	{

	system("clear");
	char kelime[100];
	int sayac=0;
	int thread_sayisi;
	int thread;

	printf("File #? ");
	scanf("%d",&thread_sayisi);

	thread_sayisi=thread_sayisi+1;
	pthread_t tid[thread_sayisi];

	printf("Word? ");
	scanf("%s",kelime);

	while(sayac<thread_sayisi)
	{

	thread = pthread_create(&tid[sayac], NULL, &arama, (void *)kelime);

	if (thread != 0)
	{

	printf("\nThread olusturulamadi  :[%s]", strerror(thread));
	}

	else
	{
	//	  printf("\n %d Thread oluştu...\n",&tid[count]);

	if(kontrol==1)
	{
	printf("inputThread%d.txt -> ",dosya_sayisi);
	printf("true\n");

	}

	if(kontrol==2)
	{
	printf("inputThread%d.txt -> ",dosya_sayisi);
	printf("false\n");

	}


	}

	pthread_join(tid[sayac],NULL);
	sayac++;


	}

	sleep(5);
	return 0;




	}

	int input_sayisi = 0;
	char *fname1 = "inputThread1.txt";
	char *fname2 = "inputThread2.txt";
	char *fname3 = "inputThread3.txt";
	char *fname4 = "inputThread4.txt";
	char *fname5 = "inputThread5.txt";


	void *arama(char *kelime) 
	{




	FILE *fp;

	int find_result = 0;

	char temp[512];

	


	if(input_sayisi==0 &&(fp = fopen(fname1, "r")) == NULL) {

	return 0;

	}

	if(input_sayisi==1 &&(fp = fopen(fname2, "r")) == NULL) {

	return 0;

	}

	if(input_sayisi==2 &&(fp = fopen(fname3, "r")) == NULL) {

	return 0;

	}

	if(input_sayisi==3 &&(fp = fopen(fname4, "r")) == NULL) {

	return 0;

	}

	if(input_sayisi==4 &&(fp = fopen(fname5, "r")) == NULL) {

	return 0;

	}

	if(input_sayisi==5)

	{

	return ;

	}

	input_sayisi++;



	while(fgets(temp, 512, fp) != NULL) {

	if((strstr(temp, kelime)) != NULL) {

	find_result++;
	kontrol =1 ;
	dosya_sayisi++;

	}


	}


	if(find_result == 0) {

	kontrol = 2;
	dosya_sayisi++;
	}


	if(fp) {

	fclose(fp);

	}



	}










