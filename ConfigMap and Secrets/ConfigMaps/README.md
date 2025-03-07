# Kubernetes Deployment Guide for BinIT

## **📌 Overview**
This guide explains how to deploy the BinIT application on Kubernetes **without Docker** by using ConfigMaps, Deployments, and Services. We will expose the application URLs directly and use **Nginx** as the web server.

## **🛠️ Architecture Overview**
Below is the **architecture diagram** of our Kubernetes deployment:

```
+----------------+          +--------------------+
|   User        | ---->    | LoadBalancer SVC  |
+----------------+         +--------------------+
                                 |
                      +----------------------+
                      |   Kubernetes Nodes   |
                      +----------------------+
                                 |
                      +----------------------+
                      |   Nginx Deployment   |
                      +----------------------+
```

## **📂 Components Used**
1. **ConfigMap** - Stores environment variables like website URLs, admin credentials.
2. **Deployment** - Manages the application replicas and runs Nginx.
3. **Service (LoadBalancer or NodePort)** - Exposes the application publicly.

---

## **🚀 Step-by-Step Deployment**

### **Step 1: Clone the Repository**
First, clone the BinIT deployment repository:
```sh
git clone <repository-url>
cd <repository-folder>
```

Alternatively, create the required YAML files manually using `vim` or `nano`:
```sh
nano deployment.yaml
```
Copy and paste the respective deployment configurations.

---

### **Step 2: Apply the Kubernetes Configurations**
Apply the ConfigMap, Deployment, and Service YAML files:
```sh
kubectl apply -f cm.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

### **Step 3: Verify the Deployment**

**Check all Kubernetes resources:**
```sh
kubectl get all -o wide
```

Example output:
```
NAME                                    READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES
pod/binit-deployment-55d5f4c898-gb72m   1/1     Running   0          6m40s   10.244.0.32   minikube   <none>           <none>
pod/binit-deployment-55d5f4c898-stdzm   1/1     Running   0          6m40s   10.244.0.31   minikube   <none>           <none>

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE     SELECTOR
service/binit-service   NodePort    10.109.88.11   <none>        80:30008/TCP   2m32s   app=binit
service/kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        5d5h    <none>
```

Check if the **service is running** and get the **EXTERNAL-IP**:
```sh
kubectl get services
```

If using **NodePort**, access the website using:
```
http://<Node-IP>:30007
```
Use `minikube service binit-service --url` if running on Minikube.

If using **Port Forwarding**, run:
```sh
kubectl port-forward deployment/binit-deployment 8080:80
```
Then access the site at:
```
http://localhost:8080
```

---

## **📌 Key Takeaways**
✅ No need for Docker images, directly deploying using Nginx.
✅ Uses Kubernetes **ConfigMaps** for storing website URLs and credentials.
✅ Deployment ensures high availability with replicas.
✅ LoadBalancer service exposes the application publicly.
✅ NodePort or `kubectl port-forward` can be used for local setups.

💡 **This setup provides a scalable, efficient, and cloud-native way to host applications on Kubernetes.** 🚀

