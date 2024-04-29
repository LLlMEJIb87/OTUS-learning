# Лабораторная работа. Настройка протокола OSPFv2 для одной области
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/18.%20OSPF/Lab_topologia.PNG">
</p>
  
## Часть 1. Создание сети и настройка основных параметров устройства
1. Подключил устройства согласно топологии
2. Произвел базовую настройку маршрутизаторов:
- R1
```
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
3. Произвел базовую настройку коммутаторов:
- S1
```
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
Switch#configure t
Switch(config)#hostname S1
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
  
## Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области
1.Настройка адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе:
- R1
```
R1(config)#int g0/0/1
R1(config-if)#ip address 10.53.0.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#interface loopback 1 
R1(config-if)#ip address 172.16.1.1 255.255.255.0
R1(config-if)#exit
R1(config)#router ospf 56
R1(config-router)#router-id 1.1.1.1
R1(config-router)#network 10.53.0.0 0.0.0.255 area 0 
R1(config-router)#exit
```

- R2
```
R2(config)#int g0/0/1
R2(config-if)#ip address 10.53.0.2 255.255.255.0
R2(config-if)#no shutdown 
R2(config-if)#exit
R2(config)#interface loopback 1 
R2(config-if)#ip address 192.168.1.1 255.255.255.0
R2(config-if)#exit
R2(config)#router ospf 56
R2(config-router)#rout
R2(config-router)#router-id 2.2.2.2
R2(config-router)#exit
R2(config)#int gigabitEthernet 0/0/1
R2(config-if)#ip ospf 56 area 0
R2(config-if)#exit
R2(config)#interface loopback 1
R2(config-if)#ip ospf 56 area 0
R2(config-if)#exit
```
  
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/18.%20OSPF/Lab_show_ip_ospf.PNG">
  
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/18.%20OSPF/Lab_ping.PNG">
  

## Часть 3.Оптимизация и проверка конфигурации OSPFv2 для одной области

