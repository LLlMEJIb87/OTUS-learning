# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах 
_ _ _

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/8.%20IPv6%20addressing/shema_seti.PNG">
</p>

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/8.%20IPv6%20addressing/tablica_adresacii.PNG">
</p>

1. Собрал и подключил тестовый стенд в эмуляторе cisco packet tracer
2. Сделал первоначальную настройку коммутатора и маршрутизатора
3. Настройка коммутатора:
- Для возможности адресации IPv6 установил шаблон dual-ipv4-and-ipv6 в качестве шаблона SDM по умолчанию командой sdm prefer dual-ipv4-and-ipv6 default, перезагрузил коммутатор
- Задал IPv6 адрес коммутатору S1
```
S1(config)#interface vlan 1
S1(config-if)#no shutdown
S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
S1(config-if)#exit
```
4. Настройки маршрутизатора:
- Активировал маршрутизацию на роутере командой IPv6 unicast-routing
- Настроил интерфейсы
```
R1(config)#Interface GigabitEthernet0/0/0
R1(config-if)#no shutdown
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#exit
```
```
R1(config)#interface g0/0/1
R1(config-if)#no shutdown
R1(config-if)#2001:db8:acad:1::1/64
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#exit
```
5. Настроил  интерфейсы на PC-A и PC-B согласно схемы.
6. Проверил доступность узлов PC-A и PC-B командой ping
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/8.%20IPv6%20addressing/ping_PC_A_to_B.PNG">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/8.%20IPv6%20addressing/ping_PC_B_to_A.PNG">