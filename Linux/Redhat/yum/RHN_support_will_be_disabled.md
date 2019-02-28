# RedHat で yum 時のエラー RHN support will be disabled
サブスクリプションが設定されていないか、サポート外のRHELなのかのどちらか
## Red Hat Network(RHN) とは
  Red Hat（レッドハット）社が提供するUpdateをダウンロードするシステム。
## update したい！
```java:rhnを無効化
# vi /etc/yum/pluginconf.d/rhnplugin.conf
---
[main]
 #enabled = 1
 enabled = 0
 gpgcheck = 1
```
「enabled = 1」－＞「enabled = 0」にする
ただし、サブスクリプションなしのRHELを使い続けるのはグレーなので別途購入すること。
