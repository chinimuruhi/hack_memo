# Linuxナレッジ

## RDPクライアント
Kali Linuxで使用できるRDPクライアントとしてFreeRDPが用いられることが多い
しかし、Win-Kex等、VNCを用いてGUI操作を行っている場合はFreeRDPでエラーを吐く場合がある

[Remmina Remote Desktop Client](https://remmina.org/)等の別のRDPクライアントを用いると良い

## SMB
以下のコマンドで接続できる
```
smbclient -U "ユーザ名" //IPアドレス/共有フォルダ名
```
下記コマンドですべてのファイルを取得できる
```
mget *
```
※カレントディレクトリにアクセス権限がないとファイル取得でエラーになるので注意

## LDAPの使用方法
データの取得
```
ldapsearch -H ldap://ホスト名 -D "認証に使用するユーザ名" -w "認証に使用するパスワード" -b "取得するデータの起点"
```
LDAP の一部の実装では、user@company.com のような標準形式でバインド・ユーザー名を受け入れますが、他の LDAP 実装ではユーザーの識別名 (DN) が必要になる場合があります。
DN フォーマットの例: uid=username,c=us,ou=subdomain,dc=company,dc=com