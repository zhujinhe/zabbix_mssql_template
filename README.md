# Zabbix template for Microsoft SQL Server monitoring via perfmon, services and ODBC.
This template monitors SQL Server databases status, jobs status, server performance.

##### Requirements:
* Zabbix 2.2
* unixODBC
* freeTDS

##### Installation:
* Before install template, create in Zabbix Value Mapping:
  
  Name of mapping: MS SQL Server database state
  
  |Value    |Mapped to
  |---------|----------
  |0        |ONLINE
  |1        |RESTORING
  |2        |RECOVERING
  |3        |RECOVERY PENDING
  |4        |SUSPECT
  |5        |EMERGENCY
  |6        |OFFLINE
  |7        |Database Does Not Exist on Server
  
* and Zabbix Regular expressions:
  ```
  Name of regexp: MSSQL Databases for discovery
  Expression: ^(master|model)$
  Expression type: Result is FALSE
  
  Name of regexp: MSSQL Jobs for discovery
  Expression: ^(test)$
  Expression type: Result is FALSE
  ```
  
* To use ODBC, install unixODBC and freeTDS.
  
  Sample config ODBC:

  note: should modified the path of *.so and tds version according to your environment.
  
  ```shell
  /etc/odbcinst.ini
  [FreeTDS]
    Description = FreeTDS Driver v0.91
    Driver = /usr/lib/i386-linux-gnu/odbc/libtdsodbc.so
    Setup = /usr/lib/i386-linux-gnu/odbc/libtdsS.so
    UsageCount = 1 
  
  /etc/odbc.ini
  [1c_base]
    Driver = FreeTDS
    ServerName = 1c_base
  
  /etc/freetds/freetds.conf
  [1c_base]
    host = 10.1.0.1
    port = 1433
	tds version = YOUR TDS VERSION
  ```
  
* And configure SQL Server to access data for ODBC user [zab]:
  ```sql
  CREATE LOGIN [zab] WITH PASSWORD=N'****', DEFAULT_DATABASE=[master], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
  
  GRANT VIEW SERVER STATE to [zab]
  GRANT VIEW ANY DEFINITION TO [zab]
  ```
  
  And create user mapping to read data from master and msdb.

* Additional resources

  [Freetds Official site](http://www.freetds.org/)

  [Choosing a TDS protocol version:](http://www.freetds.org/userguide/choosingtdsprotocol.htm#TAB.PROTOCOL.BY.PRODUCT)

  [FreeTDS Quick Start:](http://blog.mattwoodward.com/2012/08/freetds-quick-start.html)
  
