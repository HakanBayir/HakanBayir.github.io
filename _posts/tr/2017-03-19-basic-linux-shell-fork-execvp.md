---
layout: post
title: C ile Basit Linux Shell 
categories: tr
---


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strong>C</strong> dilini kullanıp basit bir <strong>linux shell</strong> yazarak <strong>parent-child process</strong> işleyiş mantığını anlatmaya çalıştım. Buradaki önemli fonksiyonlarımız <strong>fork()</strong> ve <strong>execvp()</strong> fonksiyonlarıdır. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Oluşturulan alt süreç, üst programın yaptığı gibi aynı programı çalıştırmak zorunda değildir. <strong>Exec</strong> tipi sistem çağrıları, bir işlemi, ikili bir şekilde yürütülebilir ve herhangi bir program dosyasını çalıştırmaya izin verir. Aşağıdaki programda şöyle bir sistem çağrısı görüyoruz : <strong>execvp()</strong>.
Execvp () sistem çağrısı iki bağımsız değişkeni gerektirir:

<ul>
<li>İlk argüman yürütülecek bir dosyanın adını içeren bir karakter dizesidir.</li>

<li>İkinci argüman, bir dizi karakter dizisine işaret eden bir işaretçidir. </li>
</ul>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Bu argümanların sıfır(<strong>NULL</strong>) ile bitmesi gerektiğini unutmayın. Execvp() yürütüldüğünde, ilk argüman tarafından verilen program dosyası çağıranın adres alanına yüklenecek ve orada programı over-write edecektir. Ardından, ikinci argüman programa verilecek ve yürütme işlemi başlatılacaktır. Sonuç olarak, belirtilen program dosyası çalıştırılmaya başlandığında, shell üzerindeki  çalışan program gidecek ve yeni programla değiştirilecektir.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Execvp (), yürütme başarısız olursa (örn. istek dosyası mevcut değilse) negatif bir değer döndürür. Burada yaptığımız önemli işlemlerden birisi aldığımız karakter dizisindeki ifadeyi parçalara ayırıp  hangi komut çalışacak hangi dosya yollarını kullanacak bunu belirlemektir. Yani çalıştırmak istediğimiz komutun aldığı parametreleri de ayrı birer karakter dizisinde tutup işlem yaptırıyoruz.

<h4>C Kodu</h4>


	#include <stdio.h>
	#include <stdlib.h>
	#include <string.h>
	#include <unistd.h>
	#include <sys/types.h>
	#include <sys/wait.h>


	int main()
	{

    	char* i_komut[100]; 
	char* ref_yol= "/bin/"; 
	char program_yolu[20];           
	int sayac;			
	char *komut = (char*) malloc(100);  



	printf(">>");          

	while(scanf("%[^\n]%*c",komut)) 
	{

	size_t length = strlen(komut);   	 
	if (komut[length - 1] == '\n')  
        komut[length - 1] = '\0';      





	if(strcmp(komut,"exit")==0)
	{ 
	        break;
	}

	if(strcmp(komut,"clear")==0)
	{ 
	        system("clear");
	}

	char *token;                        
	token = strtok(komut," ");          
	int i=0;                          

	while(token!=NULL)
	{
	        i_komut[i]=token;      
	        token = strtok(NULL," ");
	        i++;
    	}

    	i_komut[i]=NULL;                     

	sayac=i;                           

	for(i=0; i<sayac; i++)
	{
	        printf("%s\n", i_komut[i]);      
	}

	strcpy(program_yolu, ref_yol);         
	strcat(program_yolu, i_komut[0]);      

	for(i=0; i<strlen(program_yolu); i++)
	{     
	        if(program_yolu[i]=='\n'){           
	            program_yolu[i]='\0';           
	        }
	}
  
	int pid= fork();              

	if(pid==0)
	{                       
	        execvp(program_yolu,i_komut);        
	        fprintf(stderr, "Child process baslatilamadi !\n"); 
								
	}
	else
	{                    
	        wait(NULL);          
	        printf("Child process çalıştı ! \n");       
	}

	printf(">>");

	}


	} 



