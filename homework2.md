# Homework

## Задание 1

### В этом задании нам нужно открыть инструкцию по пользованию приложением awk. Найти секцию про использование переменных окружения. И сохранить эту секцию в отдельный файл.
Открываем инструкцию по пользованию приложением awk командой “man awk”, и находим секцию про использование переменных окружения (Environment Variables) путём команды less. 
Далее определяем строку с которой начинается секция, после же мы командой awk берём данные о переменных и помещаем в файл test.txt

```bash
 [ValkyRa@centos ~]$ man -P 'less +/ENVIRONMENT\ VARIABLES' awk | awk 'NR>=1731&&NR<=1745' > test.txt
```
После же, используя cat убеждаемся в правильности работы команд
```bash
[ValkyRa@centos ~]$ cat test.txt
ENVIRONMENT VARIABLES
       The AWKPATH environment variable can be  used  to  provide  a  list  of
       directories  that gawk searches when looking for files named via the -f
       and --file options.

       For socket communication, two special environment variables can be used
       to  control the number of retries (GAWK_SOCK_RETRIES), and the interval
       between retries (GAWK_MSEC_SLEEP).  The interval is in milliseconds. On
       systems  that  do  not support usleep(3), the value is rounded up to an
       integral number of seconds.

       If POSIXLY_CORRECT exists in the environment, then gawk behaves exactly
       as  if  --posix  had been specified on the command line.  If --lint has
       been specified, gawk issues a warning message to this effect.

```

## Задание 2

### В данном задании необходимо написать скрипт, который создаёт файл "task2.txt" директорией выше своего местоположения. И в случае ошибки, текст ошибки записывается в err.log, а пользователю выдаётся сообщение "Error."

Создадим в корне каталог test и создадим скрипт testscript.sh
```bash
[ValkyRa@centos ~]$ mkdir test
[ValkyRa@centos ~]$ cd test
[ValkyRa@centos test]$ touch testscript.sh
```
Сам скрипт прописываем таким образом: ```touch ../task2.txt 2>err.log || echo "Error."```
Для запуска скрипта требуется ещё добавить для себя права на выполнение, поэтому прописываем: ```chmod u+x testscript.sh```
Далее запускаем скрипт, прописывая полный путь, в моём случае: ```~/test/testscript.sh```
Тем самым, мы создаём файл task2.txt в каталоге выше своего местоположения и в случае ошибки, мы передаём ошибку в файл err.log и выводим на экран пользователю "Error".

## Задание 2(*)

### В этом задании необходимо изменить скрипт так, что если файл уже существует, выдаётся одна ошибка, а если нет прав для его создания - другая.

Для проверки на наличие файла нам нужно изменить наш скрипт, а именно дописать условие if
```bash
#!/bin/bash
FILE=../task2.txt

if [ -f "$FILE" ]; then
    echo "Error. File exists."
fi
touch $FILE 2>>err.log || echo "Error."
```
Таким образом, скрипт проверяет наличие файла task2.txt и в случае его наличия выдаёт “Error. File exists.”

Для проверки на права доступа нужно добавить ещё одно условие if
```bash
#!/bin/bash
DIRECTORY=../
FILE=$DIRECTORY/task2.txt

if [ -f "$FILE" ]; then
    echo "Error. File exists."
fi
if ! [ -w $DIRECTORY ]; then
    echo "Not enough permissions to create a file."
Fi

touch $FILE 2>>err.log || echo "Error."
```
В данном случае мы проверяем, может ли текущий пользователь создать файлы в папку выше директории и если нет, то выдавать соответствующую ошибку.

## Задание 3

### В задании нужно создать 2 файла: 1-й - текстовый, с указанием абслютного пути до директории. 2-й - скрипт, который при выполнении выводит содержимое директории по указанному в первом файле.

Создаём 2 файла: 1-й - текстовый с указанием абсолютного пути до директории, командой ```cat > testfile.txt```
И вписываем в файл абсолютный путь до директории, в моём случае это: ```/home/ValkyRa/test/```
2-й - скрипт, который при выполнении выводит содержимое директории по указанному в первом файле. 
emacs testscript.sh и прописываем в скрипт команду: 
``` ls `cat /home/ValkyRa/test/` ```
Выдаём права на выполнение командой chmod, запускаем скрипт и убеждаемся в работе вывода содержимого директории, что указано в первом файле
```bash 
[ValkyRa@centos ~]$ ~/test/testscript2.sh 
err.log       testscript2.sh   #testscript.sh#	testscript.sh~
testfile.txt  testscript2.sh~  testscript.sh
```

