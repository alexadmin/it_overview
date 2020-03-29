vim /var/lib/pgsql/9.3/data/pg_hba.conf
```
local   all             all                                     md5
host    all             all             127.0.0.1/32            md5
```

```
su - postgres
psql
CREATE ROLE jira WITH PASSWORD 'iumoijoi8Y&Ygf' LOGIN;
CREATE DATABASE jiradb WITH OWNER jira;
```
