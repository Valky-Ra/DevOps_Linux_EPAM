# Homework

## Задание 0

### В данном задании мы устанавливаем вторую виртуальную машину и с помощью первой машины, что была установлена заранее, было совершено подключение ко второй, используя SSH

```bash
[ValkyRa@centos ~]$ ssh ValkyRa2@192.168.0.162
ValkyRa2@192.168.0.162's password:
Last login: Sun Nov 28 16:54:36 2021
[ValkyRa2@CentOS ~]$ w
 17:06:37 up 12 min,  2 users,  load average: 0,00, 0,03, 0,05
USER 	   TTY  	FROM         	LOGIN@   IDLE   JCPU   PCPU WHAT
ValkyRa2 tty1                 16:54    11:41   0.01s  0.01s -bash
ValkyRa2 pts/0	192.168.0.106	16:55	   5.00s   0.01s  0.01s w
```

## Задание 1

### В этом задании, используя команду ls, мы выводим на экран все файлы, которые расположены в секционных директориях /usr/share/man/manX и содержат слово "config" в имени.
Способ 1:
```bash
[ValkyRa2@CentOS ~]$ ls /usr/share/man/man*/*config*
/usr/share/man/man1/pkg-config.1.gz
/usr/share/man/man5/config.5ssl.gz
/usr/share/man/man5/config-util.5.gz
/usr/share/man/man5/selinux_config.5.gz
/usr/share/man/man5/ssh_config.5.gz
/usr/share/man/man5/sshd_config.5.gz
/usr/share/man/man5/x509v3_config.5ssl.gz
/usr/share/man/man8/authconfig.8.gz
/usr/share/man/man8/authconfig-tui.8.gz
/usr/share/man/man8/chkconfig.8.gz
/usr/share/man/man8/grub2-mkconfig.8.gz
/usr/share/man/man8/iprconfig.8.gz
/usr/share/man/man8/lvm-config.8.gz
/usr/share/man/man8/lvmconfig.8.gz
/usr/share/man/man8/lvm-dumpconfig.8.gz
/usr/share/man/man8/sys-unconfig.8.gz
```
Способ 2:

```bash
[ValkyRa2@CentOS ~]$ ls /usr/share/man/man* | grep config
pkg-config.1.gz
config.5ssl.gz
config-util.5.gz
selinux_config.5.gz
ssh_config.5.gz
sshd_config.5.gz
x509v3_config.5ssl.gz
authconfig.8.gz
authconfig-tui.8.gz
chkconfig.8.gz
grub2-mkconfig.8.gz
iprconfig.8.gz
lvm-config.8.gz
lvmconfig.8.gz
lvm-dumpconfig.8.gz
sys-unconfig.8.gz
```
Также следующим пунктом следует вызовом ls найти все файлы, содержащие слово "system" в каталогах /usr/share/man/man1 и /usr/share/man/man7. Что и выполняем следующими командами:

```bash
[ValkyRa2@CentOS ~]$ ls /usr/share/man/man1/*system* /usr/share/man/man7/*system*
/usr/share/man/man1/systemctl.1.gz
/usr/share/man/man1/systemd.1.gz
/usr/share/man/man1/systemd-analyze.1.gz
/usr/share/man/man1/systemd-ask-password.1.gz
/usr/share/man/man1/systemd-bootchart.1.gz
/usr/share/man/man1/systemd-cat.1.gz
/usr/share/man/man1/systemd-cgls.1.gz
/usr/share/man/man1/systemd-cgtop.1.gz
/usr/share/man/man1/systemd-delta.1.gz
/usr/share/man/man1/systemd-detect-virt.1.gz
/usr/share/man/man1/systemd-escape.1.gz
/usr/share/man/man1/systemd-firstboot.1.gz
/usr/share/man/man1/systemd-firstboot.service.1.gz
/usr/share/man/man1/systemd-inhibit.1.gz
/usr/share/man/man1/systemd-machine-id-commit.1.gz
/usr/share/man/man1/systemd-machine-id-setup.1.gz
/usr/share/man/man1/systemd-notify.1.gz
/usr/share/man/man1/systemd-nspawn.1.gz
/usr/share/man/man1/systemd-path.1.gz
/usr/share/man/man1/systemd-run.1.gz
/usr/share/man/man1/systemd-tty-ask-password-agent.1.gz

/usr/share/man/man7/lvmsystemid.7.gz
/usr/share/man/man7/systemd.directives.7.gz
/usr/share/man/man7/systemd.generator.7.gz
/usr/share/man/man7/systemd.index.7.gz
/usr/share/man/man7/systemd.journal-fields.7.gz
/usr/share/man/man7/systemd.special.7.gz
/usr/share/man/man7/systemd.time.7.gz

```