## Задание 3(*)

### В этом задании нужно изменить скрипт так, чтобы выводил отдельно количество файлов и количество директорий.

Для подсчёта кол-ва файлов и директорий, нужно добавить несколько переменных
```bash
FULLPATH=`cat /home/ValkyRa/test/testfile.txt`
ls $FULLPATH
NUM_FILES=$(find "$FULLPATH" -type f | wc -l )
NUM_FOLDERS=$(find "$FULLPATH" -type d | wc -l )

echo "Files: ${NUM_FILES}"
echo "Folders: ${NUM_FOLDERS}"
```
В данном скрипте, мы добавляем две переменные NUM_FILES и NUM_FOLDERS, указывая в них команду find и тип поиска (f – для файлов, d – для директорий) и командой wc выводим их кол-во.
Запускаем обновлённый скрипт, и убеждаемся в правильной работоспособности
```bash
[ValkyRa@centos ~]$ ~/test/testscript2.sh 
err.log       testscript2.sh   #testscript.sh#	testscript.sh~
testfile.txt  testscript2.sh~  testscript.sh
Files: 7
Folders: 1
```
Используя команду ls убеждаемся в правильности работы.
```bash 
[ValkyRa@centos ~]$ ls -la test/
итого 32
drwxrwxr-x.  2 ValkyRa ValkyRa  152 дек  4 17:22 .
drwx------. 21 ValkyRa ValkyRa 4096 дек  4 17:10 ..
-rw-rw-r--.  1 ValkyRa ValkyRa   20 дек  4 16:33 err.log
-rw-rw-r--.  1 ValkyRa ValkyRa   20 дек  4 17:06 testfile.txt
-rwxrw-r--.  1 ValkyRa ValkyRa  213 дек  4 17:22 testscript2.sh
-rwxrw-r--.  1 ValkyRa ValkyRa  157 дек  4 17:21 testscript2.sh~
-rwxrw-r--.  1 ValkyRa ValkyRa  236 дек  4 17:00 #testscript.sh#
-rwxrw-r--.  1 ValkyRa ValkyRa  236 дек  4 16:57 testscript.sh
-rwxrw-r--.  1 ValkyRa ValkyRa  238 дек  4 16:54 testscript.sh~
```
Две верхние строчки — это переходы в другой каталог, что считается директорией.

## Задание 3(**)

### В данном задании говорится, что нужно изменить скрипт так, чтобы принимал любое количество записей в первом файле и обрабатывает их последовательно.

Тут в целом скрипт не меняется, за исключением добавления цикла for, в котором мы прогоняем все указанные в файле абсолютные пути до разных директорий
```bash 
for var in $FULLPATH
do
ls $var
NUM_FILES=$(find "$FULLPATH" -type f | wc -l )
NUM_FOLDERS=$(find "$FULLPATH" -type d | wc -l )

echo "Files: ${NUM_FILES}"
echo "Folders: ${NUM_FOLDERS}"
done
```
В текстовом файле указываем несколько путей, а именно:
```
/home/ValkyRa/test
/home/ValkyRa
``` 
Далее запускаем уже обновлённый скрипт
```bash
[ValkyRa@centos test]$ ~/test/testscript2.sh
err.log       testfile.txt~   testscript2.sh~  testscript.sh
testfile.txt  testscript2.sh  #testscript.sh#  testscript.sh~
Files: 8
Folders: 1
access.log  homework3  test2	 Документы    Музыка	     Шаблоны
bash.sh~    task2.txt  test.txt  Загрузки     Общедоступные
err.log     test       Видео	 Изображения  Рабочий стол
Files: 8337
Folders: 233
```

## Задание 4 (По желанию)

### В этом задании говорилось о том, что нужно познакомится самостоятельно с экранными мультиплексорами (screen или tmux), установить один из них себе и ознакомиться с функционалом. 

В этом пункте особо нечего расписывать. Попробовав оба варианта, а именно screen и tmux, остановился на первом варианте, поскольку бинды в нём удобнее и ближе чем в tmux, а в целом они как внешне, так и функционально мало чем отличаются друг от друга, выбор так сказать на вкус каждого.
Установка одного из экранных мультиплексоров делается командой ```sudo yum screen``` или ```tmux```.
Запуск производится либо командой ```screen```, либо ```tmux```, в зависимости от выбранного ранее мультиплексора, в каждом из них имеется дополнительные параметры запуска, но для обычного запуска достаточно только одной из этих команд.



