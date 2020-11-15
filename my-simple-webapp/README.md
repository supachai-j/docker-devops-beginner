# Simple Web Application
อันนี้เป็นการสร้าง Web Application แบบง่ายๆ โดยใช้ Python Flask

มีวิธีการดังนี้
- Install package all required dependencies
- install and Configure Web Server
- Start Web Server

## 1. Install package all required dependencies 
ติดตั้ง python และ package ที่ต้องใข้งาน
```
apt-get update
apt-get install  python -y
apt-get install python-pip -y
```
## 2. Install and Configure Web Server
ติดตั้ง Python Flask
```
pip install flask
```
## 3. Start Web Server
Start web server
```
FLASK_APP=app.py flask run --host=0.0.0.0
```
## 4. Testing
Open a browser and go to URL
``` 
http://<IP>:5000                                                    ===> Welcom
http://<IP>:5000/how%20are%20you                ===> I am good, how about you?
```

## Docker Build 

Build docker images
```
docker build .
docker build . -t my-simple-webapp:latest
```

Run Docker with new images
```
docker run my-simple-webapp
```
Testing access to web application again.

## Push docker images to Docker Hub

Login Account Docker Hub:
```
docker login
```

Rebuild change name image for push to docker hub
```
docker build . -t jaturaprom/my-simple-webapp
```