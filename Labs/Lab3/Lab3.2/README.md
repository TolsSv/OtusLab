# Лабораторная работа №3.2 Реализация DHCPv6
#### Цель: 
Настроить DHCPv6.
#### Описание:
В данной лабораторной работе необходимо настроить автоматическое получение IPv6 адресов в сети.

## Топология

В ходе выполнения лабораторной работы необходимо собрать стенд в соответствии со схемой:

![](Topoligy_lab3.2.PNG)

## Таблица адресов

В ходе выполнения лабораторной работы необходимо настроить адресацию в соответствии с таблицей адресов:

| Устройство      | Интерфейс          | IPv6 адрес             | 
| --------------- | -------------------| -----------------------|
| R1              | e0/0               | 2001:db8:acad:2::1 /64 |
|                 |                    | fe80::1                |
|                 | e0/1               | 2001:db8:acad:1::1/64  |
|                 |                    | fe80::1                |
| R2              | e0/0               | 2001:db8:acad:2::2/64  |
|                 |                    | fe80::2                |
|                 | e0/1               | 2001:db8:acad:3::1 /64 |
|                 |                    | fe80::1                |
| PC-A            | NIC                | DHCP                   |
| PC-B            | NIC                | DHCP                   |

## Части

Лабораторная работа разбита на 5 частей:
1) Создание сети в соответствии с топологией и настройка основных параметров устройств
2) Проверка назначения IPv6 адреса на АРМ c помощью технологии SLAAC и маршрутизатора R1  
3) Настройка и проверка DHCPv6 сервера без сохранения состояния на маршрутизатре R1
4) Настройка и проверка DHCPv6 сервера с сохранением состояния на маршрутизатре R1
5) Настройка и проверка ретрансляции DHCPv6 на маршрутизаторе R2

## Часть 1. Создание сети в соответствии с топологией и настройка основных параметров устройств

### Настройка схемы

В ходе лабораторной работы в eve ng был настроен стенд:

![](Lab3.2_eve.ng.PNG)

### Базовая настройка маршрутизаторов

В ходе базовой настройки маршрутизаторов: настроить его имя, отключить DNS lookup, настроить пароль на вход в привелигерованный режим, настроить аутентификацию и пароль на вход через консоль, настроить аутентификацию и пароль при удаленном подключении, настроить хранение паролей в зашифрованном виде, создать баннер, установить время на маршрутизаторе и сохраненить действующую конфигурацию в стартовую.

После базовой настройки маршрутизаторов в running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R1
```
service password-encryption
!
hostname R1
!
enable password 7 121A09160118
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 104D000A0618
 login
line vty 0
 password 7 01100F175804
 login
```

#### Маршрутизатор R2
```
service password-encryption
!
hostname R2
!
enable password 7 045807071C32
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 094F471A1A0A
 login
line vty 0
 password 7 094F471A1A0A
 login
```

### Базовая настройка коммутаторов

В качестве базовой настройки коммутаторов необходимо произвести аналогичные настройки как при базовой настройке маршрутизаторов. ВЫКЛЮЧИТЬ НЕИСПОЛЬЗКУЕМЫЕ ПОРТЫ

После базовой настройки коммутатов в running-config коммутаторов появятся настройки:

#### Коммутатор S1
```
service password-encryption
!
hostname S1
!
enable password 7 0307570A151C
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 110A1016141D
 login
line vty 0
 password 7 121A0C041104
 login
```

#### Коммутатор S2
```
service password-encryption
!
hostname S1
!
enable password 7 01100A054818
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 05080F1C2243
 login
line vty 0
 password 7 14141B180F0B
 login
```

### Настройка портов маршрутизаторов

Необходимо настроить порты на маршрутизаторах, назначить на них ip адреса в соответствии с таблицей адресов, настроить в качестве маршрута по умолчанию ip адрес соседнего маршрутизатора. 

После настройки портов маршрутизаторов в running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R1
```
ipv6 unicast-routing
!
interface Ethernet0/0
 no ip address
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
!
interface Ethernet0/1
 no ip address
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
!
ipv6 route ::/0 2001:DB8:ACAD:2::2
```

#### Маршрутизатор R2
```
ipv6 unicast-routing
!
interface Ethernet0/0
 no ip address
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:ACAD:2::2/64
!
interface Ethernet0/1
 no ip address
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:3::1/64
!
ipv6 route ::/0 2001:DB8:ACAD:2::1
```

Чтобы проверить корректность настройки совершим запрос ping c маршрутизатора R1 на маршрутизатор R2:
```
R1#ping 2001:db8:acad:2::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:2::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
R1#ping fe80::2           
Output Interface: Ethernet0/0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to FE80::2, timeout is 2 seconds:
Packet sent with a source address of FE80::1%Ethernet0/0
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

## Часть 2. Проверка назначения IPv6 адреса на АРМ c помощью технологии SLAAC и маршрутизатора R1  

Чтобы удостовериться, что АРМ сшенерировал IPv6 адрес из подсети маршрутизатора R1 с помощью технологии SLAAC введем show ipv6 на PC-A

```
VPCS> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6801/64
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6801/64
DNS               : 
ROUTER LINK-LAYER : aa:bb:cc:00:30:10
MAC               : 00:50:79:66:68:01
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500
```

Where did the host-id portion of the address come from? - АРМ сам сгенерировал ее зная префикс.

## Часть 3. Настройка и проверка DHCPv6 сервера без сохранения состояния на маршрутизатре R1

В связи с некорректной работой EVE NG в части получения АРМ IPv6 адреса по DHCPv6 был создан алогиченый стенд в cisco packet tracer для эмулации DHCPv6, а также произведены все насройки приведенные выше. К сожалению АРМ в cisco packet tracer не смог получить IPv6 адрес по SLAAC, поэтому лабораторная работа выполнена частично в eve ng, частично в cisco packet trace.


