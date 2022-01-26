### 1. Imagine you was asked to add new partition to your host for backup purposes. To simulate appearance of new physical disk in your server, please create new disk in Virtual Box (5 GB) and attach it to your virtual machine. Also imagine your system started experiencing RAM leak in one of the applications, thus while developers try to debug and fix it, you need to mitigate OutOfMemory errors; you will do it by adding some swap space.
### /dev/sdc - 5GB disk, that you just attached to the VM (in your case it may appear as /dev/sdb, /dev/sdc or other, it doesn't matter)

Заходим в настройки виртуальной машины, раздел Storage и создаём диск

![image](https://user-images.githubusercontent.com/15772109/147360465-07806f0a-4036-4187-9899-93f8f14362c5.png)


![image](https://user-images.githubusercontent.com/15772109/147360479-190e0088-c99b-4f38-96e6-5867aad4ac6b.png)

После его создания, выбираем его, и он у нас в списке жёстких дисков

![image](https://user-images.githubusercontent.com/15772109/147360502-90d3afb5-15b6-4666-ace0-8f163d634809.png)


![image](https://user-images.githubusercontent.com/15772109/147360517-11cb3784-33de-4dd5-996e-1bfc38339798.png)

После добавления нового диска, проверяем наличие его в системе, уже на машине, в нашем случае, это sdb
```
[ValkyRa@centos ~]$ cd /dev
[ValkyRa@centos dev]$ ls sd
sda   sda1  sda2  sdb   
[ValkyRa@centos dev]$ sudo /sbin/fdisk -l /dev/sdb

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### 1.1. Create a 2GB   !!! GPT !!!   partition on /dev/sdc of type "Linux filesystem" (means all the following partitions created in the following steps on /dev/sdc will be GPT as well)

Используем fdisk для создания разделов:
```
[ValkyRa@centos ~]$ sudo fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Команда (m для справки): g
Building a new GPT disklabel (GUID: B6F0B45A-C206-4BC9-B5BB-38E37D10320A)


Команда (m для справки): p

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: B6F0B45A-C206-4BC9-B5BB-38E37D10320A


#         Start          End    Size  Type            Name

```

Далее создадим раздел:
```
Команда (m для справки): n
Номер раздела (1-128, default 1): 
First sector (2048-10485726, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-10485726, default 10485726): +2048M
Created partition 1
```
И теперь посмотрим, создался ли раздел:
```
Команда (m для справки): p

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: B6F0B45A-C206-4BC9-B5BB-38E37D10320A


#         Start          End    Size  Type            Name
 1         2048      4196351      2G  Linux filesyste 

```
Далее используем “w” для записи раздела

### 1.2. Create a 512MB partition on /dev/sdc of type "Linux swap"

По аналогии, как и в 1.1 создаём раздел:
```
Команда (m для справки): n
Номер раздела (2-128, default 2):  
First sector (4196352-10485726, default 4196352): 
Last sector, +sectors or +size{K,M,G,T,P} (4196352-10485726, default 10485726): +512M
Created partition 2
```
Далее задаём тип раздела:
```
Команда (m для справки): t
Номер раздела (1,2, default 2): 2
Partition type (type L to list all types): L
Partition type (type L to list all types): 19
Changed type of partition 'Linux filesystem' to 'Linux swap'
```
И выводим:
```
Команда (m для справки): p

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: B6F0B45A-C206-4BC9-B5BB-38E37D10320A


#         Start          End    Size  Type            Name
 1         2048      4196351      2G  Linux filesyste 
 2      4196352      5244927    512M  Linux swap      
```
Записываем изменения:
```
Команда (m для справки): w
Таблица разделов была изменена!

Вызывается ioctl() для перечитывания таблицы разделов.
Синхронизируются диски.
```
### 1.3. Format the 2GB partition with an XFS file system
Изменить через fdisk тип в xfs не получилось, посему воспользуемся командой mkfs
```
[ValkyRa@centos ~]$ sudo mkfs -t xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

```
### 1.4. Initialize 512MB partition as swap space
Используем команду mkswap и указываем раздел, который мы хотим использовать как файл подкачки
```
[ValkyRa@centos ~]$ sudo mkswap /dev/sdb2
Setting up swapspace version 1, size = 524284 KiB
без метки, UUID=d308226a-2701-4934-b613-87c3d2537f9d
```
### 1.5. Configure the newly created XFS file system to persistently mount at /backup
Создадим точку монтирования:
```
[ValkyRa@centos ~]$ sudo mkdir /mnt/hw_backup
```
И монтируем в 1-ый раздел, после же проверяем всё ли получилось верно
```
[ValkyRa@centos ~]$ sudo mount -t xfs /dev/sdb1 /mnt/hw_backup/
[ValkyRa@centos ~]$ lsblk -f
```
![image](https://user-images.githubusercontent.com/15772109/147361086-2d4d5504-54ae-414f-a6ef-75974a7df067.png)

### 1.6. Configure the newly created swap space to be enabled at boot
Запустим fstab и там добавим новый файл подкачки в boot 
```
[ValkyRa@centos ~]$ sudo emacs /etc/fstab
```

![image](https://user-images.githubusercontent.com/15772109/147361108-7604c997-90b7-4918-9f3d-b9fa379cf257.png)

### 1.7. Reboot your host and verify that /dev/sdc1 is mounted at /backup and that your swap partition  (/dev/sdc2) is enabled
Перед перезапуском:
```
[ValkyRa@centos ~]$ lsblk –f
```

![image](https://user-images.githubusercontent.com/15772109/147361150-d5d14cd1-afc0-4114-93b2-bcefcda276ae.png)

```
[ValkyRa@centos ~]$ swapon –s
```

![image](https://user-images.githubusercontent.com/15772109/147361168-06f75e52-d8fc-4206-91c4-425ac09edc07.png)

```
[ValkyRa@centos ~]$ free -m
```

![image](https://user-images.githubusercontent.com/15772109/147361191-50613bdb-6601-48c6-a854-70198ebfec05.png)

После перезагрузки эти же команды:

![image](https://user-images.githubusercontent.com/15772109/147361204-ec3c544e-573f-4f9a-ab14-49dd2f7aa971.png)

## 2. LVM. Imagine you're running out of space on your root device. As we found out during the lesson default CentOS installation should already have LVM, means you can easily extend size of your root device. So what are you waiting for? Just do it!
### 2.1. Create 2GB partition on /dev/sdc of type "Linux LVM"

```
[ValkyRa@centos ~]$ sudo fdisk /dev/sdb
[sudo] пароль для ValkyRa: 
WARNING: fdisk GPT support is currently new, and therefore in an experimental phase. Use at your own discretion.
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
```
Создаём третий раздел для LVM:
```
Команда (m для справки): n
Номер раздела (3-128, default 3): 
First sector (5244928-10485726, default 5244928): 
Last sector, +sectors or +size{K,M,G,T,P} (5244928-10485726, default 10485726): +2G
Created partition 3
```

И указываем для него тип:
```
Команда (m для справки): t
Номер раздела (1-3, default 3): 3
Partition type (type L to list all types): L
Partition type (type L to list all types): 31
Changed type of partition 'Linux filesystem' to 'Linux LVM'
```
Проверяем, что он создался и записываем данные в систему:
```
Команда (m для справки): p

Disk /dev/sdb: 5368 MB, 5368709120 bytes, 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: gpt
Disk identifier: B6F0B45A-C206-4BC9-B5BB-38E37D10320A


#         Start          End    Size  Type            Name
 1         2048      4196351      2G  Linux filesyste 
 2      4196352      5244927    512M  Linux swap      
 3      5244928      9439231      2G  Linux LVM   
Команда (m для справки): w
Таблица разделов была изменена!

Вызывается ioctl() для перечитывания таблицы разделов.

WARNING: Re-reading the partition table failed with error 16: Устройство или ресурс занято.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Синхронизируются диски.
```

Снова перезапустим систему и увидим, что после перезапуска sdb3 появился, без перезапуска система не видела sdb3.

![image](https://user-images.githubusercontent.com/15772109/147361332-150737a6-9693-4e9a-b63e-079a360ebcf3.png)

### 2.2. Initialize the partition as a physical volume (PV)

```
[ValkyRa@centos ~]$ sudo pvcreate /dev/sdb3
[sudo] пароль для ValkyRa: 
  Physical volume "/dev/sdb3" successfully created.
```

### 2.3. Extend the volume group (VG) of your root device using your newly created PV

Проверим список разбитых PV
```
[ValkyRa@centos ~]$ sudo pvs
  PV         VG     Fmt  Attr PSize   PFree
  /dev/sda2  centos lvm2 a--  <59,00g 4,00m
  /dev/sdb3         lvm2 ---    2,00g 2,00g
```
И далее изменяем группу для sdb3
```
[ValkyRa@centos ~]$ sudo vgextend centos /dev/sdb3
  Volume group "centos" successfully extended
```
Теперь проверяем список ещё раз
```
[ValkyRa@centos ~]$ sudo pvs
  PV         VG     Fmt  Attr PSize   PFree 
  /dev/sda2  centos lvm2 a--  <59,00g  4,00m
  /dev/sdb3  centos lvm2 a--   <2,00g <2,00g
```

### 2.4. Extend your root logical volume (LV) by 1GB, leaving other 1GB unassigned

Для начала проверим сколько у нас места для root-а
```
[ValkyRa@centos ~]$ sudo lvs
```
![image](https://user-images.githubusercontent.com/15772109/147361492-38dd6223-e175-403f-ae76-ed165f519dc4.png)

И прописываем команду:
```
[ValkyRa@centos ~]$ sudo lvextend -L+1G /dev/mapper/centos-root
  Size of logical volume centos/root changed from <37,04 GiB (9481 extents) to <38,04 GiB (9737 extents).
  Logical volume centos/root successfully resized.
```
```
[ValkyRa@centos ~]$ sudo lvs
```

![image](https://user-images.githubusercontent.com/15772109/147361568-aa259eab-9c9a-43f6-b96e-3755779341f1.png)

### 2.5. Check current disk space usage of your root device

```
[ValkyRa@centos ~]$ df -h
```

![image](https://user-images.githubusercontent.com/15772109/147361588-6ecfbe57-c647-4d01-a31a-c6464092d7eb.png)

### 2.6. Extend your root device filesystem to be able to use additional free space of root LV

Используем команду для увеличения оставшегося свободного места для root 
```
[ValkyRa@centos ~]$ sudo lvextend -r -l +100%FREE /dev/mapper/centos-root
```

![image](https://user-images.githubusercontent.com/15772109/147361646-63f71ffd-3fe5-460f-bb6a-e1361411b84f.png)

### 2.7. Verify that after reboot your root device is still 1GB bigger than at 2.5.

Перезапускаю систему и нужно, чтобы в памяти рута было то значение, что мы получили, исходя из пункта 2.6

Вводим команду для проверки и получаем верный результат:

Командой df показывает, что 40 гб:

![image](https://user-images.githubusercontent.com/15772109/147361667-1be35400-0cdb-4c6b-adf1-5648ba0bbe3e.png)

Но если проверить другой командой, например, lvs:

![image](https://user-images.githubusercontent.com/15772109/147361683-3fbd275e-9eb9-4170-bc92-20b2cd107e2b.png)

То будет 39 гб, немного странное округление, но всё же результат соответствует тому, что мы видели в пункте 2.6.




