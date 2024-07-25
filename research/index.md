# 調査
攻情報収集に関するメモ

## ポートスキャン（nmap）
ポートスキャンすることでシステムのどのポートが空いているか、どのようなサービスが実行されているのかを把握できる
```
nmap <option> <ip>
```

### スキャン種別
一般的なものは以下の通り
* TCPスキャン(-sT)
    * 3ウェイハンドシェイクが成立したかどうかでポート開閉を判断する
* SYNスキャン(-sS)
    * SYN+ACK受取後、RSTを送り返し、コネクションを確立しないため検出され辛い
    * 実行にsudoが必要
* UDPスキャン(-sU)

ステルス性の高いスキャンとして以下のものがある
* NULLスキャン(-sN)
* FINスキャン(-sF)
* Xmasスキャン(-sX)

### スキャン時間短縮
```
-top-ports <number>
```
```<number>```を20にすれば最もよく利用される上位20個のポートに絞ってスキャンを行う

```
-T<number>
```

```-T4```または```-T5```を指定することでスキャン速度が上がる。ただし、速度と引き換えに精度が落ちる

### バージョン調査
```
-sV
```
このオプションを追加することでサービスのバージョンも調査できる

### スクリプトスキャン
```
-sC
```
このオプションを追加することで各サービスのデフォルトカテゴリーのスクリプトスキャンを実行できる

スクリプトの種類は以下の通り

https://nmap.org/nsedoc/categories/

### スキャン範囲
```
-p 1-1024
```
上記の例であれば1から1024のポートをスキャンする

## ブルートフォース（Gobuster）
DNS（subdomain）やパスなどのファジングが可能
https://github.com/OJ/gobuster

Kali Linuxであれば/usr/share/wordlistsにワードリストあり

### DNSモード
```
gobuster dns -d example.com -w /usr/share/wordlists/amass/subdomains-top1mil-5000.txt
```
* -dでドメイン指定
* -wでワードリスト指定

### Dir
```
gobuster dir -u https://example.com/ -c 'session=123456' -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -s 500 -x php
```
* -uでURL指定
* -cでcookie値指定
* -wでワードリスト指定
* -sでPositive Status Code指定（-bでNegative Status Code指定できる）
* -xで拡張子指定できる