# Лабораторная работа - Настройка NAT для IPv4
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/20.%20NAT/LAB_shema_seti.PNG">
</p>
  

<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/20.%20NAT/LAB_tablica_adresacii.PNG">
</p>
  
## Часть 1. Создание сети и настройка основных параметров устройства
1. Подключил устройтсва согласно топологии.
2. Произвел базовые настройки коммутаторов.
3. Произвел юазовые настройки маршрутизаторов.
  
## Часть 2. Настройка и проверка NAT для IPv4.
1.Настроил NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228
```
R1(config)#ip access-list standard NAT_POOL - создаем ACL, в нем указываем какие хосты разрешены для трансляции
R1(config-std-nacl)#permit 192.168.1.0 0.0.0.255
R1(config-std-nacl)#deny any 
R1(config-std-nacl)#exit 
R1(config)#ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248 - создаем пул NAT и указываем диапазон используемых IP адресов
R1(config)#ip nat inside source list NAT_POOL pool PUBLIC_ACCESS - Настраиваем перевод, связывая ACL и пул с процессом преобразования.
R1(config)#interface gigabitEthernet 0/0/1 - определили,что интерфейс смотрит в локальную сеть
R1(config-if)#ip nat inside 
R1(config-if)#exit
R1(config)#interface gigabitEthernet 0/0/0 - определили,что интерфейс смотрит в внешнюю сеть
R1(config-if)#ip nat outside 
```
2. Проверка конфигурации.
  
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/20.%20NAT/LAB_NAT_translation1.PNG">

Были произведенены пинги с 4х устройств, ПК А, ПК Б, S1 и S2, пинг S2 оказался неудачным, так как закончились свободные IP адреса в пуле.
  
