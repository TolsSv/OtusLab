# Лабораторная работа №3.1 Реализация DHCPv4
#### Цель: 
Настроить DHCPv4.
#### Описание:
В данной лабораторной работе необходимо настроить автоматическое получение IPv4 адресов в сети.

## Топология

В ходе выполнения лабораторной работы необходимо собрать стенд в соответствии со схемой:

![](Topoligy_lab3.1.PNG)

## Таблица адресов

В ходе выполнения лабораторной работы необходимо настроить адресацию в соответствии с таблицей адресов:

| Устройство      | Интерфейс          | IP адрес     | Маска подсети   | Шлюз по умолчанию |
| --------------- | -------------------| -------------|-----------------|-------------------|
| R1              | e0/0               | 10.0.0.1     | 255.255.255.252 | N/A               |
|                 | e0/1               | N/A          | N/A             |                   |
|                 | e0/1.100           | 192.168.1.1  | 255.255.255.192 | N/A               |
|                 | e0/1.200           | 192.168.1.65 | 255.255.255.224 |                   |
|                 | e0/1.1000          | N/A          | N/A             | N/A               |
| R2              | e0/0               | 10.0.0.2     | 255.255.255.252 | N/A               |
|                 | e0/1               | 192.168.1.97 | 255.255.255.240 |                   |
| S1              | VLAN 200           | 192.168.1.66 | 255.255.255.224 |                   |
| S2              | VLAN 1             |              |                 |                   |
| PC-A            | NIC                | DHCP         | DHCP            | DHCP              |
| PC-B            | NIC                | DHCP         | DHCP            | DHCP              |


## Таблица VLAN

VLAN, которые необходимо настроить в лабораторной работе, приведены в таблице vlan:

| VLAN      | Имя         | Интерфейсы                  |
| ----------|-------------| ----------------------------|
| 1         | N/A         | S2: e0/0                    | 
| 100       | Clients     | S1: e0/0                    | 
| 200       | Management  | S1: VLAN 200                | 
| 999       | Parking_Lot | S1: e0/2-3                  | 
| 1000      | Native      | N/A                         | 

## Части

Лбораторная работа разбита на 3 части:
1) Создать сеть в соответствии с топологией и настроить основные параметры устройства
2) Настроить и проверить 2 DHCPv4 сервера на R1
3) Настроить и проверить трансляцию DHCPv4 на R2

## Часть 1. Выполнение

### Шаг 1. Выполнение



### Настройка схемы

В ходе лабораторной работы в eve ng был настроен стенд:

![](Lab3.1_eve.ng.PNG)

### Базовая настройка маршрутизаторов

В ходе базовой настройки маршрутизаторов: настроить его имя, отключить DNS lookup, настроить пароль на вход в привелигерованный режим, настроить аутентификацию и пароль на вход через консоль, настроить аутентификацию и пароль при удаленном подключении, настроить хранение паролей в зашифрованном виде, создать баннер, установить время на маршрутизаторе и сохраненить действующую конфигурацию в стартовую.

После базовой настройки маршрутизаторов в running-config маршрутизаторов появятся настройки:


#### Маршрутизатор R1
```
service password-encryption
!
hostname R1
!
enable password 7 1511070D1739
!
clock timezone msk 3 0
!
no ip domain lookup
!
banner motd ^CUnautorized access is prohibited^C
!
line con 0
 password 7 13061E010803
 logging synchronous
 login
line vty 0
 password 7 13061E010803
 login
 transport input none
```

#### Маршрутизатор R2
```
service password-encryption
!
hostname R2
!
enable password 7 00071F071748
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
 password 7 0822455D0A16
 login
 transport input none
```

### Настройка портов маршрутизаторов

Необходимо создать подинтерфейсы в соответствии с таблицей адресов, назначить ip адрес и описание интерфейсу, включить подключенные интерфейсы.

В выводе команды show ip interface brief маршрутизаторов появится:

#### Маршрутизатор R1
```
R1# show ip interface brief
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.1        YES manual up                    up      
Ethernet0/1                unassigned      YES NVRAM  up                    up      
Ethernet0/1.100            192.168.1.1     YES manual up                    up      
Ethernet0/1.200            192.168.1.65    YES manual up                    up      
Ethernet0/1.1000           unassigned      YES unset  up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down 
```

#### Маршрутизатор R2
```
R2#sh ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.0.0.2        YES manual up                    up      
Ethernet0/1                192.168.1.97    YES manual up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down 
```



