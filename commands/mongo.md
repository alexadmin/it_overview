list users
```
use admin
db.system.users.find()
```

create admin
```
use admin
db.createUser({ user: "root" , pwd: "123", roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase", "readWriteAnyDatabase"]})
```

```
mongotop -h name.com -u admin --authenticationDatabase admin
```
