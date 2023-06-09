---
author: Valery Ishutin
date: 2023-05-20
---
# Сеть узлов

После установки операционной системы на каждуй узел вашего кластера, требуется настроить сетевые интерфейсы. Рекомендуется отключить автоматическую настройку параметров сети (DHCP) и указывать параметры вручную. Больше информации по настройке сети для вашей операционной системы, можно прочитать в [документации](https://ubuntu.com/server/docs/network-configuration)

Все узлы требуют подключение к интернету для установки пакетов, получения обновлений безопасности, работы с DNS и получением времени (NTP). В большинстве случаев, все узлы получают доступ в интернет, через managment сеть в которой используются частные ip адреса и доступ в интернет обеспечивается через NAT.

Существуют 2 варианта подключения к provider сети: 
- Внешняя сеть - в таком варианте все узлы напрямую подключаются к публичной сети.
- Приватная сеть - в таком случае используется сеть из приватного диапазона адресов и доступ в интернет осуществляется с помощью NAT.

![](./networklayout.png)

В примере используются следущие сети:
- Managment сеть - 10.0.0.0/24 шлюз 10.0.0.1

    В данной сети шлюз обеспечивает доступ в интернет через NAT для установки пакетов и других административных нужд.

- Provider сеть - публичная сеть с выделенным диапазоном белых IP 203.0.1113.0/24 шлюз 203.0.113.1

    Шлюз в данном случае требуется для доступа в интернет виртуальных машин, которые будут работать в OpenStack

Вы можете использовать другие сети и шлюзы, в зависимости от вашего решения.

Имена сетевых интерфейсов могут разлечаться, в зависимости от выбранной операционной системы. Обычно используется префикс eth с порядковым номером. В примерах первый интерфейс будет с наименьшим порядковым числом.

Если вы не планируете использовать конфигурацию сети из примеров, то необходимо настроить резолвинг узлов по DNS имени, например: controller=10.0.0.11.

> Изменение сетевой конфигурации влечет за собой прерывание сетевого подключения. Безопаснее будет использовать локальный терминал для настройки сети.

> По умолчанию в Ubuntu отключен фаерволл. [Подробнее про безопасность OpenStack.](https://docs.openstack.org/security-guide/)

## Вложенные разделы
- [Управляющие узлы](./controller_node.md)
- [Вычислительные узлы](./compute_node.md)
- [Узлы для хранилища (опционально)](./block_storage_node.md)
- [Проверка](./verify.md)
