# Лабораторная работа №6 BGP.Основы

#### Цель: 

Настроить BGP между автономными системами. Организовать доступность между офисами Москва и С.-Петербург.

## Топология

Выполнение лабораторной работы будет происходить в созданной при выполнении лабораторной работы №4:

![](MSK_topology.PNG)

## Ход работы

Лабораторная работа разбита на 5 частей:
1) Настройка eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас
2) Настройка eBGP между провайдерами Киторн и Ламас
3) Настройка eBGP между Ламас/Киторн и Триада
4) Настройка eBGP между офисом С.-Петербург и провайдером Триада
5) Организация IP доступности между пограничными роутерами офисами Москва и С.-Петербург

## Часть 1. Настройка eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас

Необходимо включить на маршрутизаторах eBGP, назначить router-id и настроить между ними соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```
!
router bgp 1001
 bgp router-id 14.14.14.14
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::2 remote-as 101
 neighbor 89.110.29.194 remote-as 101
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::2 activate
  neighbor 89.110.29.194 activate
 exit-address-family
!
```

#### Маршрутизатор R22:

```
router bgp 101
 bgp router-id 22.22.22.22
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::1 remote-as 1001
 neighbor 89.110.29.193 remote-as 1001
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::1 activate
  neighbor 89.110.29.193 activate
 exit-address-family
 !
```

#### Маршрутизатор R15:

```
!
router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::12 remote-as 301
 neighbor 89.110.29.198 remote-as 301
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::12 activate
  neighbor 89.110.29.198 activate
 exit-address-family
 !
```

#### Маршрутизатор R21:

```
!
router bgp 301
 bgp router-id 21.21.21.21
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::11 remote-as 1001
 neighbor 89.110.29.197 remote-as 1001
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::11 activate
  neighbor 89.110.29.197 activate
 exit-address-family
 !
```

Проверим соседство командой show ip bgp summary.

#### Маршрутизатор R14:

```
R14#show ip bgp summary
BGP router identifier 14.14.14.14, local AS number 1001
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::2
                4          101      41      42        1    0    0 00:35:57        0
89.110.29.194   4          101      42      42        1    0    0 00:36:01        0           
```

#### Маршрутизатор R22:

```
R22#show ip bgp summary   
BGP router identifier 22.22.22.22, local AS number 101
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::1
                4         1001       8       7        1    0    0 00:04:39        0
89.110.29.193   4         1001       7       8        1    0    0 00:04:35        0
```

#### Маршрутизатор R15:

```
R15#show ip bgp summary 
BGP router identifier 15.15.15.15, local AS number 1001
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::12
                4          301      23      24        1    0    0 00:18:09        0
89.110.29.198   4          301      22      23        1    0    0 00:18:06        0
```

#### Маршрутизатор R21:

```
R21#show ip bgp summary 
BGP router identifier 21.21.21.21, local AS number 301
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::11
                4         1001      24      24        1    0    0 00:18:38        0
89.110.29.197   4         1001      23      23        1    0    0 00:18:35        0
```

## Часть 2. Настройка eBGP между провайдерами Киторн и Ламас

Необходимо включить на маршрутизаторах eBGP, назначить router-id и настроить между ними соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R22:

```
!
router bgp 101
 bgp router-id 22.22.22.22
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::1 remote-as 1001
 neighbor 2A02:6B8:89:AC61:AC::22 remote-as 301
 neighbor 89.110.29.193 remote-as 1001
 neighbor 89.110.29.202 remote-as 301
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::1 activate
  neighbor 2A02:6B8:89:AC61:AC::22 activate
  neighbor 89.110.29.193 activate
  neighbor 89.110.29.202 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::1 activate
  neighbor 2A02:6B8:89:AC61:AC::22 activate
 exit-address-family
!
```

#### Маршрутизатор R21:

```
!
router bgp 301
 bgp router-id 21.21.21.21
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::11 remote-as 1001
 neighbor 2A02:6B8:89:AC61:AC::21 remote-as 101
 neighbor 89.110.29.197 remote-as 1001
 neighbor 89.110.29.201 remote-as 101
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::11 activate
  neighbor 2A02:6B8:89:AC61:AC::21 activate
  neighbor 89.110.29.197 activate
  neighbor 89.110.29.201 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::11 activate
  neighbor 2A02:6B8:89:AC61:AC::21 activate
 exit-address-family
!
```

Проверим соседство командой show ip bgp summary.

#### Маршрутизатор R22:

```
R22#show ip bgp summary 
BGP router identifier 22.22.22.22, local AS number 101
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::1
                4         1001      48      46        1    0    0 00:40:46        0
2A02:6B8:89:AC61:AC::22
                4          301       3       5        1    0    0 00:00:58        0
89.110.29.193   4         1001      47      48        1    0    0 00:40:42        0
89.110.29.202   4          301       6       6        1    0    0 00:04:05        0           
```

#### Маршрутизатор R21:

```
R21#show ip bgp summary 
BGP router identifier 21.21.21.21, local AS number 301
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::11
                4         1001      48      47        1    0    0 00:40:04        0
2A02:6B8:89:AC61:AC::21
                4          101       4       2        1    0    0 00:00:51        0
89.110.29.197   4         1001      46      47        1    0    0 00:40:01        0
89.110.29.201   4          101       6       6        1    0    0 00:03:58        0
```

