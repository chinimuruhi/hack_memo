# 発見
侵入後、システムと内部ネットワークの情報取得に関するメモ

## listenポートの調査
```
ss -tl
```
ssコマンドでソケットの情報を表示できる
* -tでTCPソケットを表示できる
* -lでlistenポートのみ表示する

## ポートフォワード
```
ssh -L 1234:localhost:5432 username@10.111.111.111
```
侵入後システムのlocalからでしかアクセスできないサービス等にアクセスする際に、ポートフォワードを行う
参考：https://qiita.com/mechamogera/items/b1bb9130273deb9426f5


