# Ограничения в сетях {{ baremetal-name }}

В сетях {{ baremetal-name }} действует ряд ограничений.

## MAC-адреса {#mac-addresses}

Если вам требуется большее количество MAC-адресов на один порт, обратитесь в [техническую поддержку]({{ link-console-support }}) и опишите задачи, для которых необходимо изменение.

Вид ограничения | Количество
--- | ---
MAC-адресов на один порт | 5

## MTU и MSS {#mtu-mss}

В сервисе действуют следующие ограничения MTU и MSS:

Вид ограничения | Значение, байт
--- | ---
MTU в публичной сети | 1500
MSS в публичной сети | 1460
MTU в приватной сети | 8910
MSS в приватной сети | 8870

## Полоса пропускания {#bandwidth}

В публичной сети действует ограничение полосы пропускания на 1 Гбит/с для всех серверов, в том числе серверов с сетевыми картами на 10 Гбит/c.

Вид ограничения | Значение, Гбит/с
--- | ---
Полоса пропускания в публичной сети | 1 

## Storm Control {#storm-control}

В сервисе действуют следующие ограничения широковещательного трафика:

Вид ограничения | Значение,</br>пакетов/с (pps)
--- | ---
Broadcast | 100
UnknownUnicast | 100
Multicast | 100

## Заблокированные сетевые порты {#blocked-ports}

Маршрутизаторы, обеспечивающие доступ серверов {{ baremetal-name }} в интернет, ограничивают входящий трафик из интернета до публичных адресов серверов на некоторых TCP- и UDP-портах, а также исходящий SMTP-трафик. Блокировка указанных портов позволяет защитить инфраструктуру {{ baremetal-full-name }} от вредоносной сетевой активности.

{% list tabs %}

- Входящий трафик

  **Порт** | **Протокол прикладного уровня** | **Транспортный протокол**
  --- | --- | ---
  `17` | QOTD | TCP, UDP
  `23` | Telnet | TCP
  `67–68` | DHCP | UDP
  `111` | SUNRPC | UDP
  `135–139` | NetBIOS | TCP, UDP
  `389` | LDAP | TCP, UDP
  `427` | SLP | TCP, UDP
  `445` | SMB | TCP, UDP
  `513` | rlogin | TCP
  `520` | RIP | UDP
  `631` | IPP | TCP, UDP
  `646` | LDP | TCP, UDP
  `750` | Kerberos-IV | UDP
  `1900` | SSDP | UDP
  `3702` | WSD | UDP
  `11211` | memcached | UDP

  Если нужный вам порт не указан в таблице, проверьте его доступность со стороны операционной системы сервера {{ baremetal-name }} при помощи [утилиты Nmap](https://ru.wikipedia.org/wiki/Nmap).

- Исходящий трафик

  **Порт** | **Протокол прикладного уровня** | **Транспортный протокол**
  --- | --- | ---
  `25` | SMTP^1^ | TCP

  ^1^ SMTP-трафик заблокирован для предупреждения несанкционированных почтовых рассылок. В качестве альтернативы для почтовых рассылок рекомендуем использовать сервис [{{ postbox-full-name }}](../../postbox/concepts/index.md).

{% endlist %}