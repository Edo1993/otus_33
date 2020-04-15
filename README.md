# otus_33
# PostgreSQL cluster

Домашнее задание

Разворачиваем кластер Patroni

Цель: 
- Развернуть кластер PostgreSQL из трех нод. Создать тестовую базу 
- проверить статус репликации 
- Сделать switchover/failover 
- Поменять конфигурацию PostgreSQL + с параметром требующим перезагрузки 
- Настроить клиентские подключения через HAProxy

__________________________________________________________________________________________________________________________

Статьи, которые помогли:

[Статья, приложенная к занятию](https://habr.com/ru/post/322036/)

[Построение кластера PostgreSQL высокой доступности с использованием Patroni, etcd, HAProxy](https://habr.com/ru/post/482314/)

[Установка и настройка HAProxy на CentOS 7](https://www.dmosk.ru/miniinstruktions.php?mini=haproxy-centos7)

[НАСТРОЙКА БАЛАНСИРОВЩИКА HAPROXY - с террасофтом плотно работаю, поэтому пусть будет](https://academy.terrasoft.ru/documents/administration/7-15/nastroyka-balansirovshchika-haproxy)


```vagrant up``` поднимает 4 vm: *pg1-pg3* - бд postgre + *consul* - vm для consul и haproxy.

1) Проверим работу репликации:

Вход на *pg1*

```
vagrant ssh pg1
sudo su
```
Cоздадим тестовую базу:
```
psql -h 192.168.10.161
create database homework;
\l
\g
```
![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/331.png)

Проверим наличие базы на других серверах:
```
psql -h 192.168.10.162
\l
\g
psql -h 192.168.10.163
\l
\g
```
![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/332.png)

2) failover

```
patronictl list otus
systemctl stop patroni
patronictl list otus
```
![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/333.png)

3) switchover

```
systemctl start patroni
patronictl list otus
patronictl switchover --master pg2 --candidate pg1 otus
patronictl list otus
```
![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/334.png)

4) Поменять конфигурацию PostgreSQL + с параметром требующим перезагрузки

Отредактируем *max_connections*

```
patronictl edit-config otus
patronictl list otus
patronictl restart otus
```
![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/335.png)

5) Настроить клиентские подключения через HAProxy

```192.168.10.150:8080```

Логин/Пароль

```otus/homework```

![Img_alt](https://github.com/Edo1993/otus_33/blob/master/pics/336.png)
