# MongoDB

## 接続方法
```
mongo 「DB名」 -u 「ユーザー名」 -p
```

## 対話モード
DB一覧を表示する
```
show dbs;
```

コレクション一覧を確認する
```
show collections;
```

コレクション内のオブジェクト一覧を表示する
```
db.foo.find()
```

オブジェクトを更新する
```
db.foo.update({name:'hoge'}, {$set:{gender:'XXX', age:100}})
```
第一引数で書き換えるオブジェクトの指定、第二引数で$set修飾子をつけることで更新する場所及び値を指定（set修飾子がないとObjectそのものを上書きしてしまうため注意）