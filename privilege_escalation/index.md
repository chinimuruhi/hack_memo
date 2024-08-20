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

## Impacket
[Impacket](https://github.com/fortra/impacket)はネットワークプロトコルを操作するためのPythonクラスのコレクションと説明されているものの、Windowsシステムへの侵入やデータ送出に役立つツールが多く含まれている

Kali Linuxでは/usr/share/doc/python3-impacket/examplesにある。

参考：https://tools.thehacker.recipes/impacket

### PsExec
SMB経由で被害者マシンとの間にシェルを貼る
```
python psexec.py Administrator@10.129.59.97
```
参考：https://book.hacktricks.xyz/v/jp/windows-hardening/lateral-movement/psexec-and-winexec

### mssqlclient

参考：https://book.hacktricks.xyz/v/rs/network-services-pentesting/pentesting-mssql-microsoft-sql-server

## PEAS-ng
ローカル権限昇格ベクトルを探すツール

参考: https://github.com/peass-ng/PEASS-ng