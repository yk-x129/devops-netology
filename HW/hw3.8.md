##3.8
1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
route-views>show ip route 62.231.19.142   
Routing entry for 62.231.0.0/19
  Known via "bgp 6447", distance 20, metric 0
  Tag 2497, type external
  Last update from 202.232.0.2 7w0d ago
  Routing Descriptor Blocks:
  * 202.232.0.2, from 202.232.0.2, 7w0d ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 2497
      MPLS label: none

route-views>show bgp 62.231.19.142   
BGP routing table entry for 62.231.0.0/19, version 307587464
Paths: (23 available, best #22, table default)
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3216:2001 3216:2999 3216:4100 3303:1004 3303:1006 3303:3052 3356:2 3356:22 3356:100 3356:123 3356:501 3356:903 3356:2065 3356:10725
      path 7FE0DCAF38D8 RPKI State not found
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 3
  2497 3216
    202.232.0.2 from 202.232.0.2 (58.138.96.254)
      Origin IGP, localpref 100, valid, external, best
      path 7FE0AE642D78 RPKI State not found
      rx pathid: 0, tx pathid: 0x0
```

2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
```
загрузить модуль «dummy» и назначить адрес:
sudo modprobe -v dummy numdummies=1
sudo ip addr add 192.168.150.150/24 dev dummy0
Поднимаем интерфейс:
sudo ip link set dummy0 up

dummy0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether be:c4:e5:c7:dd:6d brd ff:ff:ff:ff:ff:ff
    inet 192.168.150.150/24 scope global dummy0
       valid_lft forever preferred_lft forever
    inet6 fe80::bcc4:e5ff:fec7:dd6d/64 scope link 
       valid_lft forever preferred_lft forever

Добавляем маршрут через dummy0 и смотрим таблицу маршрутизации:
sudo ip route add 192.168.170.0/24 via 192.168.150.150 dev dummy0
ip r

default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100 
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15 
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100 
192.168.150.0/24 dev dummy0 proto kernel scope link src 192.168.150.150 
192.168.170.0/24 via 192.168.150.150 dev dummy0           
```
3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
```
sudo ss -tpnl
State               Recv-Q              Send-Q                           Local Address:Port                           Peer Address:Port              Process                                                 
LISTEN              0                   4096                             127.0.0.53%lo:53                                  0.0.0.0:*                  users:(("systemd-resolve",pid=615,fd=13))              
LISTEN              0                   128                                    0.0.0.0:22                                  0.0.0.0:*                  users:(("sshd",pid=717,fd=3))                          
LISTEN              0                   4096                                         *:9100                                      *:*                  users:(("node_exporter",pid=641,fd=3))                 
LISTEN              0                   128                                       [::]:22                                     [::]:*                  users:(("sshd",pid=717,fd=4))                          
LISTEN              0                   4096                                         *:9090                                      *:*                  users:(("prometheus",pid=643,fd=8))   

22 - SSH
53 - DNS
9100 - node_exporterde_exporter
```
4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
```
ss -lnpu
State                   Recv-Q                  Send-Q                                    Local Address:Port                                     Peer Address:Port                  Process                  
UNCONN                  0                       0                                         127.0.0.53%lo:53                                            0.0.0.0:*                                              
UNCONN                  0                       0                                        10.0.2.15%eth0:68                                            0.0.0.0:*   

53/UDP - DNS
68/UDP - DHCP clent port
```
5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.
```
![netplan](https://github.com/yk-x129/devops-netology/blob/main/HW/images/netplan.png)
```
