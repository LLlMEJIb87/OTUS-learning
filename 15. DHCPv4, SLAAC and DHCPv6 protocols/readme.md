# Лабораторная работа - Реализация DHCPv4 и DHCPv6
_ _ _
## DHCPv4
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_dhcp4_topologia.PNG">
</p>

### Часть 1.Создание сети и настройка основных параметров устройства
1. Создал схему адресации разбив сеть 192.168.1.0/24 на подсети:
- 192.168.1.0/26 - (клиентская VLAN на R1)
- 192.168.1.64/27 - (управляющая VLAN на R1)
- 192.168.1.96/28 - (клиентская сеть на R2)
2. Подключил устройства согласно топологии
3. Произвел базовую настройку маршрутизаторов
4. Настроил маршрутизацию между сетями VLAN на маршрутизаторе R1
```
R1(config)#interface gigabitEthernet 0/0/1
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#interface gigabitEthernet 0/0/1.100
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1.200
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1.1000
R1(config-subif)#encapsulation dot1Q 1000 native
R1(config-subif)#exit
```
5.Настроил G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов
- настроил G0/0/1 на R2
```
R2(config)#interface g0/0/1
R2(config-if)#no shutdown 
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#exit
```
- настроил G0/0/0 на R1
```
R1(config)#interface gigabitEthernet 0/0/0
R1(config-if)#no shutdown 
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#exit
```
- настроил G0/0/0 на R2
```
R2(config)#interface gigabitEthernet 0/0/0
R2(config-if)#no shutdown 
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#exit
```
- настроил маршрут по умолчанию на R1
```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
- - настроил маршрут по умолчанию на R2
```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
- сохранил кофиг
6. Настроил базовые параметры каждого коммутатора
7. Создал сети VLAN на коммутаторе S1, настроил и активировал интерфейс управления, неиспользуемы порты назначил в vlan 999 и административно отключил их
```
S1(config)#vlan 100
S1(config-vlan)#name clients
S1(config-vlan)#exit
S1(config)#vlan 200
S1(config-vlan)#name upravlenie
S1(config-vlan)#exit
S1(config)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#vlan 999
S1(config-vlan)#name Parking_lot
S1(config-vlan)#exit
S1(config)#interface vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.65
S1(config)#interface range f0/1-4,f0/7-24,G0/1-2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown 
S1(config-if-range)#exit
```
8. Настроил активные интерфейсы
```
S1(config)#interface fastEthernet 0/6
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 100
S1(config-if)#exit
S1(config)#interface fastEthernet 0/5
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#exit
```
1