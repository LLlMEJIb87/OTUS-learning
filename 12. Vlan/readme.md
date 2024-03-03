# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями 
_ _ _

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/12.%20Vlan/topologiya.PNG">
</p>

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/12.%20Vlan/tablica_adresacii.PNG">
</p>

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/12.%20Vlan/tablica_vlan.PNG">
</p>

1. Подключил сеть согласно топологии.
2. Сделал первоначальные настройки коммутаторов и маршрутизатора.
3. Настройка коммутатора S1:
- создал vlan 10,20,30,999,1000 согласно таблицы vlan, задал имя для каждой
- для возможности удаленного подключения к коммутатору по сети настроил ip на виртуальном интефейсе коммутатора vlan 10 S1(config-if)#ip address 192.168.10.11 255.255.255.0
- установил параметр шлюз по умолчанию S1(config)#ip default-gateway 192.168.10.1
- все неиспользуемые порты назначил в vlan 999 и административно отключил их
```
Switch(config)#vlan 999
Switch(config-vlan)#name parking-lot
Switch(config-vlan)#exit
Switch(config)#interface range fastEthernet 0/1-23
Switch(config-if-range)#switchport mode access 
Switch(config-if-range)#switchport access vlan 999
Switch(config-if-range)#shutdown 
Switch(config-if-range)#exit
```
- назначил порт F0/24, к которому подключен хост PC-A в vlan 20 sales
- командой show vlan brief убедился, что vlan назначены на правильные интерфейсы

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/12.%20Vlan/show_vlan_S1.PNG">

- настроил порты G0/1,G0/2 в режиме trunk, указал какие vlan могут проходить по trunk, назначил native vlan
```
S1(config)#interface range gigabitEthernet 0/1-2
S1(config-if-range)#switchport mode trunk
S1(config-if-range)#switchport trunk native vlan 1000
S1(config-if-range)#switchport trunk allowed vlan 10,20,30,1000
```

- проверил  настройки trunk портов командой show interfaces trunk

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/12.%20Vlan/show_trunk_S1.PNG">

4. Настройка коммутатора S2:
- создал vlan 10,20,30,999,1000 согласно таблицы vlan, задал имя для каждой
- для возможности удаленного подключения к коммутатору по сети настроил ip на виртуальном интефейсе коммутатора vlan 10 S1(config-if)#ip address 192.168.10.12 255.255.255.0
- установил параметр шлюз по умолчанию S1(config)#ip default-gateway 192.168.10.1
- все неиспользуемые порты назначил в vlan 999 и административно отключил их
- назначил порт F0/24, к которому подключен хост PC-B в vlan 30 operations
- командой show vlan brief убедился, что vlan назначены на правильные интерфейсы
- настроил порт G0/2 в режиме trunk, указал какие vlan могут проходить по trunk, назначил native vlan 1000
- проверил  настройки trunk порта командой show interfaces trunk
5. Настройка маршрутизатора R1:
- включил интерфейс G0/0/1
- Настроил sub интерфейсы на интерфейсе G0/0/1, назначил каждому vlan в соответсвие c таблицой vlan, настроил на sub интерфейсах адресацию ipv4:
```
Router(config)#interface gigabitEthernet 0/0/1.10
Router(config-subif)#encapsulation dot1Q 10
Router(config-subif)#ip address 192.168.10.1
Router(config-subif)#description upravlenie
Router(config-subif)#exit
Router(config)#interface gigabitEthernet 0/0/1.20
Router(config-subif)#encapsulation dot1Q 20
Router(config-subif)#ip address 192.168.20.1
Router(config-subif)#description sales
Router(config-subif)#exit
Router(config)#interface gigabitEthernet 0/0/1.30
Router(config-subif)#encapsulation dot1Q 30
Router(config-subif)#ip address 192.168.30.1
Router(config-subif)#description operations
Router(config-subif)#exit
Router(config)#interface gigabitEthernet 0/0/1.1000
Router(config-subif)#encapsulation dot1Q 1000
Router(config-subif)#description native
Router(config-subif)#exit
```
- командой show ip interfaces brief убедился, что нужные интерфейсы имееют состояние UP

