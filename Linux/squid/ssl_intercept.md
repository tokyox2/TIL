# SquidでSSL intercept をする －＞でも結果 chrome のSAN(Subject Alternative Name)対応で苦しむんでいる－＞今ココ
CentOSの場合、7以降でないとダメ（squid3以降）
```
# yum install squid
```

# 設定ファイルの書き変え
squid.conf
```
http_port 3128  ssl-bump generate-host-certificates=on dynamic_cert_mem_cache_size=4MB cert=/etc/squid/squidCA.pem
ssl_bump bump all
sslcrtd_program /usr/lib64/squid/ssl_crtd -s /var/lib/ssl_db -M 4MB
```
# 証明書が保存されるディレクトリの用意
```
#  /usr/lib64/squid/ssl_crtd c -s /var/lib/ssl_db
```

# ルート証明書の作成
```
# openssl req -new -newkey rsa:2048 -sha256 -days 3650 -nodes -x509 -keyout squidCA.pem -out squidCA.pem
# openssl x509 -in squidCA.pem -outform DER -out squidCA.der
```

# squidの起動
```
# systemctl restart squid
```

squidCA.derファイルを Windows側に導入して確認  
IE－＞OK  
FireFox－＞OK  
Chrome－＞NG  
[Chrome58で以降、この CN= を評価しなくなった模様]<https://tech.torico-corp.com/blog/chrome58-https-ssl-cert-san-error/>:TORICO 技術開発ブログより  
[SAN(Subject Alternative Name) のオレオレ証明書やっても証明書がwindows側にroot証明書としてインストールできないんだよなぁ](https://qiita.com/nis_nagaid_1984/items/b8f87d41ea108d47af61 "SAN(Subject Alternative Name) のオレオレ証明書")

