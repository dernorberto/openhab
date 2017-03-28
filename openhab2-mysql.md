# openhab2 mysql Persistence

## notes

* restart openhab2 once all the mysql settings, config & persistence files, databases and user auth are setup
* openhab2 should create all the required tables inside the database if all is setup properly
* if you're not able to add the persistence extensions in Paper UI and the error is `Unable to resolve root: missing requirement [root]`, then:
  * stop openhab2  
  * clear the contents of the folder `/var/lib/openhab2/cache/`
  * start openhab2
  * install extension again (keep an eye on the logs to confirm proper installation)
```
==> /var/log/openhab2/events.log <==
2016-12-12 20:39:25.176 [ExtensionEvent            ] - Extension 'persistence-mysql' has been installed.
```

## install software on RPi

* mysql-server
  * as usual, the mysql root password is defined when completing the installation

## setting up the mysql DB

> sudo mysql -u root -p  
> CREATE DATABASE openhab2;  
> CREATE USER openhab@localhost IDENTIFIED BY YOURPASSWORD;  
> GRANT ALL PRIVILEGES ON openhab2.* TO openhab@localhost;  
> flush privileges;  

## add extensions via Paper UI

* JDBC Persistence MySQL
* MySQL Persistence

## documenting mysql usernames:passwords

* root:root
* openhab:openhab

## documenting mysql databases

* openhab2

## Service Configuration Files

### /etc/openhab2/services/mysql.cfg && jdbc.cfg

* Note: these files are created automatically after installing the extensions

```
url=jdbc:mysql://127.0.0.1:3306/openhab2

user=openhab

password=openhab
```

## Persistence
### /etc/openhab2/persistence/mysql.persist

* Note: this file has to be created manually

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

## Charts

ref: http://docs.openhab.org/configuration/sitemaps.html#element-type-chart

* in case there is more than one persistence defined, the one storing charts has to be defined in the sitemap
  * _service sets the persistence service to use. If no service is set, openHAB will use the first queryable persistence service it finds. Therefore, for an installation with only a single persistence service, this is not required._  

```
Chart item=Switch_1_watts service="jdbc" period=W refresh=3600
Chart item=Thermo_KD_CurrentTemp service="jdbc" period=W refresh=3600

```
     
## Questions
* did openhab create the appropriate tables and are they getting populated?

> mysql> use openhab2  
> mysql> show tables;  

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

> mysql> select * from Items;

```
+--------+-------------------------+
| ItemId | ItemName                |
+--------+-------------------------+
|      1 | Thermo_LI_CurrentTemp   |
|      2 | Thermo_BE_CurrentTemp   |
|      3 | Sensor_LO_Temperature   |
|      4 | Thermo_OF_CurrentTemp   |
|      5 | Sensor_HA_Temperature   |
|      6 | Thermo_EL_CurrentTemp   |
|      7 | Thermo_LO_M_CurrentTemp |
|      8 | Sensor_HA_State         |
|      9 | Sensor_HA_Luminance     |
|     10 | Sensor_LO_Humidity      |
|     11 | Sensor_LO_Luminance     |
|     12 | Sensor_LO_Motion        |
|     13 | Switch_KI_switch        |
|     14 | Switch_2_kwh            |
|     15 | Switch_1_watts          |
|     16 | Switch_2_switch         |
|     17 | Switch_2_watts          |
|     18 | Switch_KI_switch1       |
|     19 | Switch_1_kwh            |
|     20 | Switch_1_switch         |
|     21 | Switch_KI_switch2       |
+--------+-------------------------+
21 rows in set (0.00 sec)
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
