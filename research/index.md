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

numberを20にすれば最もよく利用される上位20個のポートに絞ってスキャンを行う

```
-T<number>
```

-T4または-T5を指定することでスキャン速度が上がる。ただし、速度と引き換えに精度が落ちる

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

### 調査対象・攻撃起点となる主要ポート
TCP
```
53 dns
80 http
443 https
445 smb
5985 win-rm
```

UDP
```
53 dns
```

## ファイルの調査
SMBやFTPが動いている場合は匿名ユーザでアクセスできるファイルを調査する

### SMB
```
smbclient -N -L <ip>
```
* -Nでパスワードなしでアクセス
* -Lで利用可能なサービス一覧を表示する


接続後、再起的にすべてのファイルを取得する
```
mask ""
recurse ON
prompt OFF
mget *
```


### FTP
匿名ユーザ「anonymous」、パスワード空欄でログインできる場合がある
```
ftp anonymous@<ip>
```

### zipファイルのパスワード解析
zipファイルのパスワードが不明な場合はJohn The Ripperを使用することでパスワードを解析することができる

https://www.kali.org/tools/john/

zipjohnコマンドでzipファイルからハッシュ値を生成する
```
$ zip2john example.zip > hash.txt
```

johnコマンドで解析を行う
```
$ john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

### gitの解析
.gitのファイルにアクセスできる場合は、Gitのログ等から重要情報を取得できる可能性がある。
GitHackを使用することで.gitからソースコードを再構築できる
https://github.com/lijiejie/GitHack
```
python GitHack.py -u "http://example.com/.git/"
```


## ブルートフォース
Kali Linuxであれば/usr/share/wordlistsにワードリストあり

### DNS
```
gobuster dns -d example.com -w /usr/share/wordlists/amass/subdomains-top1mil-5000.txt
```
* -dでドメイン指定
* -wでワードリスト指定

### Dir
```
gobuster dir -u https://example.com/ -c 'session=123456' -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -s 500 -x php
```
WEBサイトに存在するパスを見つけることができる。
* -uでURL指定
* -cでcookie値指定
* -wでワードリスト指定
* -sでPositive Status Code指定（-bでNegative Status Code指定できる）
* -xで拡張子指定できる

dirsearchを試すのも良いかも
```
dirsearch -u example.com -i 200
```

### Vhost
```
gobuster vhost -u http://example.com -w /usr/share/wordlists/amass/subdomains-top1mil-5000.txt --append-domain
```
* -uでURL指定
* -cでcookie値指定
* -wでワードリスト指定
* --append-domain ワードリストの単語をサブドメインとして追加する形で探索する。指定しない場合はFQDNをワードリストの単語として指定する必要がある。

## SNMP（Simple Network Management Protocol）
UDPの161ポートが空いている場合、snmp-checkコマンドでSNMPデバイスの一覧を表示できる
```
$ snmp-check
```
DNSが存在する場合、Digコマンドで情報を取得できる
dig 
@dnsserver
のIP 対象ホスト名 ANY +tcp
* ANY: Aレコード以外も取得する
* +tcp: DNSがTCPの場合は記載する

## netexec
NetExecは、大規模ネットワークのセキュリティ評価を自動化するのに役立つネットワークのツール
https://netexec.wiki

### SMBの例
#### ランダムなユーザー名とパスワードでターゲットがゲスト ログオンを受け入れるかどうかを確認
```
nxc smb IPアドレス -u guest -p '' 
```
#### ゲストユーザでアクセス可能な共有フォルダを探索
```
nxc smb IPアドレス -u guest -p '' --shares
```
#### ユーザ名の一覧を取得
```
nxc smb IPアドレス -u guest -p '' --users 
```
#### RIDの一覧を取得
```
nxc smb IPアドレス -u guest -p '' --rid-brute
```
SidTypeUserのラベルがついたエントリが操作可能
#### パスワードスプレー
```
nxc smb IPアドレス -u ユーザ名一覧ファイルのパス -p 'パスワード' --continue-on-success 
```
### ldapの例
#### 特定のユーザ名及びパスワードで使用可能か確認
```
nxc ldap IPアドレス -u 'ユーザ名' -p 'パスワード'
```