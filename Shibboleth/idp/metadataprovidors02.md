# 設定ファイルの編集
以下のファイルを編集する。   
まずはバックアップを取得する。
```bash:
cp -p /opt/shibboleth-idp/conf/metadata-providers.xml /opt/shibboleth-idp/conf/metadata-providers.xml.org
```
ファイルは以下の通りに編集する。
```bash:
<!-- --> ← コメントアウト解除
    <MetadataProvider id="HTTPMetadata"
                      xsi:type="FileBackedHTTPMetadataProvider"
                      backingFile="%{idp.home}/metadata/gakunin-metadata-backing.xml"
                      metadataURL="https://metadata.gakunin.nii.ac.jp/gakunin-metadata.xml"
                      failFastInitialization="false">

        <MetadataFilter xsi:type="SignatureValidation" certificateFile="%{idp.home}/credentials/gakunin-signer-2017.cer" />
        <MetadataFilter xsi:type="RequiredValidUntil" maxValidityInterval="P15D"/>
        <MetadataFilter xsi:type="EntityRole">
            <RetainedRole>md:SPSSODescriptor</RetainedRole>
        </MetadataFilter>
    </MetadataProvider>
<!-- --> ← コメントアウト解除
```

