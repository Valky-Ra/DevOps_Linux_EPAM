## AWK
## Задание 1.
### В этом задании было необходимо используя команду awk и в Apache-логе с сайта  http://www.almhuette-raith.at/apache-log/access.log найти самый популярный браузер. 
Это можно выполнить, используя данный скрипт:
```bash
#!/bin/awk -f

BEGIN {
    FPAT = "([^ ]+)|(\"[^\"]+\")"

#{
#    print $10
#}
}
{ 
arr[$10]++ 
}
END {
    for( no in arr)
    { print arr[no], no}
}
```
В этом скрипте мы для начала удаляем лишние символы в колонках, а после прогоняем весь лог, используя 10-ую колонку, ибо именно эта колонка содержит данные о браузерах (в комментариях print $10 как раз и был результат исследования лога, на предмет наличия нужной колонки). После этого, используя цикл for мы выводим, сначала счётчик того, сколько раз у нас попалась определённая колонка в логе, вторая – сама колонка.
Запуск данного скрипта производился таким образом:

```[ValkyRa@centos ~]$ ~/homework3/testscript.awk ~/access.log | sort -nrk1 > test23.txt ```

Сначала указываем путь для самого скрипта, после этого путь до логов, далее указываем сортировку nrk1, т.е. сортировка по числам и по убыванию, и по первой колонки (т.е. наш счётчик).
Подождав определённое время, пока скрипт выполнит свою работу открываем наш файл с результатом test23.txt и видим следующую картину: (я приложу лишь часть файла)
```bash
340874 "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0)"
42615 "Mozilla/5.0 (Windows NT 6.1; Trident/7.0; rv:11.0) like Gecko"
17036 "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"
16086 "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0)"
9497 "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.85 Safari/537.36 OPR/32.0.1948.45"
7823 "Mozilla/5.0 (X11; Ubuntu; Linux i686; rv:28.0) Gecko/20100101 Firefox/28.0"
7564 "python-requests/2.25.1"
```
По результатам, мы видим, что больше всего используется IE (Internet Explorer) 7.0, найденный 340874 раза в логе

## Задание 2
### В данном задании необходимо показать количество запросов в месяц на IP-адрес 216.244.66.230 (к примеру, Sep 2016 – 100500 запросов, Oct 2016 – 0 запросов, и т.д.)
Создаём скрипт, указывая следующий код:
```bash
#!/bin/awk -f
{
if ($1 == "216.244.66.230")
    {
    split($4, DATE_ARRAY, ":"); 
    split(DATE_ARRAY[1], RESULT, "/"); 
    DATE=RESULT[2] RESULT[3];
    COUNT[DATE]++
    }
}
END {
for (KEY in COUNT)
    print KEY, COUNT[KEY]
}
```
В данном скрипте мы проверяем наличие в строки конкретного IP, после мы берём строку, в которой находится дата и время и разбиваем её на части, для получения месяца и года. После же, используя эти данные, прогоняем в массиве по всему логу и в конце, используем цикл for и выводим результат, отсортировав по наибольшему кол-ву запросов.
Запускаем скрипт таким образом:

```[ValkyRa@centos homework3]$ ~/homework3/testscript2.awk ~/access.log | sort -k2nr > test23.txt```

После открываем файл с результатами и убеждаемся в правильности результата:
```bash
Aug2021 108
Nov2021 106
Jan2021 43
Apr2021 34
May2021 27
Jul2021 23
Oct2021 23
Feb2021 14
Sep2021 7
Dec2021 3
Jun2021 3
Mar2021 2
Dec2020 1
```

## Задание 3
### В данном задании нужно показать сколько данных в байтах было предоставлено для каждого из уникальных IP-адресов
Создаем скрипт и вписываем в него следующее:
```bash
#!/bin/awk -f
{
sum[$1]+=$10
}
END{
    for (ip in sum)
	  print ip, sum[ip]
}
```
Здесь мы суммируем байты и после прогоняем через цикл for по всем уникальным IP адресам, и получаем суммы для всех уникальных адресов.
Запускаем скрипт и сохраняем результат в файл:

```[ValkyRa@centos homework3]$ ~/homework3/testscript3.awk ~/access.log | sort -k2nr > test3.txt```

Далее открываем файл и убеждаемся в результате. (Покажу лишь часть файла)

```bash
[ValkyRa@centos homework3]$ emacs test3.txt

51.210.183.78 36837061299
88.136.178.175 33999609146
90.39.134.147 32275895107
84.50.127.217 19422743215
35.231.163.195 18333376651
83.199.121.123 7513922252
62.39.220.35 7405409791
109.95.55.58 7008244792
80.110.106.159 5778617816
109.252.193.149 4973551527
```

