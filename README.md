# Установка системы
Если вы используете домашний сервер для хостинга, то в процессе установки системы отметьте Install OpenSSH server
# Используемые подстановочные значения в коммандах
```<usr>``` - текущий пользователь на сервере

```<remote_host>``` - адрес сервера

# Подготовка
Логинимся на сервер по ssh:

```bash
ssh <usr>@<remote_host>
```

Прописываем следующие команды:

```bash
sudo apt update && sudo apt upgrade
sudo apt install openjdk-21-jdk
mkdir minecraft
cd minecraft
mkdir server
```

Теперь необходимо установить файл сервера (например официальный или сторонний) на рабочий компьютер и используя подходящую приведенную ниже команду скопировать этот файл на сервер:

- Для Windows

  ```bash
  pscp <путь к файлу сервера> <usr>@<remote_host>:/home/<usr>/minecraft/server
  ```

- Для Linux

  ```bash
  scp <путь к файлу сервера> <usr>@<remote_host>:/home/<usr>/minecraft/server
  ```

Снова возвращаемся к ssh, и первый раз запускаем сервер:

```java -Xmx4G -Xms4G -jar server.jar nogui```

Будет ошибка, необходимо согласиться с eula:

```bash
nano eula.txt
```

```eula=false``` меняем на ```eula=true```

Снова запускаем сервер:

```java -Xmx4G -Xms4G -jar server.jar nogui```

Заходим в майнкрафт, проверяем работоспособность, адрес сервера ```<remote_host>:25565```, если все работает исправно, то в консоли, где мы подключены по ssh, прописываем:

```
save-all
stop
```

Если нужно, то настраиваем сервер:

```bash
nano server.properties
```

В случае необходимости сбросить мир:

```bash
rm -rf world
```

# Сервер как сервис

Переходим в директорию сервисов:

```bash
cd /etc/systemd/system/
```

Создаем новый сервис:

```bash
sudo nano minecraft.service
```

В содержимое записываем следующее
```bash
[Unit]
Description=Minecraft Server
Wants=network-online.target
After=network-online.target

[Service]
User=<usr>
WorkingDirectory=/home/<usr>/minecraft/server

ExecStart=/usr/bin/java -Xmx4G -Xms4G -jar server.jar nogui

Restart=always
RestartSec=30

StandardInput=null

[Install]
WantedBy=multi-user.target
```


Сохраняем, пишем следующую команду:

```bash
systemctl enable minecraft && systemctl enable minecraft
```

Все, теперь сервер будет работать всегда, когда устройство, на котором он нахдиться включено

# Безопасность

Чтобы защититься от всяких нехороших людей, которые могут наделать бед на нашем сервере, принимаем следующие меры безопасности:

1. Смена пароля:
   
   ```passwd```

2. Защита ssh:

   Откроем конфиг:

   ```sudo nano /etc/ssh/sshd_config```
   
   Раскомментируем строку ```#Port 22``` и поменем порт на любой свободный. По желанию можно поменять другие настройки.

   Сохраняем, закрываем, перезапускаем сервисы:

   ```bash
   systemctl daemon-reload
   systemctl restart ssh.socket
   ```

   Не забываем указать нужный порт, при следующем подключении по ssh

3. Защита майнкрафт сервера:

   Открываем настройки:

   ```bash
   nano /home/<usr>/minecraft/server/server.properties
   ```

   По желанию меняем порт, активируем белый список.

   Меняем ```hide-online-players``` на ```true```

   Выключаем сервис:

   ```systemctl stop minecraft```
   
   Запускаем сервер:

   ```java -Xmx4G -Xms4G -jar /home/<usr>/minecraft/server/server.jar nogui```
   
   Прописываем ники игроков в вайтлист:

   ```whitelist add <player>```

   Если нужно даем оператора игроку:

   ```op <player>```

   Закрываем:

   ```stop```

   Снова запускаем сервис

   ```systemctl start minecraft```

   ! Это лишь базовые вещи, необходимые для защиты сервера. Для полноценной защиты (особенно серверов, не проверяющих налчие лицензии) требуются плагины !
  
   
   
   

   

   

