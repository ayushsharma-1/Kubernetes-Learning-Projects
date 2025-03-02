# **Complete Guide to Setting Up Ambassador Edge Stack in Kubernetes**

## **1. Introduction**

Ambassador Edge Stack (AES) is an API gateway and ingress controller built on Envoy Proxy, designed for microservices in Kubernetes. This guide provides a step-by-step approach to installing and configuring AES while troubleshooting common issues.

---

## **2. Prerequisites**

- A running **Kubernetes Cluster** (Minikube, K3s, EKS, GKE, AKS, etc.).
- **kubectl** installed and configured.
- **Helm** installed (version 3+).
- Internet access for pulling Docker images and applying configurations.
- An **Ambassador Cloud Account** (if using the Enterprise version).

---

## **3. Installing Ambassador Edge Stack**

### **Step 1: Add the Helm Repository**

```sh
helm repo add datawire https://app.getambassador.io
helm repo update
```

### **Step 2: Install Ambassador Edge Stack**

```sh
kubectl create namespace ambassador
helm install ambassador-edge-stack datawire/ambassador --namespace ambassador
```

### **Step 3: Verify Installation**

```sh
kubectl get pods -n ambassador
```

Expected output:

```
NAME                                      READY   STATUS    RESTARTS   AGE
ambassador-xxxxxxxx-xxxxx                 1/1     Running   0          2m
```

### **Step 4: Check Service Availability**

```sh
kubectl get svc -n ambassador
```

Expected output:

```
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)
ambassador   LoadBalancer   10.100.200.100   <pending>     80:30329/TCP
```

If `EXTERNAL-IP` is `<pending>`, use the `NodePort` service:

```sh
kubectl port-forward svc/ambassador 8080:80 -n ambassador
```

Access it at: `http://localhost:8080`

---

## **4. Common Errors and Fixes**

### **4.1 License Issue: CloudConnectToken Required**

#### **Error Message:**

```
ERROR cloud-license-provider licensemgt/cloud.go:258 unable to fetch license from ambassador cloud   {"error": "CloudConnectToken is required"}
ERROR cloud-license-provider licensemgt/cloud.go:415 ambassador edge stack requires a valid license to run.
```

#### **Solution:**

1. Sign up for a free license: [Ambassador Cloud](https://app.getambassador.io/edge-stack/existing/?namespace=ambassador).
2. Apply the license:
   ```sh
   edgectl license install <LICENSE-KEY>
   ```
3. Restart the Ambassador pods:
   ```sh
   kubectl rollout restart deployment ambassador -n ambassador
   ```

---

### **4.2 Missing Gateway API CRDs**

#### **Error Message:**

```
Warning, unable to watch gatewayclasses.v1alpha1.networking.x-k8s.io, unknown kind.
Warning, unable to watch httproutes.v1alpha1.networking.x-k8s.io, unknown kind.
```

#### **Solution:**

Install the Gateway API CRDs:

```sh
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v0.6.2/standard-install.yaml
```

Verify installation:

```sh
kubectl get crds | grep gateway
```

---

### **4.3 Client-Side Throttling**

#### **Error Message:**

```
request.go:697] Waited for 1.092621791s due to client-side throttling
```

#### **Solution:**

- Reduce API server requests.
- Check for excessive controller reconciliation.

---

## **5. Configuring Ambassador Edge Stack**

### **5.1 Creating a Basic Mapping**

```yaml
apiVersion: getambassador.io/v3alpha1
kind: Mapping
metadata:
  name: example-mapping
  namespace: ambassador
spec:
  prefix: /example/
  service: example-service
```

Apply it:

```sh
kubectl apply -f mapping.yaml
```

### **5.2 Creating an Ingress Resource**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  namespace: ambassador
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: example-service
            port:
              number: 80
```

Apply it:

```sh
kubectl apply -f ingress.yaml
```

---

## **6. Debugging Issues**

### **6.1 Checking Ambassador Logs**

```sh
kubectl logs -l service=ambassador -n ambassador
```

### **6.2 Checking Ambassador Status**

```sh
kubectl exec -it deploy/ambassador -n ambassador -- ambassador status
```

### **6.3 Checking Mappings**

```sh
kubectl get mappings -n ambassador
```

---

## **7. Conclusion**

This guide provides a complete setup for Ambassador Edge Stack in Kubernetes, including installation, configuration, and troubleshooting. If issues persist, refer to the [Ambassador documentation](https://www.getambassador.io/docs/) or raise an issue in their [GitHub repository](https://github.com/datawire/ambassador).

---

### **8. Additional Resources**

- [Ambassador Docs](https://www.getambassador.io/docs/)
- [Gateway API Docs](https://gateway-api.sigs.k8s.io/)
- [Kubernetes Docs](https://kubernetes.io/docs/)

