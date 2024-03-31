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
- настроил маршрут по умолчанию на R2
```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```
- сохранил кофиг
6. Настроил базовые параметры каждого коммутатора
7. Создал сети VLAN на коммутаторе S1, настроил и активировал интерфейс управления, неиспользуемы порты назначил в vlan 999 и административно отключил их. Настроил коммутатор S2
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
```
Switch(config)#interface vlan 1
Switch(config-if)#ip address 192.168.1.98 255.255.255.224
Switch(config-if)#exit
Switch(config)#ip default-gateway 192.168.1.97
Switch(config)#exit
```
8. Настроил активные интерфейсы на коммутаторе S1
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
### Часть 2.Настройка и проверка двух серверов DHCPv4 на R1
1.Настроил R1 с пулами DHCPv4 для двух поддерживаемых подсетей
- Исключил первые 5 используемых ip адресов из  пула адресов для клиентов
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
```
- Создал и настроил пул ip адресов для клиентских подсетей 
```
R1(config)#ip dhcp pool R1_Clients_LAN
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#lease 2 12 30 - не поддерживается в CPT	
R1(dhcp-config)#exit
R1(config)#ip dhcp pool R2_Clients_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#lease 2 12 30 - не поддерживается в CPT	
R1(dhcp-config)#exit
```
2. Сохранил конфиг
3. Проверил конфигурацию сервера DHCPv4
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Show_dhcpv4.PNG">

4. Получение IP адреса на PC-A и проверка доступности, пинг R2 g0/0/1
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/ping_R2.PNG">
  
### Часть 3.Настройка и проверка DHCP-ретрансляции на R2
1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1
```
R2(config)#interface gigabitEthernet 0/0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#end
R2#copy running-config startup-config
```
2. Получение IP адреса на PC-B и проверка доступности PC-A
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/ping_B_to_A.PNG">
  
## DHCPv6
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_dhcp6_topologia.PNG">
</p>

### Часть 1.Создание сети и настройка основных параметров устройства
1. Подключил устройства согласно топологии
2. Настроил базовые параметры каждого коммутатора
3. Произвел базовую настройку маршрутизаторов
4. Настроил интерфейсы и маршрутизацию
- R1
```
R1(config)#ipv6 unicast-routing 
R1(config)#interface gigabitEthernet 0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#exit
R1(config)#interface g0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#exit
R1(config)#ipv6 route ::/0 2001:db8:acad:2::2
R1#copy running-config startup-config
```
- R2
```
R2(config)#ipv6 unicast-routing 
R2(config)#interface gigabitEthernet 0/0/0
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#ipv6 address fe80::2 link-local 
R2(config-if)#exit
R2(config)#interface gigabitEthernet 0/0/1
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64
R2(config-if)#ipv6 address fe80::1 link-local 
R2(config-if)#exit
R2(config)#ipv6 route ::/0 2001:db8:acad:2::1
R2#copy running-config startup-config
```
### Часть 2.Проверка назначения адреса SLAAC от R1
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_PCA_SLAAC.PNG">

### Часть 3.Настройка и проверка сервера DHCPv6 на R1
1. Настроил R1 для предоставления DHCPv6 без состояния для PC-A
```
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#exit
R1(config)#interface gigabitEthernet 0/0/1
R1(config-if)#ipv6 nd other-config-flag 
R1(config-if)#ipv6 dhcp server R1-STATELESS
R1(config-if)#exit
```
  
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_ping_R2.PNG">
  
### Часть 4.Настройка сервера DHCPv6 с сохранением состояния на R1
``` 
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
R1(config-dhcpv6)#exit
R1(config)#interface gigabitEthernet 0/0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
R1(config-if)#exit
```
### Часть 5.Настройка и проверка ретрансляции DHCPv6 на R2
1. Включил PC-B и проверил адрес SLAAС, который он генерирует, проверил доступность PC-A
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_PCB_SLAAC.PNG">

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/Lab_dhcp6_pingA_to_B.PNG">

2. Настроил R2 в качестве агента DHCP - ретрансляции для локальной сети G0/0/1
```
R2(config)#interface gigabitEthernet 0/0/1
R2(config-if)#ipv6 nd managed-config-flag
R2(config-if)#ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0 - CPT не поддерживает данную команду
R2(config-if)#exit
```
