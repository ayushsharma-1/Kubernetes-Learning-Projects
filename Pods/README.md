# Deploying Your First Application on Kubernetes Using Minikube

## Introduction
This guide walks you through deploying your first application on Kubernetes using Minikube. You will learn about Pods, using `kubectl`, installing dependencies, and running a simple application.

## What Are Pods?
Pods are the smallest deployable units in Kubernetes. A Pod consists of one or more containers that share storage, network, and a specification on how to run.

## What is `kubectl`?
`kubectl` is a command-line tool for interacting with a Kubernetes cluster. It allows users to deploy applications, inspect resources, and manage cluster components.

---

## Prerequisites
- Windows with WSL2 (Ubuntu) or a Linux system
- Installed `kubectl` and `minikube`

### Installing `kubectl`
Refer to the [Kubernetes official documentation](https://kubernetes.io/releases/download/) for detailed instructions.

#### Install `kubectl` binary on Linux (x86-64):
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

#### Validate the downloaded binary:
```sh
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```
**Expected Output:**
```sh
kubectl: OK
```

#### Install `kubectl`:
```sh
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### Verify Installation:
```sh
kubectl version --client
```
(Add an image here: `/assets/version.png`)

---

### Installing Minikube
Refer to the [Minikube documentation](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download) for installation details.

#### Start Minikube Cluster:
```sh
minikube start
```
(Add an image here: `minikube-start.png`)

#### Interact with the Cluster:
```sh
kubectl get po -A
```
Alternatively:
```sh
minikube kubectl -- get po -A
```

#### Verify Nodes:
```sh
kubectl get nodes
```

---

## Deploying Pods on Kubernetes
Refer to the [Kubernetes Pods documentation](https://kubernetes.io/docs/concepts/workloads/pods/).

### Creating a Pod Configuration File
```sh
nano nginx-pod.yaml
```
Copy the contents from `simple-pods.yaml` containing an Nginx pod running on port 80.

### Equivalent Docker Command:
```sh
docker run -d nginx:14.2 --name nginx -p 80:80
```

### Deploy the Pod:
```sh
kubectl create -f nginx-pod.yaml
kubectl apply -f nginx-pod.yaml
```
(Add an image here: `kubectl-apply-pod.png`)

If you encounter an issue where your Nginx pod is stuck in `ContainerCreating` status, it means Kubernetes is trying to start the container, but something is preventing it from running properly. Follow the steps below to troubleshoot.

### Troubleshooting Pod Creation Issues

#### Step 1: Check Pod Deletion Status
```sh
kubectl get pods
```
If the Nginx pod is still in the `Terminating` state, proceed with force deletion.

#### Step 2: Force Delete the Pod
```sh
kubectl delete pod nginx --grace-period=0 --force
```
Then, verify removal:
```sh
kubectl get pods
```

#### Step 3: Ensure a Clean State
Check if any Nginx-related resources remain:
```sh
kubectl get all | grep nginx
```
If anything related to Nginx remains, delete it manually.

#### Step 4: Recreate the Pod
Once everything is cleaned up, try running:
```sh
kubectl run nginx --image=nginx --port=80
```
or recreate it from the YAML file:
```sh
kubectl create -f nginx-pod.yaml
```

### Verifying Pod Status
```sh
kubectl get pods
kubectl get pods -o wide
```
The IP shown is assigned by `kube-proxy`.

### Checking the Running Pod
```sh
minikube ssh
curl <pod_ip>
```

### Debugging & Logs
```sh
kubectl logs nginx
kubectl describe pod nginx
```

---

## Deployments in Kubernetes
To convert a Pod into a Deployment, modify the YAML file and change the `kind` field from `Pod` to `Deployment`. Deployments provide automatic scaling and self-healing capabilities.

**⚠️ Note:** Running Kubernetes in cloud environments may generate **large cloud bills** due to resource provisioning.

---

