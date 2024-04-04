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
1. Настроил все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN  и отключил согласование DTP
- S1
```
S1(config)#interface fastEthernet 0/1
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk native vlan 333
S1(config-if)#switchport nonegotiate
S1(config-if)#exit
```
- S2
```
S2(config)#interface fastEthernet 0/1
S2(config-if)#switchport mode trunk 
S2(config-if)#switchport trunk native vlan 333
S1(config-if)#switchport nonegotiate
S2(config-if)#exit
```
2. Настроил порты доступа
- S1
```
S1(config)#interface range f0/5-6
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 10
S1(config-if-range)#exit
```
- S2
```
S2(config)#interface f0/18
S2(config-if)#switchport mode access 
S2(config-if)#switchport access vlan 10
S2(config-if)#exit
```
3. Отключил неиспользуемые порты и определил их в vlan 999
- S1
```
S1(config)#interface range f0/2-4,f0/7-24,g0/1-2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```
- S2
```
S2(config)#interface range f0/2-17,f0/19-24,g0/1-2
S2(config-if-range)#switchport mode access 
S2(config-if-range)#switchport access vlan 999
S2(config-if-range)#shutdown
```
4. Настройка безопасности порта
- S1
```
S1(config)#interface fastEthernet 0/6
S1(config-if)#switchport port-security 
S1(config-if)#switchport port-security maximum 3
S1(config-if)#switchport port-security violation restrict 
S1(config-if)#switchport port-security aging time 60
S1(config-if)#switchport port-security aging type inactivity - недоступно в CPT
```
- S2
```
S2(config)#interface fastEthernet 0/18
S2(config-if)#switchport port-security 
S2(config-if)#switchport port-security maximum 2
S2(config-if)#switchport port-security violation protect 
S2(config-if)#switchport port-security aging time 60
```

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/16.%20Principles%20of%20network%20security/show_port-security.PNG">
  
5. Настройка DHCP Snooping
```
S2(config)#ip dhcp snooping 
S2(config)#ip dhcp snooping vlan 10 
S2(config)#interface fastEthernet 0/1
S2(config-if)#ip dhcp snooping  trust 
S2(config-if)#exit
S2(config)#interface fastEthernet 0/18
S2(config-if)#ip dhcp snooping limit rate 5
S2(config-if)#exit
```

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/16.%20Principles%20of%20network%20security/show_ip_dhcp_snooping.PNG">
  
6. Настройка Portfast и BPDU guard
- S1
```
S1(config)#spanning-tree portfast default 
S1(config)#interface fastEthernet 0/6
S1(config-if)#spanning-tree bpduguard enable 
S1(config-if)#exit
```
- S2
```
S2(config)#spanning-tree portfast default 
S2(config)#interface fastEthernet 0/18
S2(config-if)#spanning-tree bpduguard enable 
S2(config-if)#exit}
```
  
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/16.%20Principles%20of%20network%20security/show_spanning-tree.PNG">
  

7. Проверил доступность хостов
  
