# Лабораторная работа №8 EIGRP

#### Цель: 

Настроить EIGRP в Санкт-Петербурге используя именованный EIGRP.

При этом необходимо учесть:
- R32 получает только маршрут по умолчанию;
- R16-R17 анонсируют только суммарные префиксы.

## Топология

Выполнение лабораторной работы будет происходить в созданной при выполнении лабораторной работы №4 схеме. Топология сети Санкт-Петербург приведена ниже:

![](SPB_topology.PNG)

## Ход работы

Лабораторная работа разбита на 2 части:
1) Настройка EIGRP на маршрутизаторах и L3 коммутаторах
2) Настроить на R17 и R16 автосуммаризацию
3) Создание для R16 маршрута по умолчанию через R18 и передача его на R32
4) Создать ACL и настроить его для передачи на R32 только маршруты по умолчанию

## Часть 1. Настройка EIGRP на маршрутизаторах и L3 коммутаторах

Необходимо включить на АСО именованный eigrp, назначить идентификатор узла для ipv4 и ipv6, запустить ipv4 eigrp, указать для ipv4 анонсируемые адреса и включить ipv6 eigrp.
Не участвующие в eigrp интерфейсы переведены в состояние passive.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R18:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.18.0
  eigrp router-id 18.18.18.18
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 18.18.18.18
 exit-address-family
!
```

#### Маршрутизатор R17:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !       
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.18.0
  eigrp router-id 17.17.17.17
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 17.17.17.17
 exit-address-family
!
```

#### Маршрутизатор R16:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !
  topology base
  exit-af-topology
  network 192.168.18.0
  eigrp router-id 16.16.16.16
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  topology base
  exit-af-topology
  eigrp router-id 16.16.16.16
 exit-address-family
!
```

#### Маршрутизатор R32:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.18.0
  eigrp router-id 32.32.32.32
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 32.32.32.32
 exit-address-family
!
```

#### L3 коммутатор SW10:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 80.0.0.0
  network 192.168.18.0
  eigrp router-id 10.10.10.10
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 10.10.10.10
 exit-address-family
!
```

#### L3 коммутатор SW9:

```
router eigrp LAB
 !
 address-family ipv4 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 192.168.18.0
  eigrp router-id 9.9.9.9
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 1
  !
  af-interface Ethernet0/1
   passive-interface
  exit-af-interface
  !       
  af-interface Ethernet1/1
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/2
   passive-interface
  exit-af-interface
  !
  af-interface Ethernet1/3
   passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
 exit-address-family
!
```

