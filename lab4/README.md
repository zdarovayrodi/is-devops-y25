## Ход работы
1. Написать Ansible Playbook поднимающий OpenVPN сервер
2. Через Molecule инициировать структуру для роли и вынести задачи поднятия сервера, с конфигурацией через переменные
3. При завершении работы плейбук должен разместить в playbook-dir артефактный ovpn-файл для подключения
4. Роль должна проходить стандартные тестами molecule test
5. Плейбук подключает роль через ansible-galaxy

## Validate

### 1. Подготовка окружения

```sh
python -m venv venv
source venv/bin/activate
pip install ansible molecule molecule-docker docker pytest pytest-testinfra
```

### 2. Проверка роли через molecule

```sh
cd roles/openvpn
molecule test
```

### 3. Проверка развертывания через Vagrant

```sh
vagrant up
vagrant status
ansible-playbook -i inventory.ini playbook/openvpn.yml
```

Ожидаемые результаты:
```sh
vagrant ssh srv1 -c "sudo systemctl status openvpn@server"
```

### 4. Проверка подключения к VPN

```sh
sudo cp playbook/client.ovpn /etc/openvpn/client/

sudo openvpn --config /etc/openvpn/client/client.ovpn

ip addr show tun0
ping 10.8.0.1
```

## Структура проекта

```
.
├── inventory.ini          # Инвентарь для Ansible
├── playbook/             # Директория с плейбуками
│   ├── openvpn.yml      # Основной плейбук
│   └── requirements.yml  # Зависимости ролей
├── roles/                # Директория с ролями
│   └── openvpn/         # Роль OpenVPN
└── Vagrantfile          # Конфигурация Vagrant
```