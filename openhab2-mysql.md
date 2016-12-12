# openhab2 mysql Persistence

## install software on RPi

* mysql-server

## setting up the mysql DB

> sudo mysql -u root -p  
> CREATE DATABASE openhab2;  
> CREATE USER openhab@localhost IDENTIFIED BY YOURPASSWORD;  
> GRANT ALL PRIVILEGES ON openhab2.* TO openhab@localhost;  
> flush privileges;  

## added extensions via Paper UI

* JDBC Persistence MySQL
* MySQL Persistence

## mysql usernames:passwords

* root:root
* openhab:openhab

## databases

* openhab2

## Service Configuration Files

### /etc/openhab2/services/mysql.cfg && jdbc.cfg

```
url=jdbc:mysql://127.0.0.1:3306/openhab2

user=openhab

password=openhab
```

## Persistence
### /etc/openhab2/persistence/mysql.persist

```
Strategies {
   everyMinute : "0 * * * * ?"
}

Items {
gTemperature* : strategy = everyMinute, restoreOnStartup
gSensor* : strategy = everyMinute, restoreOnStartup
gSwitch* : strategy = everyMinute, restoreOnStartup
}
```

## Questions
* did openhab create the appropriate tables?
> mysql> use openhab2  
> mysql> _show tables;  

```
+--------------------+
| Tables_in_openhab2 |
+--------------------+
| Item1              |
| Item10             |
| Item11             |
| Item12             |
| Item13             |
| Item14             |
| Item15             |
| Item16             |
| Item17             |
| Item18             |
| Item19             |
| Item2              |
| Item20             |
| Item21             |
| Item3              |
| Item4              |
| Item5              |
| Item6              |
| Item7              |
| Item8              |
| Item9              |
| Items              |
| items              |
+--------------------+
23 rows in set (0.00 sec)
```

> mysql> select * from Item1;  

```
+---------------------+-------+
| Time                | Value |
+---------------------+-------+
| 2016-12-12 21:45:00 | 21.71 |
| 2016-12-12 21:46:00 | 21.71 |
| 2016-12-12 21:47:00 | 21.71 |
| 2016-12-12 21:48:00 | 21.71 |
| 2016-12-12 21:49:00 | 21.71 |
| 2016-12-12 21:50:00 | 21.53 |
| 2016-12-12 21:51:00 | 21.53 |
| 2016-12-12 21:52:00 | 21.53 |
| 2016-12-12 21:53:00 | 21.53 |
| 2016-12-12 21:54:00 | 21.45 |
| 2016-12-12 21:55:00 | 21.45 |
| 2016-12-12 21:56:00 | 21.45 |
| 2016-12-12 21:57:00 | 21.45 |
+---------------------+-------+
13 rows in set (0.00 sec)
```
