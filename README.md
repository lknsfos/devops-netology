# devops-netology

# Домашнее задание к занятию «3.8. Компьютерные сети, лекция 3»
# Михаил Карпов

**1.** route-views>show ip route 185.41.121.9   
Routing entry for 185.41.120.0/22  
  Known via "bgp 6447", distance 20, metric 0  
  Tag 2497, type external  
  Last update from 202.232.0.2 7w0d ago  
  Routing Descriptor Blocks:  
  * 202.232.0.2, from 202.232.0.2, 7w0d ago  
      Route metric is 0, traffic share count is 1  
      AS Hops 3  
      Route tag 2497  
      MPLS label: none  
  
route-views>show bgp 185.41.121.9   
BGP routing table entry for 185.41.120.0/22, version 2393233152  
Paths: (21 available, best #20, table default)  
  Not advertised to any peer  
  Refresh Epoch 1  
  53767 14315 6939 35598 21453 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933 199933, (aggregated by 199933 185.41.120.1)  
    162.251.163.2 from 162.251.163.2 (162.251.162.3)  
      Origin IGP, localpref 100, valid, external  
      Community: 14315:2000 53767:5000  
      path 7FE140BD9398 RPKI State not found  
      rx pathid: 0, tx pathid: 0  
...  
 Refresh Epoch 1  
  852 3491 20485 20485 199933, (aggregated by 199933 185.41.120.1)  
    154.11.12.212 from 154.11.12.212 (96.1.209.43)  
      Origin IGP, metric 0, localpref 100, valid, external  
      path 7FE15BD7A158 RPKI State not found  
      rx pathid: 0, tx pathid: 0  

**2.** Создаем интерфейс  
root@ubuntu-linux-22-04-desktop:# ip link add dummy0 type dummy  
root@ubuntu-linux-22-04-desktop:# ip link list
3: dummy0: <BROADCAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000  
    link/ether 8e:31:7e:ff:27:1b brd ff:ff:ff:ff:ff:ff  

Смотрим маршруты

root@ubuntu-linux-22-04-desktop:# ip r  
default via 10.211.55.1 dev enp0s5 proto dhcp src 10.211.55.3 metric 100   
10.211.55.0/24 dev enp0s5 proto kernel scope link src 10.211.55.3 metric 100   
10.211.55.1 dev enp0s5 proto dhcp scope link src 10.211.55.3 metric 100

Добавляем один простой статический маршрут  

root@ubuntu-linux-22-04-desktop:# ip r add 192.168.0.0/16 via 10.211.55.1

Добавляем адрес на тупой интерфейс, поднимаем ему линк и добавляем маршрут через "его шлюз"

root@ubuntu-linux-22-04-desktop:# ip addr add 100.64.0.25/24 dev dummy0  
root@ubuntu-linux-22-04-desktop:# ip link set dummy0 up  
root@ubuntu-linux-22-04-desktop:# ip r add 100.64.0.0/10 via 100.64.0.1

Смотрим, что всё добавилось

root@ubuntu-linux-22-04-desktop:# ip r  
default via 10.211.55.1 dev enp0s5 proto dhcp src 10.211.55.3 metric 100   
10.211.55.0/24 dev enp0s5 proto kernel scope link src 10.211.55.3 metric 100   
10.211.55.1 dev enp0s5 proto dhcp scope link src 10.211.55.3 metric 100  
100.64.0.0/24 dev dummy0 proto kernel scope link src 100.64.0.25   
100.64.0.0/10 via 100.64.0.1 dev dummy0   
192.168.0.0/16 via 10.211.55.1 dev enp0s5    

**3.** Для прсомтра активных открытых портов используем  
sudo lsof -i -P -n | grep LISTEN  
или  
sudo ss -tulpn | grep LISTEN  
  
tcp   LISTEN 0      4096        127.0.0.53%lo:53         0.0.0.0:*    users:(("systemd-resolve",pid=452,fd=14))  
tcp   LISTEN 0      128               0.0.0.0:22         0.0.0.0:*    users:(("sshd",pid=996,fd=3))              
tcp   LISTEN 0      128             127.0.0.1:631        0.0.0.0:*    users:(("cupsd",pid=36486,fd=8))           
tcp   LISTEN 0      4096            127.0.0.1:8125       0.0.0.0:*    users:(("netdata",pid=1295,fd=38))         
tcp   LISTEN 0      4096              0.0.0.0:19999      0.0.0.0:*    users:(("netdata",pid=1295,fd=5))          
tcp   LISTEN 0      128                  [::]:22            [::]:*    users:(("sshd",pid=996,fd=4))              
tcp   LISTEN 0      128                 [::1]:631           [::]:*    users:(("cupsd",pid=36486,fd=7))   

В данном случае открыты порты не на link-local интерфейсе для ssh(22) и netdata(19999), открыт на локальном ipv4 и ipv6 адресе cupsd(631).  

**4.** sudo lsof -i -P -n | grep UDP  
или  
ss -lnpu  
State  Recv-Q  Send-Q        Local Address:Port    Peer Address:Port Process                                      
UNCONN 0       0                   0.0.0.0:631          0.0.0.0:*     users:(("cups-browsed",pid=36490,fd=7))     
UNCONN 0       0                 127.0.0.1:8125         0.0.0.0:*     users:(("netdata",pid=1295,fd=31))          
UNCONN 0       0             127.0.0.53%lo:53           0.0.0.0:*     users:(("systemd-resolve",pid=452,fd=13))   
UNCONN 0       0        10.211.55.3%enp0s5:68           0.0.0.0:*     users:(("systemd-network",pid=436,fd=17))   
UNCONN 0       0                   0.0.0.0:5353         0.0.0.0:*     users:(("avahi-daemon",pid=669,fd=12))      
UNCONN 0       0                   0.0.0.0:36221        0.0.0.0:*     users:(("avahi-daemon",pid=669,fd=14))      
UNCONN 0       0                      [::]:54232           [::]:*     users:(("avahi-daemon",pid=669,fd=15))      
UNCONN 0       0                      [::]:5353            [::]:*     users:(("avahi-daemon",pid=669,fd=13))  
Открыты сокеты для печати (cupsd), мониторинг netdata, dns (systemd-resolve(53)), dhcp-clien (systemd-network(68)) и службы avahi-daemon для поиска и обнаружения совместимого оборудования в сети.