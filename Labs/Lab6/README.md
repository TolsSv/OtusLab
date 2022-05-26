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









