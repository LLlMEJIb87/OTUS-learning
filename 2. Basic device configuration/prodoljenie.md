9. Для возможности удаленного подключения к коммутатору по сети настроил ip на виртуальном интефейсе коммутатора vlan 1
```
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no shutdown
```
10. Задал на ПК адрес из тойже подсети, что и коммутатор - 192.168.1.2/24 и кинул патчкорд от сетевой карты ПК до коммутатора порт FastEthernet0/6
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/podkluchenie_ethernet.PNG">
11. На ПК через командную строку командой "ping" 192.168.1.1 проверил доступность коммутатора.
12. подключился по Telnet к коммутатору S1 192.168.1.1
13. Сохранил конфиг командой "copy startup-config running-config"
