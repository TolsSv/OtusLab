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
2) Проверка назначения SLAAC адреса на маршрутизаторе R1  
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
 logging synchronous
 login
line vty 0
 password 7 01100F175804
 login
 transport input none
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
 logging synchronous
 login
line vty 0
 password 7 094F471A1A0A
 login
 transport input none
```





### Базовая настройка коммутаторов

В качестве базовой настройки коммутаторов необходимо произвести аналогичные настройки как при базовой настройке маршрутизаторов. 

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
 logging synchronous
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
 logging synchronous
 login
line vty 0
 password 7 14141B180F0B
 login
```


