## Процессы

### В данном ряду заданий, нам нужно работать с процессами системы.

1. Трижды запустить sleep команду в разных интервалах

```bash
[ValkyRa@centos ~]$ sleep 1
[ValkyRa@centos ~]$ sleep 10
[ValkyRa@centos ~]$ sleep 5
```
Есть ещё один вариант, это запуск команды с &, это позволяет запустить процесс в фоновом режиме с указанием его PID, и порядковым номером после ввода команды (в нашем случае, это 3132)
```
[ValkyRa@centos ~]$ sleep 15 &
[1] 3132
```
2. Отправить SIGSTOP сигнал для этих процессов, тремя разными способами

Для начала создадим фоновые процессы заново, дабы понять, что останавливать.
```
[ValkyRa@centos ~]$ sleep 1000 &
[2] 3535
[1]   Done                    sleep 15
[ValkyRa@centos ~]$ sleep 10000 &
[3] 3542
[ValkyRa@centos ~]$ sleep 100000 &
[4] 3549
```
И выведем их список:
```
[ValkyRa@centos ~]$ jobs
[2]   Running                 sleep 1000 &
[3]-  Running                 sleep 10000 &
[4]+  Running                 sleep 100000 &
```
Использовать SIGSTOP будем в трёх разных вариантах, либо указывать последовательный номер процесса, по PID задаче, либо по названию команды.
```
[ValkyRa@centos ~]$ kill -SIGSTOP %2

[2]+  Stopped                 sleep 1000

[ValkyRa@centos ~]$ kill -SIGSTOP 3542
[3]   Stopped                 sleep 10000

[ValkyRa@centos ~]$ pkill -19 sleep
[4]+  Stopped                 sleep 100000

```
3. Проверить их статусы командой job

Проверяем и убеждаемся в том, что они остановлены

```bash
[ValkyRa@centos ~]$ jobs
[2]   Stopped                 sleep 1000
[3]-  Stopped                 sleep 10000
[4]+  Stopped                 sleep 100000
```
4. Уничтожить один из процессов. (Любой)
Используем команду kill без параметров (по умолчанию используется SIGTERM)
```bash
[ValkyRa@centos ~]$ kill 3535
[2]   Убито              sleep 1000
[ValkyRa@centos ~]$ jobs
[3]-  Stopped                 sleep 10000
[4]+  Stopped                 sleep 100000
```
5. К другому отправить команду SIGCONT двумя разными способами.
```bash
[ValkyRa@centos ~]$ kill -18 3542
[ValkyRa@centos ~]$ jobs
[3]-  Running                 sleep 10000 &
[4]+  Stopped                 sleep 100000
[ValkyRa@centos ~]$ kill -SIGCONT 3549
[ValkyRa@centos ~]$ jobs
[3]-  Running                 sleep 10000 &
[4]+  Running                 sleep 100000 &
```
6. Убить один процесс, используя PID и другой по job ID
```bash
[ValkyRa@centos ~]$ kill %3
[3]-  Убито              sleep 10000
[ValkyRa@centos ~]$ kill 3549
[4]+  Убито              sleep 100000
[ValkyRa@centos ~]$ jobs
```

## systemd
### В данном ряду заданий мы будем работать с деймонами, используя скрипты, таймеры, и как сами деймоны работают.
1. Написать два деймона: один должен быть простым деймоном и делать команду sleep 10 после запуска и потом делать echo 1 > /tmp/homework, второй должен быть схожим и делать echo 2 > /tmp/homework без какой-либо команды сна.
Для начала создадим два скрипта для деймонов. Оба скрипты будут выглядеть примерно так:
```bash
[ValkyRa@centos homework5]$ cat daemon1
#!/bin/bash

sleep 10
echo 1 > /tmp/homework

[ValkyRa@centos homework5]$ cat daemon2
#!/bin/bash

echo 2 > /tmp/homework
```
Далее нам нужно написать самих деймонов. Нам необходимо перейти в директорию /lib/systemd/system и написать следующее:
```
[ValkyRa@centos system]$ sudo emacs homework5_deamon1.service
```
И указываем следующий код:
```bash
[Unit]
Description=Daemon for Echo 1

[Service]
Type=simple
User=ValkyRa
ExecStart=/home/ValkyRa/homework5/daemon1
```
И для второго деймона:
```
[ValkyRa@centos system]$ sudo emacs homework5_deamon2.service

[Unit]
Description=Daemon for Echo 2

[Service]
Type=simple
User=ValkyRa
ExecStart=/home/ValkyRa/homework5/daemon2
```
И после добавления обоих деймонов, нужно прописать перезапуск конфига systemd, командой systemctl daemon-reload от админа, чтобы прописанные ранее деймоны вступили в силу, и их можно было запускать.
Если без прав администратора:

