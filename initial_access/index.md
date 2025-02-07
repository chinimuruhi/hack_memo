# 初回アクセス

## 疎通確認
HTBのVPNにつないでいればLAN内で完結する

#### ping
* ```tcpdump -i <ネットワークデバイス名> icmpでping```で受ける
* コマンドインジェクション等で```;ping -c 3 <受けのIPアドレス>; ```等を挿入してpingが届くか確認する

#### ldap
* ```tcpdump port ldap -i <ネットワークデバイス名> -X -s 1024```で受ける
* -Xでパケットの内容を16進数とASCIIで表示する
* -sでキャプチャのバッファサイズを指定する

#### http
* ```python3 -m http.server 80```でhttp通信を受けられる
* XSS等で攻撃対象でスクリプトを動かし```http://<ip>/```へアクセスさせてリクエストが届くか確認する


## Reverse Shell
侵害したシステムから攻撃者に対し接続を行うシェルを用いた通信方法
* nc -lvnp <ポート番号> でリッスンする
* [Reverse Shell](reverse_shell/)記載のような手法で侵入後のシステムからシェルを渡す

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


## Webの脆弱性活用
[Webの脆弱性活用](web_vulnerability/)を参照