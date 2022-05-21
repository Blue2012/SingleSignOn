# 設定ファイルの編集
以下のファイルを編集する。   
まずはバックアップを取得する。
```bash:
cp -p /opt/shibboleth-idp/conf/metadata-providers.xml /opt/shibboleth-idp/conf/metadata-providers.xml.org
```
ファイルは以下の通りに編集する。テスト環境のメタデータダウンロードを記載。
```bash:
<!-- --> ← コメントアウト解除
    <MetadataProvider id="HTTPMetadata"
                      xsi:type="FileBackedHTTPMetadataProvider"
                      backingFile="%{idp.home}/metadata/gakunin-metadata-backing.xml"
                      metadataURL="https://metadata.gakunin.nii.ac.jp/gakunin-metadata.xml"
                      failFastInitialization="false">

        <MetadataFilter xsi:type="SignatureValidation" certificateFile="%{idp.home}/credentials/gakunin-test-signer-2020.cer" />
        <MetadataFilter xsi:type="RequiredValidUntil" maxValidityInterval="P15D"/>
        <MetadataFilter xsi:type="EntityRole">
            <RetainedRole>md:SPSSODescriptor</RetainedRole>
        </MetadataFilter>
    </MetadataProvider>
<!-- --> ← コメントアウト解除
```
あとは本当に必要なのかもよく分からないけれど、メタデータの検証猶予期間を設定しておくとページに記載
ちなみにメタデータ自体が必要になるので、そちらも用意をしておく。
```bash:
cd /opt/shibboleth-idp/credentials/
wget https://metadata.gakunin.nii.ac.jp/gakunin-test-signer-2020.cer
```
あとはパーミッっションも変更しておく。
```bash:
chown root:jetty gakunin-test-signer-2020.cer
chmod 640 gakunin-test-signer-2020.cer
```
