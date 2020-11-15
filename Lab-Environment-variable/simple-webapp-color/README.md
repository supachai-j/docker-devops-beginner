# Lab Environment Variable
```
docker run -d -p 8081:8080 webapp-color
docker run -d -p 80802:8080 -e APP_COLOR=blue webapp-color
```