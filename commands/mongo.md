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

create user
```
db.createUser(
  {
    user: "myuser",
    pwd: "123123",
    roles: [
      { role: 'dbAdmin', db: 'history' },
      { role: 'readWrite', db: 'history' }
    ]
  }
)
```

run script
```
mongosh "mongodb://host/admin" --username=root --password=passwd < mongo.js
```

```
mongotop -h name.com -u admin --authenticationDatabase admin

mongodump --host=XXXX --username=XXXX --password=XXXX --authenticationDatabase=admin --archive
=history-17-Jan.archive --db=history
mongorestore --host=XXX --port=27017  --archive=history-17-Jan.archive
```

reinitiate db:
- start without rs
- use local; db.dropDatabase()
- start with rs
- rs.initiate()
