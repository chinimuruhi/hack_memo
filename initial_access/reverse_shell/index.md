# Reverse Shell
Reverse Shellに関するナレッジ

## Code Injection
Webサービスが使用可能であり、脆弱性が存在する場合は活用できる
以下サイトでReverse Shellを貼るコマンドを生成できる
https://www.revshells.com/
生成したコマンドを攻撃対象に挿入する

### 文字制限Bypass
* 入力値に空白を使用できない場合、$IFS$9で代用できる

https://www.ctfnote.com/web/os-command-injection/whitespace-bypass

## Jenkins
JenkinsのScript Consoleが使用可能な場合等は以下のソースでリバースシェルを貼れる
https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76