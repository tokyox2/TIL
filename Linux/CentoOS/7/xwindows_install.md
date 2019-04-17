# CentOS7 でのTIP
# windows install
```
# yum groupinstall install "X Window System"
# yum install vlgothic-* 文字化け対策
# systemctl set-default graphical.target
# systemctl get-default
# reboot
```

# Vncserver
```
# yum install tigervnc-server
# cd /etc/systemd/system
# ln -s /lib/systemd/system/vncserver@.service vncserver@:1.service
# vi vncserver@:1.service
[Service]
Type=forking
# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
PIDFile=/home/<USER>/.vnc/%H%i.pid
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

# systemctl daemon-reload
# systemctl enable vncserver@:1
# systemctl restart vncserver@:1
```

# 仮想化
```
# yum install libguestfs libvirt libvirt-client python-virtinst qemu-kvm virt-manager virt-top virt-viewer virt-who virt-install bridge-utils
# systemctl start libvirtd
# systemctl enable libvirtd
# cp /etc/sysconfig/network-scripts/ifcfg-enp3s0 /etc/sysconfig/network-scripts/ifcfg-br0
# vi /etc/sysconfig/network-scripts/ifcfg-br0
TYPE=Bridge
NAME=br0
DEVICE=br0
ONBOOT=yes
# vim /etc/sysconfig/network-scripts/ifcfg-enp3s0
TYPE="Ethernet"
BRIDGE=br0
NAME="enp3s0"
UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
DEVICE="enp3s0"
ONBOOT="yes"

# systemctl restart network
# brctl show
bridge name bridge id       STP enabled interfaces
br0     8000.c03fd56f5970   no      enp3s0
virbr0      8000.52540083d5a7   yes     virbr0-nic

# ip a show br0
23: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
...
    inet 192.168.x.x/24 brd 192.168.11.255 scope global dynamic br0
       valid_lft 172672sec preferred_lft 172672sec
...

ブリッジデバイス経由のパケットにiptablesの適用を行わない設定をします。
# vim /etc/sysctl.conf
net.bridge.bridge-nf-call-ip6tables = 0
net.bridge.bridge-nf-call-iptables = 0
net.bridge.bridge-nf-call-arptables = 0
### パラメーターのリロード
# sudo sysctl -p /etc/sysctl.conf

```

