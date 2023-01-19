
```
# RUN IN DETACHED STATE
docker run --name container_name -p 40080:80 -p 33061:3306 -d image_name

# RUN IN INTERACTIVE STATE
docker run -t -i --name container_name centos /bin/bash

# RUN IN PRODUCTION
docker run --restart=always --name container_name -p 127.0.0.1:40080:80/tcp -d image_name

# STOP and REMOVE
docker stop container_name; docker rm container_name

# CONNECT
docker exec -i -t container_name bash

# EXEC IN CONTAINER
docker exec mycontainer ls -la /root

# SAVE CONTAINER AS IMAGE
docker commit container_name image_name

#SHOW RUNNING CONTAINERS
docker ps -a

# RUN LOCAL REPO
docker run -d -p 5000:5000 --restart=always --name container_name registry:2

# SAVE, LOAD
docker save -o <save image to path> <image name>
docker load -i <path to image tar file>

# PUSH, PULL TO LOCAL REPO
docker tag nginx localhost:5000/nginx
docker push localhost:5000/nginx
docker pull docker.somehost.com:5000/nginx

# BUILD
docker build -t image_name -f Dockerfile .

# IMAGE DETAILS
docker inspect registry

# BLOCK PORT
iptables -I DOCKER 1 -p tcp ! -s 1.1.1.1 --dport 80 -j DROP 

# DELETE ALL IMAGES, EXCEPT centos
docker images | grep -v "centos\|REPOSITORY" | awk 'system("docker rmi "$3)'

# DELETE ALL CONTAINERS
docker ps -aq | awk 'system("docker rm -f  "$1)'

# DELETE IMAGES IN REGISTRY
cd /data/docker/containers/registry/var/lib/registry/docker/registry/v2/repositories
rm -rf image_name

# pg_basebackup
docker run --name some-postgres -e POSTGRES_PASSWORD=password -d postgres:10
docker exec -i -t some-postgres pg_basebackup -D my_pg_basebackup -F p -X stream -c fast -P -v -h 192.168.99.122 -U postgres

# DELETE IMAGES WITH <none> TAG
docker images | grep '<none>' | awk 'system("docker rmi -f  "$3)'

# DELETE CONTAINERS WITH "Exited" STATUS
docker ps -a | grep Exited | awk 'system("docker rm -f  "$1)'
```

# Delete all
```
# Delete all containers using the following command:
docker rm -f $(docker ps -a -q)
# Delete all volumes using the following command:
docker volume rm $(docker volume ls -q)
```

# Databases
```
docker run --name mysql57 -p 3357:3306 -e MYSQL_ROOT_PASSWORD=qwerty -d mysql:5.7.30
```

# install pkgs
```
apt update && apt upgrade && apt install iputils-ping
```
