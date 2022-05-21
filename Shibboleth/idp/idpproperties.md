# ipp.propertiesの編集
本作業を実施する前提としてShibbolet標準の証明書は利用しないため、公的証明書を発行しておく必要がある。   
以下は参考までに。   
```bash:
chown root:jetty <証明書ファイル>
chmod 640 <証明書ファイル>
```
そして、以下のファイルを編集する。
```bash:
cp -p /opt/shibboleth-idp/conf/idp.properties /opt/shibboleth-idp/conf/idp.properties.org
vi /opt/shibboleth-idp/conf/idp.properties
``
