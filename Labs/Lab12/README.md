# Лабораторная работа №12 Основные протоколы сети интернет

#### Цель: 

Настроить DHCP в офисе Москва.

Настроить синхронизацию времени в офисе Москва.

Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах.

## Топология

Выполнение лабораторной работы будет происходить на созданном при выполнении лабораторной работы №4 стенде:

![](Moscow.PNG)
![](SPB.PNG)
![](Chokurdi.PNG)

## Ход работы

Лабораторная работа разбита на 6 частей + 5ое задание со *:
1) Настройка NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
2) Настройка NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
3) Настройка статического NAT для R20.
4) Настройка статического NAT для R19. Чтобы R19 был доступен с любого узла для удаленного управления.
5*) Настроика статического NAT(PAT) для офиса Чокурдах.
6) Настройка для IPv4 DHCP сервера в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.
7) Настройка NTP сервера на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.

При этом все офисы в лабораторной работе должны иметь IP связность.

## Часть 1. Настройка NAT(PAT) на R14 и R15

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 2. Настройка NAT(PAT) на R18

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 3. Настройка статического NAT для R20

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 4. Настройка статического NAT для R19

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 5*. Настроика статического NAT(PAT) для офиса Чокурдах

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 6. Настройка для IPv4 DHCP сервера в офисе Москва на маршрутизаторах R12 и R13

На маршрутизаторах R14 и R15 необходимо создать и назначить на интерфейс в сторону провайдера as-path фильтр разрешающий анонсировать update только о своих внутренних подсетях. 

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```
 
```

#### Маршрутизатор R15:

```

```

Настроим as-filter. В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```

С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes снова проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

#### Маршрутизатор R14:

```

```

#### Маршрутизатор R15:

```

