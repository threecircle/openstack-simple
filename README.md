 
Уcтановка производится на ubuntu server 22.04 в стандартной поставке (не минимальной) 
При установке выбрал стандартную установку и не выбирал дополнительные компоненты. Имя образа - ubuntu-22.04.1-live-server-amd64.iso

Для сервера включена вложенная виртуализация.

Подключено 2 интерфейса:
eth0 - обычный интерфейс с ip адресом
eth1 - подключен к сети в который будут выдаваться адреса ВМ (должен быть отключен - так по умолчания и происходит)

Пример вывода:
```sh
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:15:5d:58:47:0b brd ff:ff:ff:ff:ff:ff
        inet 192.168.88.105/24 brd 192.168.88.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::215:5dff:fe58:470b/64 scope link
        valid_lft forever preferred_lft forever
    3: eth1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
        link/ether 00:15:5d:58:47:0e brd ff:ff:ff:ff:ff:ff
```
Команда для запуска плейбука:
```sh
    ansible-playbook playbook.yml -i ./hosts -u
```

Очередность установки модулей:
    1. keystone             - Хранилище пользователей и проектов
    2. glance               - Хранилище образов (Все образы хранятся в /var/lib/glance/images/. При установке добавляю образ cirros)
    3. placement            - Следит за свободными ресурсами и учитывает их потребление (CPU/RAM/IP)
    4. nova                 - Управление виртуализацией (Должна быть включена вложенная виртуализация, потому что не включал qemu)
    5. neutron              - Управление сетью (используется linux-bridge вместо openVSwitch) t+10.25
    6. cinder               - (Пока не работает)Хранилище volume от виртуальных машин. Сервис не может хранить данные в папке, поэтому создаю loop устройство на 10Гб и в нем размечаю LVM
    7. horizon              - UI t+12.30

Для того, чтобы воспользоваться командной строкой openstack, нужно установить следующие переменные среды:
    export OS_USERNAME=admin;
    export OS_PASSWORD=admin;
    export OS_PROJECT_NAME=admin;
    export OS_USER_DOMAIN_NAME=Default;
    export OS_PROJECT_DOMAIN_NAME=Default;
    export OS_AUTH_URL=http://localhost:5000/v3;
    export OS_IDENTITY_API_VERSION=3;

## База данных
Установлен PostgreSQL с дефолтным конфигом. Отключен удаленный доступ - только с localhost.

Просмотр списка таблиц в БД (у всех пользователей по умолчанию пароль 1234567yY!):
    psql -U neutron_user -h 127.0.0.1 -d neutron_db
    SELECT table_catalog, table_schema, table_name FROM information_schema.tables WHERE table_schema = 'public';



systemctl restart nova-api;
systemctl restart nova-scheduler;
systemctl restart nova-conductor;
systemctl restart nova-novncproxy;
systemctl restart nova-compute;
systemctl restart apache2;