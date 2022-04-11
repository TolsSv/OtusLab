# Лабораторная работа №3.1 Реализация DHCPv4
#### Цель: 
Настроить DHCPv4.
#### Описание:
В данной лабораторной работе необходимо настроить автоматическое получение IPv4 адресов в сети.

## Топология

В ходе выполнения лабораторной работы необходимо собрать стенд в соответствии со схемой:

![](Topoligy_lab3.1.PNG)

## Таблица адресов

В ходе выполнения лабораторной работы необходимо настроить адресацию в соответствии с таблицей адресов:

| Устройство      | Интерфейс          | IP адрес     | Маска подсети   | Шлюз по умолчанию |
| --------------- | -------------------| -------------|-----------------|-------------------|
| R1              | e0/0               | 10.0.0.1     | 255.255.255.252 | N/A               |
|                 | e0/1               | N/A          | N/A             |                   |
|                 | e0/1.100           |              |                 |                   |
|                 | e0/1.200           |              |                 |                   |
|                 | e0/1.1000          | N/A          | N/A             | N/A               |
| R2              | e0/0               | 10.0.0.2     | 255.255.255.252 | N/A               |
|                 | e0/1               |              |                 |                   |
| S1              | VLAN 200           |              |                 |                   |
| S2              | VLAN 1             |              |                 |                   |
| PC-A            | NIC                | DHCP         | DHCP            | DHCP              |
| PC-B            | NIC                | DHCP         | DHCP            | DHCP              |


## Таблица VLAN

VLAN, которые необходимо настроить в лабораторной работе, приведены в таблице vlan:

| VLAN      | Имя         | Интерфейсы                  |
| ----------|-------------| ----------------------------|
| 1         | N/A         | S2: e0/0                    | 
| 100       | Clients     | S1: e0/0                    | 
| 200       | Management  | S1: VLAN 200                | 
| 999       | Parking_Lot | S1: e0/2-3                  | 
| 1000      | Native      | N/A                         | 

## Части

Лбораторная работа разбита на 3 части:
1) Создать сеть в соответствии с топологией и настроить основные параметры устройств
2) Настроить и проверить 2 пула на DHCPv4 сервере R1
3) Настроить и проверить трансляцию DHCPv4 на R2

## Часть 1. Выполнение

### Распределение пула ip адресов на подсети

В ходе выполнения лабораторной работы необходимо разделить пул ip адресов 192.168.1.0/24 на 3 подсети в соответствии с требованиями:
1) Подсеть А должна включать 58 хостов (Client VLAN маршрутизатора R1). Первый ip адрес подсети должен быть назначен маршрутизатору R1 на порт e0/1.100
2) Подсеть В должна включать 28 хостов (Management VLAN маршрутизатора R1). Первый ip адрес подсети должен быть назначен маршрутизатору R1 на порт e0/1.200, а второй ip адрес должен быть назначен коммутатору S1.
3) Подсеть C должна включать 12 хостов (Client сеть маршрутизатора R2). Первый ip адрес подсети должен быть назначен маршрутизатору R2 на порт e0/1

Таблица адресов была заполнена в соответствии с приведенными требованиями:

| Устройство      | Интерфейс          | IP адрес     | Маска подсети   | Шлюз по умолчанию |
| --------------- | -------------------| -------------|-----------------|-------------------|
| R1              | e0/0               | 10.0.0.1     | 255.255.255.252 | N/A               |
|                 | e0/1               | N/A          | N/A             | N/A               |
|                 | e0/1.100           | 192.168.1.1  | 255.255.255.192 | N/A               |
|                 | e0/1.200           | 192.168.1.65 | 255.255.255.224 | N/A               |
|                 | e0/1.1000          | N/A          | N/A             | N/A               |
| R2              | e0/0               | 10.0.0.2     | 255.255.255.252 | N/A               |
|                 | e0/1               | 192.168.1.97 | 255.255.255.240 | N/A               |
| S1              | VLAN 200           | 192.168.1.66 | 255.255.255.224 | 192.168.1.65      |
| S2              | VLAN 1             |              |                 |                   |
| PC-A            | NIC                | DHCP         | DHCP            | DHCP              |
| PC-B            | NIC                | DHCP         | DHCP            | DHCP              |

