# Kubernetes Secrets for Admin Credentials

## Overview
This guide explains how to securely store and manage **admin credentials (ID, Password, and URL)** in a Kubernetes cluster using **Kubernetes Secrets**. We will configure secrets, deploy them in a Kubernetes deployment, and compare them with ConfigMaps. Additionally, we will explore how to use **Secrets as environment variables vs. volume mounts** and provide commands for both approaches.

## Why Use Kubernetes Secrets?
Secrets provide a way to securely store sensitive information, such as passwords, tokens, and keys, without exposing them in plain text. Unlike **ConfigMaps**, which store non-sensitive configuration data, **Secrets** are stored in a more secure manner, preventing unintended exposure.

---

## 📌 Difference: Secrets vs. ConfigMaps

| Feature          | Kubernetes Secrets | Kubernetes ConfigMaps |
|-----------------|-------------------|-----------------------|
| Purpose         | Store sensitive data (passwords, tokens, etc.) | Store non-sensitive configuration data |
| Encoding        | Base64-encoded    | Plain text           |
| Security        | More secure (can be encrypted at rest) | Less secure (stored in plain text) |
| Access Control  | Requires explicit permissions | Easier to access |
| Use Cases       | Passwords, API keys, TLS certificates | Application settings, URLs, database hostnames |

---

## 🔥 Secrets: Environment Variables vs. Volume Mounts

| Approach         | Environment Variables | Volume Mounts |
|-----------------|----------------------|--------------|
| Accessibility   | Available as environment variables in the pod | Mounted as files in a specific directory inside the pod |
| Security       | Visible in the process list | More secure, avoids accidental exposure in logs |
| Dynamic Updates | Requires pod restart to reflect changes | Automatically updates when secret changes |
| Use Case       | Simple secret values like API keys | Complex secrets like TLS certificates |

---

## 🔧 Kubernetes Architecture for Secret Management

```plaintext
+--------------------------------------------------+
|                  Kubernetes Cluster            |
+--------------------------------------------------+
|                 +--------------------+          |
|                 |  Kubernetes Secret  |          |
|                 |  (Admin Credentials)|          |
|                 +---------+----------+          |
|                           |                     |
|                           v                     |
|                 +--------------------+          |
|                 | Kubernetes Deployment|        |
|                 | (Pod Environment)    |        |
|                 +---------+----------+          |
|                           |                     |
|                           v                     |
|                 +--------------------+          |
|                 |     Running Pod      |        |
|                 |     (nginx container)|        |
|                 +--------------------+          |
+--------------------------------------------------+
```

---

## 📝 Steps to Set Up Kubernetes Secrets for Admin Credentials

### 1️⃣ Clone the Repository
```sh
nano binit-admin-secret.yaml
```
Copy the Secret configuration and save the file.

### 2️⃣ Apply the Secret
```sh
kubectl apply -f binit-admin-secret.yaml
```

### 3️⃣ Verify the Secret
```sh
kubectl get secrets
kubectl describe secret binit-admin-secret
```

---

## 🔹 Using Secrets as Environment Variables

### 4️⃣ Modify the Deployment File (Environment Variable Approach)
```sh
nano binit-deployment-env.yaml
```
Copy the Deployment configuration using environment variables and save the file.

### 5️⃣ Apply the Deployment
```sh
kubectl apply -f binit-deployment-env.yaml
```

### 6️⃣ Verify Environment Variables Inside the Pod
```sh
kubectl exec -it <POD_NAME> -- env | grep ADMIN_
```

---

## 🔹 Using Secrets as Volume Mounts

### 4️⃣ Modify the Deployment File (Volume Mount Approach)
```sh
nano binit-deployment-volume.yaml
```
Copy the Deployment configuration using volume mounts and save the file.

### 5️⃣ Apply the Deployment
```sh
kubectl apply -f binit-deployment-volume.yaml
```

### 6️⃣ Verify Mounted Secrets Inside the Pod
```sh
kubectl exec -it <POD_NAME> -- ls /etc/secrets/
kubectl exec -it <POD_NAME> -- cat /etc/secrets/admin-id
```

---

### 7️⃣ Expose the Service
```sh
kubectl expose deployment binit-deployment --type=NodePort --name=binit-service
```

### 8️⃣ Get the Service URL
```sh
minikube service binit-service --url
```

### 🔄 Delete the Secret (If Needed)
```sh
kubectl delete secret binit-admin-secret
```

---

## 🔥 Deep Analysis: Why Use Kubernetes Secrets?
1. **Security**: Unlike ConfigMaps, Secrets are stored separately and can be encrypted.
2. **Base64 Encoding**: Secrets are stored in Base64 format, preventing direct readability.
3. **Access Control**: Secrets require explicit access permissions, reducing the risk of accidental exposure.
4. **Dynamic Updates**: If Secrets are updated, the changes can be reflected in the running pods without restarting them (when using Volume Mounts).

By using **Secrets**, we ensure that **sensitive credentials like Admin ID, Password, and URLs** are securely managed and not exposed in plain text inside deployment files or environment variables.

---

## ✅ Conclusion
This guide explains how to securely store and use **Kubernetes Secrets** for managing admin credentials. Unlike ConfigMaps, Secrets provide **an extra layer of security**, making them ideal for sensitive data like passwords and API keys. By following the steps above, you can **safely store, retrieve, and use secrets** in your Kubernetes applications, either via **environment variables** or **volume mounts**, based on your security requirements.

