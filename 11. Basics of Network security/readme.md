# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH<br>
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/11.%20Basics%20of%20Network%20security/topologiya.PNG">
</p>
<div align="center">

<div align="center">
__Таблица адресации:__
| Устройство       | Интерфейс         | IP адрес / префикс | Шлюз по умолчанию
| ------------- |:------------------:|------------------:|------------------:|
| R1     | G0/0/1 | 192.168.1.1/24 | - |
| S1 | Vlan1|  192.168.1.11/24 | 192.168.1.1 |
| PC - A | NIC |  192.168.1.3/24 | 192.168.1.1 |
</div>

1. Подключил сеть согласно топологии
2. Установил статическую адресацию на хосте PC-A 
3. Сделал первоначальные настройки коммутатора:
- Задал имя "hostname"S1
- Для возможности удаленного подключения к коммутатору по сети настроил ip на виртуальном интефейсе коммутатора vlan 1 S1(config-if)#ip address 192.168.1.11 255.255.255.0
- Установил параметр шлюз по умолчанию S1(config)#ip default-gateway 192.168.1.1
- создал баннер предупреждение S1(config)# banner motd "This is a secure system. Authorized Access Only!"
4. Приступил к базовым настройкам безопасности:
- Установил пароль на вход в привилегированный режим "enable secret" cisco
- Командой "service password-encryption" задал шифрование паролей
- Создал локального пользователя и задал уровень привелегий S1(config)#username adminssh privilege 15 secret cisco
- Ограничил доступ к коммутатору через порт консоли, командой "login local" активировал запрос имени и пароля при подключении, чтобы консольные сообщения не прерывали выполнение команд, использовал параметр logging synchronous.
```
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#login local
S1(config-line)#exit
```
5. Приступил к настройки активации подключения по SSH
- настроил доменное имя S1(config)#ip domain-name venya.net 
- сгенерировал криптоключ командой "crypto key generate rsa  general-keys modulus" 1024
- активировал работу 2 версии SSH протокола S1(config)#ip ssh version 2
- активировал возможность виртуального подключения по протоколу SSH
```
S1(config)#line vty 0 4 
S1(config-line)#login local
S1(config-line)#transport input ssh 
S1(config-line)#exit
```
- проверил возможность подключения по протоколу ssh c PC-A

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/11.%20Basics%20of%20Network%20security/ssh_s1.PNG">

6. Приступил к настройке маршрутизатора, выполнил первоначальные настройки
- Задал имя "hostname"R1
- Активировал маршрутизацию R1(config)#ip routing
- Настроил сетевой интерфейс, к которому подключен коммутатор S1 G0/1
```
R1(config)#interface gigabitEthernet0/1
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#exit
```
- создал баннер предупреждение S1(config)# banner motd "This is a secure system. Authorized Access Only!"
- проверил доступность узлов в сети

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/11.%20Basics%20of%20Network%20security/ping.PNG">

7.Приступил к базовым настройкам безопасности:
- Установил минимально допкстимую длину пароля R1(config)#security passwords min-length 5
- Установил пароль на вход в привилегированный режим "enable secret" cisco
- Командой "service password-encryption" задал шифрование паролей
- Для предотвращения взлома путем подбора паролей использовал команду R1(config)#login block-for 120 attempts 3 within 60
- Создал локального пользователя и задал уровень привелегий S1(config)#username adminssh privilege 15 secret cisco
- Ограничил доступ к маршрутизатору через порт консоли, командой "login local" активировал запрос имени и пароля при подключении, чтобы консольные сообщения не прерывали выполнение команд, использовал параметр logging synchronous.
```
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#login local
S1(config-line)#exit
```
8. Приступил к настройки активации подключения по SSH
- настроил доменное имя S1(config)#ip domain-name venya.net 
- сгенерировал криптоключ командой "crypto key generate rsa  general-keys modulus" 1024
- активировал работу 2 версии SSH протокола S1(config)#ip ssh version 2
- активировал возможность виртуального подключения по протоколу SSH
```
S1(config)#line vty 0 4 
S1(config-line)#login local
S1(config-line)#transport input ssh 
S1(config-line)#exit
```
- проверил возможность подключения по протоколу ssh c PC-A на R1, c R1 на S1, с S1 на R1

<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/11.%20Basics%20of%20Network%20security/ssh_r1_s1_r1.PNG">