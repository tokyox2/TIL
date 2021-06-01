CentOS7への OpenVPNインストール

```
# yum install epel-release
# yum --enablerepo=epel openvpn
# yum --enablerepo=epel install easy-rsa
# cp -pr /usr/share/easy-rsa/3.0.8/ /etc/openvpn/easy-rsa
# cd /etc/openvpn/easy-rsa/
```
初期化
```
# ./easyrsa init-pki

init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /etc/openvpn/easy-rsa/pki
```
CA証明書・秘密鍵作成
```
# ./easyrsa build-ca
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017

Enter New CA Key Passphrase:
Re-Enter New CA Key Passphrase:
Generating RSA private key, 2048 bit long modulus
................................................+++
.............+++
e is 65537 (0x10001)
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [Easy-RSA CA]:poseidonkanshjivpn.com

CA creation complete and you may now import and sign cert requests.
Your new CA certificate file for publishing is at:
/etc/openvpn/easy-rsa/pki/ca.crt
```
CA証明書をOpenVPN設定ファイル格納ディレクトリへコピー
```
cp -pr pki/ca.crt /etc/openvpn/
```
サーバー証明書・秘密鍵作成
```
# ./easyrsa build-server-full server nopass　←　サーバー証明書・秘密鍵作成
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating a 2048 bit RSA private key
........................+++
...........................................................+++
writing new private key to '/etc/openvpn/easy-rsa/pki/easy-rsa-5363.46tfEu/tmp.rewNLf'
-----
Using configuration from /etc/openvpn/easy-rsa/pki/easy-rsa-5363.46tfEu/tmp.l6ifkU
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key: ←　CA秘密鍵のパスフレーズを応答
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'server'
Certificate is to be certified until Sep  4 05:07:07 2023 GMT (825 days)

Write out database with 1 new entries
Data Base Updated

[root@poseidonkanshjivpn01 easy-rsa]# cp -pr pki/issued/server.crt /etc/openvpn/ ←　サーバー証明書をOpenVPN設定ファイル格納ディレクトリへコピー
[root@poseidonkanshjivpn01 easy-rsa]# cp -pr pki/issued/server.crt /etc/openvpn/ ←　サーバー秘密鍵をOpenVPN設定ファイル格納ディレクトリへコピー
```
DH(Diffie Hellman)パラメータ作成
```
# ./easyrsa gen-dh　←　DHパラメータ作成
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating DH parameters, 2048 bit long safe prime, generator 2
This is going to take a long time
......................+......................................................................................................+.........+...................................................................................................................................................................................................+............................................+.........................................................................................................................................................................................................................................................+.................................................................................................................................................................+...........................................................+....................+.....+....................................................................................................................................................................................................................................+.................................................................................+..........................................................................................................................................................................................................................................................................................................................+....+.............................................................+...+..........................................................................................................................................................................................+...................+.....................+..........................................................................................................................................................+..................................................+..........................................+..........++*++*

DH parameters of size 2048 created at /etc/openvpn/easy-rsa/pki/dh.pem

# cp -pr pki/dh.pem /etc/openvpn/　←　DHパラメータをOpenVPN設定ファイル格納ディレクトリへコピー
```
証明書廃止リスト作成
特定のVPNクライアントからのVPN接続を禁止できるようにするため、証明書廃止リストを作成する。
※証明書廃止リストは、実際にクライアント証明書を作成してから廃止を行わないと作成できないので、ダミーのクライアント証明書をいったん作成&廃止することにより証明書廃止リストを作成する
```
# ./easyrsa build-client-full dmy nopass　←　ダミーのクライアント証明書作成
# ./easyrsa build-client-full dmy nopass
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating a 2048 bit RSA private key
......................+++
...........................................................................................................................................................+++
writing new private key to '/etc/openvpn/easy-rsa/pki/easy-rsa-6869.7aDW9z/tmp.VclrW7'
-----
Using configuration from /etc/openvpn/easy-rsa/pki/easy-rsa-6869.7aDW9z/tmp.GKAvqN
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key:　←　CA秘密鍵のパスフレーズを応答
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'dmy'
Certificate is to be certified until May 30 05:27:22 2031 GMT (3650 days)

Write out database with 1 new entries
Data Base Updated

# ./easyrsa revoke dmy　←　ダミーのクライアント証明書廃止
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017


Please confirm you wish to revoke the certificate with the following subject:

subject=
    commonName                = dmy


Type the word 'yes' to continue, or any other input to abort.
  Continue with revocation: yes
Using configuration from /etc/openvpn/easy-rsa/pki/easy-rsa-6936.m8fIGl/tmp.lGfAIj
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key:　←　CA秘密鍵のパスフレーズを応答
Revoking Certificate 0ABE520C81435E0332616A9799C23A59.
Data Base Updated

IMPORTANT!!!

Revocation was successful. You must run gen-crl and upload a CRL to your
infrastructure in order to prevent the revoked cert from being accepted.

# ./easyrsa revoke dmy　←　ダミーのクライアント証明書廃止
]# ./easyrsa revoke dmy
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017


Please confirm you wish to revoke the certificate with the following subject:

subject=
    commonName                = dmy


Type the word 'yes' to continue, or any other input to abort.
  Continue with revocation: yes
Using configuration from /etc/openvpn/easy-rsa/pki/easy-rsa-7696.uEd0xJ/tmp.mbcjYW
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key: ←　CA秘密鍵のパスフレーズを応答
Revoking Certificate 020546AA1E40FDF498A5D29C83705BAE.
Data Base Updated

IMPORTANT!!!

Revocation was successful. You must run gen-crl and upload a CRL to your
infrastructure in order to prevent the revoked cert from being accepted.

# cp /usr/share/doc/easy-rsa-3.0.8/vars.example ./ ←　Easy-RSAパラメータ設定ファイルをサンプルよりコピー
# vi vars　←　Easy-RSAパラメータ設定ファイル編集
set_var EASYRSA_CRL_DAYS        3650　←　行頭の#を削除（コメント解除）して証明書廃止リストの有効期限を3650日に変更
※証明書廃止リストの有効期限は初期設定では180日となっており、期限が切れるたびに証明書廃止リストの再作成をしなければならなく、運用が煩雑なため、サーバー証明書、クライアント証明書の初期設定値と同じ3650日にする

# ./easyrsa gen-crl　←　証明書廃止リストを作成

Note: using Easy-RSA configuration from: /etc/openvpn/easy-rsa/vars
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Using configuration from /etc/openvpn/easy-rsa/pki/easy-rsa-7767.wW0XeC/tmp.duPwA5
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key: ←　CA秘密鍵のパスフレーズを応答

An updated CRL has been created.
CRL file: /etc/openvpn/easy-rsa/pki/crl.pem

# cp -pr /etc/openvpn/easy-rsa/pki/crl.pem /etc/openvpn/ ←　証明書廃止リストをOpenVPN設定ファイル格納ディレクトリへコピー
# chmod o+r /etc/openvpn/crl.pem　←　証明書廃止リストへ参照権限付加
```
OpenVPN設定
```
# openvpn --genkey --secret /etc/openvpn/ta.key　←　TLS認証鍵をOpenVPN設定ファイル格納ディレクトリへ作成
# cp -pr /usr/share/doc/openvpn-*/sample/sample-config-files/server.conf /etc/openvpn/　←　OpenVPN設定ファイルをサンプルよりコピー
# vi /etc/openvpn/server.conf　←　OpenVPN設定ファイル編集
dev tun　←　VPNインタフェースとしてTUNを指定(デフォルト)

dh dh.pem　←　DHパラメータファイル名を指定

server 123.xx.xx.0 255.255.255.0　←　VPNクライアント割当てアドレス範囲として10.8.0.0/24を指定(デフォルト)

;push "route 192.168.10.0 255.255.255.0"
;push "route 192.168.20.0 255.255.255.0"
push "route 172.22.0.0 255.255.0.0"　←　追加(LANへのルートをVPNサーバー経由にする)

tls-auth ta.key 0 # This file is secret　←　行頭の;を削除してコメント解除(TLS認証有効化)

user nobody　←　行頭の;を削除してコメント解除(OpenVPN実行権限を下げる)
group nobody　←　行頭の;を削除してコメント解除(OpenVPN実行権限を下げる)

log-append  /var/log/openvpn.log　←　行頭の;を削除してコメント解除(ログを/var/log/openvpn.logに記録する)

management localhost 7505　←　最終行へ追加(管理インタフェースの有効化※後述)

crl-verify crl.pem　←　最終行へ追加(証明書廃止リストの有効化)

最終行へ以下を追加(OpenVPN経由でSambaへのアクセスがエラーになる場合)
（注）iPhoneからOpenVPNへ接続する場合、アプリがfragmentオプション未対応のため、アプリで接続はできるがネットワークへアクセスできなくなるので下記設定は行わないこと
fragment 1280
mssfix 1280
link-mtu 1400
```

