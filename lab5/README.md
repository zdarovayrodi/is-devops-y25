# Лабораторная работа №5

## Ход работы
1. Развернуть виртуальную машину для приложения (app) и виртуальную машину для будущей системы базы данных (db) через Vagrantfile
2. Написать роль установки PostgreSQL и плейбук, который ее разворачивает
3. Все параметры должны быть вынесены в переменные, все переменные должны быть префиксованы, значения переменных устанавливаются через group_vars для плейбука, роль должна быть покрыта тестами
4. Добавить возможность смены директории с данными на кастомную
5. Добавить возможность создания баз данных и пользователей
6. Добавить функционал настройки streaming-репликации
7. Продумать логику определения master и replica нод СУБД и их настройки при работе роли

## Развертывание

1. Запустите виртуальные машины:
   ```sh
   vagrant up
   ```
   2 VMs created:
   - app (192.168.56.10)
   - db (192.168.56.11)

2. Check VMs are accessible
   ```sh
   ansible all -m ping
   ```

3. Запустите плейбук для установки PostgreSQL:
   ```sh
   ansible-playbook site.yml
   ```

## Проверка работоспособности

1. Проверьте статус PostgreSQL:
   ```sh
   ansible db -m shell -a "systemctl status postgresql"
   ```

2. Проверьте список баз данных:
   ```sh
   ansible db -m shell -a "sudo -u postgres psql -l"
   ```
   Вы должны увидеть базу данных `myapp` и пользователя `myapp_user`.

3. Проверьте подключение к базе данных:
   ```sh
   ansible db -m shell -a "sudo -u postgres psql -c '\du'"
   ```
   Вы должны увидеть созданного пользователя и его права.

## Архитектура репликации

В роли реализована логика master-replica через следующие механизмы:

1. Определение роли узла через переменную:
   ```yaml
   postgresql_is_master: true/false
   ```

2. Настройка master-узла (postgresql_is_master: true):
   - WAL-архивация включена (wal_level = replica)
   - Разрешены подключения для репликации
   - Настроен пользователь репликации
   - hot_standby = off

3. Настройка replica-узла (postgresql_is_master: false):
   - hot_standby = on для чтения во время репликации
   - Настроено подключение к master-узлу

4. Конфигурация безопасности:
   - В pg_hba.conf на master-узле разрешены подключения для репликации
   - Пользователь репликации имеет ограниченные права

## Пример настройки репликации

1. Настройте master-узел (group_vars/master/vars.yml):
   ```yaml
   postgresql_is_master: true
   postgresql_replica_user: "replicator"
   postgresql_replica_password: "strong_password"
   ```

2. Настройте replica-узел (group_vars/replica/vars.yml):
   ```yaml
   postgresql_is_master: false
   postgresql_replica_user: "replicator"
   postgresql_replica_password: "strong_password"
   ```

## Тестирование с нуля

1. Удалите существующие VM:
   ```sh
   vagrant destroy -f
   ```

2. Запустите развертывание заново:
   ```sh
   vagrant up
   ansible-playbook site.yml
   ```

3. Проверьте статус и конфигурацию:
   ```sh
   ansible db -m shell -a "systemctl status postgresql"
   ansible db -m shell -a "sudo -u postgres psql -l"
   ```
