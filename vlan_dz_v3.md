**Lab - Configure Router-on-a-Stick Inter-VLAN Routing**

**Начальная настройка свитчей:**

**Назначаем имя**

`Switch#conf t`  
`Switch(config)#hostname S1`


**Отключаем DNS lookup**

`S1(config)#no ip domain-lookup`

**Проверяем:**

 `S1#sh run | i domain-lookup`

   ``no ip domain-lookup`

**Задаем пароли и вешаем баннер**

`S1(config)#enabl` 
 
`S1(config)#enable secre`

`S1(config)#enable secret class`

`S1(config)#line console 0 `` 

`S1(config-line)#password cisco `` 

`S1(config-line)#login  ``

`S1(config-line)#exit  ``

`S1(config)#line vty 0 4`  

`S1(config-line)#password cisco  ``

`S1(config-line)#login`  

`S1(config-line)#exit`  

`S1(config)#serv`  

`S1(config)#service pass` 

`S1(config)#service password-encryption*`  

`S1(config)#`  

`S1(config)#banner motd $ Autorized Access Only! $`

**Установка времени вручную**

`S1#clock set 13:38:30 sep 14 2020`  

`S1#
Sep 14 11:38:30.000: %SYS-6-CLOCKUPDATE: System clock has been updated from 13:03:59 EET Mon
 Sep 14 2020 to 13:38:30 EET Mon Sep 14 2020, configured from console by console.`

**Тоже самое делаем на втором коммутаторе**

`Switch(config)#hostname S2`

`S2(config)#`

`S2(config)#no ip domain-lookup`

`S2#sh run | i domain-lookup `` 

   `no ip domain-lookup`

`S2(config)#enabl`

`S2(config)#enable secre`

`S2(config)#enable secret class`

`S2(config)#line console 0`

`S2(config-line)#password cisco`

`S2(config-line)#login`

`S2(config-line)#exit`

`S2(config)#line vty 0 4`

`S2(config-line)#password cisco`

`S2(config-line)#login`

`S2(config-line)#exit`

`S2(config)#serv`

`S2(config)#service pass`

`S2(config)#service password-encryption`

`S2(config)#`

`S2(config)#banner motd $ Autorized Access Only! $`

`S2#clock set 13:58:30 sep 14 2020*`

**Настройка Рабочих мест (PC-A; PC-B)**

`VPCS> ip 192.168.3.3 255.255.255.0 192.168.3.1 ``   

`Checking for duplicate address...`

`PC1 : 192.168.3.3 255.255.255.0 gateway 192.168.3.1`

`VPCS> ip 192.168.4.3 255.255.255.0 192.168.4.1`

`Checking for duplicate address...`

`PC1 : 192.168.4.3 255.255.255.0 gateway 192.168.4.1`

**Настройка коммутатора S1**

**Создаем Vlan-ы**

`S1#conf t`

`S1(config)#vlan 3`

`S1(config-vlan)#name Management`

`S1(config)#vlan 4`

`S1(config-vlan)#name Operations`

`S1(config-vlan)#vlan 7`

`S1(config-vlan)#name ParkingLot`

`S1(config-vlan)#vlan 8`

`S1(config-vlan)#name Native`


**Настройка интерфейса E0/2 в качестве access (в строну PC-A)**

`S1(config)#interface e0/2`

`S1(config-if)#switchport mode access`

`S1(config-if)#switchport access vlan 3`

`S1(config-if)#no shut`

**Назначение ip-адреса Vlan 3 на коммутаторе S1**

`S1(config)#interface vlan 3`

`S1(config-if)#ip add 192.168.3.11 255.255.255.0`

`S1(config-if)#no shut`

`S1(config)#ip default-gateway 192.168.3.1`


**Настройка интерфейса E0/1 в качестве trunk (в сторону S2)**

`S1(config)#interface e0/1`

`S1(config-if)#switchport trunk encapsulation dot1q`

`S1(config-if)#switchport trunk allowed vlan 3 4`

`S1(config-if)#switchport trunk native vlan 8`

`S1(config-if)#no shut`


