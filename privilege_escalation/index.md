# 権限昇格

## 特権ユーザで実行できるコマンドの表示
```
sudo -l
```
* 特権ユーザーで実行できるプログラムと禁止されているプログラムを確認することができる
```
$ sudo -l
Matching Defaults entries for josh on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User josh may run the following commands on localhost:
    (root) /usr/bin/ssh *
```
* 上記の例であれば/usr/bin/sshを利用できる
* 権限昇格については下記サイトにそれぞれのテクニックが記載されている

https://gtfobins.github.io/