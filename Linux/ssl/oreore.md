# 急ぎのオレオレ証明書(いつも忘れるので・・・）
```
$ openssl genrsa 2048 > server.key
$ openssl req -new -key server.key > server.csr
$ openssl x509 -days 3650 -req -signkey server.key < server.csr > server.crt
```
apacheなら
/etc/httpd/conf.d/ssl 配下ディレクトリに設置し、以下のようにApacheのssl.confに記述する。
```
SSLCertificateFile /etc/httpd/conf/ssl.crt/server.crt
SSLCertificateKeyFile /etc/httpd/conf/ssl.key/server.key  
```
