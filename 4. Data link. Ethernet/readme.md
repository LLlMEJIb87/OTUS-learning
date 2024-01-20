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
| PC - B | NIC    | 192.168.1.2/24   |
</div>

1. Подключил сеть согласно топологии.
2. Настроил адресацию узлов ПК
3. Настроил адресацию и базовые настройки коммутаторов
4. В командной строке узлов ПК при помощи команды "ipconfig" посмотрел MAC адреса:
   - PC-A  FE80::20A:41FF:FE00:56D7
   - PC-B  FE80::201:63FF:FEA9:29BC
5. На коммутаторах при помощи команды "show" посмотрел MAC адреса физических интерфейсов к которым подключены узлы ПК
   - S1 interface f0/1 000a.f30a.2101
   - S2 interface f0/1 0060.2f40.0301
6. На коммуаторе S1 очистил таблицу коммутации командой "clear" mac addres-table. Командой "show" mac addres-table убедился, что таблица пустая
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/4.%20Data%20link.%20Ethernet/pustaya-tablica-mac.PNG">
7.