```


## Часть 7. Настройка NTP сервера на R12 и R13

На маршрутизаторах R12 и R13 необходимо включить роутер в качестве NTP-сервера, обновление календаря (часов) роутера. На всех остальных маршрутизаторах и коммутаторах необходимо назначить в качестве ntp серверов R12 и R13.
С помощью команды show ip  bgp neighbors A.B.C.D advertised-routes проверим какие маршруты в сторону провайдеров анонсируют R14 и R15:

В выводе running-config маршрутизаторов появятся настройки:

#### Маршрутизатор R12:

```
ntp master 5
ntp update-calendar
```

#### Маршрутизатор R13:

```
ntp master 5
ntp update-calendar
```

#### Маршрутизатор R20:

```
ntp server 192.168.10.22
ntp server 192.168.10.14
```

#### Маршрутизатор R19:

```
ntp server 192.168.10.10
ntp server 192.168.10.18
```

#### Маршрутизатор R15:

```
ntp server 192.168.10.22
ntp server 192.168.10.14
```

#### Маршрутизатор R14:

```
ntp server 192.168.10.18
ntp server 192.168.10.10
```

#### Коммутатор SW4:

```
ntp server 192.168.10.25
ntp server 192.168.10.33
```

#### Коммутатор SW5:

```
ntp server 192.168.10.29
ntp server 192.168.10.37
```

#### Коммутатор SW3:

```
ntp server 192.168.10.25
ntp server 192.168.10.37
```

#### Коммутатор SW2:

```
ntp server 192.168.10.29
ntp server 192.168.10.37
```

С помощью команд show ntp associations и show ntp status проверим синхронизацию времени:

#### Маршрутизатор R20:

```
R20#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
+~192.168.10.22   127.127.1.1      5    254    512   377  0.000   0.000  3.846
*~192.168.10.14   127.127.1.1      5      2    512   377  0.000   0.000  3.068
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R20#show ntp status       
Clock is synchronized, stratum 6, reference is 192.168.10.14  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 228800 (1/100 of seconds), resolution is 4000
reference time is E68F9F03.958107C0 (15:13:55.584 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 1.00 msec
root dispersion is 18.84 msec, peer dispersion is 3.06 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000002 s/s
system poll interval is 512, last update was 867 sec ago.
```

#### Маршрутизатор R19:

```
R19#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.18  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 255600 (1/100 of seconds), resolution is 4000
reference time is E68F9FA5.6ED917B8 (15:16:37.433 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 1.00 msec
root dispersion is 19.97 msec, peer dispersion is 3.57 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000004 s/s
system poll interval is 512, last update was 941 sec ago.
R19#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
+~192.168.10.10   127.127.1.1      5    339    512   377  1.000   0.500  3.415
*~192.168.10.18   127.127.1.1      5     26    512   377  0.000   0.000  3.577
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R19#show ntp status       
Clock is synchronized, stratum 6, reference is 192.168.10.18  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 255900 (1/100 of seconds), resolution is 4000
reference time is E68F9FA5.6ED917B8 (15:16:37.433 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 1.00 msec
root dispersion is 20.01 msec, peer dispersion is 3.57 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000004 s/s
system poll interval is 512, last update was 943 sec ago.
```

#### Маршрутизатор R15:

```
R15#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
+~192.168.10.22   127.127.1.1      5     35    512   377  0.000   0.000  2.375
*~192.168.10.14   127.127.1.1      5    340    512   377  0.000   0.000  3.972
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R15#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.14  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 263100 (1/100 of seconds), resolution is 4000
reference time is E68FA084.449BA6A0 (15:20:20.268 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 17.26 msec, peer dispersion is 3.97 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 512, last update was 750 sec ago.
```

#### Маршрутизатор R14:

```
R14#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~192.168.10.18   127.127.1.1      5      0    512   377  0.000   0.000  5.397
+~192.168.10.10   127.127.1.1      5    350    512   377  0.000   0.000  3.783
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R14#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.18  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 283200 (1/100 of seconds), resolution is 4000
reference time is E68FA06B.50E56120 (15:19:55.316 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 18.14 msec, peer dispersion is 2.91 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 512, last update was 798 sec ago.
```

#### Коммутатор SW4:

```
SW4#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~192.168.10.25   127.127.1.1      5     51    512   377  0.000   0.000  2.402
+~192.168.10.33   127.127.1.1      5    116    512   377  0.000   0.000  3.339
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW4#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.25  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 290300 (1/100 of seconds), resolution is 4000
reference time is E68FA1EB.828F5D90 (15:26:19.510 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 12.82 msec, peer dispersion is 3.17 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 512, last update was 436 sec ago.
```

#### Коммутатор SW5:

```
SW5#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~192.168.10.29   127.127.1.1      5     15   1024   377  0.000   0.000  2.334
+~192.168.10.37   127.127.1.1      5     10    512   377  0.000   0.000  2.426
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW5#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.29  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 298200 (1/100 of seconds), resolution is 4000
reference time is E68FA3A1.61893858 (15:33:37.381 msk Sat Jul 30 2022)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 5.77 msec, peer dispersion is 2.33 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 1024, last update was 20 sec ago.
```

#### Коммутатор SW3:

```
SW3#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~192.168.10.25   127.127.1.1      5     86    256   377  1.000  -0.500  2.203
 ~192.168.10.37   .INIT.          16      -   1024     0  0.000   0.000 15937.
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW3#show ntp status
Clock is synchronized, stratum 6, reference is 192.168.10.25  
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 285400 (1/100 of seconds), resolution is 4000
reference time is E68FA36C.B78D51D8 (15:32:44.717 msk Sat Jul 30 2022)
clock offset is -0.5000 msec, root delay is 1.00 msec
root dispersion is 7.38 msec, peer dispersion is 2.20 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.000000004 s/s
system poll interval is 256, last update was 91 sec ago.
```

#### Коммутатор SW2:

```
SW2#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~192.168.10.29   127.127.1.1      5     22    512   377  1.000  -0.500  2.202
 ~192.168.10.37   .INIT.          16      -   1024     0  0.000   0.000 15937.
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
SW2#show ntp status       
Clock is synchronized, stratum 6, reference is 192.168.10.29  
nominal freq is 250.0000 Hz, actual freq is 250.0001 Hz, precision is 2**10
ntp uptime is 437400 (1/100 of seconds), resolution is 4000
reference time is E68FA3CE.3FBE7778 (15:34:22.249 msk Sat Jul 30 2022)
clock offset is -0.5000 msec, root delay is 1.00 msec
root dispersion is 6.38 msec, peer dispersion is 2.20 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.000000550 s/s
system poll interval is 512, last update was 23 sec ago.
```




