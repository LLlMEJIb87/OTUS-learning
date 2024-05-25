# NAT
_ _ _
NAT (от англ. Network Address Translation — «преобразование сетевых адресов») — это механизм в сетях TCP/IP, позволяющий преобразовывать IP-адреса транзитных пакетов. Также имеет названия IP Masquerading, Network Masquerading и Native Address Translation.
  
Существует 3 базовых концепции трансляции адресов: статическая (Static Network Address Translation), динамическая (Dynamic Address Translation), маскарадная (NAPT, NAT Overload, PAT).
  
## Статический NAT 
Статический NAT использует однозначное сопоставление локальных и глобальных адресов, настроенных сетевым администратором, которые остаются постоянными.
- Статическое преобразование NAT — это взаимно-однозначное сопоставление внутреннего и внешнего адресов
- Статический NAT позволяет внешним устройствам инициировать подключение к внутренним устройствам с помощью статически назначенного публичного адреса. Например, внутреннему веб-серверу может быть сопоставлен внутренний глобальный адрес, определенный таким образом, чтобы он был доступен из внешних сетей.
  
Настройка:
1. Создание сопоставления между внутренним локальным адресом и внутренними глобальными адресами с помощью команды ip nat inside source static.
2. Интерфейсы, участвующие в преобразовании, настраиваются как внутри, так и снаружи по отношению к NAT с командами ip nat inside и ip nat outside.
```
RRiht(config)#ip nat inside source static 192.168.3.254 212.22.67.74 
RRiht(config)#interface gigabitEthernet 0/0/1
RRiht(config-if)#ip nat outside 
RRiht(config-if)#exit
RRiht(config)#interface gigabitEthernet 0/0/0
RRiht(config-if)#ip nat inside 
```
```
show ip nat translations - отображает активные преобразования NAT 
```
```
clear ip nat translation * - очистить таблицу активных преобразований
```
```
show ip nat statistics -  отображает счётчики, параметры и тд.
```
```
Clear ip nat statistics
```
```
show ip nat translation verbose выводит информацию о каждом преобразовании
```
## Динамический NAT
При динамическом преобразовании NAT используется пул публичных адресов, которые назначаются в порядке очереди(«первым пришел — первымобслужили»). 
- Когда внутреннее устройство запрашивает доступ к внешней сети, динамическое преобразование NAT назначает доступный публичный IPv4-адрес из пула.
- Остальные адреса в пуле попрежнему доступны для использования. 
```
RLeft(config)#ip nat pool WS_POOL 8.8.8.4 8.8.8.8 netmask 255.255.255.0
RLeft(config)#ip access-list standard WS_ACL 
RLeft(config-std-nacl)#permit 192.168.1.0 255.255.255.0
RLeft(config-std-nacl)#exit
RLeft(config)#ip nat inside source list WS_ACL pool WS_POOL
RLeft(config)#interface gigabitEthernet 0/0/0
RLeft(config-if)#ip nat inside
RLeft(config-if)#exit
RLeft(config)#interface gigabitEthernet 0/0/1
RLeft(config-if)#ip nat outside 
RLeft(config-if)#exit
```
  
## Перегруженный NAT (PAT)
Преобразование адреса и номера порта (PAT), также называемое NAT с перегрузкой, сопоставляет множество частных IPv4-адресов одному или нескольким публичным IPv4-адресам.
- Если маршрутизатор NAT получает пакет от клиента, он использует свой номер порта источника, чтобы уникальным образом определить конкретное преобразование NAT.
- PAT гарантирует, что устройства будут использовать разные номера портов TCP для каждого сеанса взаимодействия с сервером в Интернете
  
Преобразование PAT пытается сохранить оригинальный порт источника. Если первоначальный порт источника уже используется, PAT назначает первый доступный номер порта, начиная с наименьшего в соответствующей группе портов: 0-511, 512-1,023, или 1,024-65,535.
- Если доступных портов больше нет, а в пуле адресов есть несколько внешних адресов, PAT переходит к следующему адресу, пытаясь выделить первоначальный порт источника.
- Данный процесс продолжается до тех пор, пока не исчерпаются как доступные порты, так и внешние IPv4-адреса в пуле адресов.
  
Пример статического PAT:
```
RRiht(config)#ip nat inside source static tcp 192.168.3.254 443 192.168.2.2 443
RRiht(config)#interface gigabitEthernet 0/0/1
RRiht(config-if)#ip nat outside 
RRiht(config-if)#exit
RRiht(config)#interface gigabitEthernet 0/0/0
RRiht(config-if)#ip nat inside 
```
Пример настройки PAT для одного внешнего IP адреса:
```
RLeft(config)#ip access-list standard NAT_ACC
RLeft(config-std-nacl)#permit 192.168.1.0 255.255.255.0
RLeft(config-std-nacl)#deny any
RLeft(config-std-nacl)#exit
RLeft(config)#ip nat inside source list NAT_ACC interface g0/0/1 overload 
RLeft(config)#interface gigabitEthernet 0/0/0
RLeft(config-if)#ip nat inside
RLeft(config-if)#exit
RLeft(config)#interface gigabitEthernet 0/0/1
RLeft(config-if)#ip nat outside 
RLeft(config-if)#exit
```

Пример настройки динамического PAT :
```
RLeft(config)#ip nat pool WS_POOL 8.8.8.4 8.8.8.8 netmask 255.255.255.0 
Left(config)#ip access-list standard NAT_ACC
RLeft(config-std-nacl)#permit 192.168.1.0 255.255.255.0
RLeft(config-std-nacl)#deny any
RLeft(config)#ip nat inside source list NAT_ACC pool WS_POOL overload 
RLeft(config)#interface gigabitEthernet 0/0/0
RLeft(config-if)#ip nat inside
RLeft(config-if)#exit
RLeft(config)#interface gigabitEthernet 0/0/1
RLeft(config-if)#ip nat outside 
RLeft(config-if)#exit
```
