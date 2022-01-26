## Task 1:
### As a result of each point, you should provide a corresponding command.
1.1. SSH to remotehost using username and password provided to you in Slack. Log out from remotehost.

Собственно, логинимся на учётную запись, которая была предоставлена:
```bash
[ValkyRa@centos ~]$ ssh Nikita_Semenov@18.221.144.175
Nikita_Semenov@18.221.144.175's password: 

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 2 packages available
Run "sudo yum update" to apply all updates.
[Nikita_Semenov@ip-172-31-33-155 ~]$
```
И жмём logout
```bash
[Nikita_Semenov@ip-172-31-33-155 ~]$ logout
Connection to 18.221.144.175 closed.
```
1.2. Generate new SSH key-pair on your localhost with name "hw-5" (keys should be created in ~/.ssh folder).

Переходим по скрытой директории ssh и генерим там ключи RSA, называя hw-5 и указывая passphrase (по желанию)
```bash
[ValkyRa@centos ~]$ cd ~/.ssh
[ValkyRa@centos .ssh]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ValkyRa/.ssh/id_rsa): hw-5
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in hw-5.
Your public key has been saved in hw-5.pub.
The key fingerprint is:
SHA256:hf36zL18rkQzDppTlxlsnu3FP6ZdWRpHVkwNxiZwiNM ValkyRa@centos
The key's randomart image is:
+---[RSA 2048]----+
|         o.o..o++|
|        ooE..oo +|
|        ..o  o+ o|
|         . . o X |
|        S   + @ *|
|           = = B=|
|          =   +++|
|           = ++ +|
|            +.=*.|
+----[SHA256]-----+
```
1.3. Set up key-based authentication, so that you can SSH to remotehost without password.

Нам нужно скопировать ssh-ключ командой ssh-copy-id и указываем имя по которому будет подключаться, используя уже ключ, а не пароль.
```bash
[ValkyRa@centos .ssh]$ ssh-copy-id Nikita_Semenov@18.221.144.175
/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Nikita_Semenov@18.221.144.175's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'Nikita_Semenov@18.221.144.175'"
and check to make sure that only the key(s) you wanted were added.
```

1.4. SSH to remotehost without password. Log out from remotehost.

Логинимся повторно, но уже не вводя пароль.
```bash
[ValkyRa@centos .ssh]$ ssh Nikita_Semenov@18.221.144.175
Last login: Fri Dec 17 11:25:05 2021 from 5.18.196.211

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 2 packages available
Run "sudo yum update" to apply all updates.
```
И после этого разлогиниваемся
```bash
[Nikita_Semenov@ip-172-31-33-155 ~]$ logout
Connection to 18.221.144.175 closed.
```
1.5. Create SSH config file, so that you can SSH to remotehost simply running `ssh remotehost` command. As a result, provide output of command `cat ~/.ssh/config`.

Создаём файлик командой touch config, находясь в каталоге .ssh, ну или же touch ~/.ssh/config

Config file:
```bash
Host remotehost
    HostName 18.221.144.175 
    User Nikita_Semenov
```
Пробуем запустить и видим, что SSH ругается на права, что они есть не только у нас 
```bash
[ValkyRa@centos .ssh]$ ssh remotehost
Bad owner or permissions on /home/ValkyRa/.ssh/config
```
Чтобы это исправить, забираем права у всех, и даём себе права на чтение и запись.
```
[ValkyRa@centos .ssh]$ chmod 600 config
```
Подключаемся второй раз:
```bash
[ValkyRa@centos .ssh]$ ssh remotehost
Last login: Fri Dec 17 11:27:39 2021 from 5.18.196.211

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 2 packages available
Run "sudo yum update" to apply all updates.
[Nikita_Semenov@ip-172-31-33-155 ~]$
```

1.6. Using command line utility (curl or telnet) verify that there are some webserver running on port 80 of webserver.  Notice that webserver has a private network IP, so you can access it only from the same network (when you are on remotehost that runs in the same private network). Log out from remotehost.

