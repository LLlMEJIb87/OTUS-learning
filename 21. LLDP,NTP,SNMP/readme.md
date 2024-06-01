# Лабораторная работа - Настройка протоколов CDP, LLDP и NTP
_ _ _

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/21.%20LLDP%2CNTP%2CSNMP/LAB_topologia.PNG">
</p>

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/21.%20LLDP%2CNTP%2CSNMP/LAB_adresacia.PNG">
</p>

## Часть 1. Создание сети и настройка основных параметров устройства
1. Создал сеть согласно топологии
2. Настроил базовые параметры маршрутизатора
3. Настроил базовые параметры коммутаторов
  
## Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/21.%20LLDP%2CNTP%2CSNMP/LAB_show_CDP.PNG">


<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/21.%20LLDP%2CNTP%2CSNMP/LAB_show_CDP_2.PNG">
  
```
S1#show cdp traffic - не поддерживается в CPT
```

Отключил СDP на всех устройствах
 
```
R1(config)#no cdp run
```
```
S1(config)#no cdp run
```
```
S2(config)#no cdp run
```

## Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP
Включил LLDP
```
R1(config)#LLDP run
```
```
S1(config)#LLDP run
```
```
S2(config)#LLDP run
```
```
S1#show lldp entry S2 - не поддерживается в CPT
```
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/21.%20LLDP%2CNTP%2CSNMP/LAB_show_lldp.PNG">
  
## Часть 4. Настройка NTP
```
R1#clock set 14:00:00 01 jun 2024
R1#conf t
R1(config)#ntp master 4 
```
```
S1(config)#ntp server 10.22.0.1 
S1(config)#end
S1#show clock
14:5:25.310 UTC Sat Jun 1 2024
```
```
S2(config)#ntp server 10.22.0.1 
S2(config)#end
```