# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа.
_ _ _
  
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/19.%20ACL/Lab_topologia.PNG">
</p>
  

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/19.%20ACL/Lab_topologia_vlan.PNG">
</p>
  
## Часть 1. Создание сети и настройка основных параметров устройства
  
1. Подключил устройства согласно топологии
2. Произвел базовую настройку маршрутизаторов
- R1
```
Router>en
Router#configure t
Router(config)#hostname R1
R1(config)#no ip domain lookup 
R1(config)#enable secret class
R1(config)#service password-encryption
R1(config)#login block-for 120 attempts 3 within 60
R1(config)#username admin privilege 15 secret cisco
R1(config)#line console 0
R1(config-line)#logging synchronous 
R1(config-line)#login local
R1(config-line)#exit
R1(config)#banner motd "This is a secure system. Authorized Access Only!"
R1(config)#do wr
```
- R2
```
Router>en
Router#configure t
Router(config)#hostname R2
R2(config)#no ip domain lookup 
R2(config)#enable secret class
R2(config)#service password-encryption
R2(config)#login block-for 120 attempts 3 within 60
R2(config)#username admin privilege 15 secret cisco
R2(config)#line console 0
R2(config-line)#logging synchronous 
R2(config-line)#login local
R2(config-line)#exit
R2(config)#banner motd "This is a secure system. Authorized Access Only!"
R2(config)#do wr
```
  
3. Настроил базовые параметры коммутаторов
- S1
```
Switch>en
Switch#configure t
Switch(config)#hostname S1
S1(config)#no ip domain lookup 
S1(config)#enable secret class
S1(config)#service password-encryption
S1(config)#username admin privilege 15 secret cisco
S1(config)#line console 0
S1(config-line)#logging synchronous 
S1(config-line)#login local
S1(config-line)#exit
S1(config)#banner motd "This is a secure system. Authorized Access Only!"
S1(config)#do wr
```
- S2
```
Switch>en
Switch#configure t
Switch(config)#hostname S2
S2(config)#no ip domain lookup 
S2(config)#enable secret class
S2(config)#service password-encryption
S2(config)#username admin privilege 15 secret cisco
S2(config)#line console 0
S2(config-line)#logging synchronous 
S2(config-line)#login local
S2(config-line)#exit
S2(config)#banner motd "This is a secure system. Authorized Access Only!"
S2(config)#do wr
```
  
## Часть 2. Настройка сетей VLAN на коммутаторах.
  
1. Создал сети Vlan на коммутаторах
- S1
```
S1(config)#vlan 20
S1(config-vlan)#name Managment
S1(config-vlan)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#vlan 40
S1(config-vlan)#name Sales
S1(config-vlan)#vlan 999
S1(config-vlan)#name ParkingLot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#interface vlan 20
S1(config-if)#ip address 10.20.0.2 255.255.255.0
S1(config-if)#exit
S1(config)#ip default-gateway 10.20.0.1
S1(config)#interface range f0/2-4,f0/7-24,g0/1-2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```
- S2
```
S2(config)#vlan 20
S2(config-vlan)#name Managment
S2(config-vlan)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#vlan 40
S2(config-vlan)#name Sales
S2(config-vlan)#vlan 999
S2(config-vlan)#name ParkingLot
S2(config-vlan)#vlan 1000
S2(config-vlan)#name Native
S2(config-vlan)#exit
S2(config)#interface vlan 20
S2(config-if)#ip address 10.20.0.3 255.255.255.0
S2(config-if)#exit
S2(config)#ip default-gateway 10.20.0.1
S2(config)#interface range f0/2-4,f0/6-24,f0/19-24,g0/1-2
S2(config-if-range)#switchport mode access 
S2(config-if-range)#switchport access vlan 999
S2(config-if-range)#shutdown

```
2. Назначил сети VLAN соответствующим интерфейсам коммутатора.
- S1
```
S1(config)#interface fastEthernet 0/6
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 30
```
- S2
```
S2(config)#interface fastEthernet 0/5
S2(config-if)#switchport mode access 
S2(config-if)#switchport access vlan 20
S2(config-if)#exit
S2(config)#interface fastEthernet 0/18
S2(config-if)#switchport access vlan 40
```

## Часть 3. Настроил транки (магистральные каналы)
- S1
```
S1(config)#interface fastEthernet 0/1
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk allowed vlan 20,30,40,1000
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport nonegotiate 
S2(config-if)#exit
S1(config)#interface fastEthernet 0/5
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk allowed vlan 20,30,40,1000	
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport nonegotiate 
```
- S2
```
S2(config)#interface fastEthernet 0/1
S2(config-if)#switchport mode trunk 
S2(config-if)#switchport trunk allowed vlan 20,30,40,1000
S2(config-if)#switchport trunk native vlan 1000
S2(config-if)#switchport nonegotiate 
```
## Часть 4. Настроил маршрутизацию
- R1
```
R1(config)#interface gigabitEthernet 0/0/1.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip address 10.20.0.1 255.255.255.0
R1(config-subif)#description Managment
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 10.30.0.1 255.255.255.0
R1(config-subif)#description Operations
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1.40
R1(config-subif)#encapsulation dot1Q 40
R1(config-subif)#ip address 10.40.0.1 255.255.255.0
R1(config-subif)#description Sales
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1.1000
R1(config-subif)#encapsulation dot1Q 1000 native
R1(config-subif)#description native
R1(config-subif)#exit
R1(config)#interface gigabitEthernet 0/0/1
R1(config-if)#no shutdown 
```
- R2
```
R2(config)#interface gigabitEthernet 0/0/1
R2(config-if)#ip address 10.20.0.4 255.255.255.0
R2(config-if)#no shutdown 
R2(config-if)#exit
R2(config)#ip route 172.16.1.0 255.255.255.0 10.20.0.1
```

## Часть 5. Настроил удаленный доступ по SSH
- R1
```
R1(config)#username SSHadmin privilege 15 secret $cisco123!
R1(config)#ip domain-name venya.net
R1(config)#crypto key generate rsa general-keys modulus 1024
R1(config)#ip ssh version 2
R1(config)#line vty 0 5
R1(config-line)#login local
R1(config-line)#transport input ssh
R1(config-line)#exit
R1(config)#ip http secure-server - не поддерживается в cpt
R1(config)#ip http authentication local - не поддерживается в cpt
```
- R2
```
R2(config)#username SSHadmin privilege 15 secret $cisco123!
R2(config)#ip domain-name venya.net
R2(config)#crypto key generate rsa general-keys modulus 1024
R2(config)#ip ssh version 2
R2(config)#line vty 0 5
R2(config-line)#login local
R2(config-line)#transport input ssh
R2(config-line)#exit
```


- S1
```
S1(config)#username SSHadmin privilege 15 secret $cisco123!
S1(config)#ip domain-name venya.net
S1(config)#crypto key generate rsa general-keys modulus 1024
S1(config)#ip ssh version 2
S1(config)#line vty 0 5
S1(config-line)#login local
S1(config-line)#transport input ssh
S1(config-line)#exit
```
- S2
```
S2(config)#username SSHadmin privilege 15 secret $cisco123!
S2(config)#ip domain-name venya.net
S2(config)#crypto key generate rsa general-keys modulus 1024
S2(config)#ip ssh version 2
S2(config)#line vty 0 5
S2(config-line)#login local
S2(config-line)#transport input ssh
S2(config-line)#exit
```
  
## Часть 6. Проверка доступности узлов
