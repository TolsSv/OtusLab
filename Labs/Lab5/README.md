# Лабораторная работа №5 PBR

#### Цель: 

Настроить политику маршрутизации в офисе Чокурдах. Распределить трафик между 2 линками.

## Топология

Выполнение лабораторной работы будет происходить в созданной при выполнении лабораторной работы №4 схеме. При этом необходимо:
- Настроить политику маршрутизации для сетей офиса;
- Распределить трафик между двумя линками с провайдером (только для IPv4);
- Настроить отслеживание линка через технологию IP SLA (только для IPv4);
- Настройте для офиса Лабытнанги маршрут по-умолчанию.

Схема сети приведена ниже:

![](lab5_topology.PNG)


## Ход работы

Лабораторная работа разбита на 3 части:
1) Распределение трафика между двумя линками с провайдером
2) Настройка отслеживания линка через технологию IP SLA
3) Настройка для офиса Лабытнанги маршрута по-умолчанию

## Часть 1. Распределение трафика между двумя линками с провайдером

Распределим трафик между двумя линками провайдера так, чтобы трафик из vlan 801 отправлялся через маршрутизатор R26, а vlan 103 через маршрутизатор R25. Для этого необходимо настроить R28: создать два списка доступа для обоих подсетей, создать route-map с двумя пунктами для кадого линка и с назначением в качестве next-hop необходимого маршрутизатора, назначить route-map на интерфейсы в сторону SW29.

В выводе running-config маршрутизатора R28 появятся настройки:

#### Маршрутизатор R28:

```
!
interface Ethernet0/2.103
 description Cho_Client
 encapsulation dot1Q 103
 ip address 192.168.28.1 255.255.255.0
 ip policy route-map lab5
 ipv6 address FE80::28 link-local
 ipv6 address FDE8:8A:FC:1:28::1/80
!
interface Ethernet0/2.801
 encapsulation dot1Q 801
 ip address 80.80.1.129 255.255.255.192
 ip policy route-map lab5
 ipv6 address FE80::28 link-local
 ipv6 address 2A02:6B8:89:AC62::101/120
!
!
ip access-list standard acl103
 permit 192.168.28.0 0.0.0.255
 deny   any
ip access-list standard acl801
 permit 80.80.1.128 0.0.0.63
 deny   any
!
!
route-map lab5 permit 10
 match ip address acl801
 set ip next-hop 89.110.29.221
!
route-map lab5 permit 20
 match ip address acl103
 set ip next-hop 89.110.29.217
!
```

Так как лабораторная работа выполняется уже после 10 лабораторной работы - на стенде уже настроен BGP. Для возможности проверить корректности настройки на маршрутизаторах R25 и R26 были настроены маршруты по умолчанию до подсетей 801 и 103 через R28, и настроена редистрибуция статических маршрутов в BGP. Таким образом между Триадой и Чокурды появилась ip связность - можно проверить крректность настройки PBR с помощью команды traceroute c коммутатора SW29, ПК VPC30 и ПК VPC31 до маршрутизатора R23.

#### Коммутатор SW29:

```
SW29#traceroute 192.168.52.2
Type escape sequence to abort.
Tracing the route to 192.168.52.2
VRF info: (vrf in name/id, vrf out name/id)
  1 80.80.1.129 2 msec 0 msec 1 msec
  2  * 
    89.110.29.221 0 msec 1 msec
  3 192.168.52.5 0 msec 1 msec 1 msec
  4 192.168.52.2 1 msec *  1 msec
```

#### ПК VPC30:

```
VPCS> traceroute 192.168.52.2
Bad command: "traceroute 192.168.52.2". Use ? for help.

VPCS> trace 192.168.52.2     
trace to 192.168.52.2, 8 hops max, press Ctrl+C to stop
 1   192.168.28.1   0.636 ms  0.304 ms  0.278 ms
 2     *89.110.29.217   0.851 ms  0.913 ms
 3   *192.168.52.2   1.118 ms (ICMP type:3, code:3, Destination port unreachable)  *
```

#### ПК VPC31:

```
VPCS> trace 192.168.52.2
trace to 192.168.52.2, 8 hops max, press Ctrl+C to stop
 1   192.168.28.1   0.549 ms  0.353 ms  0.309 ms
 2   89.110.29.217   0.579 ms  0.588 ms  0.659 ms
 3   *192.168.52.2   0.912 ms (ICMP type:3, code:3, Destination port unreachable)  *
```

В выводах команд видно, что пакеты от коммутатора и от ПК идут разными путями в соответствии с поставленными целями.

## Часть 2. Настройка отслеживания линка через технологию IP SLA

Настроим ip sla на маршрутизаторе R28 для проверки доступности маршрутизаторов провайдера R25 и R26.

В выводе running-config маршрутизатора R28 появятся настройки:

#### Маршрутизатор R28:

```
!
ip sla 1
 icmp-echo 89.110.29.221
 frequency 20
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 89.110.29.217
 frequency 20
ip sla schedule 2 life forever start-time now
!
```

Проверим работоспособность ip sla на маршрутизаторе R28 введя команду sh ip sla statistics:

#### Маршрутизатор R28:

```
R28#sh ip sla statistics 
IPSLAs Latest Operation Statistics

IPSLA operation id: 1
Latest RTT: 1 milliseconds
Latest operation start time: 21:40:51 msk Sun Jul 17 2022
Latest operation return code: OK
Number of successes: 25
Number of failures: 0
Operation time to live: Forever



IPSLA operation id: 2
Latest RTT: 1 milliseconds
Latest operation start time: 21:40:59 msk Sun Jul 17 2022
Latest operation return code: OK
Number of successes: 24
Number of failures: 0
Operation time to live: Forever
```

## Часть 3. Настройка для офиса Лабытнанги маршрута по-умолчанию

Необходимо настроить на маршрутизаторе R27 ipv4 и ipv6 маршрут по умолчанию.

В выводе running-config маршрутизатора R27 появятся настройки:

#### Маршрутизатор R27:

```
ip route 0.0.0.0 0.0.0.0 89.110.29.213
!
ipv6 route ::/0 2A02:6B8:89:AC61:AC::51
```


