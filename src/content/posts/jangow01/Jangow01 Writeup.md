---
title: Jangow01 Writeup
published: 2023-10-09
description: ''
image: ''
tags: [Writeup]
category: 'Boot2Root'
draft: false 
lang: 'ja'
---

頑張ってまとめてみます。正直、もう初手で他の人のWriteup読んでクリアぎり出来てないので書く意味あるのか分からないですが、勉強になった部分だけ書きます。  

侵入マシン:
[Jangow: 1.0.1](https://www.vulnhub.com/entry/jangow-101,754)  

侵入先のIP:  192.168.3.159  
攻撃側のIP:  192.168.3.71  
͏  

## はじめに
攻撃先のIPは最初に付与されていたので、ターミナル内でIPという変数を作っとくと便利です。  
```bash
$ ip=192.168.3.159
```
しかし、Proxyを通すときは多分、この変数は変換されないので、確実にしたいときはちゃんと打ち込みましょう。  
͏  

## ポートスキャン
このチートシートにある標準でスキャンしました。  
[https://github.com/0xSojalSec/OSCP-Cheatsheet#port-scanning](https://github.com/0xSojalSec/OSCP-Cheatsheet#port-scanning)
```bash
$ nmap -sC -sV 192.168.3.159 -v
```


```text
Nmap scan report for 192.168.3.159
Host is up (0.00079s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
80/tcp open  http    Apache httpd 2.4.18
|_http-title: Index of /
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2021-06-10 18:05  site/
|_
Service Info: Host: 127.0.0.1; OS: Unix
```

80ポートが空いていので、とりあえずWEBが動いてそう。  
メールアドレスを入れるFormがありましたが、「＠」がついてないと動かないため、XSSではなさそう  

Niktoを使って脆弱性を探したりしましたが、うまく行かず、ここから他の人のWriteupを見ました。  
͏  
͏  
͏  

## URLにある"=" に注目
webサイトにphpで書かれたページに飛ぶリンクがあったそうです。  
そこのサイトのURLの'='からCommand Injectionが有効だったそう。
  
あとはリバースシェルすればいいのですが、リバースシェルはFWによって遮断されていました。
͏  
͏  
͏  

## 資源を探す
とりあえず、使えそうな情報を探します。  
このとき、webページにして使うよりも、URLに**view-source**をつけるとcatなどのコマンドが正しく読めます。  
```text
ls -la
ls -la /home/*/.
cat *
```

ここでhomeディレクトリにuser.txtを見つけて、ユーザの資格情報が手に入ります。  
͏  
͏  
͏  

## ftpにログイン
ユーザの資格情報が手に入ったのでftpにログインします。下の通りにすると一発で入れます。
```text
$ftp ftp://{USERNAME}:{PASSWORD}@$ip
```
ftpはexploitコードをアップロードしたり、ファイル権限の操作のために利用しました。
```text
$ftp: put exploit.c exploit.c

$ftp: chmod 777 exploit.c
```
͏  
͏  
͏  
## プロキシを展開してリバースシェルを実行  
[https://github.com/sensepost/reGeorg](https://github.com/sensepost/reGeorg)  
なんだか、これを使うといいらしいです。  
詳しい説明は下のWriteupを読んでください。  
[https://secjourney.net/vulnhub-jangow/](https://secjourney.net/vulnhub-jangow/)

## root権限
シェルを奪えたら、権限昇格です。  
ftpを使って、exploit.cをアップロードして、奪ったシェルでコンパイルします。  
ここでgccが許可されておらず、Permission errorになりました。  
仕方がないので、コンパイルしたものをアップして実行します。  
「glibc 2.32 not found」  
「glibc 2.33 not found」  
今度はglibcのバージョンの違いでエラーが起きました。
これはもう一つ仮想環境を構築して、対応しているglibcでコンパイルする必要があります。  
