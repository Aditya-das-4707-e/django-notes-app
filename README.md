<img width="1555" height="933" alt="image" src="https://github.com/user-attachments/assets/bdee4011-7ebd-441b-804c-3bc03928aba2" />

# Kubernetes Test App

A simple guide to deploy your application on Kubernetes using Docker and Kind cluster.

---

## Prerequisites

If you don't have a Kind cluster set up yet, visit my GitHub repository for instructions:  
👉 [https://github.com/Aditya-das-4707-e/kubectl](https://github.com/Aditya-das-4707-e/kubectl)

---

## Step 1: Build Your Docker Image

**What it does:** Creates a packaged version of your application that can run anywhere.

**Why we use it:** Think of it like packing your app into a container so it can travel to different computers and work the same way everywhere.

```bash
docker build -t <choose-a-name> .
```

**Example:**
```bash
docker build -t my-test-app .
```

---

## Step 2: View Your Docker Images

**What it does:** Shows you all the Docker images saved on your computer.

**Why we use it:** To confirm that your image was created successfully and check its name.

```bash
docker images
```

You'll see your newly created image in the list!

---

## Step 3: Login to Docker Hub

**What it does:** Connects your computer to your Docker Hub account.

**Why we use it:** So you can upload (push) your images to Docker Hub, where Kubernetes can download them later.

```bash
docker login
```

Enter your Docker Hub username and password when prompted.

---

## Step 4: Tag Your Docker Image

**What it does:** Gives your image a proper name that includes your Docker Hub username.

**Why we use it:** Docker Hub needs to know who owns the image. It's like putting your name on your lunchbox before taking it to school!

```bash
docker image tag <your-docker-image-name>:latest <your-username>/<your-docker-image-name>:latest
```

**Example:**
```bash
docker image tag my-test-app:latest johndoe/my-test-app:latest
```

---

## Step 5: Push Your Image to Docker Hub

**What it does:** Uploads your Docker image to Docker Hub (an online storage for Docker images).

**Why we use it:** Kubernetes will download your image from Docker Hub when it needs to run your app. It's like uploading a file to Google Drive so you can access it from anywhere.

```bash
docker push <your-username>/<your-docker-image-name>
```

**Example:**
```bash
docker push johndoe/my-test-app
```

---

## Step 6: Configure Your deployment.yml File

**Important:** Before applying the deployment, open your `deployment.yml` file and:

1. **Update the image section** (in the containers part) with your Docker Hub image:
   ```yaml
   image: <your-username>/<your-docker-image-name>:latest
   ```

2. **Update the namespace** to match the namespace you'll create in the next step.

**Example:**
```yaml
containers:
  - name: my-app
    image: johndoe/my-test-app:latest
```

---

## Step 7: Create a Namespace

**What it does:** Creates a separate workspace in Kubernetes for your project.

**Why we use it:** Namespaces are like folders on your computer. They keep different projects organized and separated from each other.

**Use case:** If you have multiple apps, each can have its own namespace to avoid confusion.

```bash
kubectl apply -f namespace.yml
```

---

## Step 8: Create the Deployment

**What it does:** Tells Kubernetes to run your application using the Docker image you uploaded.

**Why we use it:** A deployment makes sure your app is always running. If it crashes, Kubernetes will automatically restart it.

**Use case:** This is the heart of your application. It defines how many copies (replicas) of your app should run and what image to use.

```bash
kubectl apply -f deployment.yml
```

---

## Step 9: Create the Service

**What it does:** Creates a network door that lets people access your application.

**Why we use it:** Your app is running inside Kubernetes, but without a service, no one can reach it. The service acts like a receptionist directing traffic to your app.

**Use case:** Services enable communication between different parts of your app or allow external users to access it.

```bash
kubectl apply -f service.yml
```

---

## Step 10: View Your Running Containers (Pods)

**What it does:** Shows you all the containers (called "pods" in Kubernetes) running in your namespace.

**Why we use it:** To check if your app started successfully and see its status (running, pending, or error).

```bash
kubectl get pods -n <your-namespace>
```

**Example:**
```bash
kubectl get pods -n my-test-namespace
```

You should see something like:
```
NAME                        READY   STATUS    RESTARTS   AGE
my-app-12345-abcde          1/1     Running   0          30s
```

---

## Step 11: Access Your App in the Browser

**What it does:** Creates a tunnel from your computer to your app running in Kubernetes.

**Why we use it:** Since your app is running inside Kubernetes, you need port-forwarding to access it on your browser at `localhost:8000`.

**Use case:** Perfect for testing your app locally before making it available to everyone on the internet.

```bash
kubectl port-forward service/<your-service-name>-service -n <your-namespace> 8000:8000
```

**Example:**
```bash
kubectl port-forward service/my-test-app-service -n my-test-namespace 8000:8000
```

Now open your browser and visit:
```
http://localhost:8000
```

🎉 **Your app is now running!**

---

# Kubernetes Ingress Setup Guide

## Overview
This guide helps you set up an Ingress controller in your Kind (Kubernetes in Docker) cluster. An Ingress acts like a traffic director for your applications, routing requests to the correct services based on the URL path.

**Think of it like this:** Imagine a mall with many shops. The Ingress is like the main entrance directory that tells visitors which corridor to take to reach different stores.

---

## Prerequisites
- A running Kind cluster
- kubectl installed and configured
- Basic understanding of Kubernetes concepts

---

## Step 1: Deploy the Ingress NGINX Controller

### What is this?
The Ingress NGINX Controller is a special component that manages incoming traffic to your cluster. It acts as a reverse proxy and load balancer.

### Why do we need it?
Without an Ingress controller, each service would need its own external access point. The Ingress controller allows multiple services to share a single entry point and route traffic based on rules.

### Command
```bash
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```

### What does this command do?
- Downloads the Ingress NGINX configuration from the official Kind documentation
- Creates a new namespace called `ingress-nginx`
- Deploys all necessary components (service accounts, roles, controllers, etc.)
- Sets up the traffic management system for your cluster

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
```

---

## Step 2: Verify the Ingress Namespace

### Command
```bash
kubectl get ns
```

### What does this command do?
Lists all namespaces in your Kubernetes cluster. A namespace is like a virtual cluster within your cluster, helping you organize resources.

### Why check this?
To confirm that the `ingress-nginx` namespace was created successfully.

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get ns
NAME                 STATUS   AGE
default              Active   5d
ingress-nginx        Active   4m46s
kube-node-lease      Active   5d
kube-public          Active   5d
kube-system          Active   5d
local-path-storage   Active   5d
nginx                Active   3d14h
```

---

## Step 3: Check Ingress Pods

### Command
```bash
kubectl get pods -n ingress-nginx
```

### What does this command do?
- `kubectl get pods`: Lists all running pods
- `-n ingress-nginx`: Specifies to look only in the `ingress-nginx` namespace

### Why check this?
To verify that the Ingress controller pods are running properly. Pods are the smallest units in Kubernetes that run your containers.

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-gn86m        0/1     Completed   0          8m23s
ingress-nginx-admission-patch-fcnmt         0/1     Completed   2          8m23s
ingress-nginx-controller-66fdf84d85-6jpdl   1/1     Running     0          8m23s
```

### Understanding the Status
- **STATUS: Completed** - These are jobs that run once and finish (like setup tasks)
- **STATUS: Running** - These are continuously running pods (like the main controller)

---

## Step 4: Check Ingress Services

### Command
```bash
kubectl get service -n ingress-nginx
```

### What does this command do?
Lists all services in the `ingress-nginx` namespace. Services provide stable network endpoints for pods.

### Why check this?
To see the Ingress controller service that we'll use to expose our applications to the outside world.

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster$ kubectl get service -n ingress-nginx
NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.179.123   <pending>     80:32686/TCP,443:30343/TCP   14m
ingress-nginx-controller-admission   ClusterIP      10.96.220.81    <none>        443/TCP                      14m
```

### Understanding the Output
- **LoadBalancer**: This service type exposes your application externally
- **Port 80**: HTTP traffic
- **Port 443**: HTTPS traffic (secure)
- **EXTERNAL-IP: <pending>**: Normal for Kind clusters running locally

---

## Step 5: Create Your Ingress Resource

### What is an Ingress Resource?
An Ingress resource defines the routing rules for your applications. It tells the Ingress controller which service to send traffic to based on the URL path.

### Important Setup Note
**All services you want to route must be in the same namespace!** This is crucial for the Ingress to find and route to your services.

### Create the Ingress Configuration File

Create a file named `ingress.yml` in your project folder:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: <choose_your_name>
  # Example: name: nginx-notes-ingress
  namespace: <enter_common_namespace_for_both_services>
  # Example: namespace: nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    # This annotation rewrites the URL path when routing to services
spec:
  rules: 
  - http: 
      paths: 
      - pathType: Prefix 
        path: <enter_your_path>
        # Example: path: /nginx
        backend: 
          service:
            name: nginx-service
            port:
              number: 80
      - pathType: Prefix
        path: <enter_your_path>
        # Example: path: /
        backend: 
          service:
            name: notes-app-service
            port:
              number: 8000
```

### Configuration Explanation

**metadata section:**
- `name`: Give your Ingress a descriptive name
- `namespace`: Must match the namespace where your services are deployed

**annotations:**
- `nginx.ingress.kubernetes.io/rewrite-target: /`: Removes the path prefix before forwarding to the service (e.g., `/nginx` becomes `/`)

**rules section:**
- `pathType: Prefix`: Matches URLs that start with the specified path
- `path`: The URL path that triggers this rule (e.g., `/nginx` or `/`)
- `backend.service.name`: The name of your Kubernetes service
- `backend.service.port.number`: The port your service listens on

### Use Cases
1. **Multiple applications on one domain**: Route `/app1` to Service A and `/app2` to Service B
2. **Microservices architecture**: Direct traffic to different services based on API endpoints
3. **Blue-green deployments**: Switch traffic between different versions of your app

---

## Step 6: Apply the Ingress Configuration

### Command
```bash
kubectl apply -f ingress.yml
```

### What does this command do?
- Reads your `ingress.yml` file
- Creates or updates the Ingress resource in your cluster
- Configures the routing rules you defined

---

## Step 7: Verify the Ingress

### Command
```bash
kubectl get ingress -n nginx
```

### What does this command do?
Lists all Ingress resources in the specified namespace, showing their status and configuration.

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ kubectl get ingress -n nginx
NAME                  CLASS    HOSTS   ADDRESS     PORTS   AGE
nginx-notes-ingress   <none>   *       localhost   80      71s
```

### Understanding the Output
- **ADDRESS: localhost**: Your Ingress is accessible via localhost
- **PORTS: 80**: HTTP traffic is enabled
- **HOSTS: ***: Accepts requests from any hostname

---

## Step 8: View All Resources

### Command
```bash
kubectl get all -n nginx
```

### What does this command do?
Shows a comprehensive view of all resources (pods, services, deployments, replica sets) in your namespace.

### Why use this?
To get a complete picture of your application's deployment status in one command.

### Expected Output
```
aditya@Aditya:~/devops/kubernetes/kind-cluster/nginx$ kubectl get all -n nginx
NAME                                      READY   STATUS    RESTARTS      AGE
pod/nginx-deployment-6f59995b97-7k8lq     1/1     Running   2 (81m ago)   15h
pod/nginx-deployment-6f59995b97-thqxp     1/1     Running   2 (81m ago)   15h
pod/notes-app-deployment-978b5946-t2knn   1/1     Running   0             48m

NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/nginx-service       ClusterIP   10.96.68.204    <none>        80/TCP     13h
service/notes-app-service   ClusterIP   10.96.184.149   <none>        8000/TCP   48m

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment       2/2     2            2           15h
deployment.apps/notes-app-deployment   1/1     1            1           48m

NAME                                            DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-6f59995b97     2         2         2       15h
replicaset.apps/notes-app-deployment-978b5946   1         1         1       48m
```

---

## Step 9: Expose the Ingress Controller

### Command
```bash
sudo -E kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 80:80
```

### What does this command do?
- `sudo -E`: Runs the command with elevated privileges while preserving environment variables
- `kubectl port-forward`: Creates a tunnel from your local machine to the cluster
- `service/ingress-nginx-controller`: Specifies which service to forward
- `-n ingress-nginx`: The namespace where the service exists
- `80:80`: Maps local port 80 to cluster port 80

### Why is this needed?
In a Kind cluster, the LoadBalancer service doesn't get an external IP automatically. Port forwarding creates a connection between your local machine and the cluster, allowing you to access your applications via `localhost`.

### Use Case
Development and testing environments where you want to access cluster services from your local browser without complex networking setup.

### Troubleshooting
If you see a "port already in use" error, check out this tool to clean up the port:
[Ghost Port Cleanup Tool](https://github.com/Aditya-das-4707-e/Ghost-Port-Cleanup-Tool)

---

## Step 10: Access Your Applications

Once port forwarding is active, you can access your applications:

- **Nginx App**: `http://localhost/nginx`
- **Notes App**: `http://localhost/`

---

## Summary

You've successfully:
1. ✅ Installed the Ingress NGINX Controller
2. ✅ Created routing rules for multiple services
3. ✅ Exposed your applications through a single entry point
4. ✅ Made your applications accessible via localhost

## Key Concepts Recap

- **Ingress Controller**: The traffic manager for your cluster
- **Ingress Resource**: The rules that define how traffic should be routed
- **Namespace**: A way to organize and isolate resources
- **Service**: A stable network endpoint for accessing pods
- **Port Forwarding**: A tunnel between your local machine and the cluster

---

## Additional Resources

- [Kubernetes Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/)
- [Kind Documentation](https://kind.sigs.k8s.io/)

---

## Quick Reference Commands

| Command | What It Does |
|---------|--------------|
| `docker build` | Packages your app into an image |
| `docker images` | Lists all Docker images |
| `docker login` | Logs into Docker Hub |
| `docker tag` | Renames image with your username |
| `docker push` | Uploads image to Docker Hub |
| `kubectl apply -f` | Creates resources in Kubernetes |
| `kubectl get pods` | Shows running containers |
| `kubectl port-forward` | Opens access to your app |

---

## Understanding the Flow (Simple Explanation)

1. **Build**: You create a package of your app (Docker image)
2. **Upload**: You send it to Docker Hub (online storage)
3. **Organize**: You create a workspace in Kubernetes (namespace)
4. **Deploy**: You tell Kubernetes to run your app (deployment)
5. **Connect**: You create a door for your app (service)
6. **Access**: You create a tunnel to see your app (port-forward)

Think of it like this: You're opening a restaurant!
- Building the image = Cooking the food
- Pushing to Docker Hub = Putting the recipe online
- Creating namespace = Renting a space for your restaurant
- Creating deployment = Hiring chefs to cook
- Creating service = Opening the front door
- Port-forwarding = Building a bridge so customers can walk in

---

## Troubleshooting Tips

**Problem:** Pods are not starting  
**Solution:** Check logs with `kubectl logs <pod-name> -n <your-namespace>`

**Problem:** Can't access on browser  
**Solution:** Make sure port-forward is still running in your terminal

**Problem:** Image pull error  
**Solution:** Verify your image name in `deployment.yml` matches what's on Docker Hub

---

