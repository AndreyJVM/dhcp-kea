### Установка и настройка DHCP сервера Kea

---
- Ubuntu Server 24.04
- RAM 2G
- CPU 2
- Disk 50
---

Перед установкой необходимо установить и настроить **MariaDB** для хранения
данных об аренде, резервировании хостов, опциях, а также часть параметров конфигурации сервера.

```shell
    sudo apt update -y
```

- Замените на ваш часовой пояс.
```shell
    sudo timedatectl set-timezone Europe/Moscow 
```
- Устанавливаем mariadb.
```shell
    sudo apt install -y mariadb-server mariadb-client libmariadb-dev
```
- Настраиваем mariadb.
```shell
    sudo mysql -u root -p
```

- Можете заменить пользователя, пароль и адрес DB.
```shell
    CREATE DATABASE kea;
    CREATE USER 'kea'@'localhost' IDENTIFIED BY 'ваш_надёжный_пароль';
    GRANT ALL PRIVILEGES ON kea.* TO 'kea'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
```
- Устанавливаем Kea.
```shell
    sudo apt install -y kea-dhcp4-server kea-admin libmariadb-dev
```

- Связываем Kea и MariaDB. Меняем пароль от DB.
```shell
    sudo kea-admin db-init mysql -u kea -p ваш_надёжный_пароль -n kea
```
- Проверяем работу. Должны отобразиться таблицы (lease4, schema_version и др.).
```shell
    mysql -u kea -p -e "SHOW TABLES;" kea
```

---
### Рекомендации повышения производительности MariaDB
```shell
    SET GLOBAL innodb_flush_log_at_trx_commit=2;
    SHOW SESSION VARIABLES LIKE 'innodb_flush_log%';
```