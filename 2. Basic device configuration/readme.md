# Базовая настройка коммутатора Cisco<br>
_ _ _
1. Подключился к коммутатору ( в порт console) консольным кабелем. Для входа в СLI использовал ПО PUTTY.	
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/podkluchenie_konsol'u.PNG">
</p>
<div align="center">

__Таблица адресации:__

| Устройство       | Интерфейс         | IP адрес / префикс |
| ------------- |:------------------:|------------------:|
| S1     | VLAN 1 | 192.168.1.1/24 | 
| PC - A | NIC |  192.168.1.2/24 | 
</div>

2. Вошёл в привилегированный режим "enable" и убедился командой "show" startup-config, что коммутатор имееет настройки по умолчанию.
3. Вошёл в глобальный режим конфигурации "configure terminal" и командой "hostname"S1  сменил имя коммутатора.
4. Отключил разрешение имен командой "no ip domain lookup" для того, чтобы коммутатор неправильно введённую команду не интепретировал как имя хоста для подключения по Telnet
5. Командой "service password-encryption" задал шифрование паролей
6. Ограничил доступ к коммутатору через порт консоли установив пароль cisco, командой "login" активировал запрос пароля при подключение, чтобы консольные сообщения не прерывали выполнение команд, использовал параметр logging synchronous.
```
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#login
S1(config-line)#password cisco
S1(config-line)#exit
```
7. Ограничил доступ к коммутатору при удаленном подключение (SSH,Telnet) путём конфигурирования линий виртуального терминала VTY, а именно:
```
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
```
8. Установил пароль сlass на вход в привилегированный режим командой "enable secret"
9. Для возможности удаленного подключения к коммутатору по сети настроил ip на виртуальном интефейсе коммутатора vlan 1
```
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no shutdown
```
10. Задал на ПК адрес из тойже подсети, что и коммутатор - 192.168.1.2/24 и кинул патчкорд от сетевой карты ПК до коммутатора порт FastEthernet0/6
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/podkluchenie_ethernet.PNG">

11. На ПК через командную строку командой "ping" 192.168.1.1 проверил доступность коммутатора
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/ping.PNG">
   
12. подключился по Telnet к коммутатору S1 192.168.1.1
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/telnet.PNG">

13. Сохранил конфиг командой "copy startup-config running-config"

### [ ПРОДОЛЖЕНИЕ ](https://github.com/LLlMEJIb87/OTUS-learning/blob/master/11.%20Basics%20of%20Network%20security/readme.md)

