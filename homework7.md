## Repositories and Packages

- Use rpm for the following tasks:
1. Download sysstat package.
```bash
[ValkyRa@centos ~]$ wget http://mirror.centos.org/centos/7/os/x86_64/Packages/sysstat-10.1.5-19.el7.x86_64.rpm
--2021-12-22 22:16:48--  http://mirror.centos.org/centos/7/os/x86_64/Packages/sysstat-10.1.5-19.el7.x86_64.rpm
Распознаётся mirror.centos.org (mirror.centos.org)... 109.203.112.17, 2001:41d0:d:3691::10
Подключение к mirror.centos.org (mirror.centos.org)|109.203.112.17|:80... соединение установлено.
HTTP-запрос отправлен. Ожидание ответа... 200 OK
Длина: 323020 (315K) [application/x-rpm]
Сохранение в: «sysstat-10.1.5-19.el7.x86_64.rpm»

100%[======================================>] 323 020     1,70MB/s   за 0,2s   

2021-12-22 22:16:49 (1,70 MB/s) - «sysstat-10.1.5-19.el7.x86_64.rpm» сохранён [323020/323020]
```
Другой способ просто установить нужный пакет, но исходя из задания, показалось что нужно именно пакет скачать, что и сделано было выше:
```bash
[ValkyRa@centos ~]$ sudo yum install sysstat
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * extras: mirrors.powernet.com.ru
 * updates: ftp.nsc.ru
Пакет sysstat-10.1.5-19.el7.x86_64 уже установлен, и это последняя версия.
Выполнять нечего
```
2. Get information from downloaded sysstat package file.
```bash
[ValkyRa@centos ~]$ rpm -qip sysstat-10.1.5-19.el7.x86_64.rpm
Name        : sysstat
Version     : 10.1.5
Release     : 19.el7
Architecture: x86_64
Install Date: (not installed)
Group       : Applications/System
Size        : 1172488
License     : GPLv2+
Signature   : RSA/SHA256, Сб 04 апр 2020 00:08:48, Key ID 24c6a8a7f4a80eb5
Source RPM  : sysstat-10.1.5-19.el7.src.rpm
Build Date  : Ср 01 апр 2020 07:36:37
Build Host  : x86-01.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://sebastien.godard.pagesperso-orange.fr/
Summary     : Collection of performance monitoring tools for Linux
Description :
The sysstat package contains sar, sadf, mpstat, iostat, pidstat, nfsiostat-sysstat,
tapestat, cifsiostat and sa tools for Linux.
The sar command collects and reports system activity information. This
information can be saved in a file in a binary format for future inspection. The
statistics reported by sar concern I/O transfer rates, paging activity,
process-related activities, interrupts, network activity, memory and swap space
utilization, CPU utilization, kernel activities and TTY statistics, among
others. Both UP and SMP machines are fully supported.
The sadf command may be used to display data collected by sar in various formats
(CSV, XML, etc.).
The iostat command reports CPU utilization and I/O statistics for disks.
The tapestat command reports statistics for tapes connected to the system.
The mpstat command reports global and per-processor statistics.
The pidstat command reports statistics for Linux tasks (processes).
The nfsiostat-sysstat command reports I/O statistics for network file systems.
The cifsiostat command reports I/O statistics for CIFS file systems.
```
3. Install sysstat package and get information about files installed by this package.
```bash
[ValkyRa@centos ~]$ sudo rpm -ivh sysstat-10.1.5-19.el7.x86_64.rpm
[sudo] пароль для ValkyRa: 
Подготовка...               ################################# [100%]
	пакет sysstat-10.1.5-19.el7.x86_64 уже установлен
```

