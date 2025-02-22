# Deploying First App on Kubernetes using Minikube

## Introduction
This guide covers deploying your first application on Kubernetes using Minikube. It includes defining Pods, using `kubectl`, installing dependencies, and running a simple application.

## What are Pods?
Pods are the smallest deployable units in Kubernetes, consisting of one or more containers that share storage, network, and a specification on how to run.

## What is kubectl?
`kubectl` is a command-line tool for interacting with a Kubernetes cluster. It allows users to deploy applications, inspect resources, and manage cluster components.

---

## Practical Setup

### Prerequisites
- Running Windows with WSL2 (Ubuntu)
- Installed `kubectl` and `minikube`

### Installing kubectl
Refer to the [Kubernetes official documentation](https://kubernetes.io/releases/download/) for installation details.

#### Install kubectl binary on Linux (x86-64):
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

#### Validate the downloaded binary:
```sh
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```
Expected output:
```sh
kubectl: OK
```

#### Install kubectl:
```sh
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

#### Verify installation:
```sh
kubectl version --client
```

---

### Installing Minikube
Refer to the [Minikube documentation](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download) for installation details.

#### Start Minikube Cluster:
```sh
minikube start
```

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

## Installing and Running Pods

Refer to the [Kubernetes Pods documentation](https://kubernetes.io/docs/concepts/workloads/pods/).

#### Create a Pod Configuration File:
```sh
nano nginx-pod.yaml
```
Copy the contents from `simple-pods.yaml` containing an Nginx pod running on port 80.

#### Equivalent Docker Command:
```sh
docker run -d nginx:14.2 --name nginx -p 80:80
```

#### Deploy the Pod:
```sh
kubectl create -f nginx-pod.yaml
```

#### Verify Pod Status:
```sh
kubectl get pods
kubectl get pods -o wide
```
The IP shown is assigned by `kube-proxy`.

#### Check Running Pod:
```sh
minikube ssh
curl <pod_ip>
```

#### Debugging & Logs:
```sh
kubectl logs nginx
kubectl describe pod nginx
```

#### Deleting a Pod:
```sh
kubectl delete pod nginx
```

---

## Deployments
To convert a Pod into a Deployment, change the `kind` field in the YAML file from `Pod` to `Deployment`. Deployments provide automatic scaling and self-healing capabilities.

**⚠️ Note:** This setup may generate **large cloud bills** due to resource provisioning.

---

## Additional Notes & Images
(Add any relevant images or diagrams at the end of this README.)
