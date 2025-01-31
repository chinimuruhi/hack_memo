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

## SUID(Set User ID)
Linuxの特殊なパーミッション属性の一つ。誰がそのファイルを実行しても、セットされたユーザで実行されるという状態。SUIDでrootユーザを指定してある場合、そのファイルから権限昇格できる可能性がある。

以下のコマンドでSUIDビットが設定されているすべてのファイルを見つけることができる。
```
$find / -user root -perm -4000 -exec ls -ldb {} \;
```

また、ls -laで特定のファイルのパーミッションを確認し、「s」があるときはSUIDが設定されている。
```
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 118168 Jul  7 22:30 /usr/bin/passwd  
```

参考：https://tryhackme.com/r/resources/blog/linux-privilege-escalation-suid

### strace
straceコマンドは、トレース対象のプログラムが発行するシステムコールと受信したシグナルを出力するコマンド。SUIDビットが設定されている実行ファイルについて調査すると良い

```
strace [プログラム] 2>&1 | grep exec
```
出力が多いためgrepを併用すると良い。「exec」や「open」等で検索をかける


## Pathの変更
rootで動いているアプリケーションが特定のコマンドを実行している場合、環境変数PATHを書き換えることで実行する内容を変えることができる

```
rootで動いているアプリケーションがcatを実行している場合の例
$ cd /tmp
$ touch cat
$ chmod +x cat
$ echo "/bin/bash" > cat
$ export PATH="/tmp:$PATH"
```

## DACLの確認
各々のファイルなどに対して、登録ユーザーやユーザーのグループに対するアクセスの許可や拒否、行使可能な権限（読み取り、書き込みなど）を列挙したリストをACL(Access Control List)という。
DACL（Discretionary Access Control List）はWindowsで使用されているACLの一つである。

```
PS C:\>icacls [ファイル名]
```
参考：https://learn.microsoft.com/ja-jp/windows-server/administration/windows-commands/icacls


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

### LinPEAS
Linuxの権限昇格の手がかりを探すシェルスクリプト
https://github.com/peass-ng/PEASS-ng/tree/master/linPEAS

## Windowsユーザの権限確認
以下のコマンドで現在の権限を確認できる
```
whoami /priv
```
SeBackupPrivilege権限を持つ場合はレジストリからNTLMハッシュを取得できる可能性がある


## LXD/LXCの権限昇格
lxdまたはlxcグループに属している場合は、rootになることができる。

参考：https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation

