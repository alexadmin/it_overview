list users
```
use admin
db.system.users.find()
```

create admin
```
use admin
db.createUser({ user: "root" , pwd: "123", roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase", "readWriteAnyDatabase", "root" ]})
```

```
mongotop -h name.com -u admin --authenticationDatabase admin

mongodump --host=XXXX --username=XXXX --password=XXXX --authenticationDatabase=admin --archive
=history-17-Jan.archive --db=history
mongorestore --host=XXX --port=27017  --archive=history-17-Jan.archive
```
