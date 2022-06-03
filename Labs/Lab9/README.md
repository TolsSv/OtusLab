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

Проверим соседство командами show ip bgp neighbors и show ip bgp summary.

#### Маршрутизатор R14:

```
R14#show ip bgp summary
BGP router identifier 14.14.14.14, local AS number 1001
BGP table version is 1, main routing table version 1

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2A02:6B8:89:AC61:AC::2
                4          101      41      42        1    0    0 00:35:57        0
89.110.29.194   4          101      42      42        1    0    0 00:36:01        0
R14#show ip bgp neighbors 
BGP neighbor is 2A02:6B8:89:AC61:AC::2,  remote AS 101, external link
  BGP version 4, remote router ID 22.22.22.22
  BGP state = Established, up for 00:35:43
  Last read 00:00:42, last write 00:00:52, hold time is 180, keepalive interval is 60 seconds
  Neighbor sessions:
    1 active, is not multisession capable (disabled)
  Neighbor capabilities:
    Route refresh: advertised and received(new)
    Four-octets ASN Capability: advertised and received
    Address family IPv4 Unicast: advertised and received
    Address family IPv6 Unicast: received
    Enhanced Refresh Capability: advertised and received
    Multisession Capability: 
    Stateful switchover support enabled: NO for session 1
  Message statistics:
    InQ depth is 0
    OutQ depth is 0
    
                         Sent       Rcvd
    Opens:                  1          1
    Notifications:          0          0
    Updates:                1          1
    Keepalives:            39         39
    Route Refresh:          0          0
    Total:                 41         41
  Default minimum time between advertisement runs is 30 seconds

 For address family: IPv4 Unicast
  Session: 2A02:6B8:89:AC61:AC::2
  BGP table version 1, neighbor version 1/0
  Output queue size : 0
  Index 1, Advertise bit 0
  1 update-group member
  Slow-peer detection is disabled
  Slow-peer split-update-group dynamic is disabled
  Interface associated: Ethernet0/2
                                 Sent       Rcvd
  Prefix activity:               ----       ----
    Prefixes Current:               0          0
    Prefixes Total:                 0          0
    Implicit Withdraw:              
```

#### Маршрутизатор R22:

```

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
