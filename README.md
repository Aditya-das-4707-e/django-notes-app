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

