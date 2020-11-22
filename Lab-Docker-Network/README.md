# Docker Networking
ปกติ docker จะสร้าง network ไว้ 3 ตัว คือ 
- ชื่อ bridge เป็นชนิดหรือ driver แบบ bridge
    โดยปกติ Container ที่เราสร้างขึ้นมาจะใช้ network bridge เป็นค่าเริ่มต้น 
    ```
    docker run ubuntu
    ```

- ชื่อ none เป็นชนิดหรือ driver แบบ none
    ถ้าต้องการให้ Container เข้าไปใน network none ต้องใช้คำสั่ง
    ```
    docker run ubuntu --network=none
    ```
     none คือการที่เราต้องการให้ Container ไม่ถูกเชื่อมต่อกับ Network ใดๆ เลย
- ชื่อ host เป็นชนิดหรือ driver แบบ host
ถ้าต้องการให้ Container เข้าไปใน network none ต้องใช้คำสั่ง
    ```
    docker run ubuntu --network=host
    ```

### User-defined networks
web container1 ---172.17.0.2----|172.17.0.1 docker0|--------172.17.0.4---web container2
web container1 ---182.18.0.3----|182.18.0.1 docker0|--------182.18.0.2---web container2

```
docker network create --driver bridge --subnet  182.18.0.0/16 backend-network
docker network ls
```
### Inspect Docker Network
```
docker inspect backend-network
```
### Embedded DNS
source code refer server with name server
```
mysql.connect( mysql )
```
##### system diagram
web-container ---172.17.0.2<----->docker0<------>172.17.0.3----mysql-container

DNS Server (Embedded DNS) - 127.0.0.11

Host         |       IP-Address
web          |       172.17.0.2
mysql       |       172.17.0.3


### Example Docker Network
```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
4c90f5afb338        bridge              bridge              local
bcf86d8fbb40        host                host                local
da6b348da636        none                null                local
$ docker network create --driver bridge --subnet  182.18.0.0/16 backend-network
2f564ff850e9bab0530752f13448db63480ae07982e63e9983a441ae76fb4d43
$docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
2f564ff850e9        backend-network     bridge              local
4c90f5afb338        bridge              bridge              local
bcf86d8fbb40        host                host                local
da6b348da636        none                null                local
$docker inspect backend-network 
[
    {
        "Name": "backend-network",
        "Id": "2f564ff850e9bab0530752f13448db63480ae07982e63e9983a441ae76fb4d43",
        "Created": "2020-11-22T02:57:25.138110565Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "182.18.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]

$docker run -d -p 8080:80 --network=backend-network nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
852e50cd189d: Pull complete 
a29b129f4109: Pull complete 
b3ddf1fa5595: Pull complete 
c5df295936d3: Pull complete 
232bf38931fc: Pull complete 
Digest: sha256:c3a1592d2b6d275bef4087573355827b200b00ffc2d9849890a4f3aa2128c4ae
Status: Downloaded newer image for nginx:latest
50181267f44d5a423a0cc7096a02ea6899a45a71aaf4d586a57ebb2c06dd0b9d

$docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
50181267f44d        nginx               "/docker-entrypoint.…"   2 seconds ago       Up 3 seconds        0.0.0.0:8080->80/tcp   sharp_ishizaka
$docker-machine ls
NAME       ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER      ERRORS
docker01   *        virtualbox   Running   tcp://192.168.99.104:2376           v19.03.12   
docker02   -        virtualbox   Stopped                                       Unknown     
$docker inspect sharp_ishizaka 
.......
        "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "backend-network": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "50181267f44d"
                    ],
                    "NetworkID": "2f564ff850e9bab0530752f13448db63480ae07982e63e9983a441ae76fb4d43",
                    "EndpointID": "ea7449a04400fe95d97aaaf1ba92f4936db409600ba30aaeccb87a1e4622f1a2",
                    "Gateway": "182.18.0.1",
                    "IPAddress": "182.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:b6:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]   
```
##### Demo
```
$docker network create --driver bridge --subnet=182.18.0.1/24 --gateway=182.18.0.1 wp-mysql-network
$docker run -d -e MYSQL_ROOT_PASSWORD=db_pass123 --name mysql-db --network wp-mysql-network mysql:5.6
$docker run --network=wp-mysql-network -e DB_Host=mysql-db -e DB_Password=db_pass123 -p 38080:8080 --name webapp --link mysql-db:mysql-db -d kodekloud/simple-webapp-mysql
```