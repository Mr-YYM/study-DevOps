# Zabbix 监听各种数据库

## Mysql


- /tmp/mysql-passwd.txt
  
  ```ini
  [client]
  user=root
  password=xxx
  ```
  
- 配置指标查询命令
  
  ```shell
  # vim /etc/zabbix/zabbix_agentd.d/userparameter_mysql.conf
  UserParameter=mysql.ping[*], mysqladmin --defaults-file=/var/lib/zabbix/.my.cnf ping
  UserParameter=mysql.get_status_variables[*], mysql --defaults-file=/var/lib/zabbix/.my.cnf -sNX -e "show global status"
  UserParameter=mysql.version[*], mysqladmin --defaults-file=/var/lib/zabbix/.my.cnf -s version
  UserParameter=mysql.db.discovery[*], mysql --defaults-file=/var/lib/zabbix/.my.cnf -sN -e "show databases"
  UserParameter=mysql.dbsize[*], mysql --defaults-file=/var/lib/zabbix/.my.cnf -sN -e "SELECT SUM(DATA_LENGTH + INDEX_LENGTH) FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA='$3'"
  UserParameter=mysql.replication.discovery[*], mysql --defaults-file=/var/lib/zabbix/.my.cnf -sNX -e "show slave status"
  UserParameter=mysql.slave_status[*], mysql --defaults-file=/var/lib/zabbix/.my.cnf -sNX -e "show slave status"
  UserParameter=mysql.connections, mysqladmin --defaults-extra-file=/tmp/mysql-passwd.txt status |awk '{print $4}'
  UserParameter=mysql.errors_max_connections, mysql --defaults-file=/var/lib/zabbix/.my.cnf -sNX -e "show global status like 'Connection_errors_max_connections'"
  UserParameter=mysql.errors_internal, mysql --defaults-file=/var/lib/zabbix/.my.cnf -sNX -e "show global status like 'Connection_errors_internal'"
  ```



## Redis



## MongoDB

