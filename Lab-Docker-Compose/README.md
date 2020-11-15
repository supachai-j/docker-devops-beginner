# Docker Compose

## 1. Deploy App with Docker run:
```
docker run -d --name=redis redis
docker run -d --name=db postgres:9.4
docker run -d --name=vote -p 5000:80 --link redis:redis voting-app
docker run -d --name=result -p 5001:80 --link db:db result-app
docker run -d --name=worker --link db:db --link redis:redis worker 
```
## 2. Deploy App with Docker Compose:
vi docker-compose.yml
```
redis:
    image: redis
db:
    image: postgres:9.4
vote:
    image: voting-app           <-------------> ถ้าต้องการให้ทำการ docker build ก็ใช้ build: ./vote แทน
    ports: 
        - 5000:80
    links:
        - redis
result:
    image: result-app           <-------------> ถ้าต้องการให้ทำการ docker build ก็ใช้ build: ./result แทน
    ports: 
        - 5001:80
    links:
        - db
worker:
    image: worker                   <-------------> ถ้าต้องการให้ทำการ docker build ก็ใช้ build: ./worker แทน
    links:
        - redis
        - db
```

## 3. Docker Compose - Versions
- Version 1 
    จะใช้ default bridge network 
- Version 2 ได้กำหนดคำว่า services: เป็น directory ให้กับ container ต่างๆ 
    - โดยใน version 2 จะมีการสร้าง bridge network ให้อัตโนมัติสำหรับ กลุ่มของ container ที่เราสร้างให้เลย
    - ใน version 2 ไม่ต้องใช้ links: 
    - ใน version 2 สามารถกำหนด depends_on ให้กับ Container ที่ต้องมี Container อื่นๆ เริ่มต้นก่อนได้ เช่น webfront end ควรเริ่มต้นหลังจากที่ container database เริ่มทำงานเสร็จแล้ว
```
version: 2
services: 
    redis:
        image: redis
    db:
        image: postgres:9.4
    vote:
        image: voting-app 
        .........
```
- Version 3  รองรับ docker swarm
```
version: 3
services: 
    redis:
        image: redis
```

## 4. Create Networking on Docker Compose

Client  ------Front-end-Network -----> Voting-app (Python) <------> Back-end-Network <--------> Redis, db, worker
                                                        -----> Result-app (Nodejs)  <------> 

```
version: 2
services:
redis:
    image: redis
    networks:
        - back-end
db:
    image: postgres:9.4
    networks:
        - back-end
vote:
    image: voting-app 
    ports: 
        - 5000:80
    networks:
        - front-end
        - back-end
result:
    image: result-app
    ports: 
        - 5001:80
    networks:
        - front-end
        - back-end
worker:
    image: worker 
    networks:
        - back-end

networks:
    front-end:
    back-end:
```
## 5. Deploy example voting app with Docker run
Git Clone repo example voting app
```
git clone https://github.com/supachai-j/example-voting-app.git
```
5.1 Build and deploy redis container:
```
docker run -d --name=redis redis
```
5.2 Build and deploy vote container:
```
cd vote 
docker build . -t  voting-app
docker run -d -p 5000:80 --link redis:redis voting-app
```
5.3 Build and deploy postgress container:
```
docker run -d -e POSTGRES_PASSWORD=postgres --name=db postgres:9.4
```
5.4 Build and deploy worker container:
```
cd worker
docker build . -t worker-app
docker run --link redis:redis --link db:db worker-app
```
5.5 Build and deploy worker container:
```
cd result
docker build . -t result-app
docker run -d -p 5001:80 --link db:db result-app
```

## 6. Deploy example voting app with Docker Compose version 1
vi docker-compose.yml
``
docker-compose up
``
## Lab Testing 
```
docker run -d -e POSTGRES_PASSWORD=mysecretpassword --name=db postgres
docker run -d --name wordpress --link db:db -p 8085:80 wordpress
```
docker-compose.yml
```
version: '3.0'
services:
  db:
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    image: postgres
  wordpress:
    image: wordpress
    links:
    - db
    ports:
    - 8085:80
```
YAML and JSON PATH Training
https://kodekloud.com/courses/635226/lectures/11338837