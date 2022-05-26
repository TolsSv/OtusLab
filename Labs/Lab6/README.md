# Лабораторная работа №6 OSPF

#### Цель: 

Настроить OSPF офисе Москва. Разделить сеть на зоны. Настроить фильтрацию между зонами.

## Топология

Выполнение лабораторной работы будет происходить в созданной при выполнении лабораторной работы №4 схеме. При этом необходимо учесть:
- маршрутизаторы R14-R15 находятся в зоне 0 - backbone;
- маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию;
- маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию;
- маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101;
- настройка для IPv6 повторяет логику IPv4.

В соответствии с приведенными выше требованиями схема ospf маршрутизации на площадке Москва будет выглядеть как на схеме ниже:

![](MSK_topology.PNG)

## Ход работы

Лабораторная работа разбита на 5 частей:
1) Настройка ospf на маршрутизаторах и коммутаторах L3
2) Настройка распространения маршрута по умолчанию
3) Фильтрация для маршрутизаторов для зоны 10
4) Фильтрация для маршрутизаторов для зоны 101
5) Фильтрация для маршрутизаторов для зоны 102

## Часть 1. Настройка ospf на маршрутизаторах и коммутаторах L3

Необходимо глобально включить на маршрутизаторах и L3 комутаторах ospf, назначить router-id, перевести не участвующие в ospf интерфейсы в режим passive, включить на интерфейсах ospf ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```
!
interface Ethernet0/0
 ip address 192.168.10.9 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::14 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:21/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/1
 ip address 192.168.10.17 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::14 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:41/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/2
 ip address 89.110.29.193 255.255.255.252
 ipv6 address FE80::14 link-local
 ipv6 address 2A02:6B8:89:AC61:AC::1/124
!
interface Ethernet0/3
 ip address 192.168.10.1 255.255.255.252
 ip ospf 1 area 101
 ipv6 address FE80::14 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:1/124
 ipv6 ospf 1 area 101
!
router ospf 1
 router-id 14.14.14.14
 passive-interface Ethernet0/2
!
ipv6 router ospf 1
!
```

#### Маршрутизатор R15:

```
!
interface Ethernet0/0
 ip address 192.168.10.13 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::15 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:31/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/1
 ip address 192.168.10.21 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::15 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:51/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/2
 ip address 89.110.29.197 255.255.255.252
 ipv6 address FE80::15 link-local
 ipv6 address 2A02:6B8:89:AC61:AC::11/124
!
interface Ethernet0/3
 ip address 192.168.10.5 255.255.255.252
 ip ospf 1 area 102
 ipv6 address FE80::15 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:11/124
 ipv6 ospf 1 area 102
!
router ospf 1
 router-id 15.15.15.15
 passive-interface Ethernet0/2
!
ipv6 router ospf 1
!
```

#### Маршрутизатор R12:

```
!
 ip address 192.168.10.25 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::12 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:61/124
 ipv6 ospf 1 area 10
!
interface Ethernet0/1
 ip address 192.168.10.29 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::12 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:71/124
 ipv6 ospf 1 area 10
!
interface Ethernet0/2
 ip address 192.168.10.10 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::12 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:22/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/3
 ip address 192.168.10.22 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::12 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:52/124
 ipv6 ospf 1 area 0
!
router ospf 1
 router-id 12.12.12.12
!
ipv6 router ospf 1
!
```

#### Маршрутизатор R13:

```
! 
interface Ethernet0/0
 ip address 192.168.10.37 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::13 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:91/124
 ipv6 ospf 1 area 10
!
interface Ethernet0/1
 ip address 192.168.10.33 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::13 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:81/124
 ipv6 ospf 1 area 10
!
interface Ethernet0/2
 ip address 192.168.10.14 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::13 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:32/124
 ipv6 ospf 1 area 0
!
interface Ethernet0/3
 ip address 192.168.10.17 255.255.255.252
 ip ospf 1 area 0
 ipv6 address FE80::13 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:42/124
 ipv6 ospf 1 area 0
!
router ospf 1
 router-id 13.13.13.13
!         
ipv6 router ospf 1
! 
```

#### Маршрутизатор R19:

```
!
interface Ethernet0/0
 ip address 192.168.10.2 255.255.255.252
 ip ospf 1 area 101
 ipv6 address FE80::19 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:2/124
 ipv6 ospf 1 area 101
!
router ospf 1
 router-id 19.19.19.19
 passive-interface Ethernet0/1
 passive-interface Ethernet0/2
 passive-interface Ethernet0/3
!
ipv6 router ospf 1
!
```

#### Маршрутизатор R20:

```
!
interface Ethernet0/0
 ip address 192.168.10.6 255.255.255.252
 ip ospf 1 area 102
 ipv6 address FE80::20 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:12/124
 ipv6 ospf 1 area 102
!
router ospf 1
 router-id 20.20.20.20
 passive-interface Ethernet0/1
 passive-interface Ethernet0/2
 passive-interface Ethernet0/3
!
ipv6 router ospf 1
!
```

#### L3 коммутатор SW4:

```
!         
interface Ethernet0/2
 ip address 192.168.10.41 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::4 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:101/124
 ipv6 ospf 1 area 10
!
interface Ethernet1/0
 ip address 192.168.10.26 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::4 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:62/124
 ipv6 ospf 1 area 10
!
interface Ethernet1/1
 ip address 192.168.10.34 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::4 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:82/124
 ipv6 ospf 1 area 10
!
router ospf 1
 router-id 4.4.4.4
 passive-interface Ethernet0/0
 passive-interface Ethernet0/1
 passive-interface Ethernet0/3
 passive-interface Ethernet1/2
 passive-interface Ethernet1/3
!
ipv6 router ospf 1
!
```

#### L3 коммутатор SW5:

```
!
interface Ethernet0/2
 ip address 192.168.10.42 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::5 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:102/124
 ipv6 ospf 1 area 10
!
interface Ethernet1/0
 ip address 192.168.10.38 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::5 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:92/124
 ipv6 ospf 1 area 10
!
interface Ethernet1/1
 ip address 192.168.10.30 255.255.255.252
 ip ospf 1 area 10
 ipv6 address FE80::5 link-local
 ipv6 address FDE8:8A:FC:1:10:A3:0:72/124
 ipv6 ospf 1 area 10
!
router ospf 1
 router-id 5.5.5.5
 passive-interface Ethernet0/0
 passive-interface Ethernet0/1
 passive-interface Ethernet0/3
 passive-interface Ethernet1/2
 passive-interface Ethernet1/3
!
ipv6 router ospf 1
!
```

Чтобы проверить, что АСО видят соседей, получают маршруты и их взаимодействие настроено корректно необходимо ввести show ip ospf neighbors, show ip route ospf, show ipv6 route ospf, show ip protocols:

#### Маршрутизатор R14:

```
 R14#show ip ospf neighbor 

Neighbor ID     Pri   State           Dead Time   Address         Interface
12.12.12.12       1   FULL/BDR        00:00:35    192.168.10.10   Ethernet0/0
19.19.19.19       1   FULL/BDR        00:00:31    192.168.10.2    Ethernet0/3
R14#show ip route ospf    
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is not set

      192.168.10.0/24 is variably subnetted, 14 subnets, 2 masks
O IA     192.168.10.4/30 [110/30] via 192.168.10.10, 00:48:44, Ethernet0/0
O        192.168.10.12/30 [110/30] via 192.168.10.10, 00:48:44, Ethernet0/0
O        192.168.10.20/30 [110/20] via 192.168.10.10, 00:48:44, Ethernet0/0
O IA     192.168.10.24/30 [110/20] via 192.168.10.10, 00:49:36, Ethernet0/0
O IA     192.168.10.28/30 [110/20] via 192.168.10.10, 00:49:12, Ethernet0/0
O IA     192.168.10.32/30 [110/30] via 192.168.10.10, 00:28:59, Ethernet0/0
O IA     192.168.10.36/30 [110/30] via 192.168.10.10, 00:25:29, Ethernet0/0
O IA     192.168.10.40/30 [110/30] via 192.168.10.10, 00:28:49, Ethernet0/0
R14#show ipv6 route ospf
IPv6 Routing Table - default - 17 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
OI  FDE8:8A:FC:1:10:A3:0:10/124 [110/30]
     via FE80::12, Ethernet0/0
     via FE80::13, Ethernet0/1
O   FDE8:8A:FC:1:10:A3:0:30/124 [110/20]
     via FE80::13, Ethernet0/1
O   FDE8:8A:FC:1:10:A3:0:50/124 [110/20]
     via FE80::12, Ethernet0/0
OI  FDE8:8A:FC:1:10:A3:0:60/124 [110/20]
     via FE80::12, Ethernet0/0
OI  FDE8:8A:FC:1:10:A3:0:70/124 [110/20]
     via FE80::12, Ethernet0/0
OI  FDE8:8A:FC:1:10:A3:0:80/124 [110/20]
     via FE80::13, Ethernet0/1
OI  FDE8:8A:FC:1:10:A3:0:90/124 [110/20]
     via FE80::13, Ethernet0/1
OI  FDE8:8A:FC:1:10:A3:0:100/124 [110/30]
     via FE80::13, Ethernet0/1
     via FE80::12, Ethernet0/0
R14#show ip protocols   
*** IP Routing is NSF aware ***

Routing Protocol is "application"
  Sending updates every 0 seconds
  Invalid after 0 seconds, hold down 0, flushed after 0
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Maximum path: 32
  Routing for Networks:
  Routing Information Sources:
    Gateway         Distance      Last Update
  Distance: (default is 4)

Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 14.14.14.14
  It is an area border router
  Number of areas in this router is 2. 2 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
  Routing on Interfaces Configured Explicitly (Area 0):
    Ethernet0/1
    Ethernet0/0
  Routing on Interfaces Configured Explicitly (Area 101):
    Ethernet0/3
  Passive Interface(s):
    Ethernet0/2
  Routing Information Sources:
    Gateway         Distance      Last Update
    13.13.13.13          110      00:31:53
    15.15.15.15          110      00:49:15
    12.12.12.12          110      00:26:00
  Distance: (default is 110)
```

#### Маршрутизатор R15:

```

```

#### Маршрутизатор R12:

```

```

#### Маршрутизатор R13:

```

```

#### Маршрутизатор R19:

```

```

#### Маршрутизатор R20:

```

```

#### L3 коммутатор SW4:

```

```

#### L3 коммутатор SW5:

```

```