### Настройка схемы

В ходе лабораторной работы в eve ng был настроен стенд:

![](Lab3.1_eve.ng.PNG)

### Базовая настройка маршрутизаторов

В ходе базовой настройки маршрутизаторов: настроить его имя, отключить DNS lookup, настроить пароль на вход в привелигерованный режим, настроить аутентификацию и пароль на вход через консоль, настроить аутентификацию и пароль при удаленном подключении, настроить хранение паролей в зашифрованном виде, создать баннер, установить время на маршрутизаторе и сохраненить действующую конфигурацию в стартовую.

После базовой настройки маршрутизаторов в running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R1
```
service password-encryption
!
hostname R1
!
enable password 7 1511070D1739
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 13061E010803
 logging synchronous
 login
line vty 0
 password 7 13061E010803
 login
 transport input none
```

#### Маршрутизатор R2
```
service password-encryption
!
hostname R2
!
enable password 7 00071F071748
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 094F471A1A0A
 logging synchronous
 login
line vty 0
 password 7 0822455D0A16
 login
 transport input none
```

### Настройка портов маршрутизаторов

Необходимо создать подинтерфейсы в соответствии с таблицей адресов, назначить ip адрес и описание интерфейсу, включить подключенные интерфейсы, а также назначить в качестве маршрута по умолчанию адрес соседнего маршрутизатора.

В выводе команды show ip interface brief маршрутизаторов появится:

#### Маршрутизатор R1
```
R1# show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.1        YES manual up                    up      
Ethernet0/1                unassigned      YES NVRAM  up                    up      
Ethernet0/1.100            192.168.1.1     YES manual up                    up      
Ethernet0/1.200            192.168.1.65    YES manual up                    up      
Ethernet0/1.1000           unassigned      YES unset  up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down 
```

#### Маршрутизатор R2
```
R2#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.2        YES manual up                    up      
Ethernet0/1                192.168.1.97    YES manual up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down 
```

В выводе команды show ip interface brief маршрутизаторов появится:

#### Маршрутизатор R1
```
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.2 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.2
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.1/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 4 subnets, 3 masks
C        192.168.1.0/26 is directly connected, Ethernet0/1.100
L        192.168.1.1/32 is directly connected, Ethernet0/1.100
C        192.168.1.64/27 is directly connected, Ethernet0/1.200
L        192.168.1.65/32 is directly connected, Ethernet0/1.200
```

#### Маршрутизатор R2
```
R2#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

S*    0.0.0.0/0 [1/0] via 10.0.0.1
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/0
L        10.0.0.2/32 is directly connected, Ethernet0/0
      192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.1.96/28 is directly connected, Ethernet0/1
L        192.168.1.97/32 is directly connected, Ethernet0/1
```

Чтобы убедиться, что статический маршрут работает произведем ping с маршрутизатора R1 на маршрутизатор R2:
```
R1#ping 10.0.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

### Базовая настройка коммутаторов


В качестве базовой настройки коммутаторов необходимо произвести аналогичные настройки как при базовой настройке коммутаторов. 

После базовой настройки коммутатов в running-config коммутаторов появятся настройки:

#### Коммутатор S1
```
service password-encryption
!
hostname S1
!
enable password 7 0307570A151C
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 045802150C2E
 logging synchronous
 login
line vty 0
 password 7 13061E010803
 login
```

#### Коммутатор S2
```
service password-encryption
!
hostname S1
!
enable password 7 0205085A1815
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 01100F175804
 logging synchronous
 login
line vty 0
 password 7 14141B180F0B
 login
