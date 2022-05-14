# metadataprovidorsの設定
学認連携用のメタデータ自動ダウンロードの設定を追加する。   
その前にトラストアンカー証明書をインストールしておく必要があるとのことで以下の手順で用意する。

```bash:
https://repository.secomtrust.net/SC-Root2/index.html
```

上記のURLからCA証明書をダウンロードし、名前を「SCRoot2ca.cer」とする。   
サイトにはPEM形式のファイルも置かれているが、ダウンロードするファイルはDER形式のファイルを選ぶこと。   

```bash:
scp -p ./SCRoot2ca.cer root@x.x.x.x:/root
```

```bash:
openssl x509 -inform DER -in SCRoot2ca.cer -noout -fingerprint -md5
MD5 Fingerprint=6C:39:7D:A4:0E:55:59:B2:3F:D6:41:B1:12:50:DE:43
```
```bash:
openssl x509 -inform DER -in SCRoot2ca.cer -noout -fingerprint -sha1
SHA1 Fingerprint=5F:3B:8C:F2:F8:10:B3:7D:78:B4:CE:EC:19:19:C3:73:34:B9:C7:74
```
```bash:
openssl x509 -inform DER -in SCRoot2ca.cer -noout -fingerprint -sha256
SHA256 Fingerprint=51:3B:2C:EC:B8:10:D4:CD:E5:DD:85:39:1A:DF:C6:C2:DD:60:D8:7B:B7:36:D2:B5:21:48:4A:A4:7A:0E:BE:F6
```
上記のフィンガープリントが表示されることを確認する。   
※合っていない場合、改竄された証明書の可能性がある。   

トラストアンカーにCA2証明書が含まれているかを確認する。   

```bash:
keytool -cacerts -list -keystore /etc/alternatives/jre/lib/security/cacerts | \
    grep -e '6C:39:7D:A4:0E:55:59:B2:3F:D6:41:B1:12:50:DE:43' \
         -e '5F:3B:8C:F2:F8:10:B3:7D:78:B4:CE:EC:19:19:C3:73:34:B9:C7:74'
```

パスワードを入力。ここはカスタムのパスワードを設定していない限りは「changeit」でいいはず。   
証明書が既に含まれている場合はフィンガープリントが表示される。   
フィンガープリントが表示されない場合は以下のコマンドで証明書をインポートする。   

```bash:
keytool -list -keystore /etc/alternatives/jre/lib/security/cacerts -alias scrootca2
```

改めて、インポートされたかを確認してみる。   

```bash:
keytool -cacerts -list -keystore /etc/alternatives/jre/lib/security/cacerts | \
    grep -e '6C:39:7D:A4:0E:55:59:B2:3F:D6:41:B1:12:50:DE:43' \
         -e '5F:3B:8C:F2:F8:10:B3:7D:78:B4:CE:EC:19:19:C3:73:34:B9:C7:74'
```
何も表示されないが、まぁいいか。
