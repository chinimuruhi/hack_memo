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