Используем curl, находясь на удалённой машине, чтобы подключиться к webserver-у, а именно к приватному IP, и получаем следующую картину: 
```bash
[Nikita_Semenov@ip-172-31-33-155 ~]$ curl 172.31.45.237
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
                <title>Apache HTTP Server Test Page powered by CentOS</title>
                <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

    <!-- Bootstrap -->
    <link href="/noindex/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="noindex/css/open-sans.css" type="text/css" />

<style type="text/css"><!--

body {
  font-family: "Open Sans", Helvetica, sans-serif;
  font-weight: 100;
  color: #ccc;
  background: rgba(10, 24, 55, 1);
  font-size: 16px;
}

h2, h3, h4 {
  font-weight: 200;
}

h2 {
  font-size: 28px;
}

.jumbotron {
  margin-bottom: 0;
  color: #333;
  background: rgb(212,212,221); /* Old browsers */
  background: radial-gradient(ellipse at center top, rgba(255,255,255,1) 0%,rgba(174,174,183,1) 100%); /* W3C */
}

.jumbotron h1 {
  font-size: 128px;
  font-weight: 700;
  color: white;
  text-shadow: 0px 2px 0px #abc,
               0px 4px 10px rgba(0,0,0,0.15),
               0px 5px 2px rgba(0,0,0,0.1),
               0px 6px 30px rgba(0,0,0,0.1);
}

.jumbotron p {
  font-size: 28px;
  font-weight: 100;
}

.main {
   background: white;
   color: #234;
   border-top: 1px solid rgba(0,0,0,0.12);
   padding-top: 30px;
   padding-bottom: 40px;
}

.footer {
   border-top: 1px solid rgba(255,255,255,0.2);
   padding-top: 30px;
}

    --></style>
</head>
<body>
  <div class="jumbotron text-center">
    <div class="container">
          <h1>Hello!</h1>
                <p class="lead">You are here because you're probably a DevOps courses member. In that case you should open <a href=" https://www.youtube.com/watch?v=dQw4w9WgXcQ "> THIS LINK </a></p>
                </div>
  </div>
</body></html>
```
Убедившись, что оно работает, разлогиниваемся.
```
[Nikita_Semenov@ip-172-31-33-155 ~]$ logout
Connection to 18.221.144.175 closed.
```

1.7. Using SSH setup port forwarding, so that you can reach webserver from your localhost (choose any free local port you like).

Находясь на локальной машине, коннектимся к webserver-у, используя –L что имеется в виду подключение через локальный порт, и используем remotehost, как данные, под которыми логинились ранее на удалённую машину.
```bash
[ValkyRa@centos ~]$ ssh -L 8080:172.31.45.237:80 remotehost
Last login: Fri Dec 17 12:22:45 2021 from localhost

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
No packages needed for security; 2 packages available
Run "sudo yum update" to apply all updates.
```

1.8. Like in 1.6, but on localhost using command line utility verify that localhost and port you have specified act like webserver, returning same result as in 1.6.

По аналогии с заданием 1.6, не выходя из удалённой машины, открываем второй терминал и проверяем порт, прокинутый ранее в 1.7
```bash
[ValkyRa@centos ~]$ curl localhost:8080
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"><html><head>
<meta http-equiv="content-type" content="text/html; charset=UTF-8">
                <title>Apache HTTP Server Test Page powered by CentOS</title>
                <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

    <!-- Bootstrap -->
    <link href="/noindex/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="noindex/css/open-sans.css" type="text/css" />

<style type="text/css"><!--

body {
  font-family: "Open Sans", Helvetica, sans-serif;
  font-weight: 100;
  color: #ccc;
  background: rgba(10, 24, 55, 1);
  font-size: 16px;
}

h2, h3, h4 {
  font-weight: 200;
}

h2 {
  font-size: 28px;
}

.jumbotron {
  margin-bottom: 0;
  color: #333;
  background: rgb(212,212,221); /* Old browsers */
  background: radial-gradient(ellipse at center top, rgba(255,255,255,1) 0%,rgba(174,174,183,1) 100%); /* W3C */
}

.jumbotron h1 {
  font-size: 128px;
  font-weight: 700;
  color: white;
  text-shadow: 0px 2px 0px #abc,
               0px 4px 10px rgba(0,0,0,0.15),
               0px 5px 2px rgba(0,0,0,0.1),
               0px 6px 30px rgba(0,0,0,0.1);
}

.jumbotron p {
  font-size: 28px;
  font-weight: 100;
}

.main {
   background: white;
   color: #234;
   border-top: 1px solid rgba(0,0,0,0.12);
   padding-top: 30px;
   padding-bottom: 40px;
}

.footer {
   border-top: 1px solid rgba(255,255,255,0.2);
   padding-top: 30px;
}

    --></style>
</head>
<body>
  <div class="jumbotron text-center">
    <div class="container">
          <h1>Hello!</h1>
                <p class="lead">You are here because you're probably a DevOps courses member. In that case you should open <a href="https://www.youtube.com/watch?v=dQw4w9WgXcQ"> THIS LINK </a></p>
                </div>
  </div>
</body></html>
```
В конечном итоге получаем идентичную картину, только на сей раз с localhost-а.

