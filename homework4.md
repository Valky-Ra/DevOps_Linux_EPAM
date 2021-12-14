## Задание 1: Пользователи и группы
### В данном задании нужно создать группу и пользователей, изменить им пароли, все новые аккаунты должны обязательно менять свои пароли каждый 30 дней.
### А также новые аккаунты группы sales должны истечь по окончанию 90 дней срока, а bob должен изменять его пароль каждые 15 дней.

Создаём группу sales с UID 4000, а после создаём трех пользователей: bob, alice, eve и добавляем сразу в эту группу
```bash 
[ValkyRa@centos ~]$ sudo groupadd sales -g 4000
[ValkyRa@centos ~]$ sudo useradd bob -g 4000
[ValkyRa@centos ~]$ sudo useradd alice -g 4000
[ValkyRa@centos ~]$ sudo useradd eve -g 4000
```

Проверяем созданных пользователей на нахождение в группе sales:
```bash
[ValkyRa@centos ~]$ groups bob alice eve
bob : sales
alice : sales
eve : sales
```

Есть другой способ проверки пользователей, найдя нужную группу в файле group 
``` 
[ValkyRa@centos ~]$ sudo emacs /etc/group 
```


![image](https://user-images.githubusercontent.com/15772109/145995794-875155fd-73b2-4ea7-b5e2-ba1266de71cb.png)

Ну или есть вариант третий, используя cat и grep
```bash
[ValkyRa@centos ~]$ cat /etc/group | grep sales
sales:x:4000:bob,alice,eve
```

Для новоприбывших пользователей необходимо создать пароли, что и делаем командой passwd
```bash
[ValkyRa@centos ~]$ sudo passwd bob
Изменяется пароль пользователя bob.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.

[ValkyRa@centos ~]$ sudo passwd alice
Изменяется пароль пользователя alice.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.

[ValkyRa@centos ~]$ sudo passwd eve
Изменяется пароль пользователя eve.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
```
Далее нужно указать для группы sales дату истечения действия пользователя модификатором –e, и для пользователя bob указать, что пароль нужно менять каждые 15 дней модификатором -f 
```bash 
[ValkyRa@centos ~]$ sudo usermod -aG sales -e 2022-04-13 -f 15 bob
[sudo] пароль для ValkyRa: 
[ValkyRa@centos ~]$ sudo usermod -aG sales -e 2022-04-13 alice
[ValkyRa@centos ~]$ sudo usermod -aG sales -e 2022-04-13 eve
```
Меняем для всех трёх пользователей максимальное кол-во дней для изменения пароля, используя –M и –d0 для того, чтобы при входе пользователя, система заставляла изменить пароль пользователю
```bash
[ValkyRa@centos ~]$ sudo chage -d0 -M 30 bob
[ValkyRa@centos ~]$ sudo chage -d0 -M 30 alice
[ValkyRa@centos ~]$ sudo chage -d0 -M 30 eve
```

И проверяем правильность настроек пользователя, на примере eve
```
[ValkyRa@centos ~]$ sudo chage -l eve
Последний раз пароль был изменён			: пароль должен быть изменён
Срок действия пароля истекает         : пароль должен быть изменён
Пароль будет деактивирован через			: пароль должен быть изменён
Срок действия учётной записи истекает	: апр 13, 2022
Минимальное количество дней между сменой пароля		: 0
Максимальное количество дней между сменой пароля	: 30
Количество дней с предупреждением перед деактивацией пароля	: 7
```
И теперь, проверяем необходимость смены пароля для пользователя при входе в систему, на примере bob
```bash
[ValkyRa@centos ~]$ su bob
Пароль: 
Вам необходимо немедленно сменить пароль (в принудительном режиме root)
Смена пароля для bob.
(текущий) пароль UNIX:
```
## Задание 2: Контроль правами доступа к файлам в системе Linux
### В этом задании нужно создать трёх пользователей: glen, antony, lesly. При этом должна быть директория /home/students, где эти три пользователя могут работать совместно с файлами.
### Также, должен быть возможен только пользовательский и групповой доступ, создание и удаление файлов в /home/students. И файлы, созданные в этой директории, должны автоматически присваиваться группе студентов students.

Для начала создадим группу students, и я хочу задать ей UID 5000. Необходимость UID в задании нет, просто моё желание.
```
[ValkyRa@centos ~]$ sudo groupadd -g 5000 students
```
Используя один из способов, приложенных ранее как найти группу, убеждаемся в правильности работы команды
```
[ValkyRa@centos ~]$ cat /etc/group | grep students
students:x:5000:
```
Далее создаём трёх нужных пользователей: glen, antony, lesly.
```bash
[ValkyRa@centos ~]$ sudo useradd glen -g 5000
[ValkyRa@centos ~]$ sudo useradd antony -g 5000
[ValkyRa@centos ~]$ sudo useradd lesly -g 5000
```
И убеждаемся в том, что они есть в группе students
```bash
[ValkyRa@centos ~]$ cat /etc/group | grep students
students:x:5000:
```
Однако, открывая файл с группой, замечаю интересный факт, в файле не указано, что они в группе, но если прописать следующую команду:
```bash
[ValkyRa@centos ~]$ groups glen antony lesly
glen : students
antony : students
lesly : students
```
То видим, что в группе они есть (не знаю баг это или нет), но для гарантии решил добавить пользователей в группу через usermod, а именно:
```bash
[ValkyRa@centos ~]$ sudo usermod -aG students glen
[sudo] пароль для ValkyRa: 
[ValkyRa@centos ~]$ sudo usermod -aG students antony
[ValkyRa@centos ~]$ sudo usermod -aG students lesly
```
Проверяем, и видим, что теперь работает корректно.
```bash
[ValkyRa@centos ~]$ cat /etc/group | grep students
students:x:5000:glen,antony,lesly
```
Далее нам нужно сделать владельцем группу students директорию /home/students, и создадим туда одну директорию и один файлик
```bash
[ValkyRa@centos ~]$ sudo chown :students /home/students
[ValkyRa@centos ~]$ sudo touch /home/students/sometestdirectory
[ValkyRa@centos ~]$ sudo touch /home/students/sometestfile.txt
```
Посмотрим содержимое, для того, чтобы убедиться что владелец директории группа students 
```bash
[ValkyRa@centos ~]$ ls -la /home/students
итого 0
drwxr-xr-x. 2 root students 55 дек 13 17:55 .
drwxr-xr-x. 7 root root     76 дек 13 17:40 ..
-rw-r--r--. 1 root root      0 дек 13 17:55 sometestdirectory
-rw-r--r--. 1 root root      0 дек 13 17:52 sometestfile.txt
```
Далее нужно для группы дать разрешение на чтение и запись файлов:
```bash
[ValkyRa@centos ~]$ sudo chmod g+srw /home/students
```
Теперь попробуем создать файлик пользователем, из группы students. Залогинимся под пользователем glen 
```
[ValkyRa@centos ~]$ su glen
Пароль: 
```
Перейдём в директорию students и попробуем посмотреть содержимое
```bash
[glen@centos ValkyRa]$ cd /home/students
[glen@centos students]$ ls -la
итого 0
drwxrwsr-x. 2 root students 55 дек 13 17:55 .
drwxr-xr-x. 7 root root     76 дек 13 17:40 ..
-rw-r--r--. 1 root root      0 дек 13 17:55 sometestdirectory
-rw-r--r--. 1 root root      0 дек 13 17:52 sometestfile.txt
```
Теперь создадим ещё один тестовый файлик уже от лица glen
```
[glen@centos students]$ > testfile.txt
```
Проверим и убедимся, что всё работает и владелец нового файла является группа students.
```bash
[glen@centos students]$ ll
итого 0
-rw-r--r--. 1 root root     0 дек 13 17:55 sometestdirectory
-rw-r--r--. 1 root root     0 дек 13 17:52 sometestfile.txt
-rw-r--r--. 1 glen students 0 дек 13 18:03 testfile.txt
```

## Задание 3: ACL
Детективное агентство Бейкер Стрит создает коллекцию совместного доступа для хранения файлов дел, в которых члены группы bakerstreet будут иметь права на чтение и запись.
Ведущий детектив, Шерлок Холмс, решил, что члены группы scotlandyard также должны иметь возможность читать и писать в общую директорию. Тем не менее, Холмс считает, что инспектор Джонс является достаточно растерянным, и поэтому он должен иметь доступ только для чтения. 
Миссис Хадсон только начала осваивать Linux и смогла создать общую директорию и скопировать туда несколько файлов. Но сейчас время чаепития, и она попросила вас закончить работу.
Наша задача - завершить настройку директории общего доступа. 
Директория и всё её содержимое должно принадлежать группе bakerstreet, при этом файлы должны обновляться для чтения и записи для владельца и группы (bakerstreet). У других пользователей не должно быть никаких разрешений. 
Также необходимо предоставить доступы на чтение и запись для группы scotlandyard, за исключением Jones, который может только читать документы.
И нужно проверить, что настройка применима к существующим и будущим файлам. После установки всех разрешений в директории проверить от каждого пользователя все его возможные доступы.
### А также нужно:
Создать общую директорию /share/cases.

Создать группу bakerstreet с пользователями holmes, watson.

Создать группу scotlandyard с пользователями lestrade, gregson, jones.

Задать всем пользователям безопасные пароли.

Для начала создаём группы bakerstreet и scotlandyard 
```bash 
[ValkyRa@centos ~]$ sudo groupadd bakerstreet
[sudo] пароль для ValkyRa: 
[ValkyRa@centos ~]$ sudo groupadd scotlandyard
```
Далее создаём необходимых пользователей для двух разных групп:
```bash
[ValkyRa@centos ~]$ sudo useradd -G bakerstreet holmes
[ValkyRa@centos ~]$ sudo useradd -G bakerstreet watson
[ValkyRa@centos ~]$ sudo useradd -G scotlandyard lestrade
[ValkyRa@centos ~]$ sudo useradd -G scotlandyard gregson
[ValkyRa@centos ~]$ sudo useradd -G scotlandyard jones
```
После, проверяем правильность нахождение пользователей в группах:
```bash
[ValkyRa@centos ~]$ cat /etc/group | grep bakerstreet
bakerstreet:x:1001:holmes,watson
[ValkyRa@centos ~]$ cat /etc/group | grep scotlandyard
scotlandyard:x:1002:lestrade,gregson,jones
```
Далее необходимо создать пароли для каждого пользователя, в противном случае, войти под этими пользователями не получится.
```bash
[ValkyRa@centos ~]$ sudo passwd holmes
Изменяется пароль пользователя holmes.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
[ValkyRa@centos ~]$ sudo passwd watson
Изменяется пароль пользователя watson.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
[ValkyRa@centos ~]$ sudo passwd lestrade
Изменяется пароль пользователя lestrade.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
[ValkyRa@centos ~]$ sudo passwd gregson
Изменяется пароль пользователя gregson.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
[ValkyRa@centos ~]$ sudo passwd jones
Изменяется пароль пользователя jones.
Новый пароль : 
Повторите ввод нового пароля : 
passwd: все данные аутентификации успешно обновлены.
```
Далее создаём общую директорию для новоприбывших пользователей /share/cases.
```bash
[ValkyRa@centos /]$ sudo mkdir /share
[ValkyRa@centos /]$ sudo mkdir /share/cases
```
Меняем владельца данной директорией на группу bakerstreet
```
[ValkyRa@centos ~]$ sudo chown :bakerstreet /share/cases
```
После же, нам нужно выставить определённые разрешения на директории и файлы. 
Проделаем такой ряд команд. Мы ищем путь до нужной директории командой find, указываем тип что мы ищем, т.е. d – directory или f - file, и выполняем setfacl для выставления прав определённой группы на файлы и директории, используя –m т.е. модифицируя права для группы.
```bash
[ValkyRa@centos ~]$ sudo find /share/cases/ -type d -exec setfacl -m g:scotlandyard:rwx {} \;
[ValkyRa@centos ~]$ sudo find /share/cases/ -type f -exec setfacl -m g:scotlandyard:rw {} \;
[ValkyRa@centos ~]$ sudo find /share/cases/ -type f -exec setfacl -m u:jones:r {} \;
[ValkyRa@centos ~]$ sudo find /share/cases/ -type d -exec setfacl -m u:jones:rx {} \;
```
Есть другой, более простой вариант, например: 
```
setfacl –m g:scotlandyard:rwx /share/cases/ 
```
Но минус в том, что мы задаём одновременно для директорий и файлов, и если нужно под разные нужды, то лучше использовать команды выше
Проверяем их правильность:
```bash
[ValkyRa@centos ~]$ sudo getfacl /share/cases/
getfacl: Removing leading '/' from absolute path names
# file: share/cases/
# owner: root
# group: bakerstreet
user::rwx
user:jones:r-x
group::rwx
group:scotlandyard:rwx
mask::rwx
other::---
```
Логинимся под учётной записью holmes
```bash
[ValkyRa@centos ~]$ 
[ValkyRa@centos ~]$ su holmes
Пароль: 
[holmes@centos ValkyRa]$ cd /share/cases/
```
Проверяем, можем ли читать информацию с необходимой директории.
```bash
[holmes@centos cases]$ ls -la
итого 0
drwxrwx---+ 2 root bakerstreet 45 дек 13 20:41 .
drwxr-xr-x. 3 root root        19 дек 13 20:38 ..
-rw-rw-r--+ 1 root root         0 дек 13 20:41 moriarty.txt
-rw-rw-r--+ 1 root root         0 дек 13 20:41 murders.txt
```
Пробуем создать файл holmes.txt и проверяем правильность работы
```bash
[holmes@centos cases]$ touch holmes.txt
[holmes@centos cases]$ ls -la
итого 0
drwxrwx---+ 2 root   bakerstreet 63 дек 13 20:56 .
drwxr-xr-x. 3 root   root        19 дек 13 20:38 ..
-rw-rw-r--. 1 holmes holmes       0 дек 13 20:56 holmes.txt
-rw-rw-r--+ 1 root   root         0 дек 13 20:41 moriarty.txt
-rw-rw-r--+ 1 root   root         0 дек 13 20:41 murders.txt
```
Чтобы проверить правильность работы прав у группы scotlandyard, попробуем зайти под другим пользователем из этой группы, например, gregson 
```bash
[jones@centos cases]$ su gregson
Пароль: 
[gregson@centos cases]$ touch gregson.txt
[gregson@centos cases]$ ls -la

итого 0
drwxrwx---+ 2 root    bakerstreet 82 дек 13 20:58 .
drwxr-xr-x. 3 root    root        19 дек 13 20:38 ..
-rw-rw-r--. 1 gregson gregson      0 дек 13 20:58 gregson.txt
-rw-rw-r--. 1 holmes  holmes       0 дек 13 20:56 holmes.txt
-rw-rw-r--+ 1 root    root         0 дек 13 20:41 moriarty.txt
-rw-rw-r--+ 1 root    root         0 дек 13 20:41 murders.txt
```
Теперь логинимся на jones
```bash
[holmes@centos cases]$ su jones
Пароль:
```
И пробуем провернуть ту же процедуру, по идее нам не должно хватить прав.
```bash
[jones@centos cases]$ touch jones.txt
touch: невозможно выполнить touch для «jones.txt»: Отказано в доступе
```
Проверим, находится ли Jones в необходимой группе 
```bash
[jones@centos cases]$ id -Gn
jones scotlandyard
```





