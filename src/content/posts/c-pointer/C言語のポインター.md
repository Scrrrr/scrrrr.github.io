---
title: C言語のポインター
published: 2024-11-23
description: ''
image: ''
tags: []
category: 'Study'
draft: true 
lang: ''
---

## 基本的なポインター。
```c
#include <stdio.h>

int main(void)
{
    int *p;    //int型のポインタ変数を宣言

    int a = 3; //int型の変数を宣言

    p = &a;     //ポインタに変数aのアドレスを入れる　

    printf("a = %d\n",*p); //*pでaのアドレスを参照して、aの実体を出力

    return 0;
}
```
基本的なポインタの使い方です。`&`は変数のアドレスを表します。  
ポインタには`p`と`*p`の状態の二つがあります。`p`はメモリ値を表しており、`*p`はそのメモリにある実体(上記の例であれば3)が参照されます。

## 複数のポインタ型
上記の例では**int型のポインタ変数**を宣言しました。しかし他にも**double型のポインタ変数**や**char型のポインタ変数**を定義することが出来ます。
```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int    *p_int;         //int型のポインタ変数を宣言
    double *p_double;      //double型のポインタ変数を宣言
    char   *p_char;        //char型のポインタ変数を宣言

    //各変数ポインタをmalloc関数で初期化。
    //sizeof(各要素の型)でそのサイズ分のメモリを確保することができる。
    p_int    = malloc(sizeof(int));
    p_double = malloc(sizeof(double));
    p_char   = malloc(sizeof(char));

    //変数ポインタの実体に値を入れていく
    *p_int    = 3;
    *p_double = 3.33;
    *p_char   = 'A';

    printf("*p_int    = %d\n", *p_int);
    printf("*p_double = %f\n", *p_double);
    printf("*p_char   = %c\n", *p_char);

    free(p_int);
    free(p_double);
    free(p_char);

    return 0;
}
```
宣言したポインタ変数は直接値を入れることは出来ません。最初に初期化が必要になります。  
mallocはポインタ変数を初期化するために使われる変数です。使用するためには`stdlib.h`ヘッダーが必要になります。

## void型のポインタ変数
```c
#include <stdio.h>

int main(void)
{
	void* p; //void型のポインタ変数を宣言

	int    a = 1;
	double b = 3.33;

	p = &a; //void型のポインタ変数に変数a(int型)のアドレス値を代入
	printf("p = %d\n", *(int *)p);

	p = &b; //void型のポインタ変数に変数b(double型)のアドレス値を代入
	printf("p = %f\n", *(double*)p);

    return 0;
}
```
void型のポインタ変数は型が決まっていません。なので後で自分で自分が好きなように型を決めることができます。
代入した値を表示するときはキャストすることで値を表示できます。

## void型のポインタ変数を返り値とする関数

```c
#include <stdio.h>

void* return_any_type(int *p)
{

}

int main(void)
{

}
```







### 勉強になる資料

[print(&a)ではなくprint(*a)とすると間違いですか? [ご質問へのお答え]](https://www.youtube.com/watch?v=SCzp6W9HX6o)