## sed
## Задание 1
### В этом задании необходимо сменить все наименования браузеров в “lynx”
Делается это примерно так:

```[ValkyRa@centos ~]$ sed 's/"\ ".\+"\ /"\ "lynx"\ /g' ~/access.log > testsed1.txt```

По аналогии, как было на лекции, убираем ненужные нам символы, и заменяем наименования на lynx, и результат отправляем в отдельный файл.
Открываем частично файл и убеждаемся в правильности работы:
```bash
[ValkyRa@centos ~]$ head testsed1.txt 
13.66.139.0 - - [19/Dec/2020:13:57:26 +0100] "GET /index.php?option=com_phocagallery&view=category&id=1:almhuette-raith&Itemid=53 HTTP/1.1" 200 32653 "-" "lynx" "-"
157.48.153.185 - - [19/Dec/2020:14:08:06 +0100] "GET /apache-log/access.log HTTP/1.1" 200 233 "-" "lynx" "-"
157.48.153.185 - - [19/Dec/2020:14:08:08 +0100] "GET /favicon.ico HTTP/1.1" 404 217 "http://www.almhuette-raith.at/apache-log/access.log" "lynx" "-"
216.244.66.230 - - [19/Dec/2020:14:14:26 +0100] "GET /robots.txt HTTP/1.1" 200 304 "-" "lynx" "-"
54.36.148.92 - - [19/Dec/2020:14:16:44 +0100] "GET /index.php?option=com_phocagallery&view=category&id=2%3Awinterfotos&Itemid=53 HTTP/1.1" 200 30662 "-" "lynx" "-"
92.101.35.224 - - [19/Dec/2020:14:29:21 +0100] "GET /administrator/index.php HTTP/1.1" 200 4263 "" "lynx" "-"
```
## Задание 2
### В этом задании нужно замаскировать IP адреса и переписать файл с логами.
Тут мы в 4-ой колонке лога, где хранятся IP-адреса меняем все значения на другое, в нашем случае, заменим все IP адреса, на 127.0.0.1 и указываем файл с логами

```[ValkyRa@centos ~]$ sed -i -E 's/^([0-9]{1,3}.{0,1}){4}/127.0.0.1/g' ~/access.log```

Добавляем параметр –i для редактирования файла «на месте» и –E для исполнения в скрипте.
Открываем частично файл с логами, уже изменённый и убеждаемся в выполненном задании.
```bash
[ValkyRa@centos ~]$ head access.log 
127.0.0.1- - [19/Dec/2020:13:57:26 +0100] "GET /index.php?option=com_phocagallery&view=category&id=1:almhuette-raith&Itemid=53 HTTP/1.1" 200 32653 "-" "Mozilla/5.0 (compatible; bingbot/2.0; +http://www.bing.com/bingbot.htm)" "-"
127.0.0.1- - [19/Dec/2020:14:08:06 +0100] "GET /apache-log/access.log HTTP/1.1" 200 233 "-" "Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36" "-"
127.0.0.1- - [19/Dec/2020:14:08:08 +0100] "GET /favicon.ico HTTP/1.1" 404 217 "http://www.almhuette-raith.at/apache-log/access.log" "Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36" "-"
127.0.0.1- - [19/Dec/2020:14:14:26 +0100] "GET /robots.txt HTTP/1.1" 200 304 "-" "Mozilla/5.0 (compatible; DotBot/1.1; http://www.opensiteexplorer.org/dotbot, help@moz.com)" "-"
127.0.0.1- - [19/Dec/2020:14:16:44 +0100] "GET /index.php?option=com_phocagallery&view=category&id=2%3Awinterfotos&Itemid=53 HTTP/1.1" 200 30662 "-" "Mozilla/5.0 (compatible; AhrefsBot/7.0; +http://ahrefs.com/robot/)" "-"
127.0.0.1- - [19/Dec/2020:14:29:21 +0100] "GET /administrator/index.php HTTP/1.1" 200 4263 "" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 1.1.4322)" "-"
127.0.0.1- - [19/Dec/2020:14:58:59 +0100] "GET /apache-log/access.log HTTP/1.1" 200 1299 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.101 Safari/537.36" "-"
127.0.0.1- - [19/Dec/2020:14:58:59 +0100] "GET /favicon.ico HTTP/1.1" 404 217 "http://www.almhuette-raith.at/apache-log/access.log" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.101 Safari/537.36" "-"
127.0.0.1- - [19/Dec/2020:15:09:30 +0100] "GET /robots.txt HTTP/1.1" 200 304 "-" "Mozilla/5.0 (compatible; AhrefsBot/7.0; +http://ahrefs.com/robot/)" "-"
```
Extra задание сделать увы у меня не получилось.

