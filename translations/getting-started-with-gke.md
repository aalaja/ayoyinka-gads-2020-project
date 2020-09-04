# LAB: Getting Started with GKE
## Objectives:

In this lab, you learn how to perform the following tasks:
* Provision a Kubernetes cluster using Kubernetes Engine.
* Deploy and manage Docker containers using `kubectl`.

## Steps:

**1. Enable the required APIs:**

```
gcloud config set project <Enter Project_ID>
gcloud services enable container.googleapis.com
gcloud services enable containerregistry.googleapis.com
```

**2. Start a Kubernetes Engine cluster:**

```
export MY_ZONE=us-central1-a
gcloud container clusters create webfrontend --zone $MY_ZONE --num-nodes 2
```
Check the `kubectl` version and the running nodes.

```
kubectl version
gcloud compute instances list --zones $MY_ZONE
```

**3. Run and deploy a container:**
a. Launch an instance of the nginx container and view the pod running it.
```
kubectl create deploy nginx --image=nginx:1.17.10
kubectl get pods
```
b. Expose the nginx container to the internet, view the service and curl the Load Balancer's external IP to test that the nginx container is up and running.
```
kubectl expose deployment nginx --port 80 --type LoadBalancer
kubectl get services
curl <nginx EXTERNAL-IP>
```

c. Scale the number of pods running the service and confirm the number of pods
```
kubectl scale deployment nginx --replicas 3
kubectl get pods
kubectl get services
```
