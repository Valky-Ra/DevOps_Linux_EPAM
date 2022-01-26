Задача:
Установить, настроить и запустить Hadoop Сore в минимальной конфигурации. Для этого потребуется подготовить 2 виртуальные машины: VM1 - headnode; VM2 - worker.

Детальная формулировка:

### 1. Установить CentOS на 2 виртуальные машины:
###  •	VM1: 2CPU, 2-4G памяти, системный диск на 15-20G и дополнительные 2 диска по 5G
###  •	VM2: 2CPU, 2-4G памяти, системный диск на 15-20G и дополнительные 2 диска по 5G
### Все дальнейшие действия будут выполняться на обеих машинах, если не сказано иначе.

Установлена первая машина с указанными выше требованиями:

![image](https://user-images.githubusercontent.com/15772109/150944392-8bbd3d0f-4a9e-4990-8a6f-52f6118bc6a9.png)

Добавлено 2 дополнительных жёстких диска по 5 гб:

![image](https://user-images.githubusercontent.com/15772109/150944434-e451558d-d63d-46a8-97e0-17c5757aa92a.png)

И тоже самое с второй машиной:

![image](https://user-images.githubusercontent.com/15772109/150944517-f09fd59b-1f30-40a6-b216-a71bf44689d0.png)

![image](https://user-images.githubusercontent.com/15772109/150944533-d2d4ece2-3922-4974-8c5e-d1837db31980.png)

Далее, для дальнейшей работы с машинами, я с основной машины буду подключаться по SSH к двум созданным машинам, предварительно проверив их IP по команде ```ip -4 a```:
```
ssh headnode@192.168.0.164 - первая машина
ssh worker@192.168.0.103 - вторая машина
```
В отчёте будут команды, выполненные только на одной из машин, т.к. они будут идентичны на обоих машинах, кроме случаев, когда задание подразумевает выполнение только на конкретной машине.

### 2. При установке CentOS создать дополнительного пользователя exam и настроить для него использование sudo без пароля. Все последующие действия необходимо выполнять от этого пользователя, если не указано иное.
 
Создаём пользователя в обоих машинах:
```
[headnode@exammachine1 ~]$ sudo useradd exam
[worker@exammachine2 ~]$ sudo useradd exam
```
Далее нужно изменить данные в файле, отвечающий за sudo. Обычно это делается командой
```
sudo visudo
```
Но т.к. я не пользуюсь вимом, воспользуюсь прямым запросом, а именно:
```sudo emacs /etc/sudoers```, предварительно установив текстовый редактор.
Открываем файл и доходим до пункта:
```
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
```
И прописываем здесь вот эту строку:
```
exam    ALL=(ALL)       NOPASSWD: ALL
```
Сохраняем файл, и перед тем как зайти на пользователя, необходимо задать ему пароль, при помощи passwd.
Задаём пароль и входим на пользователя:
```
su exam
```
Вводим пароль и проверим работоспособность введённого ранее в конфиг, на примере следующего пункта с JDK
```
[exam@exammachine1 headnode]$ sudo yum search java 
```
И видим, что sudo выполняется без требования пароля.

### 3. Установить OpenJDK8 из репозитория CentOS.

Поскольку JDK является частью Java (Java Development Kit) соответственно копать будем в эту сторону и искать нужные нам пакеты, а именно:
```
[exam@exammachine1 ~]$ sudo yum search java | grep jdk
```
И находим интересующие нас пакеты:
```
java-1.8.0-openjdk.i686 : OpenJDK Runtime Environment 8
java-1.8.0-openjdk.x86_64 : OpenJDK 8 Runtime Environment
java-1.8.0-openjdk-accessibility.i686 : OpenJDK accessibility connector
java-1.8.0-openjdk-accessibility.x86_64 : OpenJDK accessibility connector
java-1.8.0-openjdk-demo.i686 : OpenJDK Demos 8
java-1.8.0-openjdk-demo.x86_64 : OpenJDK 8 Demos
java-1.8.0-openjdk-devel.i686 : OpenJDK Development Environment 8
java-1.8.0-openjdk-devel.x86_64 : OpenJDK 8 Development Environment
java-1.8.0-openjdk-headless.i686 : OpenJDK Headless Runtime Environment 8
java-1.8.0-openjdk-headless.x86_64 : OpenJDK 8 Headless Runtime Environment
java-1.8.0-openjdk-javadoc.noarch : OpenJDK 8 API documentation
java-1.8.0-openjdk-javadoc-zip.noarch : OpenJDK 8 API documentation compressed
java-1.8.0-openjdk-src.i686 : OpenJDK Source Bundle 8
java-1.8.0-openjdk-src.x86_64 : OpenJDK 8 Source Bundle
```
Устанавливаем их, при помощи выборки конкретных пакетов, а именно:
```
[exam@exammachine1 ~]$ sudo yum install java-1.8.0-openjdk*
```
Тем самым, «*» позволяет установить все пакеты, начинающие с этого названия.

### 4. Скачать архив с Hadoop версии 3.1.2 (https://hadoop.apache.org/release/3.1.2.html)
Переходим на сайт и копируем ссылку “Download tar.gz”
Далее используем wget для скачивания архива (в минимальной комплектации CentOS его может не быть)
И выполняем команду:
```
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.1.2/hadoop-3.1.2.tar.gz
```
И таким образом, архив будет находиться в директории, откуда была запущена команда.

### 5. Распаковать содержимое архива в /opt/hadoop-3.1.2/
Поскольку архив формата tar, то и действовать будем именно с командой tar
```
sudo tar -C /opt -xvf hadoop-3.1.2.tar.gz
```
-C используем для переноса в конкретную директорию, x – извлечение файлов, v – подробно описывает, какие файлы копируются и f собственно файл, в нашем случае – наш архив.

### 6. Сделать симлинк /usr/local/hadoop/current/ на директорию /opt/hadoop-3.1.2/
Для создания симлинка, нужно в первую очередь создать данные папки, т.к. при указывании пути симлинка, требуется иметь наличие папок (сам он их не создаст), а именно hadoop и current
После же, прописываем следующую команду:
```
[exam@exammachine2 /]$ sudo ln -s /opt/hadoop-3.1.2 /usr/local/hadoop/current   
```
После выполнения проверим и убедимся в наличии симлинка:
```
[exam@exammachine1 hadoop]$ ls -la
итого 0
drwxr-xr-x.  2 root root  21 янв 21 22:48 .
drwxr-xr-x. 13 root root 145 янв 15 17:13 ..
lrwxrwxrwx.  1 root root  17 янв 21 22:48 current -> /opt/hadoop-3.1.2
```

### 7. Создать пользователей hadoop, yarn и hdfs, а также группу hadoop, в которую необходимо добавить всех этих пользователей
Создаём пользователей по аналогии с лекцией 4.
```
[exam@exammachine1 /]$ sudo groupadd hadoop -g 1111
[exam@exammachine1 /]$ sudo useradd hadoop -g 1111
[exam@exammachine1 /]$ sudo useradd yarn -g 1111
[exam@exammachine1 /]$ sudo useradd hdfs -g 1111
```
И проверим наличие этих трёх пользователей в группе hadoop:
```
[exam@exammachine1 /]$ groups hadoop yarn hdfs
hadoop : hadoop
yarn : hadoop
hdfs : hadoop
```

### 8. Создать для обоих дополнительных дисков разделы размером в 100% диска.

Проверим, что у нас там вообще есть из дисков.
Зайдём в директорию dev и проверим диски:
```
[exam@exammachine2 dev]$ ls sd
sda   sda1  sda2  sdb   sdc  
```
И видим два диска, что были добавлены ранее (в нашем случае, это sdb и sdc)
Далее создаём разделы на дисках, используя команду:
```
[exam@exammachine2 dev]$ sudo fdisk /dev/sdb
```

Далее, создаём GPT части, используя параметр g, и проверим, что тип диска gpt, параметром p, убедившись в правильности, создаём раздел, используя параметр n, и используем всё пространство для создания раздела:
```
Номер раздела (1-128, default 1): 
First sector (2048-10485726, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-10485726, default 10485726): 
Created partition 1
```
Далее, проверяем, создался ли раздел, снова используя p
```
Команда (m для справки): p

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: 96402BA2-98BC-4796-82DE-A3B31B5E6A55

#         Start          End    Size  Type            Name
 1         2048     10485726      5G  Linux filesyste 

```
Убедившись, что раздел создался, используем параметр w, для записи изменений, поскольку в противном случае, раздел не будет создан.
```
Команда (m для справки): w
Таблица разделов была изменена!

Вызывается ioctl() для перечитывания таблицы разделов.
Синхронизируются диски.
```
Также делаем и со вторым диском sdc.

### 9. Инициализировать разделы из п.8 в качестве физических томов для LVM.
По аналогии с пунктом 8, мы используем те же команды, какие использовали при создании разделов.
И для каждого из дисков мы используем параметр t, для изменения типа раздела, и используем 31 вариант, это Linux LVM.
Перед записью, проверим что тип изменился на LVM
```
Команда (m для справки): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: A1B236DD-9278-432B-B3A7-9AB0F318CE5C

#         Start          End    Size  Type            Name
 2         2048     10485726      5G  Linux LVM       
```
Убедившись в результате, записываем изменения в разделы. 

### 10. Создать две группы LVM и добавить в каждую из них по одному физическому тому из п.9.

Для начала нам нужно создать физические тома, ранее разделённых разделов, делается это командой pvcreate
```
[exam@exammachine2 dev]$ sudo pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created.
[exam@exammachine2 dev]$ sudo pvcreate /dev/sdc2
  Physical volume "/dev/sdc2" successfully created.
```
Создаём тома и проверяем их наличие
```
[exam@exammachine2 dev]$ sudo pvs
  PV         VG     Fmt  Attr PSize   PFree 
  /dev/sda2  centos lvm2 a--  <14,00g     0 
  /dev/sdb1         lvm2 ---   <5,00g <5,00g
  /dev/sdc2         lvm2 ---   <5,00g <5,00g
```
И после этого создаём две группы LVM, добавляя в них по одному физическому тому, созданных ранее.
```
[exam@exammachine2 dev]$ sudo vgcreate grp1 /dev/sdb1
  Volume group "grp1" successfully created
[exam@exammachine2 dev]$ sudo vgcreate grp2 /dev/sdc2
  Volume group "grp2" successfully created
```

### 11. В каждой из групп из п.10 создать логический том LVM размером 100% группы.
Для того чтобы создать логический том LVM нам необходимо выполнить команду lvcreate:
```
[exam@exammachine1 /]$ sudo lvcreate -l100%vg grp1
  Logical volume "lvol0" created.
```
-l указываем размер, в нашем случае 100% от размера группы, что указывается через суффикс vg 
После этого указываем группу, в которой необходимо создать логический том, в нашем случае, это grp1
Тоже самое делаем с другой группой
```
[exam@exammachine1 /]$ sudo lvcreate -l100%vg grp2
  Logical volume "lvol0" created.
```
Для проверки, что группа действительно создалась, мы используем команду lvdisplay с параметром -v (verbose), который покажет подробный результат
```
[exam@exammachine1 /]$ sudo lvdisplay -v /dev/grp1/lvol0 
  --- Logical volume ---
  LV Path                /dev/grp1/lvol0
  LV Name                lvol0
  VG Name                grp1
  LV UUID                SmLJYM-leHN-3woJ-gpd3-6dmv-CLdo-AaXMZw
  LV Write Access        read/write
  LV Creation host, time exammachine1, 2022-01-15 19:47:40 +0300
  LV Status              available
  # open                 0
  LV Size                <5,00 GiB
  Current LE             1279
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:2
```

### 12. На каждом логическом томе LVM создать файловую систему ext4.

Создаём файловые системы на логических томах путём команды mkfs:
```
[exam@exammachine1 /]$ sudo mkfs.ext4 /dev/grp1/lvol0
```
При вводе этой команды происходит процесс создания.
```
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1309696 blocks
65484 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```
Тоже самое проделываем и со второй группой
```
[exam@exammachine1 /]$ sudo mkfs.ext4 /dev/grp2/lvol0 
```
И проверим результат, командой lsblk

![image](https://user-images.githubusercontent.com/15772109/150947124-20af28ac-ca25-44cd-b438-d0f2834ddb19.png)

### 13. Создать директории и использовать их в качестве точек монтирования файловых систем из п.12:
```
• /opt/mount1
• /opt/mount2
```

Для начала, создадим две новые директории в /opt
```
[exam@exammachine1 ~]$ sudo mkdir /opt/mount1 /opt/mount2
```
Проверим, создались ли они:
```
[exam@exammachine1 opt]$ ls
hadoop-3.1.2  mount1  mount2
```
Далее монтируем наши файловые системы, созданные ранее в п.12
```
[exam@exammachine1 opt]$ sudo mount /dev/grp1/lvol0 /opt/mount1
[exam@exammachine1 opt]$ sudo mount /dev/grp2/lvol0 /opt/mount2
```
И смотрим результат:

![image](https://user-images.githubusercontent.com/15772109/150947412-ff130753-d16a-491f-be27-d14ace159aa3.png)

Но монтирование такое сработает до момента перезапуска системы, после чего придётся монтирование запускать заново. 

### 14. Настроить систему так, чтобы монтирование происходило автоматически при запуске системы. Произвести монтирование новых файловых систем.

Для добавления монтирования в автозагрузку, необходимо перейти в файл по пути /etc/fstab
И по аналогии с другими путями в файле, в конец файла добавить две наши точки монтирования:
```
[exam@exammachine1 opt]$ sudo emacs /etc/fstab
```

![image](https://user-images.githubusercontent.com/15772109/150947471-a38d418a-9439-475c-b56e-c5eec67d7bca.png)

Перезапускаем систему, и используем lsblk

![image](https://user-images.githubusercontent.com/15772109/150947502-dbca1079-a918-4c02-a263-d44333c1a1e7.png)

И видим, что монтирование после перезапуска не слетело.

### Для VM1 (шаги 15-16):
### 15. После монтирования создать 2 директории для хранения файлов Namenode сервиса HDFS:
```
•	/opt/mount1/namenode-dir
•	/opt/mount2/namenode-dir
```
```
[exam@exammachine1 ~]$ sudo mkdir /opt/mount1/namenode-dir /opt/mount2/namenode-dir
```
Проверим содержимое директорий:
```
[exam@exammachine1 ~]$ ls /opt/mount1/ /opt/mount2
/opt/mount1/:
lost+found  namenode-dir

/opt/mount2:
lost+found  namenode-dir
```
### 16. Сделать пользователя hdfs и группу hadoop владельцами этих директорий.
Сначала задаём права для hdfs:
```
[exam@exammachine2 ~]$ sudo chown hdfs /opt/mount1/namenode-dir/
[exam@exammachine2 ~]$ sudo chown hdfs /opt/mount2/namenode-dir/
```
Теперь для группы:
```
[exam@exammachine1 ~]$ sudo chown :hadoop /opt/mount1/namenode-dir/
[exam@exammachine1 ~]$ sudo chown :hadoop /opt/mount2/namenode-dir/
```
Проверяем правильно ли настроено владение директориями
```
[exam@exammachine1 ~]$ ls -la  /opt/mount1/ /opt/mount2
/opt/mount1/:
итого 24
drwxr-xr-x. 4 root root    4096 янв 18 18:45 .
drwxr-xr-x. 5 root root      54 янв 18 13:38 ..
drwx------. 2 root root   16384 янв 15 20:11 lost+found
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 18:45 namenode-dir

/opt/mount2:
итого 24
drwxr-xr-x. 4 root root    4096 янв 18 18:45 .
drwxr-xr-x. 5 root root      54 янв 18 13:38 ..
drwx------. 2 root root   16384 янв 15 20:12 lost+found
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 18:45 namenode-dir
```

### Для VM2 (шаги 17-20):
### 17. После монтирования создать 2 директории для хранения файлов Datanode сервиса HDFS:
```
•	/opt/mount1/datanode-dir
•	/opt/mount2/datanode-dir
```

По аналогии с пунктом 15, создаём две директории.
```
[exam@exammachine2 ~]$ sudo mkdir /opt/mount1/datanode-dir /opt/mount2/datanode-dir
```
### 18. Сделать пользователя hdfs и группу hadoop владельцами директорий из п.17.
Также по аналогии ранее.
```
[exam@exammachine2 ~]$ sudo chown hdfs /opt/mount1/datanode-dir/
[exam@exammachine2 ~]$ sudo chown hdfs /opt/mount2/datanode-dir/

[exam@exammachine2 ~]$ sudo chown :hadoop /opt/mount1/datanode-dir/
[exam@exammachine2 ~]$ sudo chown :hadoop /opt/mount2/datanode-dir/

[exam@exammachine2 ~]$ ls -la  /opt/mount1/ /opt/mount2
/opt/mount1/:
итого 24
drwxr-xr-x. 4 root root    4096 янв 18 19:22 .
drwxr-xr-x. 5 root root      54 янв 18 13:39 ..
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 19:22 datanode-dir
drwx------. 2 root root   16384 янв 15 20:17 lost+found

/opt/mount2:
итого 24
drwxr-xr-x. 4 root root    4096 янв 18 19:22 .
drwxr-xr-x. 5 root root      54 янв 18 13:39 ..
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 19:22 datanode-dir
drwx------. 2 root root   16384 янв 15 20:17 lost+found
```
### 19. Создать дополнительные 4 директории для Nodemanager сервиса YARN:
```
•	/opt/mount1/nodemanager-local-dir
•	/opt/mount2/nodemanager-local-dir
•	/opt/mount1/nodemanager-log-dir
•	/opt/mount2/nodemanager-log-dir
```
```
[exam@exammachine2 ~]$ sudo mkdir /opt/mount1/nodemanager-local-dir /opt/mount1/nodemanager-log-dir
[exam@exammachine2 ~]$ sudo mkdir /opt/mount2/nodemanager-local-dir /opt/mount2/nodemanager-log-dir
```
### 20. Сделать пользователя yarn и группу hadoop владельцами директорий из п.19.
```
[exam@exammachine2 ~]$ sudo chown yarn /opt/mount1/nodemanager-local-dir /opt/mount1/nodemanager-log-dir
[exam@exammachine2 ~]$ sudo chown yarn /opt/mount2/nodemanager-local-dir /opt/mount2/nodemanager-log-dir
[exam@exammachine2 ~]$ sudo chown :hadoop /opt/mount1/nodemanager-local-dir /opt/mount1/nodemanager-log-dir
[exam@exammachine2 ~]$ sudo chown :hadoop /opt/mount2/nodemanager-local-dir /opt/mount2/nodemanager-log-dir
```
```
[exam@exammachine2 ~]$ ls -la /opt/mount1/ /opt/mount2/
/opt/mount1/:
итого 32
drwxr-xr-x. 6 root root    4096 янв 19 06:56 .
drwxr-xr-x. 5 root root      54 янв 18 13:39 ..
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 19:22 datanode-dir
drwx------. 2 root root   16384 янв 15 20:17 lost+found
drwxr-xr-x. 2 yarn hadoop  4096 янв 19 06:56 nodemanager-local-dir
drwxr-xr-x. 2 yarn hadoop  4096 янв 19 06:56 nodemanager-log-dir

/opt/mount2/:
итого 32
drwxr-xr-x. 6 root root    4096 янв 19 06:57 .
drwxr-xr-x. 5 root root      54 янв 18 13:39 ..
drwxr-xr-x. 2 hdfs hadoop  4096 янв 18 19:22 datanode-dir
drwx------. 2 root root   16384 янв 15 20:17 lost+found
drwxr-xr-x. 2 yarn hadoop  4096 янв 19 06:57 nodemanager-local-dir
drwxr-xr-x. 2 yarn hadoop  4096 янв 19 06:57 nodemanager-log-dir
```

### Для обеих машин:
### 21. Настроить доступ по SSH, используя ключи для пользователя hadoop.

По аналогии с 6-ой домашкой, создаём сначала пару ключей:
```
[exam@exammachine1 .ssh]$ sudo ssh-keygen -t rsa
```
Затем копируем ssh-ключ командой ssh-copy-id и указываем имя по которому будем подключаться, используя уже ключ, а не пароль.
```
[exam@exammachine1 .ssh]$ sudo ssh-copy-id -i /home/exam/.ssh/task21 hadoop@192.168.0.103
/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/exam/.ssh/task21.pub"
/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
hadoop@192.168.0.103's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'hadoop@192.168.0.103'"
and check to make sure that only the key(s) you wanted were added.
```
Теперь логинимся на вторую машину под пользователем hadoop, используя ключ.
```
[exam@exammachine1 .ssh]$ sudo ssh -i ~/.ssh/task21 hadoop@192.168.0.103
Last login: Thu Jan 20 14:08:19 2022 from 192.168.0.164
[hadoop@exammachine2 ~]$
```
Теперь всё тоже самое, только уже наоборот к первой машине, разница будет заключаться в разном IP при подключении, к первой машине будет hadoop@192.168.0.164.

### 22. Добавить VM1 и VM2 в /etc/hosts.
```
[exam@exammachine1 ~]$ sudo emacs /etc/hosts
```
И добавим эту строку в конфиг
```
192.168.0.103 exammachine2
```
И пингуем вторую машину:
```
[exam@exammachine1 ~]$ ping exammachine2

PING exammachine2 (192.168.0.103) 56(84) bytes of data.
64 bytes from exammachine2 (192.168.0.103): icmp_seq=1 ttl=64 time=0.533 ms
64 bytes from exammachine2 (192.168.0.103): icmp_seq=2 ttl=64 time=0.818 ms
64 bytes from exammachine2 (192.168.0.103): icmp_seq=3 ttl=64 time=0.479 ms
^C
--- exammachine2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2094ms
rtt min/avg/max/mdev = 0.479/0.610/0.818/0.148 ms
```

Теперь со второй машины, по той же аналогии.
```
[exam@exammachine2 .ssh]$ sudo emacs /etc/hosts
```
```
192.168.0.164 exammachine1
```
```
[exam@exammachine2 .ssh]$ ping exammachine1
PING exammachine1 (192.168.0.164) 56(84) bytes of data.
64 bytes from exammachine1 (192.168.0.164): icmp_seq=1 ttl=64 time=0.628 ms
64 bytes from exammachine1 (192.168.0.164): icmp_seq=2 ttl=64 time=0.849 ms
64 bytes from exammachine1 (192.168.0.164): icmp_seq=3 ttl=64 time=0.777 ms
^C
--- exammachine1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.628/0.751/0.849/0.094 ms
```

### 23. Скачать файлы по ссылкам в /usr/local/hadoop/current/etc/hadoop/{hadoop-env.sh,core-site.xml,hdfs-site.xml,yarn-site.xml}. При помощи sed заменить заглушки на необходимые значения 

``` •	hadoop-env.sh (https://gist.github.com/rdaadr/2f42f248f02aeda18105805493bb0e9b) ```

Необходимо определить переменные JAVA_HOME (путь до директории с OpenJDK8, установленную в п.3), HADOOP_HOME (необходимо указать путь к симлинку из п.6) и HADOOP_HEAPSIZE_MAX (укажите значение в 512M)

``` •	core-site.xml (https://gist.github.com/rdaadr/64b9abd1700e15f04147ea48bc72b3c7) ```

Необходимо указать имя хоста, на котором будет запущена HDFS Namenode (VM1)

``` •	hdfs-site.xml (https://gist.github.com/rdaadr/2bedf24fd2721bad276e416b57d63e38) ```

Необходимо указать директории namenode-dir, а также datanode-dir, каждый раз через запятую (например, /opt/mount1/namenode-dir,/opt/mount2/namenode-dir)

``` •	yarn-site.xml (https://gist.github.com/Stupnikov-NA/ba87c0072cd51aa85c9ee6334cc99158) ```

Необходимо подставить имя хоста, на котором будет развернут YARN Resource Manager (VM1), а также пути до директорий nodemanager-local-dir и nodemanager-log-dir (если необходимо указать несколько директорий, то необходимо их разделить запятыми)

Для начала разберёмся с *hadoop-env.sh*.
Сначала скачиваем файл по указанной ссылке, в заранее созданную для этого папку task23:
```
[exam@exammachine1 task23]$ wget https://gist.githubusercontent.com/rdaadr/2f42f248f02aeda18105805493bb0e9b/raw/6303e424373b3459bcf3720b253c01373666fe7c/hadoop-env.sh
```
Теперь в файле при помощи sed нам нужно заменить переменные на нужные пути.
Начнём с пути, где у нас установлен JDK 
```
[exam@exammachine1 task23]$ sudo sed -i 's|"%PATH_TO_OPENJDK8_INSTALLATION%"| /usr/lib/jvm/java-1.8.0 |' hadoop-env.sh 
```
Далее, к месту, где скачан hadoop
```
[exam@exammachine1 task23]$ sudo sed -i 's|"%PATH_TO_HADOOP_INSTALLATION"|/usr/local/hadoop/current|' hadoop-env.sh 
```
И указываем размер памяти для hadoop
```
[exam@exammachine1 task23]$ sudo sed -i 's|"%HADOOP_HEAP_SIZE%"|512M|' hadoop-env.sh
```

Теперь откроем файл, убирая лишние символы и строки.
```
[exam@exammachine1 task23]$ grep -v '^$\|^#' hadoop-env.sh
export JAVA_HOME= /usr/lib/jvm/java-1.8.0
export HADOOP_HOME=/usr/local/hadoop/current
export HADOOP_HEAPSIZE_MAX=512M
export HADOOP_OS_TYPE=${HADOOP_OS_TYPE:-$(uname -s)}
```

Теперь с *core-site.xml*
```
[exam@exammachine1 task23]$ sudo wget https://gist.githubusercontent.com/rdaadr/64b9abd1700e15f04147ea48bc72b3c7/raw/2d416bf137cba81b107508153621ee548e2c877d/core-site.xml
```
И по принципу, как и с предыдущим файлом, укажем имя хоста, на котором будет запущена HDFS Namenode (VM1) через sed:
```
[exam@exammachine1 task23]$ sudo sed -i 's|%HDFS_NAMENODE_HOSTNAME%|exammachine1|' core-site.xml 
```
Далее открываем нужные нам строки файла, для проверки данных
```
[exam@exammachine1 task23]$ tail -3 core-site.xml 
        <value>hdfs://exammachine1:8020</value> 
    </property> 
</configuration>
```
Следующий, *hdfs-site.xml*
```
[exam@exammachine1 task23]$ sudo wget https://gist.githubusercontent.com/rdaadr/2bedf24fd2721bad276e416b57d63e38/raw/640ee95adafa31a70869b54767104b826964af48/hdfs-site.xml
```
Необходимо указать директории namenode-dir, а также datanode-dir, каждый раз через запятую, что и выполняем:
```
[exam@exammachine1 task23]$ sudo sed -i 's|%NAMENODE_DIRS%|/opt/mount1/namenode-dir,/opt/mount2/namenode-dir|' hdfs-site.xml 
[exam@exammachine1 task23]$ sudo sed -i 's|%DATANODE_DIRS%|/opt/mount1/datanode-dir,/opt/mount2/datanode-dir|' hdfs-site.xml
```
Далее проверим сам файл, используя grep и [value] для того, чтобы вывести все строки, в которых и содержится пути до точек монтирования:
```
[exam@exammachine1 task23]$ cat hdfs-site.xml | grep " <value>"
      <value>/opt/mount1/namenode-dir,/opt/mount2/namenode-dir</value> 
      <value>/opt/mount1/datanode-dir,/opt/mount2/datanode-dir</value> 
      <value>1</value>
```

И напоследок, скачиваем *yarn-site.xml*
```
[exam@exammachine1 task23]$ sudo wget https://gist.githubusercontent.com/Stupnikov-NA/ba87c0072cd51aa85c9ee6334cc99158/raw/bda0f760878d97213196d634be9b53a089e796ea/yarn-site.xml
```
Необходимо подставить имя хоста, на котором будет развернут YARN Resource Manager (VM1), а также пути до директорий nodemanager-local-dir и nodemanager-log-dir (если необходимо указать несколько директорий, то необходимо их разделить запятыми)
```
[exam@exammachine1 task23]$ sudo sed -i 's|%YARN_RESOURCE_MANAGER_HOSTNAME%|exammachine1|' yarn-site.xml 
[exam@exammachine1 task23]$ sudo sed -i 's|%NODE_MANAGER_LOCAL_DIR%|/opt/mount1/nodemanager-local-dir,/opt/mount2/nodemanager-local-dir|' yarn-site.xml 
[exam@exammachine1 task23]$ sudo sed -i 's|%NODE_MANAGER_LOG_DIR%|/opt/mount1/nodemanager-log-dir,/opt/mount2/nodemanager-log-dir|' yarn-site.xml 
```
Ну и проверим, так ли изменились пути в значениях
```
[exam@exammachine1 task23]$ cat yarn-site.xml | grep " <value>"
        <value>false</value> 
        <value>exammachine1</value> 
        <value>128</value> 
        <value>256</value> 
        <value>256</value> 
        <value>/opt/mount1/nodemanager-local-dir,/opt/mount2/nodemanager-local-dir</value> 
        <value>/opt/mount1/nodemanager-log-dir,/opt/mount2/nodemanager-log-dir</value>
```
По итогу, берём все 4 файла изменённых и переносим в нужную директорию. А именно: /usr/local/hadoop/current/etc/hadoop/
```
[exam@exammachine1 ~]$ sudo mkdir -p /usr/local/hadoop/current/etc/hadoop
[exam@exammachine1 ~]$ sudo mv /home/exam/task23/* /usr/local/hadoop/current/etc/hadoop/
```
```
[exam@exammachine1 ~]$ ls /usr/local/hadoop/current/etc/hadoop/
core-site.xml  hadoop-env.sh  hdfs-site.xml  yarn-site.xml
```

### 24. Задать переменную окружения HADOOP_HOME через /etc/profile
Откроем текстовым редактором файл profile
```
[exam@exammachine1 ~]$ sudo emacs /etc/profile
```
И вписываем в него строку:
```
export HADOOP_HOME="/usr/local/hadoop/current"
```
Пересчитываем файл для применения переменной
```
[exam@exammachine1 ~]$ source /etc/profile
```
Проверяем переменную
```
[exam@exammachine1 ~]$ echo $HADOOP_HOME/
/usr/local/hadoop/current/
```

### Для VM1 (шаги 25-26):
### 25. Произвести форматирование HDFS (от имени пользователя hdfs):
```
$HADOOP_HOME/bin/hdfs namenode -format cluster1
```
Перед выполнением данной команды, нужно выполнить несколько команд. Для начала, указать владельцем current пользователя hdfs
```
[exam@exammachine1 hadoop]$ sudo chown hdfs current/
```
Далее нам необходимо задать пароль для hdfs
```
[exam@exammachine1 hadoop]$ sudo passwd hdfs
```
После же заходим на пользователя
```
[exam@exammachine1 hadoop]$ su hdfs
```
И после этого выполняем команду, для запуска namenode
```
$HADOOP_HOME/bin/hdfs namenode -format cluster1
```
И получаем успешно выполненное форматирование. Последние строчки выполненного процесса
```
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at exammachine1/192.168.0.164
************************************************************/
```

### 26. Запустить демоны сервисов Hadoop:

Для запуска Namenode (от имени пользователя hdfs):
```
$HADOOP_HOME/bin/hdfs --daemon start namenode
```
Перед запуском namenode, нам нужно задать права для группы hadoop, чтобы можно было вписывать логи в соответствующую директорию, в противном случае, деймон не запустится, выдав соответствующую ошибку.  
```
[exam@exammachine1 current]$ sudo chmod g+w logs
```
После этого, запускаем namenode
Для запуска Resource Manager (от имени пользователя yarn):
```
•	$HADOOP_HOME/bin/yarn --daemon start resourcemanager
```
Права необходимые для группы уже заданы, так что второй деймон запустится без проблем
После, проверим процессы командой ```ps ax``` и видим два процесса, что запущены были ранее
```
2108 pts/0    Sl     0:08 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_namenode -Dja
2364 pts/0    Sl     0:10 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_resourcemanag
```

### Для VM2 (шаг 27):
### 27. Запустить демоны сервисов:
Для запуска Datanode (от имени hdfs):
```
$HADOOP_HOME/bin/hdfs --daemon start datanode
```
Для запуска Node Manager (от имени yarn):
```
$HADOOP_HOME/bin/yarn --daemon start nodemanager
```

По аналогии с предыдущими пунктами, где запускали деймонов на первой машине, зададим права также на второй машине, после же, запускаем деймонов и той же командой ps ax видим запущенные процессы.

```
2342 pts/0    Sl     0:05 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_datanode -Djava.net.preferIPv4Sta
2723 pts/0    Sl     0:06 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_nodemanager -Djava.net.preferIPv4
```

### 28. Проверить доступность Web-интефейсов HDFS Namenode и YARN Resource Manager по портам 9870 и 8088 соответственно (VM1). << порты должны быть доступны с хостовой системы.

```
[exam@exammachine1 ~]$ sudo firewall-cmd --zone=public --permanent --add-port=9870/tcp
success
[exam@exammachine1 ~]$ sudo firewall-cmd --zone=public --permanent --add-port=8088/tcp
success
[exam@exammachine1 ~]$ sudo firewall-cmd --reload
success
```

И проверим, находясь на основной машине, введя IP-адрес первой машины с портом 9870 в браузере

![image](https://user-images.githubusercontent.com/15772109/150949965-3a400da1-2d1f-462f-9edd-7cdc115fa636.png)


Открывая, получаем окно Hadoop с overview и краткой информации о версии, когда запущена и т.д.


Проверим порт 8088 по тому же принципу:

![image](https://user-images.githubusercontent.com/15772109/150950006-451e762d-3ecd-424a-86fb-cdc22d6fc2c1.png)

И видим здесь пустой список задач Hadoop, а также метрики кластера.

### 29. Настроить управление запуском каждого компонента Hadoop при помощи systemd (используя юниты-сервисы).

Для VM1
```
[exam@exammachine1 ~]$ sudo emacs /etc/systemd/system/yarn-resource-manager.service
```
```
[Unit]
Description=Yarn ResourceManager
After=network-online.target

[Service]
User=yarn
Group=hadoop
Type=simple
ExecStart=/usr/local/hadoop/current/bin/yarn resourcemanager
Restart=on-failure
```
```
[exam@exammachine1 ~]$ sudo emacs /etc/systemd/system/hdfs-namenode.service
```
```
[Unit]
Description=HDFS Namenode
After=network-online.target

[Service]
User=hdfs
Group=hadoop
Type=simple
ExecStart=/usr/local/hadoop/current/bin/hdfs namenode
Restart=on-failure
```

Поскольку два инстанса не могут находиться на одном tcp-порту, то отстрелим деймонов, которых запустили ранее:
```
[exam@exammachine1 ~]$ sudo kill -9 2086
[exam@exammachine1 ~]$ sudo kill -9 2163
```
Далее перезапускаем службы
```
[exam@exammachine1 ~]$ sudo systemctl daemon-reload
```
И проверим статусы наших созданных деймонов
```
[exam@exammachine1 ~]$ systemctl status yarn-resource-manager
● yarn-resource-manager.service - Yarn ResourceManager
   Loaded: loaded (/etc/systemd/system/yarn-resource-manager.service; static; vendor preset: disabled)
   Active: inactive (dead)
```
```
[exam@exammachine1 ~]$ systemctl status hdfs-namenode
● hdfs-namenode.service - HDFS Namenode
   Loaded: loaded (/etc/systemd/system/hdfs-namenode.service; static; vendor preset: disabled)
   Active: inactive (dead)
```
Убеждаясь, что созданных деймонов видит systemctl, запустим их
```
[exam@exammachine1 ~]$ systemctl start yarn-resource-manager
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Для управления системными службами и юнитами, необходимо пройти аутентификацию.
Authenticating as: headnode
Password: 
==== AUTHENTICATION COMPLETE ===
```
И проверим работоспособность:
```
[exam@exammachine1 ~]$ systemctl status yarn-resource-manager
● yarn-resource-manager.service - Yarn ResourceManager
   Loaded: loaded (/etc/systemd/system/yarn-resource-manager.service; static; vendor preset: disabled)
   Active: active (running) since Сб 2022-01-22 21:13:29 MSK; 18s ago
 Main PID: 2604 (java)
   CGroup: /system.slice/yarn-resource-manager.service
           └─2604 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_resourcemanager -Djava.net.preferIPv4Stack=...
```
Теперь тоже самое с HDFS Namenode 
```
[exam@exammachine1 ~]$ systemctl start hdfs-namenode
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Для управления системными службами и юнитами, необходимо пройти аутентификацию.
Authenticating as: headnode
Password: 
==== AUTHENTICATION COMPLETE ===
```
```
[exam@exammachine1 ~]$ systemctl status hdfs-namenode
● hdfs-namenode.service - HDFS Namenode
   Loaded: loaded (/etc/systemd/system/hdfs-namenode.service; static; vendor preset: disabled)
   Active: active (running) since Сб 2022-01-22 21:14:31 MSK; 5s ago
 Main PID: 2878 (java)
   CGroup: /system.slice/hdfs-namenode.service
           └─2878 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_namenode -Djava.net.preferIPv4Stack=true -D...
```

Теперь тоже самое для VM2, по той же схеме
```
[exam@exammachine2 ~]$ sudo emacs /etc/systemd/system/hdfs-datanode.service
[Unit]
Description=HDFS Datanode
After=network-online.target

[Service]
User=hdfs
Group=hadoop
Type=simple
ExecStart=/usr/local/hadoop/current/bin/hdfs datanode
Restart=on-failure

```
```
[exam@exammachine2 ~]$ sudo emacs /etc/systemd/system/yarn-nodemanager.service 

[Unit]
Description=Yarn Nodemanager
After=network-online.target

[Service]
User=yarn
Group=hadoop
Type=simple
ExecStart=/usr/local/hadoop/current/bin/yarn nodemanager
Restart=on-failure
[exam@exammachine2 ~]$ sudo systemctl daemon-reload
[exam@exammachine2 ~]$ systemctl start hdfs-datanode
[exam@exammachine2 ~]$ systemctl status hdfs-datanode
● hdfs-datanode.service - HDFS Datanode
   Loaded: loaded (/etc/systemd/system/hdfs-datanode.service; static; vendor preset: disabled)
   Active: active (running) since Сб 2022-01-22 21:48:07 MSK; 38s ago
 Main PID: 4145 (java)
   CGroup: /system.slice/hdfs-datanode.service
           └─4145 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_datanode -Djava.net.preferIPv4Stack=true -Dhadoop.securi...
```
```
[exam@exammachine2 ~]$ systemctl start yarn-nodemanager
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Для управления системными службами и юнитами, необходимо пройти аутентификацию.
Authenticating as: worker
Password: 
==== AUTHENTICATION COMPLETE ===
```
```
[exam@exammachine2 ~]$ systemctl status yarn-nodemanager
● yarn-nodemanager.service - Yarn Nodemanager
   Loaded: loaded (/etc/systemd/system/yarn-nodemanager.service; static; vendor preset: disabled)
   Active: active (running) since Сб 2022-01-22 21:46:43 MSK; 3s ago
 Main PID: 3905 (java)
   CGroup: /system.slice/yarn-nodemanager.service
           └─3905 /usr/lib/jvm/java-1.8.0/bin/java -Dproc_nodemanager -Djava.net.preferIPv4Stack=true...
```





