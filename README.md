# Установка системы
Если вы используете домашний сервер для хостинга, то в процессе установки системы отметьте Install OpenSSH server
# Используемые подстановочные значения в коммандах
```[usr]``` - текущий пользователь на сервере

```[remote_host]``` - адрес сервера

# Подготовка
Логинимся на сервер по ssh:

```ssh [usr]@[remote_host]```

Прописываем следующие команды:

```bash
sudo apt update && sudo apt upgrade
sudo apt install openjdk-21-jdk
mkdir minecraft
cd minecraft
mkdir server
```

Теперь необходимо установить файл сервера (например официальный или сторонний) на рабочий компьютер, после этого открыть терминал

- Для Windows надо прописать следующую команду:

```pscp Disk:\Users\CurrentUser\path\to\server.jar [usr]@[remote_host]:/home/[usr]/minecraft/server```

- Для Linux:

```scp /home/user/path/to/server.jar [usr]@[remote_host]:/home/[usr]/minecraft/server```

Снова возвращаемся к ssh, и первый раз запускаем сервер:

```java -Xmx4G -Xms4G -jar server.jar nogui```

Будет ошибка, необходимо согласиться с eula:

```nano eula.txt```

```eula=false``` меняем на ```eula=true```

Снова запускаем сервер:

```java -Xmx4G -Xms4G -jar server.jar nogui```

Заходим в майнкрафт, проверяем работоспособность, адрес сервера ```[remote_host]:25565```, если все работает исправно, то в консоли, где мы подключены по ssh, прописываем:

```
save-all
stop
```

Если нужно, то настраиваем сервер:

```nano server.properties```

В случае необходимости сбросить мир:

```rm -rf world```

# Сервер как сервис

Переходим в директорию сервисов:

```cd /etc/systemd/system/```

Создаем новый сервис:

```sudo nano minecraft.service```

В содержимое записываем следующее
```bash
[Unit]
Description=Minecraft Server
Wants=network-online.target
After=network-online.target

[Service]
User=[usr]
WorkingDirectory=/home/[usr]/minecraft/server

ExecStart=/usr/bin/java -Xmx4G -Xms4G -jar server.jar nogui

Restart=always
RestartSec=30

StandardInput=null

[Install]
WantedBy=multi-user.target
```


Сохраняем, пишем следующую команду:

```systemctl enable minecraft && systemctl enable minecraft```

Все, теперь сервер будет работать всегда, когда устройство, на котором он нахдиться включено

