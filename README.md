<img width="1555" height="933" alt="image" src="https://github.com/user-attachments/assets/bdee4011-7ebd-441b-804c-3bc03928aba2" /># Kubernetes test app
<p>If you don't have kind cluster then visit my this github repo</p>
https://github.com/Aditya-das-4707-e/kubectl
<p>Build a docker file</p>

```
docker build -t <choose a name> .
```
<p>To see docker images</p>
<p>You will see there your docker image name what you choose</p>

```
docker images
```
<p>Next you login in docker</p>
<p>Now tag your docker image</p>

```
docker image tag <your_docker_image_name>:latest <your_user_name>/<your_docker_image_name>:latest
```
<p>Docker push now</p>

```
docker push <your_user_name>/<your_docker_image_name>
```
<p>Now configure deployment.yml file</p>
<p>Make sure you first go inside the deployment.yml file and in last you see in containers section image option. In this image section you add your own image id which you push in dockerhub recently. And also namespace section you enter your current build or previous namespace where you use this project</p>
<p>Now you create namespace for this project</p>
<p>Now create namespace with help of namespace.yml file</p>

```
kubectl apply -f namespace.yml
```
<p>Now create deployment with help of deployment.yml file</p>

```
kubectl apply -f deployment.yml
```
<p>Now create service with help of service.yml</p>

```
kubectl apply -f service.yml
```
<p>To viwe your container</p>

```
kubectl get pods -n <your_namespace>
```
<p>To expose and see on my browser</p>

```
kubectl port-forward service/<your_service_name>-service -n <your_namespace> 8000:8000
```
