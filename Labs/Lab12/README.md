# Лабораторная работа №12 Основные протоколы сети интернет

#### Цель: 

Настроить DHCP в офисе Москва.
Настроить синхронизацию времени в офисе Москва.
Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах.

## Топология

Выполнение лабораторной работы будет происходить на созданном при выполнении лабораторной работы №4 стенде:

![](lab11.PNG)
![](lab11.PNG)

## Ход работы

Лабораторная работа разбита на 4 части:
1) Настройка NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
2) Настройка NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
2) Настройка статического NAT для R20.
3) Настройка провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию
4) Настройка провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург

При этом все внешние сети в лабораторной работе должны иметь IP связность.

## Часть 1. Настройка фильтрации в офисе Москва так, чтобы не появилось транзитного трафика(As-path)

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
R14#sh ip bgp neighbors 89.110.29.194 advertised-routes 
BGP table version is 16, local router ID is 14.14.14.14
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i 80.80.1.128/26   15.15.15.15              0    250      0 301 520 ?
 r>i 89.110.29.192/30 15.15.15.15              0    250      0 301 101 i
 *>i 89.110.29.196/30 15.15.15.15              0    250      0 301 i
 *>i 89.110.29.200/30 15.15.15.15              0    250      0 301 101 i
 *>i 89.110.29.204/30 15.15.15.15              0    250      0 301 101 i
 *>i 89.110.29.208/30 15.15.15.15              0    250      0 301 i
 *>i 89.110.29.224/30 15.15.15.15              0    250      0 301 520 i
 *>i 89.110.29.228/30 15.15.15.15              0    250      0 301 520 i
 *>i 192.168.28.0     15.15.15.15              0    250      0 301 520 ?

Total number of prefixes 9 
```

#### Маршрутизатор R15:

```
R15#sh ip bgp neighbors 2a02:6b8:89:ac61:ac::12 advertised-routes   
BGP table version is 16, local router ID is 15.15.15.15
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  80.80.1.128/26   89.110.29.198                 250      0 301 520 ?
 *>  89.110.29.192/30 89.110.29.198                 250      0 301 101 i
 r>  89.110.29.196/30 89.110.29.198            0    250      0 301 i
 *>  89.110.29.200/30 89.110.29.198                 250      0 301 101 i
 *>  89.110.29.204/30 89.110.29.198                 250      0 301 101 i
 *>  89.110.29.208/30 89.110.29.198            0    250      0 301 i
 *>  89.110.29.224/30 89.110.29.198                 250      0 301 520 i
 *>  89.110.29.228/30 89.110.29.198                 250      0 301 520 i
 *>  192.168.28.0     89.110.29.198                 250      0 301 520 ?

Total number of prefixes 9 
```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```
!
router bgp 1001
 bgp router-id 14.14.14.14
 bgp log-neighbor-changes
 neighbor 15.15.15.15 remote-as 1001
 neighbor 15.15.15.15 update-source Loopback0
 neighbor 2A02:6B8:89:AC61:AC::2 remote-as 101
 neighbor 89.110.29.194 remote-as 101
 !
 address-family ipv4
  neighbor 15.15.15.15 activate
  neighbor 15.15.15.15 next-hop-self
  neighbor 2A02:6B8:89:AC61:AC::2 activate
  neighbor 2A02:6B8:89:AC61:AC::2 filter-list 1 out
  neighbor 89.110.29.194 activate
  neighbor 89.110.29.194 filter-list 1 out
 exit-address-family
 !
 address-family ipv6
  neighbor 15.15.15.15 activate
  neighbor 2A02:6B8:89:AC61:AC::2 activate
 exit-address-family
!
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
!
```

#### Маршрутизатор R15:

```
!
router bgp 1001
 bgp router-id 15.15.15.15
 bgp log-neighbor-changes
 neighbor 14.14.14.14 remote-as 1001
 neighbor 14.14.14.14 update-source Loopback0
 neighbor 2A02:6B8:89:AC61:AC::12 remote-as 301
 neighbor 89.110.29.198 remote-as 301
 !
 address-family ipv4
  neighbor 14.14.14.14 activate
  neighbor 14.14.14.14 next-hop-self
  neighbor 2A02:6B8:89:AC61:AC::12 activate
  neighbor 2A02:6B8:89:AC61:AC::12 filter-list 1 out
  neighbor 89.110.29.198 activate
  neighbor 89.110.29.198 route-map as301-in in
  neighbor 89.110.29.198 filter-list 1 out
 exit-address-family
 !
 address-family ipv6
  neighbor 14.14.14.14 activate
  neighbor 2A02:6B8:89:AC61:AC::12 activate
  neighbor 2A02:6B8:89:AC61:AC::12 route-map as301-in in
  neighbor 2A02:6B8:89:AC61:AC::12 filter-list 1 out
 exit-address-family
!
ip as-path access-list 1 permit ^$
ip as-path access-list 1 permit .*
!
```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
R14#sh ip bgp neighbors 89.110.29.194 advertised-routes 

Total number of prefixes 0  
```

#### Маршрутизатор R15:

```
R15#sh ip bgp neighbors 89.110.29.198 advertised-routes 

Total number of prefixes 0
```

