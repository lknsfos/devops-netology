# devops-netology

# Домашнее задание к занятию «3.7. Компьютерные сети, лекция 2»
# Михаил Карпов

**1.** В линукс инструментарии из net-tools2 (ip: ip a для просмотра интерфейсов с их адресами, ip link для промотра статуса интерфейсов)  
В более старых системах или если установлен net-tools можно использовать ifconfig. Он же чаще встречается на других unix-like системах.
В windows используем ipconfig /all

**2.** Для распознования соседа обычно используется lldp. В линукс ставится apt install lldpd  

**3.** Для разделения коммутатора на несколько виртуальных сетей на начальном этапе используется vlan (802.1q). В линукс надо поставить пакет vlan.
Пример centos
cat /etc/sysconfig/network-scripts/ifcfg-vlan49  
VLAN="yes"  
VLAN_NAME_TYPE=VLAN_PLUS_VID_NO_PAD  
DEVICE="vlan49"  
PHYSDEV="eth0"  
BOOTPROTO="static"  
ONBOOT="yes"  
TYPE="Ethernet"  
IPADDR="172.16.31.2"  
NETMASK="255.255.255.0"  
DESCRIPTION="unset"  

Пример ubuntu с netplan  
  vlans:  
    vlan29:  
      id: 29  
      link: enp7s0f0  
      addresses: [192.168.209.187/24]  
    vlan25:  
      id: 25  
      link: enp7s0f0  
      addresses: [10.224.194.154/24, 10.224.194.153/32]  
      routes:  
        - to: 10.0.0.0/8  
          via: 10.224.194.129  
          metric: 1  
        - to: 172.16.40.0/22  
          via: 10.224.194.129  
          metric: 1  

Пример debian, bridge+vlan  
auto br0.432  
iface br0.432 inet static  
    bridge_ports eth0.432  
    bridge_fd 0  
    bridge_stp off  
    address 192.168.209.1  
    netmask 255.255.255.0  

**4.** В линукс используется bonding или teaming (teamd)
Teaming конфигов не приведу, только тестировал несколько лет назад,
bonding довольно просто настраивается через networkd в debian-like  
auto enp7s0f0  
iface enp7s0f0 inet manual  
    bond-master enp7s0f0  
    bond-primary eth0  

auto enp7s0f1  
iface enp7s0f1 inet manual  
    bond-master bond0  

auto bond0  
iface bond0 inet static  
    address 10.11.0.2  
    gateway 10.11.0.1  
    netmask 255.255.255.0  
    bond-mode 4  
    bond-miimon 100  
    bond-slaves none
    bond-xmit-hash-policy layer3+4
  
bond-mode 4 указывает, что используется протокол lacp (802.3ad), можно также использовать другие методы (статической аггрегации),  
bond-xmit-hash-policy layer3+4 указывает на балансировку по совокупности информации о ip-адресах и номерах портов назначения.  
Также для балансировки можно указывать балансировку по mac-адресам, ip+mac.  
**5.** В сети /29, если не использовать для переразбиения, можно использовать 6 адресов, всего будет занято 8 адресов (включая адрес сети и бродкаст)  
Одну /24 сеть можно разбить на 32 /29 сети (256/8=32).
Примеры сетей: 10.10.10.128/29, 10.10.10.32/29  
**6.** Если заняты все private-range адреса, то предлагается использовать для 40-50 хостов либо незанятую область 240.0.0.0/26, либо, что не совсем по стандарту, но в целом взлетит: private-pool для cg-nat:
100.64.0.0/26.

**7.** В windows arp -a для просмотра записей arp. arp -d для удаления всего кэша и arp -d ADDRESS для удаления одной записи.   
в Linux - ip neigh для просмотра. ip neigh flush для очистки и ip neigh del ADDRESS dev DEVICE для удаления одной записи. 