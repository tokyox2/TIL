# SquidでSSL intercept をする －＞ chrome のSAN(Subject Alternative Name)対応必要
CentOSの場合、7以降でないとダメ（squid3以降）
```
# yum install squid
```

# 設定ファイルの書き変え
squid.conf
```
http_port 3128 ssl-bump \
   cert=/etc/squid/ssl_cert/server.pem \
   key=/etc/squid/ssl_cert/server.key \
   generate-host-certificates=on dynamic_cert_mem_cache_size=4MB

acl step1 at_step SslBump1
ssl_bump peek step1
sslcrtd_program /usr/lib64/squid/ssl_crtd -s /var/lib/ssl_db -M 4MB

```
# 証明書が保存されるディレクトリの用意
```
#  /usr/lib64/squid/ssl_crtd c -s /var/lib/ssl_db
```

# ルート証明書の作成
```
# openssl req -new -newkey rsa:2048 -sha256 -days 36500 -nodes -x509 -extensions v3_ca -keyout /etc/squid/ssl_cert/server.key -out /etc/squid/ssl_cert/server.pem -subj "/C=JP/ST=TOKYO/L=SHINJYUKU/O=Company/CN=HOST.com" -config <(cat /etc/pki/tls/openssl.cnf <(printf "[SAN]\nsubjectAltName='DNS:HOST.com'"))
```

# squidの起動
```
# systemctl restart squid
```

server.pemファイルを server.crtに改名して Windows側に導入して確認(ブラウザ毎に設定が違う）  

Chrome 対策のためにSAN (Subject Alternative Name) を付与したけど。-extensions SAN というオプションだと Firefox で「この証明書は認証局の証明書ではないため、認証局の一覧には追加できません。」と怒られてルート証明書として読み込めなかった！！

IE－＞OK  
FireFox－＞OK  
Chrome－＞OK のために SAN をつけた
[Chrome58で以降、この CN= を評価しなくなった模様]<https://tech.torico-corp.com/blog/chrome58-https-ssl-cert-san-error/>:TORICO 技術開発ブログより  

