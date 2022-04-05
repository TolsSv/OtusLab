# Лабораторная работа №2 Развертывание коммутируемой сети с резервными каналами
## Цели 

1) Создание сети и настройка основных параметров устройства.
2) Выбор корневого моста.
3) Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов.
4) Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.

## Топология

Для выполнения лабораторной работы был собран стенд в соответствии со схемой:

![](Topoligy_lab2.PNG)

## Таблица адресов

Данный стенд настроен в соответствии с таблицей адресов:

| Устройство      | Интерфейс          | IP адрес     | Маска подсети | 
| --------------- | -------------------| -------------|---------------|
| S1              | VLAN 1             | 192.168.1.1  | 255.255.255.0 | 
| S2              | VLAN 1             | 192.168.1.2  | 255.255.255.0 | 
| S3              | VLAN 1             | 192.168.1.3  | 255.255.255.0 | 

## Часть 1

### Шаги

1) Создать сеть согласно топологии
2) Выполнить инициализацию и перезагрузку коммутаторов
3) Настроить базовые параметры каждого коммутатора
4) Проверить связь

### Результаты шагов 1-3
В качестве результатов шагов 1-3 ниже приведена часть вывод команды show running-config коммутаторов, соответствующиая настройкам указанным в пунке 3

#### Коммутатор S1:
```
service password-encryption
!
hostname S1
!
enable password 7 14141E0A1F17
!
no ip domain-lookup
!
interface Vlan1
 ip address 192.168.1.1 255.255.255.0
!
banner motd ^CUnauthorized access is prohibited^C
!
line con 0
 password 7 045802150C2E
 logging synchronous
 login
line vty 0
 password 7 05080F1C2243
 login
```

#### Коммутатор S2:
```
service password-encryption
!
hostname S2
!
enable password 7 070C2D4D5D1A
!
no ip domain-lookup
!
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
!
banner motd ^CUnauthorized access is prohibited^C
!
line con 0
 password 7 094F471A1A0A
 logging synchronous
 login
line vty 0
 password 070C285F4D06
 login
```

#### Коммутатор S3:
```
service password-encryption
!
hostname S3
!
enable password 7 070C2D4D5D1A
!
no ip domain-lookup
!
interface Vlan1
 ip address 192.168.1.3 255.255.255.0
!
banner motd ^CUnauthorized access is prohibited^C
!
line con 0
 password 7 121A0C041104
 logging synchronous
 login
line vty 0
 password 0822455D0A16
 login
```

### Результаты шага 4
 В качестве результатов шага 4 приведена проверка способность коммутаторов обмениваться эхо-запросами:
 1) S1 - S2
 2) S1 - S3
 3) S2 - S3
 
#### S1 - S2:
```
S1#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

#### S1 - S3:
```
S1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
```

#### S2 - S3:
```
S2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
```

## Часть 2

### Шаги

1) Отключить все порты на коммутаторах
2) Настроить подключенные порты в качестве транковых
3) Включить порты e0/1 и e0/3 на всех коммутаторах
4) Отобразить данные протокола spanning-tree





 
 
 
 
 
