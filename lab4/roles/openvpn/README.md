# OpenVPN Role

Ansible роль для установки и настройки OpenVPN сервера.

## Возможности

- Установка и настройка OpenVPN сервера
- Генерация всех необходимых сертификатов и ключей
- Создание конфигурации для клиентов
- Поддержка современных настроек безопасности
- Автоматическая генерация .ovpn файла для клиентов

## Требования

- Ubuntu 20.04 или новее
- Python 3
- Ansible 2.9 или новее

## Переменные роли

### Основные настройки

```yaml
# Порт OpenVPN сервера
openvpn_port: 1194

# Протокол (udp/tcp)
openvpn_proto: udp

# Сеть для VPN клиентов
openvpn_network: "10.8.0.0"
openvpn_netmask: "255.255.255.0"

# DNS серверы для клиентов
openvpn_dns_servers:
  - "8.8.8.8"
  - "8.8.4.4"
```

### Настройки безопасности

```yaml
# Шифрование
openvpn_cipher: "AES-256-CBC"
openvpn_auth: "SHA256"
```

### Пути к файлам

```yaml
openvpn_server_config_dir: "/etc/openvpn/server"
openvpn_easy_rsa_dir: "/etc/openvpn/easy-rsa"
openvpn_client_config_dir: "/etc/openvpn"
```

## Пример использования

1. Добавьте роль в requirements.yml:
```yaml
- src: https://github.com/your-username/ansible-role-openvpn
  name: openvpn
  version: master
```

2. Установите роль:
```bash
ansible-galaxy install -r requirements.yml
```

3. Используйте роль в вашем плейбуке:
```yaml
- hosts: vpn_servers
  roles:
    - role: openvpn
      vars:
        openvpn_port: 1194
        openvpn_proto: udp
```

## Тестирование

Роль включает тесты molecule для проверки:
- Установки OpenVPN
- Генерации конфигурации
- Запуска сервиса
- Проверки портов
- Валидации конфигурации клиента

Для запуска тестов:
```bash
molecule test
```

## Проверка работы

### Подготовка окружения

1. Установите необходимые инструменты:
```bash
# Установка Vagrant
sudo pacman -S vagrant    # Arch Linux
sudo apt install vagrant  # Ubuntu/Debian

# Установка VirtualBox
sudo pacman -S virtualbox # Arch Linux
sudo apt install virtualbox # Ubuntu/Debian

# Установка Python зависимостей
python -m venv venv
source venv/bin/activate
pip install ansible molecule molecule-docker docker pytest pytest-testinfra
```

2. Клонируйте репозиторий:
```bash
git clone <repository-url>
cd <repository-name>
```

### Тестирование роли

1. Запустите тесты molecule:
```bash
cd roles/openvpn
molecule test
```

Все тесты должны пройти успешно, включая:
- Проверку синтаксиса
- Создание тестового окружения
- Конвергенцию
- Идемпотентность
- Верификацию
- Очистку

### Развертывание в Vagrant

1. Запустите виртуальные машины:
```bash
vagrant up
```

2. Проверьте статус машин:
```bash
vagrant status
```

3. Запустите playbook:
```bash
ansible-playbook -i inventory.ini playbook/openvpn.yml
```

4. Проверьте результаты:
- В директории playbook должен появиться файл `client.ovpn`
- На сервере srv1 (192.168.56.201) должен быть запущен OpenVPN сервер
- Порт 1194/udp должен быть открыт

5. Проверка статуса OpenVPN на сервере:
```bash
vagrant ssh srv1
sudo systemctl status openvpn@server
```

### Проверка подключения

1. Установите OpenVPN клиент:
```bash
sudo pacman -S openvpn    # Arch Linux
sudo apt install openvpn  # Ubuntu/Debian
```

2. Скопируйте конфигурацию клиента:
```bash
sudo cp playbook/client.ovpn /etc/openvpn/client/
```

3. Подключитесь к VPN:
```bash
sudo openvpn --config /etc/openvpn/client/client.ovpn
```

4. Проверьте подключение:
```bash
ip addr show tun0
ping 10.8.0.1
```

### Очистка

1. Остановите VPN подключение:
```bash
sudo killall openvpn
```

2. Остановите виртуальные машины:
```bash
vagrant destroy -f
```

## Устранение неполадок

1. Если тесты molecule не проходят:
- Проверьте наличие всех зависимостей
- Убедитесь, что Docker запущен и у вас есть права на его использование
- Проверьте логи: `MOLECULE_DEBUG=1 molecule test`

2. Если не удается подключиться к VPN:
- Проверьте статус сервера: `systemctl status openvpn@server`
- Проверьте логи: `journalctl -u openvpn@server`
- Убедитесь, что порт 1194/udp открыт: `netstat -ulnp | grep openvpn`

## Лицензия

MIT
