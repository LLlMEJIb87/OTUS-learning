# DHCPv4
_ _ _
DHCP (от англ. Dynamic Host Configuration Protocol — протокол динамической настройки узла) — сетевой протокол, позволяющий сетевым устройствам автоматически получать IP-адрес и другие параметры, необходимые для работы в сети TCP/IP.
- DHCP сервер можно поднять как на самом роутере, так и на отдельном сервере
- Сервер DHCPv4 динамически назначает или выдаёт в аренду IPv4-адрес из пула адресов на ограниченный период вермени по выбору сервера или до тех пор, пока у клиента есть необходимость в адресе.
- Клиенты арендуют данные у сервера на период (lease time) определенный администратором. Далее клиент отправляет запрос на продление адреса.
  
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/dhcpv4.PNG">
</p>
  
Настройка DHCPv4 на маршрутизаторе CISCO
```
R2(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.9 - исключить из выдачи адреса с 1го по 9ый
R2(config)#ip dhcp excluded-address 192.168.10.254 -исключить из выдачи один адрес
R2(config)#ip dhcp pool LAN_POOL -задать имя пулу и перейти в концигурацию
R2(dhcp-config)#network 192.168.10.0 255.255.255.0 задаём сеть и маску для выдачи
R2(dhcp-config)#default-router 192.168.10.1 - задаём шлюз по умолчанию
R2(dhcp-config)#dns-server 192.168.11.5 - задаём адрес dns сервера
R2(dhcp-config)#domain-name example.com - задаём доменное имя
```
  
Проверка работы DHCPv4
1. Show running-config | section dhcp - отображает команды DHCP настроенные на маршрутизаторе
2. Show ip dhcp binding - отображает список всех привязок IPv4 к MAC адресам, предоставляемой службой DHCPv4
3. show ip dhcp server statistics - отображает информацию о кол-ве принятых и отправленных сообщений DHCPv4
  
Отключение службы DHCP:
- no service dhcp -команда отключает службу, по умолчанию служба включена.
  
Ретрансляция DHCPv4  (Relay) - требуется, когда сервер DHCP и клиенты находятся в разных подсетях
```
R2(config)#interface gigabitEthernet 0/0 - заходим на интерфейс
R2(config-if)#ip helper-address 192.168.11.6 - указываем куда ретранслировать полученный dhcp запрос
```
Когда маршрутизатор сконфигурирован как DHCP агент ретрансляции, он принимает широковещательные запросы, а затем отправляет эти запросы как одноадресную рассылку на IPv4-адрес
  
# SLAAC
_ _ _
SLAAC - Stateless Address Auto-configuration Метод позволяющий хостам создавать свой собственный GUA IPv6 адрес без использования служб DHCPv6 сервера
- SLAAC - это служба без определения состояния, которая означает, что нет сервера, который поддерживает информацию о сетевых адресах, чтобы узнать, какие IPv6 адреса используются и какие из них доступны
- SLAAC - отправляет переодические ICMPv6 RA - Router Advertisement, предоставляя адресацию и другую информацию о конфигурации для узлов, для автонастройки их IPv6 адреса на основе информации в RA
- Хост также может отправить сообщение RS - Router Solicitation c запросом RA ( чтобы не ждать RA рассылку от маршрутизатора)
  
Включение SLAAC происходит автоматически при включение маршрутизации IPv6 на роутере и настройки IPv6 адресации на интерфейсе.
  
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/SLAAC.PNG">
</p>

# SLAAC + DHCPv6
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/SLAAC%2BDHCPv6.PNG">
</p>
  
Настройка протокола DHCPv6 без сохранения сотояния 
```
Router(config)#ipv6 dhcp pool  TEST - задаём имя пула ipv6
Router(config-dhcpv6)#dns-server 2001:db8:acad:1::2 - указываем адрес DNS сервере в рассылки RA
Router(config-dhcpv6)#domain-name TEST_TESTOV.com -указываем доменное имя в рассылке RA
Router(config-dhcpv6)#exit
Router(config)#interface gigabitEthernet 0/1 - заходим на интерфей, который смотрит в нашу локальную сеть
Router(config-if)#ipv6 dhcp server TEST - привязываем интерфейс к пулу
Router(config-if)#ipv6 nd other-config-flag указываем  в каком режиме будут получать адресацию хосты
```

__**show ipv6 interface g0/1**__ | begin ND посмотреть настройки интерфейса ipv6 ND neighbor discovery

# DHCPv6
_ _ _
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/dhcpv6.PNG">
</p>
  
Настройка протокола DHCPv6 с сохранением сотояния 
```  
Router(config)#ipv6 dhcp pool NEW_TEST
Router(config-dhcpv6)#address prefix 2001:db8:acad:1::/64
Router(config-dhcpv6)#dns-server 2001:db8:acad:1::2
Router(config-dhcpv6)#domain-name TEST_TESTOV.com
Router(config-dhcpv6)#exit
Router(config)#interface gigabitEthernet 0/2
Router(config-if)#ipv6 dhcp server NEW_TEST
Router(config-if)#ipv6 nd managed-config-flag 
```
__**Show ipv6 dhcp pool**__ - посмотреть настройки DHCPv6

__**Show ipv6 dhcp binding**__ - отображает ipv6 локального адреса канала и GUI адреса назначенного сервером
  
Ретрансляция DHCPv6 (Relay) - требуется, когда сервер DHCP и клиенты находятся в разных подсетях
<p align="center">
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/15.%20DHCPv4%2C%20SLAAC%20and%20DHCPv6%20protocols/DHCP%20_relay.PNG">
</p>
  
Проверка relay осуществляется командой __**Show ipv6 dhcp interface**__