1.9. (*) Open webserver webpage in browser of your Host machine of VirtualBox (Windows, or Mac, or whatever else you use). You may need to setup port forwarding in settings of VirtualBox.

Это задание увы, пропустил.

## Task 2:
### Following tasks should be executed on your localhost as you will need root privileges
2.1. Imagine your localhost has been relocated to Havana. Change the time zone on the localhost to Havana and verify the time zone has been changed properly (may be multiple commands).

Поскольку Havana является столицей Кубы, соответственно менять будем на часовой пояс Кубы.
```bash
[ValkyRa@centos ~]$ date
Пт дек 17 15:07:51 MSK 2021
[ValkyRa@centos ~]$ sudo ln -sf /usr/share/zoneinfo/Cuba /etc/localtime
[sudo] пароль для ValkyRa: 
[ValkyRa@centos ~]$ date
Пт дек 17 07:05:24 CST 2021
```
Есть ещё другая Havana, что находится в Америке, так что решил обозначить и этот вариант.
```
[ValkyRa@centos ~]$ sudo ln -sf /usr/share/zoneinfo/America/Havana /etc/localtime
```
Но как оказалось, разницы в часовых поясах нет, посему сойдёт как за второй способ :)

2.2. Find all systemd journal messages on localhost, that were recorded in the last 50 minutes and originate from a system service started with user id 81 (single command).

Запуская journalctl с флагом –u unit, используем awk с флагом F формат разделителя, разделитель у нас :, и в файле по пути /etc/passwd ищем нужный нам id пользователя, и получаем по нему username и ищем по этому юзернейму в journalctl используя флаг –S since и необходимую дату и время и для удобства, сохраняем результат в отдельный файл
```bash
[ValkyRa@centos ~]$ journalctl -u `awk -F":" '/81:81/ {print $1}' /etc/passwd` -S '2021-12-17 15:11:00' > file.txt
Открываем сам файл с результатом
[ValkyRa@centos ~]$ cat file.txt
-- Logs begin at Пт 2021-12-17 16:01:19 MSK, end at Пт 2021-12-17 16:03:01 MSK. --
дек 17 16:01:39 centos systemd[1]: Started D-Bus System Message Bus.
дек 17 16:01:44 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.systemd1'
дек 17 16:01:51 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.hostname1' unit='dbus-org.freedesktop.hostname1.service'
дек 17 16:01:51 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.hostname1'
дек 17 16:01:52 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.nm_dispatcher' unit='dbus-org.freedesktop.nm-dispatcher.service'
дек 17 16:01:52 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.nm_dispatcher'
дек 17 16:02:08 centos dbus[760]: [system] Activating service name='org.freedesktop.problems' (using servicehelper)
дек 17 16:02:08 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.problems'
дек 17 16:02:22 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.UPower' unit='upower.service'
дек 17 16:02:22 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.UPower'
дек 17 16:02:23 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.RealtimeKit1' unit='rtkit-daemon.service'
дек 17 16:02:23 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.RealtimeKit1'
дек 17 16:02:24 centos dbus[760]: [system] Activating via systemd: service name='org.bluez' unit='dbus-org.bluez.service'
дек 17 16:02:27 centos dbus[760]: [system] Reloaded configuration
дек 17 16:02:31 centos dbus[760]: [system] Reloaded configuration
дек 17 16:02:34 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.Accounts' unit='accounts-daemon.service'
дек 17 16:02:34 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.Accounts'
дек 17 16:02:34 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.GeoClue2' unit='geoclue.service'
дек 17 16:02:34 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.bolt' unit='bolt.service'
дек 17 16:02:35 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.bolt'
дек 17 16:02:35 centos dbus[760]: [system] Activating via systemd: service name='fi.w1.wpa_supplicant1' unit='wpa_supplicant.service'
дек 17 16:02:35 centos dbus[760]: [system] Successfully activated service 'fi.w1.wpa_supplicant1'
дек 17 16:02:35 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.GeoClue2'
дек 17 16:02:36 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.UDisks2' unit='udisks2.service'
дек 17 16:02:40 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.UDisks2'
дек 17 16:02:40 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.PackageKit' unit='packagekit.service'
дек 17 16:02:41 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.PackageKit'
дек 17 16:02:41 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.hostname1' unit='dbus-org.freedesktop.hostname1.service'
дек 17 16:02:41 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.locale1' unit='dbus-org.freedesktop.locale1.service'
дек 17 16:02:41 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.ColorManager' unit='colord.service'
дек 17 16:02:41 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.hostname1'
дек 17 16:02:41 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.locale1'
дек 17 16:02:42 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.ColorManager'
дек 17 16:02:46 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.GeoClue2' unit='geoclue.service'
дек 17 16:02:46 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.GeoClue2'
дек 17 16:02:49 centos dbus[760]: [system] Activating via systemd: service name='org.freedesktop.fwupd' unit='fwupd.service'
дек 17 16:02:49 centos dbus[760]: [system] Successfully activated service 'org.freedesktop.fwupd'
дек 17 16:02:49 centos dbus[760]: [system] Failed to activate service 'org.bluez': timed out
```

