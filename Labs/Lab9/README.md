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
3) Настройка eBGP между Ламас и Триада
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

#### Маршрутизатор R22:

```
R14#show ip bgp summary
BGP router identifier 14.14.14.14, local AS number 1001
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::2
                4          101      41      42        1    0    0 00:35:57        0
89.110.29.194   4          101      42      42        1    0    0 00:36:01        0           
```

#### Маршрутизатор R21:

```
R22#show ip bgp summary   
BGP router identifier 22.22.22.22, local AS number 101
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::1
                4         1001       8       7        1    0    0 00:04:39        0
89.110.29.193   4         1001       7       8        1    0    0 00:04:35        0
```