**Настройка интерфейса E0/0 в качестве trunk (в сторону R1)**

`S1(config)#interface e0/0`

`S1(config-if)#switchport trunk encapsulation dot1q`

`S1(config-if)#switchport trunk allowed vlan 3 4`

`S1(config-if)#switchport trunk native vlan 8`

`S1(config-if)#no shut`

**Настройка не использующихся интерфейсов на S1**

`S1(config)#interface e0/3`

`S1(config-if)#switchport access vlan 7`

`S1(config-if)#shut`

`S1(config)#interface range e1/0-3,e2/0-3,e3/0-3,e4/0-3,e5/0-3`

`S1(config-if-range)#switchport mode access`

`S1(config-if-range)#switchport access vlan 7`

`S1(config-if)#shut`

**Смотрим общее состояние Vlan на S1**


`S1#sh vlan brief`

`VLAN Name                             Status    Ports`
---- -------------------------------- --------- -------------------------------
`1    default                          active ``   

`3    Management                       active    Et0/2`

`4    Operations                       active`

`7    ParkingLot                       active    Et0/3, Et1/0, Et1/1, Et1/2`

 `                                               Et1/3, Et2/0, Et2/1, Et2/2`
 `                                               Et2/3, Et3/0, Et3/1, Et3/2`
 `                                               Et3/3,Et4/0, Et4/1, Et4/2`
 `                                               Et4/3, Et5/0, Et5/1, Et5/2`
 `                                               Et5/3`

`8    Native                           active`

`1002 fddi-default                     act/unsup`

`1003 token-ring-default               act/unsup`

`1004 fddinet-default                  act/unsup`

`1005 trnet-default                    act/unsup`


**Настройка коммутатора S2**

**Создаем Vlan-ы**

`S2#conf t`

`S2(config)#vlan 3`

`S2(config-vlan)#name Management`

`S2(config)#vlan 4`

`S2(config-vlan)#name Operations`

`S2(config-vlan)#vlan 7`

`S2(config-vlan)#name ParkingLot`

`S2(config-vlan)#vlan 8`

`S2(config-vlan)#name Native`

**Настройка интерфейса E0/3 в качестве access (в строну PC-B)**

`S2(config)#interface e0/3`

`S2(config-if)#switchport mode access`

`S2(config-if)#switchport access vlan 4`

`S2(config-if)#no shut`

**Назначение ip-адреса Vlan 3 на коммутаторе S2**

`S2(config)#interface vlan 3`

`S2(config-if)#ip add 192.168.3.12 255.255.255.0`

`S2(config-if)#no shut`

`S2(config)#ip default-gateway 192.168.3.1`




**Настройка интерфейса E0/1 в качестве trunk (в сторону S1)**

`S2(config)#interface e0/1`

`S2(config-if)#switchport trunk encapsulation dot1q`

`S2(config-if)#switchport trunk allowed vlan 3 4`

`S2(config-if)#switchport trunk native vlan 8`

`S2(config-if)#no shut`


**Настройка не использующихся интерфейсов на S2**

`S2(config)#interface range e1/0-3`

`S2(config-if-range)#switchport mode access`

`S2(config-if-range)#switchport access vlan 7`

`S2(config-if-range)#shut`

`S2(config)#interface range e2/0-3`

`S2(config-if-range)#switchport mode access`

`S2(config-if-range)#switchport access vlan 7`

`S2(config-if-range)#shut`

`S2(config)#interface range e3/0-3,e4/0-3`

`S2(config-if-range)#switchport mode access`

`S2(config-if-range)#switchport access vlan 7`

`S2(config-if-range)#shut`

`S2(config)#interface range e5/0-3`

`S2(config-if-range)#switchport mode access`

`S2(config-if-range)#switchport access vlan 7`

`S2(config-if-range)#shut`

`S2(config)#interface e0/0`

`S2(config-if)#switchport mode access`

`S2(config-if)#switchport access vlan 7`

`S2(config-if)#shut`

**Смотрим общее состояние Vlan на S2**

`S2#sh vlan brief`