- Add NGINX repository (need to find repository config on https://www.nginx.com/) and complete the following tasks using yum:

1. Check if NGINX repository enabled or not.

Проверяем /etc/yum.repos.d и видим, что NGINX у меня не установлен и сам репозиторий недоступен:
```bash
[ValkyRa@centos yum.repos.d]$ ls -la
итого 52
drwxr-xr-x.   2 root root  220 ноя 24 16:19 .
drwxr-xr-x. 145 root root 8192 дек 23 14:30 ..
-rw-r--r--.   1 root root 1664 ноя 23  2020 CentOS-Base.repo
-rw-r--r--.   1 root root 1309 ноя 23  2020 CentOS-CR.repo
-rw-r--r--.   1 root root  649 ноя 23  2020 CentOS-Debuginfo.repo
-rw-r--r--.   1 root root  314 ноя 23  2020 CentOS-fasttrack.repo
-rw-r--r--.   1 root root  630 ноя 23  2020 CentOS-Media.repo
-rw-r--r--.   1 root root 1331 ноя 23  2020 CentOS-Sources.repo
-rw-r--r--.   1 root root 8515 ноя 23  2020 CentOS-Vault.repo
-rw-r--r--.   1 root root  616 ноя 23  2020 CentOS-x86_64-kernel.repo
```
2. Install NGINX.
Попробуем установить nginx, и обнаруживаем что не находит репозиторий:
```bash
[ValkyRa@centos yum.repos.d]$ sudo yum install nginx
[sudo] пароль для ValkyRa: 
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
base                                                     | 3.6 kB     00:00     
extras                                                   | 2.9 kB     00:00     
updates                                                  | 2.9 kB     00:00     
Пакета с названием nginx не найдено.
Ошибка: Выполнять нечего
```
Поэтому идём обходным путём:

Открываем путь /etc/yum.repos.d и создаём файл nginx.repo и вписываем в него следующее:
```
[ValkyRa@centos yum.repos.d]$ sudo emacs nginx.repo
```
```bash
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=0
enabled=1
```
Далее теперь пробуем установить:
```bash
[ValkyRa@centos yum.repos.d]$ sudo yum install nginx
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
nginx                                                    | 2.9 kB     00:00     
nginx/x86_64/primary_db                                    | 227 kB   00:00     
Разрешение зависимостей
--> Проверка сценария
---> Пакет nginx.x86_64 1:1.21.4-1.el7.ngx помечен для установки
--> Проверка зависимостей окончена

Зависимости определены

================================================================================
 Package       Архитектура    Версия                        Репозиторий   Размер
================================================================================
Установка:
 nginx         x86_64         1:1.21.4-1.el7.ngx            nginx         795 k

Итого за операцию
================================================================================
Установить  1 пакет

Объем загрузки: 795 k
Объем изменений: 2.8 M
Is this ok [y/d/N]: y
Downloading packages:
nginx-1.21.4-1.el7.ngx.x86_64.rpm                          | 795 kB   00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Установка   : 1:nginx-1.21.4-1.el7.ngx.x86_64                             1/1 
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* https://nginx.org/en/docs/

Please subscribe to nginx-announce mailing list to get
the most important news about nginx:
* https://nginx.org/en/support.html

Commercial subscriptions for nginx are available on:
* https://nginx.com/products/

----------------------------------------------------------------------
  Проверка    : 1:nginx-1.21.4-1.el7.ngx.x86_64                             1/1 

Установлено:
  nginx.x86_64 1:1.21.4-1.el7.ngx      
```

3. Check yum history and undo NGINX installation.
```bash
ValkyRa@centos yum.repos.d]$ sudo yum history
Загружены модули: fastestmirror, langpacks
ID     | Вход пользователя        | Дата и время     | Действия       | Изменен
-------------------------------------------------------------------------------
    14 | ValkyRa <ValkyRa>        | 2021-12-23 15:14 | Install        |    1 EE
    13 | Система <unset>          | 2021-12-22 18:55 | Update         |   12   
    12 | Система <unset>          | 2021-12-13 15:30 | Update         |    6  <
    11 | ValkyRa <ValkyRa>        | 2021-12-04 17:37 | Erase          |    1 > 
    10 | Система <unset>          | 2021-12-03 14:19 | I, U           |   49 EE
     9 | ValkyRa <ValkyRa>        | 2021-12-01 17:12 | Install        |    3   
     8 | ValkyRa <ValkyRa>        | 2021-12-01 16:57 | Install        |    6   
     7 | ValkyRa <ValkyRa>        | 2021-12-01 15:46 | Install        |    1   
     6 | ValkyRa <ValkyRa>        | 2021-12-01 15:35 | Install        |    1   
     5 | ValkyRa <ValkyRa>        | 2021-11-24 21:09 | Install        |    1   
     4 | ValkyRa <ValkyRa>        | 2021-11-24 19:55 | Install        |    9   
     3 | ValkyRa <ValkyRa>        | 2021-11-24 16:29 | Install        | 1105 EE
     2 | ValkyRa <ValkyRa>        | 2021-11-24 16:19 | I, U           |   99   
     1 | Система <unset>          | 2021-11-24 16:09 | Install        |  301   
history list
```
Данная установка прописалась в истории, теперь удалим nginx путём erase
```bash
[ValkyRa@centos yum.repos.d]$ sudo yum erase nginx
Загружены модули: fastestmirror, langpacks
Разрешение зависимостей
--> Проверка сценария
---> Пакет nginx.x86_64 1:1.21.4-1.el7.ngx помечен для удаления
--> Проверка зависимостей окончена

Зависимости определены

================================================================================
 Package       Архитектура    Версия                       Репозиторий    Размер
================================================================================
Удаление:
 nginx         x86_64         1:1.21.4-1.el7.ngx           @nginx         2.8 M

Итого за операцию
================================================================================
Удалить  1 пакет

Объем изменений: 2.8 M
Продолжить? [y/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Удаление    : 1:nginx-1.21.4-1.el7.ngx.x86_64                             1/1 
  Проверка    : 1:nginx-1.21.4-1.el7.ngx.x86_64                             1/1 

Удалено:
  nginx.x86_64 1:1.21.4-1.el7.ngx                                               

Выполнено!
```

4. Disable NGINX repository.

Я не очень понял вопроса о том, что нужно отключить NGINX, но что отключать, если мы это удалили в предыдущем пункте?

Но если он бы остался, то по идее нужно выполнить две команды:
```
sudo /etc/init.d/nginx stop
sudo chkconfig nginx off
```
И проверить его отсутствие в chkconfig по команде:
```
chkconfig --list
```

Либо есть другой вариант.
```
yum-config-manager --disable nginx
```
Тем самым мы отключим репозиторий через yum.

Либо вариант третий:

Заходим в конфиг nginx, что находится по пути, /etc/yum.repos.d/nginx.repo
```
[ValkyRa@centos yum.repos.d]$ sudo emacs nginx.repo
```
Находим ранее введённый конфиг для nginx, дабы мы могли его установить, и в конфиге изменяем "enabled=1" на 0. Сохраняем конфиг и закрываем, и после этого в repolist-е, nginx пропадёт, что будет говорить о том, что он отключён.

5. Remove sysstat package installed in the first task.
Удаляем сам файл с пакетом, что мы через wget подтягивали, для установки sysstat-а
```
[ValkyRa@centos init.d]$ cd ~
[ValkyRa@centos ~]$ rm -rf sysstat-10.1.5-19.el7.x86_64.rpm
```
А затем удалим и сам sysstat командой: ``` sudo yum erase sysstat ```

6. Install EPEL repository and get information about it.

Пробую установить и как и с nginx-ом, сразу установить не получается. 
```bash
sudo yum install epel
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
Пакета с названием epel не найдено.
Ошибка: Выполнять нечего
```
В таком случае, делаем поиск: 
```bash
[ValkyRa@centos ~]$ yum search epel
Загружены модули: fastestmirror, langpacks
Determining fastest mirrors
 * base: mirror.docker.ru
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
============================== N/S matched: epel ===============================
epel-release.noarch : Extra Packages for Enterprise Linux repository
                    : configuration

  Показаны только совпадения по названиям и описаниям, для большего используйте «search all».
```
Находим и устанавливаем:
```bash
[ValkyRa@centos ~]$ sudo yum install epel-release
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * extras: mirrors.powernet.com.ru
 * updates: ftp.nsc.ru
Разрешение зависимостей
--> Проверка сценария
---> Пакет epel-release.noarch 0:7-11 помечен для установки
--> Проверка зависимостей окончена

Зависимости определены

================================================================================
 Package                Архитектура      Версия          Репозиторий      Размер
================================================================================
Установка:
 epel-release           noarch           7-11            extras            15 k

Итого за операцию
================================================================================
Установить  1 пакет

Объем загрузки: 15 k
Объем изменений: 24 k
Is this ok [y/d/N]: y
Downloading packages:
epel-release-7-11.noarch.rpm                               |  15 kB   00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Установка   : epel-release-7-11.noarch                                    1/1 
  Проверка    : epel-release-7-11.noarch                                    1/1 

Установлено:
  epel-release.noarch 0:7-11                                                    
```
Теперь получим информацию:
```bash
[ValkyRa@centos ~]$ yum repoinfo epel
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirror.docker.ru
 * epel: ftp.lysator.liu.se
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
Код репозитория      : epel/x86_64
Имя репозитория      : Extra Packages for Enterprise Linux 7 - x86_64
Состояние репозитория: включено
Ревизия репозитория  : 1640137513
Репозиторий обновлен : Wed Dec 22 04:56:31 2021
Пакеты репозитория   : 13 699
Размер репозитория   : 16 G
metalink репозитория : https://mirrors.fedoraproject.org/metalink?repo=epel-7&arch=x86_64
  Обновлено          : Wed Dec 22 04:56:31 2021
baseurl репозитория  : https://ftp.lysator.liu.se/pub/epel/7/x86_64/ (101 more)
Окончание срока репозитория: 21 600 секунд(а) (осталось: Thu Dec 23 15:36:51
                           : 2021)
  Filter     : read-only:present
Файл репозитория:/etc/yum.repos.d/epel.repo

repolist: 13 699
```
7. Find how much packages provided exactly by EPEL repository.
```
[ValkyRa@centos ~]$ sudo yum repo-pkgs epel list
```
Результат скринить здесь не буду, потому что он слишком длинный.

8. Install ncdu package from EPEL repo.
```bash
[ValkyRa@centos ~]$ sudo yum repo-pkgs epel install ncdu
Загружены модули: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.powernet.com.ru
 * epel: ftp.lysator.liu.se
 * extras: mirror.axelname.ru
 * updates: mirror.axelname.ru
Разрешение зависимостей
--> Проверка сценария
---> Пакет ncdu.x86_64 0:1.16-1.el7 помечен для установки
--> Проверка зависимостей окончена

Зависимости определены

================================================================================
 Package         Архитектура       Версия                 Репозиторий     Размер
================================================================================
Установка:
 ncdu            x86_64            1.16-1.el7             epel             53 k

Итого за операцию
================================================================================
Установить  1 пакет

Объем загрузки: 53 k
Объем изменений: 89 k
Is this ok [y/d/N]: y
Downloading packages:
предупреждение: /var/cache/yum/x86_64/7/epel/packages/ncdu-1.16-1.el7.x86_64.rpm: Заголовок V4 RSA/SHA256 Signature, key ID 352c64e5: NOKEY
Публичный ключ для ncdu-1.16-1.el7.x86_64.rpm не установлен
ncdu-1.16-1.el7.x86_64.rpm                                 |  53 kB   00:00     
Получение ключа из file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Импорт GPG ключа 0x352C64E5:
 Владелец   : "Fedora EPEL (7) <epel@fedoraproject.org>"
 Отпечаток  : 91e9 7d7c 4a5e 96f1 7f3e 888f 6a2f aea2 352c 64e5
 Пакет      : epel-release-7-11.noarch (@extras)
 Источник   : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Продолжить? [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Установка   : ncdu-1.16-1.el7.x86_64                                      1/1 
  Проверка    : ncdu-1.16-1.el7.x86_64                                      1/1 

Установлено:
  ncdu.x86_64 0:1.16-1.el7                                                      

Выполнено!

```
*Extra task:
    Need to create an rpm package consists of a shell script and a text file. The script should output words count stored in file.
    
Это задание было решено пропустить.    

## Work with files
1. Find all regular files below 100 bytes inside your home directory.

Находясь в домашней директории пишем:
```
[ValkyRa@centos ~]$ find ~/ -size -100c -type f 
```
Здесь мы используем команду find, в домашней директории, -size -100c подразумевает размер файлов меньше 100 байт, где “c” – bytes и тип файла

Выводить результат не буду, т.к. список в очередной раз очень длинный

2. Find an inode number and a hard links count for the root directory. The hard link count should be about 17. Why?

Для того, чтобы найти нужное кол-во ссылок и inode значение введём команду stat и укажем /
```
[ValkyRa@centos ~]$ stat /
  Файл: «/»
  Размер: 251       	Блоков: 0          Блок В/В: 4096   каталог
Устройство: fd00h/64768d	Inode: 64          Ссылки: 18
Доступ: (0555/dr-xr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Контекст: system_u:object_r:root_t:s0
Доступ: 2021-12-23 22:23:50.486000000 +0300
Модифицирован: 2021-12-22 18:55:02.968000000 +0300
Изменён: 2021-12-22 18:55:02.968000000 +0300
 Создан: -
``` 
И получаем Inode: 64 и кол-во hard-ссылок – 18.

Почему ссылок 18? Введём ls –la
```bash
[ValkyRa@centos ~]$ ls -la /
итого 44
dr-xr-xr-x.  18 root root     237 дек 23 22:32 .
dr-xr-xr-x.  18 root root     237 дек 23 22:32 ..
lrwxrwxrwx.   1 root root       7 ноя 24 16:09 bin -> usr/bin
dr-xr-xr-x.   5 root root    4096 дек  3 14:23 boot
drwxr-xr-x.  20 root root    3220 дек 23 22:24 dev
drwxr-xr-x. 145 root root    8192 дек 23 22:24 etc
drwxr-xr-x.  12 root root     148 дек 13 20:14 home
lrwxrwxrwx.   1 root root       7 ноя 24 16:09 lib -> usr/lib
lrwxrwxrwx.   1 root root       9 ноя 24 16:09 lib64 -> usr/lib64
drwxr-xr-x.   2 root root       6 дек  1 17:17 media
drwxr-xr-x.   2 root root       6 апр 11  2018 mnt
drwxr-xr-x.   4 root root      62 дек 16 17:22 opt
dr-xr-xr-x. 243 root root       0 дек 23 22:23 proc
dr-xr-x---.   7 root root     245 дек 23 15:39 root
drwxr-xr-x.  42 root root    1280 дек 23 22:26 run
lrwxrwxrwx.   1 root root       8 ноя 24 16:09 sbin -> usr/sbin
drwxrwx---.   1 root vboxsf  4096 дек 13 21:11 share
drwxr-xr-x.   2 root root       6 апр 11  2018 srv
dr-xr-xr-x.  13 root root       0 дек 23 22:23 sys
drwxrwxrwt. 133 root root   16384 дек 23 22:31 tmp
drwxr-xr-x.  13 root root     155 ноя 24 16:09 usr
drwxr-xr-x.  21 root root    4096 ноя 24 16:36 var
```
И получаем, что при создании директории, создаются hard-ссылка, и поскольку директорий в корне 18, то и hard-ссылок будет 18, также стоит учитывать, что переходы "." и ".." тоже считаются директориями.

3. Check what inode numbers have "/" and "/boot" directory. Why?
 
![image](https://user-images.githubusercontent.com/15772109/147344799-5812785c-bb7d-4251-bf43-26c2f604863b.png)

![image](https://user-images.githubusercontent.com/15772109/147344812-4d2b2c4b-6ed0-477e-8681-f37555b6b9b2.png)


Поскольку “/” и boot являются разными файловыми системами и имеют разные точки монтирования, их мы указывали при установке системы. Поэтому у них могут быть одинаковые inode, но при этом они не пересекаются. Проверить это можно путём команды df -Th
 
![image](https://user-images.githubusercontent.com/15772109/147344823-ac52614c-7f2e-4d1e-8813-d25c0f04d04f.png)

И видим, что они находятся в разных разделах и имеют разные точки монтирования.

4. Check the root directory space usage by du command. Compare it with an information from df. If you find differences, try to find out why it happens. 
```
[ValkyRa@centos ~]$ sudo du -shx /
[sudo] пароль для ValkyRa: 
5,0G	/
```
```
[ValkyRa@centos ~]$ sudo df -h /
Файловая система        Размер Использовано  Дост Использовано% Cмонтировано в
/dev/mapper/centos-root    38G         5,1G   32G           14% /
```

5. Check disk space usage of /var/log directory using ncdu

Проверяем командой: 
```bash
[ValkyRa@centos ~]$ ncdu /var/log/

ncdu 1.16 ~ Use the arrow keys to navigate, press ? for help                    
--- /var/log -------------------------------------------------------------------
    2,0 MiB [###########] /sa                                                   
    2,0 MiB [########## ] /anaconda
    1,8 MiB [#########  ]  messages-20211219
    1,4 MiB [#######    ]  messages-20211205
    1,1 MiB [#####	]  messages-20211213
    1,0 MiB [#####      ]  messages-20211128
  740,0 KiB [###        ]  messages
  248,0 KiB [#          ]  wtmp
  132,0 KiB [           ]  boot.log-20211217
   84,0 KiB [           ]  secure-20211219
   80,0 KiB [           ] /tuned
   76,0 KiB [           ]  yum.log-20211124
   52,0 KiB [           ]  cron-20211219
   52,0 KiB [           ]  boot.log-20211218
   44,0 KiB [           ]  lastlog
   40,0 KiB [           ]  secure-20211205
   40,0 KiB [           ]  boot.log-20211223
   40,0 KiB [           ]  boot.log-20211215
   36,0 KiB [           ]  dmesg.old
   36,0 KiB [           ]  dmesg
   36,0 KiB [           ]  boot.log-20211222
 Total disk usage:  11,9 MiB  Apparent size:  11,9 MiB  Items: 154  
 ```
И выходим через “Q”
