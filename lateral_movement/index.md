# 横展開

## WinRM
WinRMサービスが有効(通常5985/tcp)かつ、資格情報とアクセス許可がある場合にはEvil-WinRMというツール使用し、インタラクティブなPoweShellを取得できる。
```
evil-winrm -i <ipアドレス> -u <ユーザ名> -p <パスワード>
```