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
- настроил порты G0/1,G0/2 в режиме trunk, указал какие vlan могут проходить по trunk, установил native vlan