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

__以下は変更箇所のみを記載__

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
__以下のようにファイルを利用してDITを登録する。__

```bash:
vi /usr/local/etc/openldap/init.ldif
```

```bash:
# Organization for Example Corporation
dn: dc=shibboleth,dc=progdence,dc=name
objectClass: dcObject
objectClass: organization
dc: progdencename
o: progdence

# Organizational Role for Directory Manager
dn: cn=Manager,dc=shibboleth,dc=progdence,dc=name
objectClass: organizationalRole
cn: Manager
```

```bash:
ldapadd -x -D "cn=Manager,dc=shibboleth,dc=progdence,dc=name" -W -f init.ldif
```
