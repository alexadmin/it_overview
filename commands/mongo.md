list users
```
use admin
db.system.users.find()
```

create admin
```
use admin
db.createUser({ user: "root" , pwd: "mgt-rc_v3", roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase", "readWriteAnyDatabase"]})
```