2.3. Configure rsyslogd by adding a rule to the newly created configuration file /etc/rsyslog.d/auth-errors.conf to log all security and authentication messages with the priority alert and higher to the /var/log/auth-errors file. Test the newly added log directive with the logger command (multiple commands).
```bash
[ValkyRa@centos ~]$ sudo emacs /etc/rsyslog.d/auth-errors.conf
[sudo] пароль для ValkyRa: 
```
Вводим данный скрипт:
```bash
if
( $syslogseverity < 2 and $syslogfacility-text startswith "auth" )
then
{ action(type="omfile" file="/var/log/auth-errors") }
```
По скрипту, если у нас значение Severity < 2 (то бишь Alert с severity 1 или Emergency с 0) и при этом Facility у нас auth, то записывать данные в файл /var/log/auth-errors
Перед тестом, надо перезапустить службу rsyslog
```
[ValkyRa@centos ~]$ systemctl restart rsyslog
```
И после этого тестируем:
```
[ValkyRa@centos ~]$ logger -p auth.emerg "Test"

Broadcast message from systemd-journald@centos (Sun 2021-12-19 14:33:13 MSK):

ValkyRa[4965]: Test


Message from syslogd@centos at Dec 19 14:33:13 ...
 ValkyRa:Test
```
Попробовали вывести сообщение Test приоритета Emergency, и теперь посмотрим лог файла: 
```bash
[ValkyRa@centos ~]$ sudo cat /var/log/auth-errors 
Dec 19 14:33:13 centos ValkyRa: Test
```
И видим, что данные появились в логе.
Теперь попробуем пониже приоритет, а именно Critical
```bash
[ValkyRa@centos ~]$ logger -p auth.crit "CritTest"
[ValkyRa@centos ~]$ sudo cat /var/log/auth-errors 
```
Проверяем лог, и видим что данные не внеслись в лог, поскольку стоит условие, заполнять только Alert-ы и Emergency.
```
Dec 19 14:33:13 centos ValkyRa: Test
```
Ну и на крайний случай, попробуем alert-ы
```bash
[ValkyRa@centos ~]$ logger -p auth.alert "AlertTest"
[ValkyRa@centos ~]$ sudo cat /var/log/auth-errors
```
Открываем лог, и получаем там новую запись alerttest   
```bash
Dec 19 14:33:13 centos ValkyRa: Test
Dec 19 14:35:34 centos ValkyRa: AlertTest
```