## Задание 2
### В этом задании было необходимо найти в директории /usr/share/man все файлы, которые содержат слово "help" в имени, найти там же все файлы, имя которых начинается на "conf".

```bash
[ValkyRa2@CentOS ~]$ find /usr/share/man -name "conf*"
/usr/share/man/man5/config.5ssl.gz
/usr/share/man/man5/config-util.5.gz
[ValkyRa2@CentOS ~]$ find /usr/share/man -name "*help*"
/usr/share/man/man1/help.1.gz
/usr/share/man/man5/firewalld.helper.5.gz
/usr/share/man/man8/mkhomedir_helper.8.gz
/usr/share/man/man8/pwhistory_helper.8.gz
/usr/share/man/man8/ssh-pkcs11-helper.8.gz
```

Далее следовал вопрос о том, какие действия мы можем выполнить с файлами, найденными командой find (не запуская других команд)?
Есть два варианта действий, не запуская других команд:
### 1. Вывод результатов поиска в файл
```bash
[ValkyRa2@CentOS ~]$ find /usr/share/man -name "conf*" > ~/result.txt
[ValkyRa2@CentOS ~]$ cat result.txt
/usr/share/man/man5/config.5ssl.gz
/usr/share/man/man5/config-util.5.gz
```
В данном варианте мы ищем в указанной директории файлы, что имеют в названии "conf" и результаты поиска выводим в файл result.txt
Далее используем команду "cat" для того, чтобы убедиться в результатах выполненного поиска

### 2. Удаление файлов и директорий
```bash
find ~ -type d -empty -delete
```
Здесь мы ищем в домашней директории пустые папки и удаляем их

## Задание 3
### В задании сказано, что при помощи команд head и tail, вывести последние 2 строки файла /etc/fstab и первые 7 строк файла /etc/yum.conf
Выполняем эти команды поочерёдно:

```bash
[ValkyRa2@CentOS /]$ tail -n 2 /etc/fstab
UUID=e1fb6693-272d-4cd6-bc32-746c4489ee0b /boot               	xfs 	defaults    	0 0
/dev/mapper/centos-swap swap                	                  swap	defaults    	0 0

[ValkyRa2@CentOS /]$ head -n 7 /etc/yum.conf
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
```
### Также стоял вопрос о том, что произойдёт, если мы запросим больше строк, чем есть в файле. 
И результат таков, что если указать больше строк для head или tail, чем существует в файле, то команды просто выведут все строки, без лишних пробелов. 
Продемонстрируем на примере:

```bash
[ValkyRa2@CentOS /]$ wc -l /etc/fstab
11 /etc/fstab
[ValkyRa2@CentOS /]$ head -n 15 /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sat Nov 27 18:25:14 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                   	xfs 	defaults    	0 0
UUID=e1fb6693-272d-4cd6-bc32-746c4489ee0b /boot               	xfs 	defaults    	0 0
/dev/mapper/centos-swap swap                	swap	defaults    	0 0


[ValkyRa2@CentOS /]$ tail -n 15 /etc/fstab

#
# /etc/fstab
# Created by anaconda on Sat Nov 27 18:25:14 2021
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                   	xfs 	defaults    	0 0
UUID=e1fb6693-272d-4cd6-bc32-746c4489ee0b /boot               	xfs 	defaults    	0 0
/dev/mapper/centos-swap swap                	swap	defaults    	0 0
```

## Задание 4
### В данном задании говорится о необходимости создать три файла и используя {} переименовать их
Создаём эти файлы командой touch, или же используя ">" перед именем файла и убедимся в их наличии:
```bash
[ValkyRa2@CentOS ~]$ touch filename{1..3}.md
[ValkyRa2@CentOS ~]$ ls
filename1.md  filename2.md  filename3.md 
```
Используя {} переименовываем file_name1.md в file_name1.textdoc
```bash
[ValkyRa2@CentOS ~]$ mv filename1{.md,.textdoc}

```

