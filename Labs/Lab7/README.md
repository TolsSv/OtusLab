# Лабораторная работа №7 IS-IS

#### Цель: 

Настроить IS-IS в офисе Триада.

## Топология

Выполнение лабораторной работы будет происходить в созданной при выполнении лабораторной работы №4 схеме. При этом необходимо учесть:
- R23 и R25 находятся в зоне 2222;
- R24 находятся в зоне 24;
- R26 находятся в зоне 26.

В соответствии с приведенными выше требованиями схема is-is маршрутизации на площадке Триада будет выглядеть как на схеме ниже:

![](/triada_topology.PNG)

## Ход работы

Лабораторная работа разбита на 2 части:
1) Настроить is-is на маршрутизаторах
2) Проверить корректность настройки is-is

## Часть 1. Настройка is-is на маршрутизаторах

Необходимо глобально включить на маршрутизаторах isis, назначить iso адрес, запустить ipv4 isis, ipv6 isis и назначить необходимый уровень взаимодействия на интерфейсах в соответствии с топологией.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R23:

```
!
interface Ethernet0/1
 ip address 192.168.52.2 255.255.255.252
 ip router isis 
 ipv6 address FE80::23 link-local
 ipv6 address FDE8:8A:FC:1:52::2/124
 ipv6 router isis 
 isis circuit-type level-1
!
interface Ethernet0/2
 ip address 192.168.52.14 255.255.255.252
 ip router isis 
 ipv6 address FE80::23 link-local
 ipv6 address FDE8:8A:FC:1:52::32/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
router isis
 net 49.2222.0001.0001.0001.00
```

#### Маршрутизатор R25:

```
!
interface Ethernet0/0
 ip address 192.168.52.1 255.255.255.252
 ip router isis 
 ipv6 address FE80::25 link-local
 ipv6 address FDE8:8A:FC:1:52::1/124
 ipv6 router isis 
 isis circuit-type level-1
!
interface Ethernet0/2
 ip address 192.168.52.5 255.255.255.252
 ip router isis 
 ipv6 address FE80::25 link-local
 ipv6 address FDE8:8A:FC:1:52::15/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
router isis
 net 49.2222.0002.0002.0002.00
```

#### Маршрутизатор R24:

```
!
interface Ethernet0/1
 ip address 192.168.52.10 255.255.255.252
 ip router isis 
 ipv6 address FE80::24 link-local
 ipv6 address FDE8:8A:FC:1:52::21/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
interface Ethernet0/2
 ip address 192.168.52.13 255.255.255.252
 ip router isis 
 ipv6 address FE80::24 link-local
 ipv6 address FDE8:8A:FC:1:52::31/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
router isis
 net 49.0024.0000.0000.0003.00
```

#### Маршрутизатор R26:

```
!
interface Ethernet0/0
 ip address 192.168.52.9 255.255.255.252
 ip router isis 
 ipv6 address FE80::26 link-local
 ipv6 address FDE8:8A:FC:1:52::29/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
interface Ethernet0/2
 ip address 192.168.52.6 255.255.255.252
 ip router isis 
 ipv6 address FE80::26 link-local
 ipv6 address FDE8:8A:FC:1:52::16/124
 ipv6 router isis 
 isis circuit-type level-2-only
!
router isis
 net 49.0026.0000.0000.0004.00
```

## Часть 2. Проверка корректности настройки is-is

Чтобы проверить, что маршрутизаторы видят соседей, получают маршруты и их взаимодействие настроено корректно необходимо ввести show isis neighbors, show ip route isis, show ipv6 route isis, show isis database:

#### Маршрутизатор R23:

```
R23#show isis neighbors 

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/2       192.168.52.13   UP    8        R24.02             
R25            L1   Et0/1       192.168.52.1    UP    8        R25.01   
R23#show ip route isis
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

      192.168.52.0/24 is variably subnetted, 6 subnets, 2 masks
i L2     192.168.52.4/30 [115/30] via 192.168.52.13, 00:48:24, Ethernet0/2
i L2     192.168.52.8/30 [115/20] via 192.168.52.13, 02:48:49, Ethernet0/2
R23#show ipv6 route isis 
IPv6 Routing Table - default - 9 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
I2  FDE8:8A:FC:1:52::10/124 [115/30]
     via FE80::24, Ethernet0/2
I2  FDE8:8A:FC:1:52::20/124 [115/20]
     via FE80::24, Ethernet0/2
R23#show isis database 

IS-IS Level-1 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R23.00-00           * 0x00000012   0x3A67        633               1/0/0
R25.00-00             0x00000016   0x9902        1114              1/0/0
R25.01-00             0x0000000E   0x90A9        1159              0/0/0
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R24.00-00             0x00000013   0xFB03        381               0/0/0
R24.02-00             0x0000000E   0x1FA8        660               0/0/0
R26.00-00             0x00000014   0x9B99        1049              0/0/0
R26.01-00             0x0000000D   0x3493        628               0/0/0
R26.02-00             0x0000000F   0x6859        523               0/0/0
R23.00-00           * 0x00000015   0x2E04        544               0/0/0
R25.00-00             0x00000016   0xBEB7        645               0/0/0
```

