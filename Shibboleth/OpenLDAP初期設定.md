## OpenLDAPのインストールまで
まぁ、ほぼ、以下の記事を参考に行けました。   
唯一、不足しているパッケージのインストール位か。   

参考URL   
https://www.designet.co.jp/ossinfo/openldap/install/

```bash:
dnf install gcc -y
dnf install cyrus-sasl* -y
dnf install make -y
```

## OpenLDAPの初期設定
問題はここから。   

### 初期設定ファイル
```bash:
cp -p /usr/local/etc/openldap/slapd.conf /usr/local/etc/openldap/slapd.conf.org
```

```bash:
slappasswd
```

以下は変更箇所のみを記載

```bash:
suffix          "dc=shibboleth,dc=progdence,dc=name"
rootdn          "cn=Manager,dc=shibboleth,dc=progdence,dc=name"
rootpw          {SSHA}
```
```bash:
mkdir /usr/local/var/openldap-data
cd /usr/local/etc/openldap
mkdir slapd.d
slaptest -f slapd.conf -F slapd.d -u
```
```bash:
vi /usr/lib/systemd/system/slapd.service
```
```bash:
[Unit]
Description=OpenLDAP server
After=syslog.target network.target
[Service]
Type=forking
PIDFile=/usr/local/var/run/slapd.pid
ExecStart=/usr/local/libexec/slapd -h "ldap:/// ldapi:///"
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/bin/kill -TERM $MAINPID
[Install]
WantedBy=multi-user.target
```
```bash:
systemctl daemon-reload
```
```bash:
systemctl start slapd.service
```

## DITの登録

以下のようにファイルを利用してDITを登録する。

```bash:
vi /usr/local/etc/openldap/init.ldif
```

```bash:
dn: dc=shibboleth,dc=progdence,dc=name
objectClass: dcObject
objectClass: organization
dc: shibboleth
o: shibboleth
```
追加する時のユーザーは先ほどのslappasswdで登録したパスワードを利用する。

```bash:
ldapadd -x -D "cn=Manager,dc=shibboleth,dc=progdence,dc=name" -W -f /usr/local/etc/openldap/init.ldif
```

## OUの登録
以下のようにファイルを利用してOUを登録する。

```bash:
vi /usr/local/etc/openldap/people_org.ldif
```

```bash:
dn: ou=people,dc=shibboleth,dc=progdence,dc=name
objectclass: organizationalUnit
ou: people
```

```bash:
ldapadd -x -D "cn=Manager,dc=shibboleth,dc=progdence,dc=name" -W -f /usr/local/etc/openldap/people_org.ldif
```

## オブジェクトクラスの追加/スキーマの追加
以下のように設定ファイルを変更して、標準でインストールされるスキーマを読み込む。   

```bash:
vi /usr/local/etc/openldap/slapd.conf
```

```bash:
include         /usr/local/etc/openldap/schema/core.schema
include         /usr/local/etc/openldap/schema/collective.schema
include         /usr/local/etc/openldap/schema/corba.schema
include         /usr/local/etc/openldap/schema/cosine.schema
include         /usr/local/etc/openldap/schema/dsee.schema
include         /usr/local/etc/openldap/schema/duaconf.schema
include         /usr/local/etc/openldap/schema/java.schema
include         /usr/local/etc/openldap/schema/misc.schema
include         /usr/local/etc/openldap/schema/namedobject.schema
include         /usr/local/etc/openldap/schema/pmi.schema
include         /usr/local/etc/openldap/schema/nis.schema
include         /usr/local/etc/openldap/schema/inetorgperson.schema
```

```bash:
systemctl restart slapd
systemctl status slapd
```