![image](https://user-images.githubusercontent.com/15772109/146426993-63c3c0f1-b17a-4532-b398-b3597af4efda.png)

2. Сделать второй деймон зависимым от первого (запуск должен быть только после первого деймона)
Для этого нужно во второй деймон добавить строчку After, чтобы деймон запускался только после первого.
Изменённый 2ой деймон:
```bash
[Unit]
Description=Daemon for Echo 2
After=homework5_daemon1.service

[Service]
Type=simple
User=ValkyRa
ExecStart=/home/ValkyRa/homework5/daemon2
```
3. Для второго деймона написать таймер и настроить его для запуска в 01.01.2019 в 00:00
Создаём файлик homework5_daemon.timer
```bash
[Unit]
Description=Run 2nd daemon at timer

[Timer]
OnCalendar=2019-01-01 00:00
Unit=homework5_daemon2.service
```
4. Запустить всех деймонов и таймер, проверить их статусы, список таймеров и /tmp/homework

Запускаю первый деймон:
```bash
[ValkyRa@centos system]$ sudo systemctl start homework5_daemon1.service 
[ValkyRa@centos system]$ systemctl -l status homework5_daemon1.service 
● homework5_daemon1.service - Daemon for Echo 1
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon1.service; static; vendor preset: disabled)
   Active: active (running) since Чт 2021-12-16 13:13:18 MSK; 2s ago
 Main PID: 3544 (daemon1)
    Tasks: 2
   CGroup: /system.slice/homework5_daemon1.service
           ├─3544 /bin/bash /home/ValkyRa/homework5/daemon1
           └─3546 sleep 10

дек 16 13:13:18 centos systemd[1]: Started Daemon for Echo 1.
[ValkyRa@centos system]$

[ValkyRa@centos system]$ cat /tmp/homework 
1
```
Запускаю второй деймон:
```bash
[ValkyRa@centos system]$ sudo systemctl start homework5_daemon2.service
[ValkyRa@centos system]$ systemctl -l status homework5_daemon2.service 
● homework5_daemon2.service - Daemon for Echo 2
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon2.service; static; vendor preset: disabled)
   Active: inactive (dead)

дек 16 13:14:05 centos systemd[1]: Started Daemon for Echo 2.

[ValkyRa@centos system]$ cat /tmp/homework 
2
```
И теперь запускаю таймер:
```bash
[ValkyRa@centos system]$ systemctl start homework5_daemon.timer
[ValkyRa@centos system]$ systemctl status homework5_daemon.timer
● homework5_daemon.timer - Run 2nd daemon at timer
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon.timer; static; vendor preset: disabled)
   Active: active (elapsed) since Чт 2021-12-16 13:18:53 MSK; 9s ago

дек 16 13:18:53 centos systemd[1]: Started Run 2nd daemon at timer.
```
5. Остановить всех деймонов и таймер
```
[ValkyRa@centos system]$ systemctl stop homework5_daemon.timer 
[ValkyRa@centos system]$ systemctl stop homework5_daemon1.service
[ValkyRa@centos system]$ systemctl stop homework5_daemon2.service

```
Таймер:
```
[ValkyRa@centos system]$ systemctl status homework5_daemon.timer
● homework5_daemon.timer - Run 2nd daemon at timer
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon.timer; static; vendor preset: disabled)
   Active: inactive (dead)

дек 16 13:18:53 centos systemd[1]: Started Run 2nd daemon at timer.
дек 16 13:20:14 centos systemd[1]: Stopped Run 2nd daemon at timer.
```
Первый деймон:
```bash
[ValkyRa@centos system]$ systemctl -l status homework5_daemon1.service 
● homework5_daemon1.service - Daemon for Echo 1
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon1.service; static; vendor preset: disabled)
   Active: inactive (dead)
```
Второй:
```
● homework5_daemon2.service - Daemon for Echo 2
   Loaded: loaded (/usr/lib/systemd/system/homework5_daemon2.service; static; vendor preset: disabled)
   Active: inactive (dead)
```


## cron/anacron
### В данном задании мы попробуем использование anacron и cron для планирования задач, используя скрипты или команды. 


1. Создать anacron задание, которое выполняет скрипт: echo Hello > /opt/hello и запускает его каждые 2 дня

Для начала создаём скрипт:
```
[ValkyRa@centos homework5]$ emacs anacron-hello.sh
```
Код скрипта:
```bash
#!/bin/bash
echo hello > /opt/hello
```
Далее нужно изменить конфиг для anacron. 
Выполняем команду: sudo emacs /etc/anacrontab

```bash
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
# RANDOM_DELAY=0
# the jobs will be started during the following hours only
# START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
@monthly 45	cron.monthly		nice run-parts /etc/cron.monthly
2	 10	echo			/bin/bash      /home/ValkyRa/homework5/anacron-hello.sh      
```

И добавляем последнюю строчку, где мы указываем идентификатор, периодичность дней и путь до скрипта. Также в конфиге я указал RANDOM_DELAY=0 для тестирования работы, чтобы не было рандома.

Запускаем anacron, используя модификатор –d для показа всех действий во время процесса и –n для немедленного выполнения, игнорируя указанные в строчке ранее таймеры
```
[ValkyRa@centos homework5]$ sudo anacron -d -n
Anacron started on 2021-12-16
Checking against 0 with 30
Will run job `echo'
Jobs will be executed sequentially
Job `echo' started
Job `echo' terminated
Normal exit (1 job run)
```
Проверяем нашу /opt директорию
```bash
[ValkyRa@centos homework5]$ ls /opt
hello  rh  VBoxGuestAdditions-6.1.30
[ValkyRa@centos homework5]$ cat /opt/hello 
hello
```
2. Создайте cron задание, что выполняет ту же команду (будет лучше создать скрипт для этого) и запускается с задержкой в минуту после перезагрузки системы.

Используем команду sudo crontab с модификатором –e для внесения команд, которые мы хотим запустить.
```
@reboot sleep 60 && echo Hello > /opt/hello
```
3. Перезапустите виртуальную машину и проверьте, что предыдущая задача была правильно исполнена.

Перезапускаем машину командой reboot и логинимся на учётную запись, после идём в директорию и обнаруживаем нужный файл с нужным Hello

![image](https://user-images.githubusercontent.com/15772109/146417941-ef1207df-f7ea-45a7-8940-d3e6cfc38dcf.png)

## lsof
### В этих заданиях мы ознакомимся с lsof для мониторинга процессов и вывод необходимых процессов для мониторинга к примеру о том, какие процессы используются для реализации команд

1. Запустить sleep команду, перенаправляя stdout и stderr в два разных файла (оба файла будут пустыми).

```
[ValkyRa@centos ~]$ sleep 600 1>sleep 2>sleep_error &
[1] 3009
```
2. Найти через lsof команду которая отображает какие файлы использует этот процесс, также найти с какого файла получает stdin.
```
[ValkyRa@centos ~]$ lsof -p 3009
```

![image](https://user-images.githubusercontent.com/15772109/146419140-9b850f92-fda6-45ba-89de-8346e9bebe9b.png)

stdin поток идёт в 7ой строке на скрине, если быть точнее в /dev/pts/0


3. List all ESTABLISHED TCP connections ONLY with lsof
```
[ValkyRa@centos ~]$ sudo lsof -itcp

[sudo] пароль для ValkyRa: 
```

![image](https://user-images.githubusercontent.com/15772109/146419267-9c2f5624-859a-4613-967a-fe339f315da4.png)

Решил вывести все TCP соединения, т.к у меня нет Established соединений, чтобы их поймать, можно добавить | grep ESTABLISHED, но в моём случае, тогда не выведет ничего.

В интернете нашёл ещё один вариант вывода: netstat -nat

![image](https://user-images.githubusercontent.com/15772109/146419294-bf4f3f32-a278-4c64-96b0-db99ffc88443.png)

Но к сожалению, нельзя вывести того, чего нет :)  