VPNインタフェース用ファイアウォール自動設定
```
# vi /etc/openvpn/openvpn-startup　←　OpenVPN起動時実行スクリプト新規作成
#!/bin/bash

# VPNインタフェースiptablesルール削除スクリプト実行※必須
/etc/openvpn/openvpn-shutdown

# VPNサーバーからの送信を許可※必須
iptables -I OUTPUT -o tun+ -j ACCEPT
iptables -I FORWARD -o tun+ -j ACCEPT

# VPNクライアントからVPNサーバーへのアクセスを許可する場合
iptables -I INPUT -i tun+ -j ACCEPT

# VPNクライアントからLANへのアクセスを許可する場合
# (例としてVPNクライアントから192.168.1.0/24へのアクセスを許可する場合)
# ※192.168.1.0/24側の各端末のファイアウォール等でVPNクライアント(10.8.0.0/24)からのアクセスを許可すること
iptables -I FORWARD -i tun+ -d 192.168.1.0/24 -j ACCEPT

# VPNクライアントからLAN内特定マシンのみへのアクセスを許可する場合
# (例としてVPNクライアントから192.168.1.30へのアクセスを許可する場合)
# ※192.168.1.30側のファイアウォール等でVPNクライアント(10.8.0.0/24)からのアクセスを許可すること
iptables -I FORWARD -i tun+ -d 192.168.1.30 -j ACCEPT
# chmod +x /etc/openvpn/openvpn-startup　←　OpenVPN起動時実行スクリプトへ実行権限付加
```

