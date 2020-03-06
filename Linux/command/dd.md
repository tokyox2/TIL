# ddコマンドだと IOが上がるので

```
ionice -c3 dd if=/dev/zero of=/vmdata/www7download31add5.img bs=1M count=20480 oflag=direct
```
これだと ionice がきく！
