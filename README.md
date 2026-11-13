# **My first Kubernetes deployment – nginx**

### **Step 1. Check Kubernetes nodes**

```
kubectl get nodes
```

### **Step 2. Create a Deployment**

```
kubectl create deployment nginx --image=nginx
```

This creates a Deployment named `nginx` using the nginx Docker image.

### **Step 3. Check Deployment**

```
kubectl get deployments
```

### **Step 4. Check Pods**

```
kubectl get pods
```

### **Step 5. Expose Deployment using NodePort**

```
kubectl expose deployment nginx --type=NodePort --port=80
```

This creates a Service named `nginx`.

### **Step 6. Check Services**

```
kubectl get services
```

You saw output similar to:

```
nginx   NodePort   10.96.121.89   <none>   80:30127/TCP
```

### **Step 7. Access nginx application**

```
kubectl port-forward service/nginx 8080:80
```

Open your browser:

```
http://localhost:8080
```

You should see the nginx welcome page.

### **Step 8. Check application logs**

```
kubectl logs deployment/nginx
```

### **Step 9. Increase application replicas**

```
kubectl scale deployment nginx --replicas=3
```

Check:

```
kubectl get pods
```

### **Step 10. Delete Deployment and Service**

```
kubectl delete service nginx
kubectl delete deployment nginx
```

## **Remember this deployment flow**

```
1. kubectl get nodes
2. kubectl create deployment nginx --image=nginx
3. kubectl get deployments
4. kubectl get pods
5. kubectl expose deployment nginx --type=NodePort --port=80
6. kubectl get services
7. kubectl port-forward service/nginx 8080:80
8. kubectl logs deployment/nginx
9. kubectl scale deployment nginx --replicas=3
10. kubectl delete service nginx
11. kubectl delete deployment nginx
```