VPNインタフェース用ファイアウォール自動設定解除
```
# vi /etc/openvpn/openvpn-shutdown　←　OpenVPN停止時実行スクリプト新規作成
#!/bin/bash

# VPNインタフェース(tun+)用iptablesルール削除関数
delete() {
    rule_number=`iptables -L $target --line-numbers -n -v|grep tun.|awk '{print $1}'|sort -r`
    for num in $rule_number
    do
        iptables -D $target $num
    done
}

# VPNインタフェース(tun+)用iptables受信ルール削除
target='INPUT'
delete

# VPNインタフェース(tun+)用iptables転送ルール削除
target='FORWARD'
delete

# VPNインタフェース(tun+)用iptables送信ルール削除
target='OUTPUT'
delete

[root@centos ~]# chmod +x /etc/openvpn/openvpn-shutdown　←　OpenVPN停止時実行スクリプトへ実行権限付加
```

OpenVPNログローテーション設定
```
# vi /etc/logrotate.d/openvpn　←　OpenVPNログローテーション設定ファイル新規作成
/var/log/openvpn.log {
    missingok
    notifempty
    sharedscripts
    postrotate
        systemctl restart openvpn 2>&1 > /dev/null || true
    endscript
}
```
OpenVPN起動(サーバー側)
```
# vi /etc/rc.d/init.d/openvpn　←　OpenVPN起動スクリプト編集
        echo 1 > /proc/sys/net/ipv4/ip_forward　←　行頭の#を削除してコメント解除(パケット転送有効化)

[root@centos ~]# systemctl daemon-reload　←　OpenVPN起動スクリプト変更反映※CentOS7の場合

[root@centos ~]# /etc/rc.d/init.d/openvpn start　←　OpenVPN起動

[root@centos ~]# chkconfig openvpn on　←　OpenVPN自動起動設定

```


