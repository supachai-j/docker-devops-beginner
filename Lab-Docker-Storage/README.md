# Lab Docker Storage

## Volume in Docker
directory on docker host
/var/lib/docker
    - volumes
        - data_volume1

```
docker volume create data_volume
docker run -d --name=app_mysql -v data_volume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -p 3306:3306 mysql
docker exec -it app_mysql mysql -uroot -p
mysql>create database stamjub;
mysql>show databases;
mysql>quit
docker rm -f app_mysql
docker volume ls
docker run -d --name=app_mysql -v data_volume:/var/lib/mysql -p 3306:3306 mysql
docker exec -it app_mysql mysql -uroot -p
mysql>show databases;
docker rm -f app_mysql
docker volume rm data_volume
```

mount directory on Docker Host
```
mkdir /data/mysql
docker run -v /data/mysql:/var/lib/mysql -d --name=app_mysql -e MYSQL_ROOT_PASSWORD=password -p 3306:3306 mysql

docker exec -it app_mysql mysql -uroot -p
mysql>create database stamjub;
mysql>show databases;
mysql>quit

docker rm -f app_mysql

docker run -d --name=app_mysql -v /data/mysql:/var/lib/mysql -p 3306:3306 mysql
docker exec -it app_mysql mysql -uroot -p
mysql>show databases;
docker rm -f app_mysql
rm -f /data/mysql

```

### Storage drivers
- AUFS
- ZFS
- BTRFS
- Device Mapper
- Overlay
- Overlay2

### Demo Storage docker 
```
ls -l /var/lib/docker
docker pull jaturaprom/my-simple-webapp
docker image history jaturaprom/my-simple-webapp
docker system df
docker system df -v
```