`VLAN Name                             Status    Ports`
---- -------------------------------- --------- -------------------------------
`1    default                          active`

`3    Management                       active`

`4    Operations                       active    Et0/3`

`7    ParkingLot                       active    Et0/0, Et0/2, Et1/0, Et1/1`

 `                                               Et1/2, Et1/3, Et2/0, Et2/1`
 `                                               
 `                                               Et2/2, Et2/3, Et3/0, Et3/1`
 `                                            
  `                                              Et3/2, Et3/3, Et4/0, Et4/1`
  `                                              
 `                                               Et4/2, Et4/3, Et5/0, Et5/1`
  `                                             
  `                                              Et5/2, Et5/3`
                                                
`8    Native                           active`

`1002 fddi-default                     act/unsup`

`1003 token-ring-default               act/unsup`

`1004 fddinet-default                  act/unsup`

`1005 trnet-default                    act/unsup`


**Настройка роутера R1**

`R1#conf t`

`R1(config)#int e0/1`

`R1(config-if)no shut`

`R1(config-subif)#interface e0/1.3`

`R1(config-subif)#encapsulation dot1Q 3`

`R1(config-subif)#ip address 192.168.3.1 255.255.255.0`

`R1(config-subif)#interface e0/1.4`

`R1(config-subif)#encapsulation dot1Q 4`

`R1(config-subif)#ip address 192.168.4.1 255.255.255.0`

`R1(config-subif)#`

**Проверяем работу**

**PC-A**

`VPCS> ping 192.168.3.1`

`84 bytes from 192.168.3.1 icmp_seq=1 ttl=255 time=1.153 ms`
`84 bytes from 192.168.3.1 icmp_seq=2 ttl=255 time=1.042 ms`
`84 bytes from 192.168.3.1 icmp_seq=3 ttl=255 time=0.867 ms`
`84 bytes from 192.168.3.1 icmp_seq=4 ttl=255 time=0.962 ms`
`84 bytes from 192.168.3.1 icmp_seq=5 ttl=255 time=0.817 ms`

`VPCS> ping 192.168.4.3`

`84 bytes from 192.168.4.3 icmp_seq=1 ttl=63 time=4.806 ms`
`84 bytes from 192.168.4.3 icmp_seq=2 ttl=63 time=2.564 ms`
`84 bytes from 192.168.4.3 icmp_seq=3 ttl=63 time=12.130 ms`
`84 bytes from 192.168.4.3 icmp_seq=4 ttl=63 time=2.501 ms`
`84 bytes from 192.168.4.3 icmp_seq=5 ttl=63 time=1.367 ms`

`VPCS> ping 192.168.3.12`

`84 bytes from 192.168.3.12 icmp_seq=1 ttl=255 time=1.195 ms`
`84 bytes from 192.168.3.12 icmp_seq=2 ttl=255 time=1.030 ms`
`84 bytes from 192.168.3.12 icmp_seq=3 ttl=255 time=0.951 ms`
`84 bytes from 192.168.3.12 icmp_seq=4 ttl=255 time=1.265 ms`
`84 bytes from 192.168.3.12 icmp_seq=5 ttl=255 time=1.243 ms`


**PC-B**

`VPCS> trace 192.168.3.3`

`trace to 192.168.3.3, 8 hops max, press Ctrl+C to stop`

 `1   192.168.4.1   1.143 ms  0.691 ms  0.625 ms`
 
 `2   *192.168.3.3   2.443 ms (ICMP type:3, code:3, Destination port unreachable)`
 
 
`VPCS> ping 192.168.3.3`

`84 bytes from 192.168.3.3 icmp_seq=1 ttl=63 time=3.638 ms`
`84 bytes from 192.168.3.3 icmp_seq=2 ttl=63 time=1.827 ms`
`84 bytes from 192.168.3.3 icmp_seq=3 ttl=63 time=1.660 ms`
`84 bytes from 192.168.3.3 icmp_seq=4 ttl=63 time=1.742 ms`
`84 bytes from 192.168.3.3 icmp_seq=5 ttl=63 time=1.988 ms`


