# Просмотр таблицы MAC-адресов коммутатора 
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/shema-seti.PNG">
</p>
<div align="center">

__Таблица адресации:__

| Устройство       | Интерфейс         | IP адрес / префикс |
| ------------- |:------------------:|------------------:|
| S1     | VLAN 1 | 192.168.1.100/24 | 
| S2     | VLAN 1 | 192.168.1.200/24 | 
| PC - A | NIC    | 192.168.1.2/24   | 
| PC - B | NIC    | 192.168.1.5/24   |
</div>

1. Подключил сеть согласно топологии.
2. Настроил адресацию узлов ПК
3. Настроил адресацию и базовые настройки коммутаторов
4. В командной строке узлов ПК при помощи команды "ipconfig/all" посмотрел MAC адреса:
- PC-A  000a.4100.56d7
- PC-B  0001.63a9.29bc       
5. На коммутаторах при помощи команды "show" interface G0/1 посмотрел MAC адреса физических интерфейсов которыми скомутированы между собой S1 и S2
- S1 interface G0/1 000a.f30a.2119
- S2 interface G0/1 0060.2f40.0319
6. На коммуаторе S1 очистил таблицу коммутации командой "clear" mac address-table. Командой "show" mac address-table убедился, что таблица пустая
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/pustaya-tablica-mac.PNG">
7. C ПК B послал ICMP запрос на ПК А , через командную строку командой "ping" 192.168.1.2
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/ping-B-to-A.PNG">
8. На коммутаторе S1 посмотрел таблицу комутации после ICMP запроса
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/tablica-mac.PNG">

Из таблицы коммутации я вижу, что есть активные узлы за портами коммутатора:
- за портом fastethernet0/1 MAC 000a.4100.56d7 ПК A
- за портом gigabitethernet0/1 MAC 0001.63a9.29bc ПК B и MAC интерфейса G0/1 свитча S2 0060.2f40.0319
9. С ПК B пропинговал ПК А, S1, S2. Далее на ПК B командой "arp" -a посмотрел arp таблицу
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/arp.PNG">
Основная задача протокола ARP – получить L2 адрес устройства при известном L3 адресе устройства, для взаимодействия устройтсв на канальном уровне L2.