```

### Настройка VLAN и интерфейсов коммутатора S1 

Необходимо создать и задать имя VLAN в соответствии с таблицей VLAN и ip адресов, назначить ip адрес на vlan управления, назначить шлюз по умолчанию, все неиспользуемые порты перевести в ражим access, назначить в парковочный VLAN и отключить. Необходимо назначить VLAN на интерфейсы в соответствии с таблицей vlan. Интерфейс e0/1 необходимо перевести в режим trunk, назначить 1000 VLAN в качестве vlan по умолчанию и назначить необходимые vlan для передачи. Необходимо назначить на коммутатор ip адрес шлюза по умолчанию. 

После настройки VLAN коммутатора S1 в running-config коммутатора появятся настройки VLAN:

```
interface Ethernet0/0
 switchport access vlan 100
 switchport mode access
!
interface Ethernet0/1
 switchport trunk allowed vlan 100,200,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
!
interface Ethernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
 !
 interface Ethernet0/3
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface Vlan100
 description Clients
 no ip address
!
interface Vlan200
 description Management
 ip address 192.168.1.66 255.255.255.224
!
interface Vlan999
 description Parking_Lot
 no ip address
 shutdown
 !
 ip route 0.0.0.0 0.0.0.0 192.168.1.65
```

В выводе команды show ip interface brief коммутатора появится:

```
S1#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  up                    up      
Ethernet0/1            unassigned      YES unset  up                    up      
Ethernet0/2            unassigned      YES unset  administratively down down    
Ethernet0/3            unassigned      YES unset  administratively down down    
Vlan100                unassigned      YES unset  up                    up      
Vlan200                192.168.1.66    YES manual up                    up      
Vlan999                unassigned      YES unset  administratively down down  
```

В выводе команды show interfaces trunk коммутатора появится:

```
S1#show interfaces trunk 

Port        Mode             Encapsulation  Status        Native vlan
Et0/1       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/1       100,200,1000

Port        Vlans allowed and active in management domain
Et0/1       100,200

Port        Vlans in spanning tree forwarding state and not pruned
Et0/1       100,200
```

Чтобы убедиться, что статический маршрут работает произведем ping с коммутатора S1 на маршрутизатор R1:
```
S1#ping 192.168.1.65
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.65, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

### Настройка VLAN и интерфейс коммутатора S2 

На коммутаторе S1 необходимо создать VLAN 1, назначить ему второй ip адрес диапазона адресов маршрутизатора R2 и настроить на коммутаторе ip адрес шлюза по умолчанию. 

После настройки VLAN коммутатора S2 в running-config коммутатора появятся настройки:

```
interface Ethernet0/0
!
interface Ethernet0/1
!
interface Ethernet0/2
!
interface Ethernet0/3
!
interface Vlan1
 ip address 192.168.1.98 255.255.255.240
 shutdown
!         
ip route 0.0.0.0 0.0.0.0 192.168.1.97
```

## Часть 2. Выполнение

### Настройка DHCPv4 сервер на маршрутизаторе R1

Необходимо настроить 2 пула адресов, настроенных на маршрутизаторе, при этом исключить из пула выдаваемых адресов первые 5 адресов диапазона. Для 2 пулов адресов необходимо назначить в качестве доменного имени ccna-lab.com, указать в качестве маршрута по умолчанию ip адрес маршрутизатора R1, назначить в качестве срока DHCP-аренды 2 дня 12 часов 30 минут.

В выводе running-config маршрутизатора R1 появятся настройки:

```
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97 192.168.1.101
!
ip dhcp pool R1_Client_LAN
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1 
 domain-name ccna-lab.com
 lease 2 12 30
!
ip dhcp pool R2_Client_LAN
 network 192.168.1.96 255.255.255.240
 default-router 192.168.1.97 
 domain-name ccna-lab.com
 lease 2 12 30
```

