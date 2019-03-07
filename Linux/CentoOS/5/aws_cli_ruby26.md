# 古いCentOS5系で aws cli を使用したい場合
Ruby2.6 をインストールする
```
# rpm -Uvh  https://archives.fedoraproject.org/pub/archive/epel/5/x86_64/epel-release-5-4.noarch.rpm
# vi /etc/yum.repo.d/epel.repo を適宜編集
# yum install python26 (--disablerepo=XXX --enablerepo=xxx  export http_proxy=など指定）
(pipじゃないバージョンで）
# curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip" 
# unzip awscli-bundle.zip
# ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```
