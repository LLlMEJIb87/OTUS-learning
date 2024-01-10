# Базовая настройка коммутатора Cisco
_ _ _


1. Подключился к коммутатору ( в порт console) консольным кабелем. Для входа в СLI использовал (пример)-ПО PUTTY.
<image src="https://github.com/LLlMEJIb87/OTUS-learning/blob/master/2.%20Basic%20device%20configuration/podkluchenie_konsol'u.PNG">

2. Вошёл в привилегированный режим "enable" и убедился командой "show" startup-config, что коммутатор имееет настройки по умолчанию.
3. Вошёл в глобальный режим конфигурации "configure terminal" и командой "hostname" S1 -пример сменить имя коммутатора.
4. Отключил разрешение имен командой "no ip domain lookup" для того, чтобы коммутатор неправильно введённую команду не интепретировал как имя хоста для подключения по Telnet
5. Командой "service password-encryption" задал шифрование паролей
6. Ограничил доступ к коммутатору через порт консоли установив пароль cisco -пример, командой "login" активировал запрос пароля при подключение Чтобы консольные сообщения не прерывали выполнение команд, использовать параметр logging synchronous.
```
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#login
S1(config-line)#password cisco
S1(config-line)#exit
```