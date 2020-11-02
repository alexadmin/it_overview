```
# CREATE TABLE INNODB
CREATE TABLE test.`users` (
         `uid` INT(10) NOT NULL AUTO_INCREMENT,
          created TIMESTAMP DEFAULT NOW(),
         `user` VARCHAR(6) NULL DEFAULT NULL,
         `pass` VARCHAR(6) NULL DEFAULT NULL,
         `email` VARCHAR(6) NULL DEFAULT NULL,
          PRIMARY KEY (`uid`)
          ) ENGINE = INNODB;

# CREATE TABLE MYISAM
CREATE TABLE test.`users` (
         `uid` INT(10) NOT NULL AUTO_INCREMENT,
          created TIMESTAMP DEFAULT NOW(),
         `user` VARCHAR(6) NULL DEFAULT NULL,
         `pass` VARCHAR(6) NULL DEFAULT NULL,
         `email` VARCHAR(6) NULL DEFAULT NULL,
          PRIMARY KEY (`uid`)
          ) ENGINE = MYISAM;

# INSERT
INSERT INTO MyGuests (firstname, lastname, email) VALUES ('John', 'Doe', 'john@example.com');

# CALLS DURING LAST SECOND
select count(*) from test.userinfo4 where created >= DATE_SUB(NOW(),INTERVAL 5 SECOND);

# RESET AUTOINCREMENT
ALTER TABLE tablename AUTO_INCREMENT = 1
[/bash]


[bash]
# RESTORE ONE DATABASE FROM ALL DATABASES DUMP
mysql -D mydb -o < mydump.sql

# RESTORE SPEED LIMIT - 1 MEGABYTE PER SECOND
pv -L 1m MyDB.sql | mysql -u root -p'XXX' MyDB

# RESTORE
mysql -u root -p[root_password] [database_name] < dump.sql

# RESTORE ONE DATABASE FROM ALL DATABASE BACKUP
mysql -u root --one-database mydb -p < all-databases.sql

# RESTORE ONE TABLE
sed -n -e '/DROP TABLE.*`table_name`/,/UNLOCK TABLES/p' full_dump > table_name.sql
[/bash]

<strong>mysqldump</strong>

[bash]
# MYSQLDUMP SPEED LIMIT - 3 KILOBYTES PER SECOND
mysqldump -u root -p'XXX' --all-databases | pv -L 3000 > /var/backups/MyDB.sql

# COPY DATABASE
mysqldump db_name | mysql new_db_name

# BACKUP
mysqldump -u root -p[root_password] [database_name] > dump.sql
[/bash]

<strong>mysql syntax</strong>

[bash]
# ADD LOCAL ADMIN
CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'newuser'@'localhost';
FLUSH PRIVILEGES;

# ADD REMOTE ADMIN
CREATE USER 'newuser'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'newuser'@'%';

# READ ONLY USER
GRANT SELECT, SHOW VIEW, PROCESS, REPLICATION CLIENT ON *.* TO 'prometheus'@'%';

# Show table engine
SHOW TABLE STATUS WHERE Name = 'xxx'

# DELETE SLAVE
MariaDB [(none)]> slave stop;
MariaDB [(none)]> reset slave all;
systemctl restart mariadb
[/bash]

<strong>mysql etc</strong>

[bash]
# SHOW PROCESSES
mysqladmin -uroot processlist

# KILL PROCESS
mysqladmin -uroot kill 22

# BASIC MONITORING
mysqladmin -uroot status
mysqladmin -uroot extended-status

# SHOW DB ENGINE
mysqlshow -u root -p -i dbname

# CONVERT GZIP TO PLAIN
zcat dump.sql.gz > dump.sql
[/bash]

<strong>INNODB file per table</strong>

[bash]
mysqldump -u root -p --all-databases > /all.sql
systemctl stop mariadb
Delete ibdata* and ib_logfile*
systemctl stop mariadb
mysql -u root -p < /all.sql
vim my.cnf

[mysqld]
innodb_file_per_table
[/bash]



# LOG ALL QUERIES
set @@global.general_log = ON;

#
set max_execution_time = 2000;

expire_logs_days - controls binary logging, not relay logging.
relay logs are purged when the slave has finished applying the data from them
[/bash]



# STRESS TEST
MYSQL_HOSTNAME='localhost' && for table in $(mysql -h $MYSQL_HOSTNAME mmarket_mmarket_test -e "show tables;" -B); do mysql -h $MYSQL_HOSTNAME mmarket_mmarket_test -e "select * from "$table";"; done
```
