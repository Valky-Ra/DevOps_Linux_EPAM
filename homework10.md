## Boot process

### 1. enable recovery options for grub, update main configuration file and find new item in grub2 config in /boot.

Для начала нужно открыть файл с конфигом grub
Находится он по пути ```/etc/default/grub``` , туда и отправимся:
```
[valkyra@ValkyRa ~]$ sudo emacs /etc/default/grub
```
Находим строчку с recovery и изменяем её на “false”
```
GRUB_DISABLE_RECOVERY="false"
```
Сохраняем и далее обновляем конфиг для grub.
```
[valkyra@ValkyRa ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
[sudo] пароль для valkyra: 
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-1160.53.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.53.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1160.49.1.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.49.1.el7.x86_64.img
Found linux image: /boot/vmlinuz-3.10.0-1160.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-1160.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-b13b11c45b1fd84da631a83fff62a8d2
Found initrd image: /boot/initramfs-0-rescue-b13b11c45b1fd84da631a83fff62a8d2.img
done
```

После подобных действий, перезапускаем систему, и видим, что к каждой возможности запуска линукса добавился recovery mode

![image](https://user-images.githubusercontent.com/15772109/151134659-dbb5ce81-8dc3-4960-a7a7-b9cf37369c7c.png)

Запустим систему и откроем файл конфига в boot и найдём новодобавленные строки для запуска recovery мода, на примере моей версии CentOS, это выглядит так:
```
menuentry 'CentOS Linux (3.10.0-1160.53.1.el7.x86_64) 7 (Core) (recovery mode)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-1160.53.1.el7.x86_64-recovery-b29b1a4a-bd8c-480a-b3fe-eae3d717c571' {
	load_video
	set gfxpayload=keep
	insmod gzio
	insmod part_msdos
	insmod xfs
	set root='hd0,msdos1'
	if [ x$feature_platform_search_hint = xy ]; then
	  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  0fa8b76a-7829-4081-a4b6-afac207a04fa
	else
	  search --no-floppy --fs-uuid --set=root 0fa8b76a-7829-4081-a4b6-afac207a04fa
	fi
	linux16 /vmlinuz-3.10.0-1160.53.1.el7.x86_64 root=/dev/mapper/centos-root ro single crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet
	initrd16 /initramfs-3.10.0-1160.53.1.el7.x86_64.img
}
```

### 2. modify option vm.dirty_ratio:
###   - using echo utility
Открываем через cat файл vm.dirty_ratio, что находится по следующему пути:
```
[valkyra@ValkyRa ~]$ cat /proc/sys/vm/dirty_ratio 
30
```
И видим, что там есть значение 30. 

Используя echo, изменим это.
```
[valkyra@ValkyRa ~]$ echo 35 > /proc/sys/vm/dirty_ratio 
bash: /proc/sys/vm/dirty_ratio: Отказано в доступе 
```
Но мне не хватило доступа, даже с sudo, посему сделаю через рута.
```
[root@ValkyRa ~]# echo 35 > /proc/sys/vm/dirty_ratio
```
Также использую cat, только уже обратно на обычном пользователе и видим, что значение изменено.
```
[valkyra@ValkyRa ~]$ cat /proc/sys/vm/dirty_ratio 
35
```
###   - using sysctl utility
Используем sysctl с –w для записи изменений и получаем изменённое значение vm.dirty_ratio
```
[valkyra@ValkyRa ~]$ sudo sysctl -w vm.dirty_ratio=45
[sudo] пароль для valkyra: 
vm.dirty_ratio = 45
```
### - using sysctl configuration files
Проверим значение перед изменениями:
```
[valkyra@ValkyRa ~]$ sysctl vm.dirty_ratio
vm.dirty_ratio = 45
```
Открываем один из файлов конфига sysctl и прописываем в конце строку со значением vm.dirty_ratio 
```
[valkyra@ValkyRa ~]$ sudo emacs /etc/sysctl.conf

vm.dirty_ratio = 30
```
Далее обновляем информацию, используя –system
```
[valkyra@ValkyRa ~]$ sudo sysctl --system
* Applying /usr/lib/sysctl.d/00-system.conf ...
* Applying /usr/lib/sysctl.d/10-default-yama-scope.conf ...
kernel.yama.ptrace_scope = 0
* Applying /usr/lib/sysctl.d/50-default.conf ...
kernel.sysrq = 16
kernel.core_uses_pid = 1
kernel.kptr_restrict = 1
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.accept_source_route = 0
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.promote_secondaries = 1
net.ipv4.conf.all.promote_secondaries = 1
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /usr/lib/sysctl.d/60-libvirtd.conf ...
fs.aio-max-nr = 1048576
* Applying /etc/sysctl.d/99-sysctl.conf ...
vm.dirty_ratio = 30
* Applying /etc/sysctl.conf ...
vm.dirty_ratio = 30
```
После этого, наше значение для vm.dirty_ratio изменилось до стандартных 30-ти.

## Selinux
### Disable selinux using kernel cmdline
Проверим изначально, работает ли selinux
```
[valkyra@ValkyRa ~]$ getenforce 
Enforcing
```
Enforcing означает, что он работает (или же значение = 1)
Открываем конфиг и прописываем selinux=0
```
[valkyra@ValkyRa ~]$ sudo emacs /etc/default/grub
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet selinux=0"
```
Применяем изменения для grub и перезапускаем систему
```
[valkyra@ValkyRa ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
[valkyra@ValkyRa ~]$ reboot
```
Проверяем отключился ли selinux:
```
[valkyra@ValkyRa ~]$ getenforce 
Disabled
```
Есть второй способ как это сделать, можно вместо прописывания в файле, перезапустить систему и изменить параметры запуска выбранной системы, дописав как раз эту строку selinux=0 и будет то же самое.

## Firewalls

### 1.	Add rule using firewall-cmd that will allow SSH access to your server *only* from network 192.168.56.0/24 and interface enp0s8 (if your network and/on interface name differs - change it accordingly).
Для начала пропишем для фаерволла разрешение для 22-ого порта
```
[valkyra@ValkyRa ~]$ sudo firewall-cmd --add-port=22/tcp --permanent
success
```
Далее для нашего сетевого интерфейса (в моём случае, это enp0s3)
```
[valkyra@ValkyRa ~]$ sudo firewall-cmd --add-interface=enp0s3 --permanent
The interface is under control of NetworkManager and already bound to the default zone
The interface is under control of NetworkManager, setting zone to default.
success
```
После же, для нужного IP-адреса
```
[valkyra@ValkyRa ~]$ sudo firewall-cmd --add-source=192.168.56.1/24 --permanent
Success
```
Перезапускаем фаерволл и его изменённые конфиги
[valkyra@ValkyRa ~]$ firewall-cmd –reload

![image](https://user-images.githubusercontent.com/15772109/151134718-4cf24e29-ea38-4987-bb2b-5bdfaa02fa5b.png)

success

Теперь смотрим список и убеждаемся в том, что мы открыли доступ для нужного IP-адреса
```
[valkyra@ValkyRa ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3
  sources: 192.168.56.1/24
  services: dhcpv6-client ssh
  ports: 22/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules:
```


### 2. Shutdown firewalld and add the same rules via iptables.
Выключаем службу фаерволла и проверим её статус
```
[valkyra@ValkyRa ~]$ sudo systemctl stop firewalld
[valkyra@ValkyRa ~]$ sudo systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: inactive (dead) since Вт 2022-01-25 20:02:41 MSK; 5s ago
     Docs: man:firewalld(1)
  Process: 841 ExecStart=/usr/sbin/firewalld --nofork --nopid $FIREWALLD_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 841 (code=exited, status=0/SUCCESS)

янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 19:55:41 ValkyRa firewalld[841]: WARNING: COMMAND_FAILED: '/usr/sbin/....
янв 25 20:02:40 ValkyRa systemd[1]: Stopping firewalld - dynamic firewall d.....
янв 25 20:02:41 ValkyRa systemd[1]: Stopped firewalld - dynamic firewall daemon.
```
Теперь откроем список iptables
```
[valkyra@ValkyRa ~]$ sudo iptables --list
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination  
```
Теперь добавляем тоже самое, что в пункте ранее те же данные, но уже для iptables
```
[valkyra@ValkyRa ~]$ sudo iptables -A INPUT -i enp0s3 -p tcp --dport 22 -s 192.168.56.1/24 -j ACCEPT
```
И после, используем снова список iptables и видим успешный результат

![image](https://user-images.githubusercontent.com/15772109/151134758-04d3c473-ca94-4195-868d-53fb77bbee83.png)
