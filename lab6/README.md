## Приложение для мониторинга
https://github.com/grafana/spring-boot-demo

## Ход работы
1. Развернуть виртуальную машину для приложения (app) и виртуальную машину для будущей системы мониторинга (monitoring) через Vagrant-file
2. Написать Ansible Playbook для развертывания системы мониторинга и приложения
3. Через molecule разработать отдельные Ansible-роли для развертывания Grafana, Prometheus, Loki, Docker и пр. Использовать Galaxy для хранения ролей, роли должны проходить стандартные "molecule test"
4. Установить на monitoring сервисы Grafana, Prometheus и Loki
5. Установить на app само приложение и настроить сбор метрик из app /actuator/prometheus
6. Разработать шаблоны для конфигурационных файлов поднимаемых сервисов
7. Добавить в шаблоны дашборды графаны и визуализировать метрики
8. Добавить в шаблоны алертинг в виде письма на почту

## Запуск проекта

### Предварительные требования
- VirtualBox
- Vagrant
- Ansible

```sh
ansible-galaxy install -r requirements.yml
vagrant up
ansible-playbook -i inventory.ini site.yml
```

- **Grafana**: http://localhost:3000
  - Логин: admin
  - Пароль: admin

- **Prometheus**: http://localhost:9090
  - Метрики приложения: http://localhost:9090/targets

- **Loki**: http://localhost:3100
  - Доступ через Grafana Data Sources

- **Spring Boot приложение**: http://localhost:8080
  - Метрики приложения: http://localhost:8080/actuator/prometheus