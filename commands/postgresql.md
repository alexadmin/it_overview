```
# SHOW DATA IN TABLE
\x

# SHOW DATABASES
\l

# SHOW TABLES
\dt

# SELECT
select * from company;

# INSERT
INSERT INTO company (id, name, age, address, salary) VALUES (1, 'alex', '20', 'cap st.', '10000');

# CREATE DB
create database alex01;

# USE DB
\c alex001

# DUMP CONTAINER
docker exec -t -u postgres container_name pg_dumpall -c | gzip > /tmp/dump.sql

# SHOW REPLICA MASTER
-bash-4.2$ psql -c "select application_name, state, sync_priority, sync_state from pg_stat_replication;"
 application_name |   state   | sync_priority | sync_state
------------------+-----------+---------------+------------
 slave01          | streaming |             1 | sync
(1 row)

# CHECK REPLICA
ps wuax | grep receiver

# SHOW ALL SESSIONS
select count(*) from pg_stat_activity;

# SHOW IDLE CONNECTIONS
select COUNT(*) from pg_stat_activity WHERE state = 'idle';

# SHOW DATABASE SIZE
SELECT pg_size_pretty( pg_database_size('DB_NAME') );
```
9.3
```
yum -y install https://download.postgresql.org/pub/repos/yum/9.3/redhat/rhel-7-x86_64/pgdg-centos93-9.3-3.noarch.rpm
yum -y install postgresql93-server
/usr/pgsql-9.3/bin/postgresql93-setup initdb
systemctl enable postgresql-9.3
systemctl start postgresql-9.3
```

```
# CREATE READ ONLY USER
\c DB_NAME
CREATE ROLE readaccess;

-- Grant access to existing tables
GRANT USAGE ON SCHEMA public TO readaccess;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readaccess;
#GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO readaccess;

-- Grant access to future tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO readaccess;

-- Create a final user with password
CREATE USER tomek WITH PASSWORD 'secret';
GRANT readaccess TO tomek;
```