#### Маршрутизатор R25:

```
R25#show isis neighbors 

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R26            L2   Et0/2       192.168.52.6    UP    8        R26.02             
R23            L1   Et0/0       192.168.52.2    UP    23       R25.01  
R25#show ip route isis
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

      192.168.52.0/24 is variably subnetted, 6 subnets, 2 masks
i L2     192.168.52.8/30 [115/20] via 192.168.52.6, 00:51:20, Ethernet0/2
i L2     192.168.52.12/30 [115/30] via 192.168.52.6, 00:48:51, Ethernet0/2
R25#show ipv6 route isis
IPv6 Routing Table - default - 11 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
I2  FDE8:8A:FC:1:52::20/124 [115/20]
     via FE80::26, Ethernet0/2
I2  FDE8:8A:FC:1:52::30/124 [115/30]
     via FE80::26, Ethernet0/2
R25#show isis database 

IS-IS Level-1 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R23.00-00             0x00000012   0x3A67        471               1/0/0
R25.00-00           * 0x00000016   0x9902        956               1/0/0
R25.01-00           * 0x0000000E   0x90A9        1000              0/0/0
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R24.00-00             0x00000014   0xF904        1068              0/0/0
R24.02-00             0x0000000E   0x1FA8        498               0/0/0
R26.00-00             0x00000014   0x9B99        891               0/0/0
R26.01-00             0x0000000D   0x3493        470               0/0/0
R26.02-00             0x00000010   0x665A        1140              0/0/0
R23.00-00             0x00000016   0x2C05        1156              0/0/0
R25.00-00           * 0x00000016   0xBEB7        491               0/0/0
```

#### Маршрутизатор R24:

```
R24#show isis neighbors 

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R26            L2   Et0/1       192.168.52.9    UP    9        R26.01             
R23            L2   Et0/2       192.168.52.14   UP    24       R24.02     
R24#show ip route isis
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

      192.168.52.0/24 is variably subnetted, 6 subnets, 2 masks
i L2     192.168.52.0/30 [115/20] via 192.168.52.14, 02:53:44, Ethernet0/2
i L2     192.168.52.4/30 [115/20] via 192.168.52.9, 02:53:10, Ethernet0/1
R24#show ipv6 route isis
IPv6 Routing Table - default - 11 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
I2  FDE8:8A:FC:1:52::/124 [115/20]
     via FE80::23, Ethernet0/2
I2  FDE8:8A:FC:1:52::10/124 [115/20]
     via FE80::26, Ethernet0/1
R24#show isis database 

IS-IS Level-1 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R24.00-00           * 0x00000010   0xA72D        995               1/0/0
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R24.00-00           * 0x00000014   0xF904        963               0/0/0
R24.02-00           * 0x0000000E   0x1FA8        394               0/0/0
R26.00-00             0x00000014   0x9B99        783               0/0/0
R26.01-00             0x0000000E   0x3294        1151              0/0/0
R26.02-00             0x00000010   0x665A        1032              0/0/0
R23.00-00             0x00000016   0x2C05        1052              0/0/0
R25.00-00             0x00000016   0xBEB7        379               0/0/0
```

#### Маршрутизатор R26:

```
R26#show isis neighbors 

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/0       192.168.52.10   UP    27       R26.01             
R25            L2   Et0/2       192.168.52.5    UP    24       R26.02 
R26#show ip route isis
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

      192.168.52.0/24 is variably subnetted, 6 subnets, 2 masks
i L2     192.168.52.0/30 [115/20] via 192.168.52.5, 00:55:01, Ethernet0/2
i L2     192.168.52.12/30 [115/20] via 192.168.52.10, 02:54:21, Ethernet0/0
R26#show ipv6 route isis
IPv6 Routing Table - default - 11 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
I2  FDE8:8A:FC:1:52::/124 [115/20]
     via FE80::25, Ethernet0/2
I2  FDE8:8A:FC:1:52::30/124 [115/20]
     via FE80::24, Ethernet0/0
R26#sh isis database 

IS-IS Level-1 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R26.00-00           * 0x00000011   0xC905        1040              1/0/0
IS-IS Level-2 Link State Database:
LSPID                 LSP Seq Num  LSP Checksum  LSP Holdtime      ATT/P/OL
R24.00-00             0x00000014   0xF904        897               0/0/0
R24.02-00             0x0000000F   0x1DA9        1180              0/0/0
R26.00-00           * 0x00000014   0x9B99        721               0/0/0
R26.01-00           * 0x0000000E   0x3294        1089              0/0/0
R26.02-00           * 0x00000010   0x665A        970               0/0/0
R23.00-00             0x00000016   0x2C05        986               0/0/0
R25.00-00             0x00000017   0xBCB8        1155              0/0/0     
```