Используя {} переименовываем file_name2.md в file_name2
```bash
[ValkyRa2@CentOS ~]$ mv filename2{.md,}
```
Используя {} переименовываем file_name3.md в file_name3.md.latest
```bash
[ValkyRa2@CentOS ~]$ mv filename3{.md,.md.latest}
```
Убедимся в наших выполненных действиях:
```bash
ValkyRa2@CentOS ~]$ ls
filename2  filename1.textdoc  filename3.md.latest
```

Используя {} переименовываем file_name1.textdoc в file_name1.txt
```bash
mv filename1{.textdoc,.txt}
```
## Задание 5
### В этом задании нужно перейти в каталог /mnt/ а после, указать возможные варианты перехода в домашнюю директорию пользователя используя команду "cd"
Переходим в каталог mnt:
```bash
[ValkyRa2@CentOS /]$ cd /mnt
[ValkyRa2@CentOS mnt]$
```
И есть 4 варианта как можно перейти в домашнюю директорию:

Способ 1:
```bash
[ValkyRa2@CentOS mnt]$ cd ~
[ValkyRa2@CentOS ~]$
```
Способ 2:
```bash
[ValkyRa2@CentOS mnt]$ cd /home/ValkyRa2/
[ValkyRa2@CentOS ~]$ 
```
Способ 3:
```bash
[ValkyRa2@CentOS mnt]$ cd
[ValkyRa2@CentOS ~]$
```
Способ 4:
```bash
[ValkyRa2@CentOS mnt]$ cd ../home/ValkyRa2/
[ValkyRa2@CentOS ~]$
```

## Задание 6:
### В задании мы работаем с созданием файлов, копированием и перемещением в разные папки, а также сравниваем каталоги.

В начале одной командой в домашней директории 3 папки new, in-process, processed. При этом in-process должна содержать в себе еще 3 папки tread0, tread1, tread2.
```bash
[ValkyRa2@CentOS ~]$ mkdir -p new in-process/tread{0..2} processed
```
И убеждаемся в правильности создания:
```bash
[ValkyRa2@CentOS ~]$ ls in-process
tread0  tread1  tread2
[ValkyRa2@CentOS ~]$ ls
in-process  new  processed
```
Далее создаём 100 файлов формата data[[:digit:]][[:digit:]] в папке new и убеждаемся в их наличии:
```bash
[ValkyRa2@CentOS ~]$ touch new/data{1..100}
[ValkyRa2@CentOS ~]$ ls new
data1	data18  data27  data36  data45  data54  data63  data72  data81  data90
data10   data19  data28  data37  data46  data55  data64  data73  data82  data91
data100  data2   data29  data38  data47  data56  data65  data74  data83  data92
data11   data20  data3   data39  data48  data57  data66  data75  data84  data93
data12   data21  data30  data4   data49  data58  data67  data76  data85  data94
data13   data22  data31  data40  data5   data59  data68  data77  data86  data95
data14   data23  data32  data41  data50  data6   data69  data78  data87  data96
data15   data24  data33  data42  data51  data60  data7   data79  data88  data97
data16   data25  data34  data43  data52  data61  data70  data8   data89  data98
data17   data26  data35  data44  data53  data62  data71  data80  data9   data99
```
Далее копируем 34 файла в tread0 и по 33 в tread1 и tread2 соответственно. 
```bash
[ValkyRa2@CentOS new]$ cp data{1..33} ../in-process/tread0
[ValkyRa2@CentOS new]$ cp data{34..66} ../in-process/tread1
[ValkyRa2@CentOS new]$ cp data{67..100} ../in-process/tread2
```
И выводим содержимое каталога in-process одной командой
```bash
[ValkyRa2@CentOS new]$ ls -la ../in-process/*

./in-process/tread0:
итого 4
drwxrwxr-x. 2 ValkyRa2 ValkyRa2 4096 ноя 28 18:14 .
drwxrwxr-x. 5 ValkyRa2 ValkyRa2   48 ноя 28 18:09 ..
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data1
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data10
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data11
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data12
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data13
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data14
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data15
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data16
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data17
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data18
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data19
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data2
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data20
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data21
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data22
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data23
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data24
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data25
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data26
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data27
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data28
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data29
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data3
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data30
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data31
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data32
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data33
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data4
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data5
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data6
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data7
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data8
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data9

../in-process/tread1:
итого 4
drwxrwxr-x. 2 ValkyRa2 ValkyRa2 4096 ноя 28 18:14 .
drwxrwxr-x. 5 ValkyRa2 ValkyRa2   48 ноя 28 18:09 ..
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data34
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data35
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data36
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data37
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data38
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data39
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data40
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data41
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data42
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data43
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data44
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data45
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data46
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data47
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data48
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data49
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data50
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data51
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data52
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data53
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data54
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data55
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data56
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data57
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data58
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data59
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data60
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data61
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data62
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data63
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data64
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data65
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data66

../in-process/tread2:
итого 4
drwxrwxr-x. 2 ValkyRa2 ValkyRa2 4096 ноя 28 18:14 .
drwxrwxr-x. 5 ValkyRa2 ValkyRa2   48 ноя 28 18:09 ..
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data100
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data67
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data68
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data69
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data70
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data71
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data72
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data73
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data74
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data75
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data76
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data77
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data78
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data79
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data80
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data81
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data82
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data83
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data84
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data85
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data86
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data87
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data88
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data89
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data90
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data91
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data92
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data93
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data94
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data95
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data96
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data97
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data98
-rw-rw-r--. 1 ValkyRa2 ValkyRa2	0 ноя 28 18:14 data99
```

