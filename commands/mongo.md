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

```
reinitiate db:
- start without rs
- use local; db.dropDatabase()
- start with rs
- rs.initiate()

# generate key file
openssl rand  -base64  756
```

rs in docker
- install without auth
- create admin
- start like rs
- rs.initiate()
```
  mongodb_main:
    hostname: myhost
    container_name: mongodb
    image:  mongo:5.0.1
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: XXXXXXX
    volumes:
      - /etc/mongod.conf:/etc/mongod.conf
      - /opt/docker_data/mongodb/data:/data/db
      - ./rskey:/data/replicaset.key.devel
    ports:
      - 27017:27017
#    entrypoint: mongod --dbpath /data/db --config /etc/mongod.conf
    entrypoint:
      - bash
      - -c
      - |
        cp /data/replicaset.key.devel /data/replicaset.key
        chmod 400 /data/replicaset.key
        chown 999:999 /data/replicaset.key
        exec docker-entrypoint.sh $$@
    command: "mongod --dbpath /data/db --config /etc/mongod.conf --keyFile /data/replicaset.key"
    restart: unless-stopped
```
