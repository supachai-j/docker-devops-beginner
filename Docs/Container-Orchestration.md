# Container Orchestration - Docker Swarm & Kubernetes
ในการสร้าง Container ก่อนหน้านี้เราใช้ Docker run ในการสร้างหรือการกำหนดค่าต่างๆ ในกับ Container ที่ใช้ในการ Deploy Application ของเรา 
แต่ในแง่มุมของการ Maintanance หรือการบริหารจัดการ Container บนกรณีที่ต้องการทำ auto scale ในการเพิ่มหรือลดจำนวนของ Container ตาม policy ที่เราต้องไว้ หรือถ้ากรณีที่เกิด container cash แล้วต้องการให้ทำ auto restart ให้กับ Container เป็นต้น ดังนั้นเราต้องมีเครื่องมือในการบริหารจัดการ Container เพื่อให้ตรงตามความต้องการต่างๆ ดังนี้
- Docker Swarm
- Kubernetes
- MESOS

## Docker Swarm
คือ เครื่องมือที่ช่วยในการบริหารจัดการ Container หรือ Docker Host หลายๆ เครื่องใน Cluster เดียวกัน
โดยจะมี Swarm Manager เป็นตัวช่วยบริหาร Worker nodes ต่างๆ ใน Cluster
Swarm Manager node (Docker Host)
```
docker swarm init --advertise-add 192.168.1.12
```
Worker Nodes (Docker Host)
```
docker swarm join --token <token> 
```
### Docker Service

Create container on standalone docker host.
```
docker run my-web-server
```
Create Service (Group of Containers) on Swarm Cluster
```
docker service create --replicas=3 my-web-server
docker service create --replicas=3 -p 8080:80 my-web-server
docker service create --replicas=3 --network frontend my-web-server
```
## Kubernetes Cluster
kubernetes ก็เป็นเครื่องมือที่ได้รับความนิยมเป็นอย่างมากในการนำไปใช้บริหารจัดการ Docker Host และ Container โดยมี Feature เรื่อง POD AutoScales หรือ Cluster AutoScalers  และการทำ Rolling update / rollback ของ container services ได้อย่างมีประสิทธิภาพ, การทำ A/B deployment และรองรับ Networking Plugin หรือ Storage Plugin อีกมากมาย 
โดยที่ Cloud Provider (Amazon EKS, CGP, Azure) ก็ยังให้บริการ Native Kubernetes แบบเต็มรูปแบบ อีกต่างหาก
```
kubectl scale --replicas=2000 my-web-server
kubectl run --replicas=1000 my-web-server

kubectl rolling-update my-web-server --image=web-server:2
kubectl rolling-update my-web-server --rollback
```
Kubernetes จะมี Master Node ที่ค่อยเป็น Control Plan ให้กับ Nodes ต่างๆ ที่ทำหน้าที่ในการให้บริการ Container 
K8S Components:
    - etcd ----------------> key value store
    - api Server  ----------> Frontend for management kubernetes cluster
    - kubelet  -------------> Agent run on Nodes for communication between Master node and Nodes.
    - Container Runtime ---> 
    - Controller 
    - Scheduler
    - kubectl --------------> Command Client for management k8s cluster
```
kubectl run hello-minikube
kubectl run cluster-info
kubectl get nodes
kubectl run my-web-app --image=my-web-app -replicas=100
```