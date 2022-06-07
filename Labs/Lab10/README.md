# Лабораторная работа №10 iBGP

#### Цель: 

Настроить iBGP в офисе Москва. Настроить iBGP в сети провайдера Триада

## Топология

Выполнение лабораторной работы будет происходить на созданном при выполнении лабораторной работы №4 стенде:

![](topology.lab9.PNG)

## Ход работы

Лабораторная работа разбита на 5 частей:
1) Настроить iBGP в офисе Москва между маршрутизаторами R14 и R15
2) Настроить iBGP в провайдере Триада
3) Настройть офис Москва так, чтобы приоритетным провайдером стал Ламас
4) Настройть офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно

## Часть 1. Настроить iBGP в офисе Москва между маршрутизаторами R14 и R15

Необходимо включить на маршрутизаторах R14, R22, R15, R21 eBGP, назначить им router-id и настроить между R14 и R22, R15 и R21 соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

## Часть 2. Настроить iBGP в провайдере Триада

Необходимо включить на маршрутизаторе R25 iBGP. На маршрутизаторах R23, R24, R25, R26 добавить loopback интерфейс, добавить в качестве iBGP соседней все маршрутизаторы Триада, включить isis на loopback интерфейсе, настроить update-source для всех соседей на Loopback интерфейс.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R23:

```

```

#### Маршрутизатор R24:

```

```

#### Маршрутизатор R25:

```

```

#### Маршрутизатор R26:

```

```

## Часть 3. Настройть офис Москва так, чтобы приоритетным провайдером стал Ламас

Необходимо включить на маршрутизаторах R14, R22, R15, R21 eBGP, назначить им router-id и настроить между R14 и R22, R15 и R21 соседство для ipv4 и ipv6.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

## Часть 4. Настройть офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно

Необходимо настроить балансировку трафика на маршрутизаторе R18 между R24 И R26.

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```
R18(config-router)#do sh ip route bgp
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 89.110.29.229 to network 0.0.0.0

      89.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
B        89.110.29.192/30 [20/0] via 89.110.29.229, 00:06:01
B        89.110.29.196/30 [20/0] via 89.110.29.229, 00:06:01
B        89.110.29.200/30 [20/0] via 89.110.29.229, 00:06:01
```

```
R18(config-router)#do sh ip route bgp
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 89.110.29.229 to network 0.0.0.0

      89.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
B        89.110.29.192/30 [20/0] via 89.110.29.229, 00:06:01
B        89.110.29.196/30 [20/0] via 89.110.29.229, 00:06:01
B        89.110.29.200/30 [20/0] via 89.110.29.229, 00:06:01
```




