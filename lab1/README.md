# Лаброаторная работа №1

## Ход работы
1. Установить Ansible
2. Установить Vagrant
3. Поднять Vagrant хост при помощи Vagrantfile используя команду `vagrant up`
4. Написать inventory файл
5. Написать плейбук для установки Docker
6. Клонировать [репо](https://github.com/mdn/django-locallibrary-tutorial) на ноды групп [app]
7. Запустить приложние на нодах группы [app] используя ansible.

## Запуск приложения

```sh
vagrant halt && vagrant up
```

```sh
ansible-playbook -i inventory.ini playbook.yml
```

### Проверка работы приложения
```sh
curl -I http://192.168.56.11:8000/catalog/ && echo "---" && curl -I http://192.168.56.12:8000/catalog/
```

