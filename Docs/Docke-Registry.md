# Docker Registry
## Registry Local
Image: nginx/nginx  
                |       |-----> Image/Repository 
                |----->User/Account

## Registry Remote from Docker Hub
Image: docker.io/nginx/nginx
                |               |           |-----> Image/Repository
                |               |----->User/Account
                |----->Registry

Example is gcr.io/kubernetes-e2e-test-images/dnsutils

## Private Registry
```
docker login private-registry.io

docker run private-registry.io/apps/internal-app
```

## How to deploy private registry
```
docker run -d -p 5000:5000 --name registry registry:2
docker image tag my-image localhost:5000/my-image
docker push localhost:5000/my-image
docker pull localhost:5000/my-image
docker pull 192.168.56.100:5000/my-image
```
```
docker image tag web-color 192.168.99.104:5000/my-image
docker push 192.168.99.104:5000/my-image
```