## Часть 3. Настройка eBGP между Ламас/Киторн и Триада

Необходимо включить на маршрутизаторах eBGP, назначить router-id и настроить между ними соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R21:

```
!
router bgp 301
 bgp router-id 21.21.21.21
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::11 remote-as 1001
 neighbor 2A02:6B8:89:AC61:AC::21 remote-as 101
 neighbor 2A02:6B8:89:AC61:AC::42 remote-as 520
 neighbor 89.110.29.197 remote-as 1001
 neighbor 89.110.29.201 remote-as 101
 neighbor 89.110.29.210 remote-as 520
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::11 activate
  neighbor 2A02:6B8:89:AC61:AC::21 activate
  neighbor 2A02:6B8:89:AC61:AC::42 activate
  neighbor 89.110.29.197 activate
  neighbor 89.110.29.201 activate
  neighbor 89.110.29.210 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::11 activate
  neighbor 2A02:6B8:89:AC61:AC::21 activate
  neighbor 2A02:6B8:89:AC61:AC::42 activate
 exit-address-family
!
```

#### Маршрутизатор R24:

```
!
router bgp 520
 bgp router-id 24.24.24.24
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::41 remote-as 301
 neighbor 89.110.29.209 remote-as 301
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::41 activate
  neighbor 89.110.29.209 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::41 activate
 exit-address-family
!
```

#### Маршрутизатор R22:

```
!
router bgp 101
 bgp router-id 22.22.22.22
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::1 remote-as 1001
 neighbor 2A02:6B8:89:AC61:AC::22 remote-as 301
 neighbor 2A02:6B8:89:AC61:AC::32 remote-as 520
 neighbor 89.110.29.193 remote-as 1001
 neighbor 89.110.29.202 remote-as 301
 neighbor 89.110.29.206 remote-as 520
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::1 activate
  neighbor 2A02:6B8:89:AC61:AC::22 activate
  neighbor 2A02:6B8:89:AC61:AC::32 activate
  neighbor 89.110.29.193 activate
  neighbor 89.110.29.202 activate
  neighbor 89.110.29.206 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::1 activate
  neighbor 2A02:6B8:89:AC61:AC::22 activate
  neighbor 2A02:6B8:89:AC61:AC::32 activate
 exit-address-family
!
```

#### Маршрутизатор R23:

```
!
router bgp 520
 bgp router-id 23.23.23.23
 bgp log-neighbor-changes
 neighbor 2A02:6B8:89:AC61:AC::31 remote-as 101
 neighbor 89.110.29.205 remote-as 101
 !
 address-family ipv4
  neighbor 2A02:6B8:89:AC61:AC::31 activate
  neighbor 89.110.29.205 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2A02:6B8:89:AC61:AC::31 activate
 exit-address-family
!
```

Проверим соседство командой show ip bgp summary.

#### Маршрутизатор R21:

```
R21#show ip bgp summary 
BGP router identifier 21.21.21.21, local AS number 301
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::11
                4         1001      75      74        1    0    0 01:04:19        0
2A02:6B8:89:AC61:AC::21
                4          101      31      29        1    0    0 00:25:05        0
2A02:6B8:89:AC61:AC::42
                4          520       2       2        1    0    0 00:00:18        0
89.110.29.197   4         1001      73      73        1    0    0 01:04:16        0
89.110.29.201   4          101      33      33        1    0    0 00:28:13        0
89.110.29.210   4          520       5       3        1    0    0 00:01:11        0
```

#### Маршрутизатор R24:

```
R24#show ip bgp summary
BGP router identifier 24.24.24.24, local AS number 520
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::41
                4          301       2       2        1    0    0 00:00:28        0
89.110.29.209   4          301       3       5        1    0    0 00:01:22        0
```

#### Маршрутизатор R22:

```
R22#show ip bgp summary 
BGP router identifier 22.22.22.22, local AS number 101
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::1
                4         1001      91      90        1    0    0 01:19:49        0
2A02:6B8:89:AC61:AC::22
                4          301      46      47        1    0    0 00:40:02        0
2A02:6B8:89:AC61:AC::32
                4          520       4       2        1    0    0 00:00:22        0
89.110.29.193   4         1001      90      90        1    0    0 01:19:45        0
89.110.29.202   4          301      50      50        1    0    0 00:43:09        0
89.110.29.206   4          520      15      13        1    0    0 00:10:58        0
```

#### Маршрутизатор R23:

```
R23#show ip bgp summary 
BGP router identifier 23.23.23.23, local AS number 520
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::31
                4          101       2       4        1    0    0 00:00:46        0
89.110.29.205   4          101      14      16        1    0    0 00:11:22        0
```

## Часть 4. Настройка eBGP между офисом С.-Петербург и провайдером Триада

Необходимо включить на маршрутизаторах eBGP, назначить router-id и настроить между ними соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R18:

```

```

#### Маршрутизатор R24:

```

```

#### Маршрутизатор R26:

```

```

Проверим соседство командой show ip bgp summary.

#### Маршрутизатор R18:

```

```

#### Маршрутизатор R24:

```

```

#### Маршрутизатор R26:

```

```

#### Маршрутизатор R23:

```

```






