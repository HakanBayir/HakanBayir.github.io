---
layout: post
title: Binary Search
categories: tr
---


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Binary search sıralı diziler üzerinde çalışan bir algoritmadır. Aranan değer sıralı dizinin en ortasındaki eleman ile kıyaslanır. Bu değer orta değere eşit ise aranan değer bulunmuş demektir. Bunun dışında eğer orta değerden küçük ise dizinin sol tarafında yer aldığını anlarız. Büyük ise arama işlemine dizinin sağ tarafında devam edilir. Dizinin hangi tarafında olduğuna karar verildikten sonra asıl eleman bulunana kadar bu işlem `recursive` olarak devam eder.


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aşağıdaki örnekte sıralı bir dizi oluşturdum. Bu diziyi `binary_search` fonksiyonuna gönderdim. Aynı zamanda dizinin boyutu ile ilgili olan first ve last değişkenlerini de fonksiyona yolladım. Bunlar dizinin ortasını bulmaya yönelik değişkenlerdir. Sonrasında küçük veya büyük olma durumuna göre arama gerçekleşecektir.


	#include<iostream>

	using namespace std;


	int binary_search(int array[],int search,int first,int last)
	{

		if(first>last)
			return (-1);

		int mid = (first+last)/2;

		if(search == array[mid])
		{

		cout << array[mid] << " bulundu." << endl;

		cout << mid << " numaralı sıradadır" << endl ;


		}


		else if(search < array[mid])
		{

		cout << array[mid] << " 'den küçüktür." << endl;

		binary_search(array,search,first,mid-1);


		}

		else 
		{

		cout << array[mid] << " 'den büyüktür." << endl;

		binary_search(array,search,mid+1,last);

		}

	}

	int main()
	{


	int array[9];
	int i;


	for(i=0;i<9;i++)
	{

	array[i]=i*3;

	}

	for(i=0;i<9;i++)
	{

	cout << "Sıra numarası : " << i << " -> "<< array[i] << endl;

	}


	binary_search(array,21,0,8);


	}





<img src="/img/binary-search/binary-search.png">