В выводе команды show ip dhcp pool маршрутизатора R1 появятся:

```
R1#show ip dhcp pool

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.1          192.168.1.1      - 192.168.1.62      0

Pool R1_Management_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 30
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.65         192.168.1.65     - 192.168.1.94      0
```

В выводе команды show ip dhcp bindings маршрутизатора R1:

```
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/     Lease expiration        Type
    Hardware address/
    User name
```


В выводе команды show ip dhcp server statistics маршрутизатора R1:

```
R1#show ip dhcp server statistics 
Memory usage         25179
Address pools        2
Database agents      0
Automatic bindings   0
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
```

### Преобретение ip адреса с помощью DHCP для PC-A 

Необходимо назначить на PC-A ip адрес с помощью DHCP. 

В выводе команды dhcp -r АРМа PC-A представлено:

```
VPCS> dhcp -r
DDORA IP 192.168.1.6/26 GW 192.168.1.1
```

В выводе команды dhcp -d АРМа PC-A представлено:

```
VPCS> dhcp -d
Opcode: 1 (REQUEST)
Client IP Address: 0.0.0.0
Your IP Address: 0.0.0.0
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:02
Option 53: Message Type = Discover
Option 12: Host Name = VPCS1
Option 61: Client Identifier = Hardware Type=Ethernet MAC Address = 00:50:79:66:68:02

Opcode: 2 (REPLY)
Client IP Address: 0.0.0.0
Your IP Address: 192.168.1.6
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:02
Option 53: Message Type = Offer
Option 54: DHCP Server = 192.168.1.1
Option 51: Lease Time = 217786
Option 58: Renewal Time = 108893
Option 59: Rebinding Time = 190561
Option 1: Subnet Mask = 255.255.255.192
Option 3: Router = 192.168.1.1
Option 15: Domain = ccna-lab.com

Opcode: 1 (REQUEST)
Client IP Address: 192.168.1.6
Your IP Address: 0.0.0.0
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:02
Option 53: Message Type = Request
Option 54: DHCP Server = 192.168.1.1
Option 50: Requested IP Address = 192.168.1.6
Option 61: Client Identifier = Hardware Type=Ethernet MAC Address = 00:50:79:66:68:02
Option 12: Host Name = VPCS1

Opcode: 2 (REPLY)
Client IP Address: 192.168.1.6
Your IP Address: 192.168.1.6
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:02
Option 53: Message Type = Ack
Option 54: DHCP Server = 192.168.1.1
Option 51: Lease Time = 217800
Option 58: Renewal Time = 108900
Option 59: Rebinding Time = 190575
Option 1: Subnet Mask = 255.255.255.192
Option 3: Router = 192.168.1.1
Option 15: Domain = ccna-lab.com

 IP 192.168.1.6/26 GW 192.168.1.1
```

Чтобы убедиться, что все работает корректно создадим запрос ping с АРМ PC-A на маршрутизатор R1:
```
VPCS> ping 192.168.1.1 

84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=0.685 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=0.581 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=0.594 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=0.603 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=0.559 ms

VPCS> ping 192.168.1.65

84 bytes from 192.168.1.65 icmp_seq=1 ttl=255 time=0.462 ms
84 bytes from 192.168.1.65 icmp_seq=2 ttl=255 time=0.561 ms
84 bytes from 192.168.1.65 icmp_seq=3 ttl=255 time=0.632 ms
84 bytes from 192.168.1.65 icmp_seq=4 ttl=255 time=0.623 ms
84 bytes from 192.168.1.65 icmp_seq=5 ttl=255 time=0.633 ms
```

## Часть 3. Выполнение

### Настройка ретрансляции DHCPv4 на маршрутизаторе R2  

Необходимо настроить ретрансляцию DHCPv4 на маршрутизаторе R2 на интерфейс e0/1.

### Преобретение ip адреса с помощью DHCP для PC-B 

