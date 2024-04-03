# Лабораторная работа - Конфигурация безопасности коммутатора 
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/16.%20Principles%20of%20network%20security/topologiya.PNG">
</p>
  
## Часть 1. Настройка основного сетевого устройства
1. Создал сеть согласно топологии
2. Настроил маршрутизатор R1
```
Router>enable
Router#configure terminal
Router(config)#hostname R1
R1(config)#no ip domain lookup
R1(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.9
R1(config)#ip dhcp excluded-address 192.168.10.201 192.168.10.202
R1(config)#ip dhcp pool Students
R1(dhcp-config)#network 192.168.10.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.10.1
R1(dhcp-config)#domain-name CCNA2.Lab-11.6.1
R1(dhcp-config)#interface Loopback0
R1(config-if)#ip address 10.10.1.1 255.255.255.0
R1(config-if)#interface GigabitEthernet0/0/1
R1(config-if)# description Link to S1
R1(config-if)#ip dhcp relay information trusted
R1(config-if)#ip address 192.168.10.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#line con 0
R1(config-line)# logging synchronous
R1(config-line)# exec-timeout 0 0
R1(config-line)#exit
```
3. Настройка основных параметров коммутаторов
- S1
```
Switch(config)#no ip domain-lookup 
Switch(config)#hostname S1
S1(config)#interface fastEthernet 0/5
S1(config-if)#description link_na_R1
S1(config-if)#exit
S1(config)#interface fastEthernet 0/6
S1(config-if)#description link_na_PCA
S1(config-if)#exit
S1(config)#interface fastEthernet 0/1
S1(config-if)#description link_na_S2
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.10.1
```
- S2
```
Switch(config)#no ip domain-lookup 
Switch(config)#hostname S2
S2(config)#interface fastEthernet 0/1
S2(config-if)#description link_na_S2
S2(config-if)#exit
S2(config)#interface fastEthernet 0/18
S2(config-if)#description link_na_PCB
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.10.1
```
## Часть 2. Настройка сетей VLAN на коммутаторах.
1. Сконфигурировал VLAN 10 на коммутаторах S1 и S2
- S1
```
S1(config)#vlan 10
S1(config-vlan)#name Managment
```
- S2
```
S2(config)#vlan 10
S2(config-vlan)#name Managment
```
2. Cконфигурировал SVI для VLAN 10 на коммутаторах S1 и S2
- S1
```
S1(config)#interface vlan 10
S1(config-if)#description Managment
S1(config-if)#ip address 192.168.10.200 255.255.255.0
```
- S2
```
S2(config)#interface vlan 10
S2(config-if)#description Managment
S2(config-if)#ip address 192.168.10.201 255.255.255.0
```
3. Настроил VLAN 333 c именем Native на коммутаторах S1 и S2
- S1
```
S1(config)#vlan 333
S1(config-vlan)#name Native
```
- S2
```
S2(config)#vlan 333
S2(config-vlan)#name Native
```
4. Настроил VLAN 999 c именем ParkingLot на коммутаторах S1 и S2
- S1
```
S1(config)#vlan 999
S1(config-vlan)#name ParkingLot
```
- S2
```
S1(config)#vlan 999
S1(config-vlan)#name ParkingLot
```

## Часть 3. Настройки безопасности коммутатора.