После этого перемещаем все файлы из каталогов tread в processed одной командой.
```bash
[ValkyRa2@CentOS ~]$ find ~/in-process/tread* -type f -print0 | xargs -0 mv -t ~/processed
```
И выводим содержимые каталогов in-process и processed опять же одной командой
```bash
[ValkyRa2@CentOS ~]$ ls in-process/* processed
in-process/tread0:

in-process/tread1:

in-process/tread2:

processed:
data1	data18  data27  data36  data45  data54  data63  data72  data81  data90
data10   data19  data28  data37  data46  data55  data64  data73  data82  data91
data100  data2   data29  data38  data47  data56  data65  data74  data83  data92
data11   data20  data3   data39  data48  data57  data66  data75  data84  data93
data12   data21  data30  data4   data49  data58  data67  data76  data85  data94
data13   data22  data31  data40  data5   data59  data68  data77  data86  data95
data14   data23  data32  data41  data50  data6   data69  data78  data87  data96
data15   data24  data33  data42  data51  data60  data7   data79  data88  data97
data16   data25  data34  data43  data52  data61  data70  data8   data89  data98
data17   data26  data35  data44  data53  data62  data71  data80  data9   data99
```

Сравним количество файлов в каталогах new и processed при помощи изученных ранее команд, если они равны удаляем файлы из new
Для начала создадим файл для нашего скрипта и запишем скрипт:
```bash
[ValkyRa2@CentOS ~]$ cat > deletefile.sh
for file in ~/new/*; do
	fileName=${file##*/}
	diff -q <(sort "$file") <(sort ~/processed/"$fileName") &&
	rm ~/new/"$fileName"
done
```
Просто запустить скрипт не получится, т.к. для запуска нет доступа для выполнения, изменим это добавив права для текщуего пользователя командой chmod
```bash
[ValkyRa2@CentOS ~]$ chmod u+x deletefile.sh
```
Запускаем скрипт, и убеждаемся в правильности выполненной работы:
```bash
[ValkyRa2@CentOS ~]$ ~/deletefile.sh
[ValkyRa2@CentOS ~]$ ls new/ processed
new/:

processed:
data1	data18  data27  data36  data45  data54  data63  data72  data81  data90
data10   data19  data28  data37  data46  data55  data64  data73  data82  data91
data100  data2   data29  data38  data47  data56  data65  data74  data83  data92
data11   data20  data3   data39  data48  data57  data66  data75  data84  data93
data12   data21  data30  data4   data49  data58  data67  data76  data85  data94
data13   data22  data31  data40  data5   data59  data68  data77  data86  data95
data14   data23  data32  data41  data50  data6   data69  data78  data87  data96
data15   data24  data33  data42  data51  data60  data7   data79  data88  data97
data16   data25  data34  data43  data52  data61  data70  data8   data89  data98
data17   data26  data35  data44  data53  data62  data71  data80  data9   data99
```

## Задание 7 (*)

### В задании нужно изменить данную ранее команду ```bash echo file{$a..$b}```и изменить так, чтобы при указании переменных a=1 и b=3 при выводе выдавало file1 file2 file3

Данное задание можно выполнить при помощи команды eval, которая исполняет строку, получившуюся в результате подстановки переменных.

И таким образом, выполняя данные команды:
```bash
[ValkyRa@centos ~]$ a=1
[ValkyRa@centos ~]$ b=3
[ValkyRa@centos ~]$ eval echo file{$a..$b}
file1 file2 file3
```
Мы получаем искомый результат.