Необходимо назначить на PC-B ip адрес с помощью DHCP. 

В выводе команды dhcp -r АРМа PC-B представлено:

```
VPCS> dhcp -r   
DORA IP 192.168.1.102/28 GW 192.168.1.97
```

В выводе команды dhcp -d АРМа PC-B представлено:

```
VPCS> dhcp -d
Opcode: 1 (REQUEST)
Client IP Address: 0.0.0.0
Your IP Address: 0.0.0.0
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:06
Option 53: Message Type = Discover
Option 12: Host Name = VPCS1
Option 61: Client Identifier = Hardware Type=Ethernet MAC Address = 00:50:79:66:68:06

Opcode: 2 (REPLY)
Client IP Address: 0.0.0.0
Your IP Address: 192.168.1.102
Server IP Address: 0.0.0.0
Gateway IP Address: 192.168.1.97
Client MAC Address: 00:50:79:66:68:06
Option 53: Message Type = Offer
Option 54: DHCP Server = 10.0.0.1
Option 51: Lease Time = 217775
Option 58: Renewal Time = 108887
Option 59: Rebinding Time = 190547
Option 1: Subnet Mask = 255.255.255.240
Option 3: Router = 192.168.1.97
Option 15: Domain = ccna-lab.com

Opcode: 1 (REQUEST)
Client IP Address: 192.168.1.102
Your IP Address: 0.0.0.0
Server IP Address: 0.0.0.0
Gateway IP Address: 0.0.0.0
Client MAC Address: 00:50:79:66:68:06
Option 53: Message Type = Request
Option 54: DHCP Server = 10.0.0.1
Option 50: Requested IP Address = 192.168.1.102
Option 61: Client Identifier = Hardware Type=Ethernet MAC Address = 00:50:79:66:68:06
Option 12: Host Name = VPCS1

Opcode: 2 (REPLY)
Client IP Address: 192.168.1.102
Your IP Address: 192.168.1.102
Server IP Address: 0.0.0.0
Gateway IP Address: 192.168.1.97
Client MAC Address: 00:50:79:66:68:06
Option 53: Message Type = Ack
Option 54: DHCP Server = 10.0.0.1
Option 51: Lease Time = 217800
Option 58: Renewal Time = 108900
Option 59: Rebinding Time = 190575
Option 1: Subnet Mask = 255.255.255.240
Option 3: Router = 192.168.1.97
Option 15: Domain = ccna-lab.com

 IP 192.168.1.102/28 GW 192.168.1.97
```

Чтобы убедиться, что все работает корректно создадим запрос ping с АРМ PC-B на маршрутизатор R1:

```
VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=254 time=0.512 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=254 time=0.739 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=254 time=0.653 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=254 time=0.682 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=254 time=0.761 ms

VPCS> ping 192.168.1.65

84 bytes from 192.168.1.65 icmp_seq=1 ttl=254 time=0.827 ms
84 bytes from 192.168.1.65 icmp_seq=2 ttl=254 time=0.734 ms
84 bytes from 192.168.1.65 icmp_seq=3 ttl=254 time=0.696 ms
84 bytes from 192.168.1.65 icmp_seq=4 ttl=254 time=0.696 ms
84 bytes from 192.168.1.65 icmp_seq=5 ttl=254 time=0.730 ms
```

В выводе команды show ip dhcp binding маршрутизатора R1 представлено:

```
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/     Lease expiration        Type
    Hardware address/
    User name
192.168.1.6         0100.5079.6668.02       Apr 13 2022 05:32 PM    Automatic
192.168.1.102       0100.5079.6668.06       Apr 13 2022 06:05 PM    Automatic
```

В выводе команды show ip dhcp server statistics маршрутизатора R1 представлено:

```
R1#show ip dhcp server statistics 
Memory usage         43402
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         8
DHCPREQUEST          6
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            6
DHCPACK              6
DHCPNAK              0
```


