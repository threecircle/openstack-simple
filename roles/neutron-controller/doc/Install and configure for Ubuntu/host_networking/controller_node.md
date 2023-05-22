---
author: Valery Ishutin
date: 2023-05-20
---

# Настройка сети управляющих узлов

## Настройка сетевых интерфейсов

1. Настройка **managment** сетевого интерфейса
    - IP адрес: 10.0.0.11
    - Маска сети: 255.255.255.0 (/24)
    - Шлюз: 10.0.0.1
2. Интерфейс для провайдерской сети не конфигурируется на определенный IP адрес. В примерах будет использваться интерфейс **ens4**.

    Отредактируйте **/etc/network/interfaces**
    ```cmake
    # The provider network interface
    auto ens4
    iface ens4 inet manual
    up ip link set dev $IFACE up
    down ip link set dev $IFACE down
    ```
3. Перезагрузите систему, чтобы изменения вступили в силу

## Настройка DNS

1. Установите имя узла (hostname) как **controller**

```bash
sudo hostname controller
```

2. Для упращения руководства, будем редакитровать файл **/etc/hosts**, чтобы нет углубляться в использование DNS сервера.

```cmake
# controller
10.0.0.11       controller

# compute1
10.0.0.31       compute1

# block1
10.0.0.41       block1

# object1
10.0.0.51       object1

# object2
10.0.0.52       object2
```

> При редактировании, не удаляйте записи с 127.0.0.1 адресом

3. Перезагрузите систему, чтобы изменения вступили в силу