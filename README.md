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

# **Kubernetes Notes –Deployment YAML**

## **1. What is YAML?**

YAML stands for **YAML Ain't Markup Language**.

- YAML is used to define Kubernetes resources.
- It stores configuration in a human-readable format.
- Kubernetes reads YAML files to create or update resources.
- YAML uses indentation (spaces) to define relationships between fields.

Example command:

```
kubectl apply -f deployment.yaml
```

This command creates or updates Kubernetes resources using a YAML file.

## **2. Deployment YAML Example**

File name: `deployment.yaml`

```
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

## **3. Explanation of Each YAML Tag**

| Field           | Explanation                                                       |
| --------------- | ----------------------------------------------------------------- |
| `apiVersion`    | Specifies the Kubernetes API version used to create the resource. |
| `kind`          | Specifies the type of Kubernetes resource.                        |
| `metadata`      | Contains information about the resource.                          |
| `name`          | Defines the name of the resource.                                 |
| `spec`          | Defines the desired configuration of the resource.                |
| `replicas`      | Specifies how many Pod copies should run.                         |
| `selector`      | Identifies which Pods the Deployment manages.                     |
| `matchLabels`   | Matches Pods based on their labels.                               |
| `template`      | Defines the blueprint used to create Pods.                        |
| `labels`        | Assigns key-value labels to Pods.                                 |
| `containers`    | Defines the containers running inside a Pod.                      |
| `name`          | Specifies the container name.                                     |
| `image`         | Specifies the Docker image used by the container.                 |
| `ports`         | Lists the container ports.                                        |
| `containerPort` | Specifies the port the application uses inside the container.     |

## **4. Important Concepts**

**A. apiVersion**

```
apiVersion: apps/v1
```

- Tells Kubernetes which API version to use.
- Deployments use `apps/v1`.

**B. kind**

```
kind: Deployment
```

Defines the resource type.

Examples:

- Deployment
- Service
- Pod
- ConfigMap
- Secret

**C. metadata**

```
metadata:
  name: nginx-deployment
```

Contains resource information, such as its name.

**D. spec**

```
spec:
  replicas: 3
```

Defines the desired state of the resource.

**E. replicas**

```
replicas: 3
```

- Tells Kubernetes to maintain three Pods.
- If a Pod is deleted, Kubernetes creates a replacement.

**F. selector and matchLabels**

```
selector:
  matchLabels:
    app: nginx
```

- `selector` identifies the Pods managed by the Deployment.
- `matchLabels` matches the specified Pod labels.

**G. template**

```
template:
  metadata:
    labels:
      app: nginx
```

- Defines the Pod blueprint.
- `labels` assigns labels to newly created Pods.

**H. containers**

```
containers:
  - name: nginx
    image: nginx
```

- Defines the containers inside the Pod.
- `name` specifies the container name.
- `image` specifies which Docker image to run.

**I. ports**

```
ports:
  - containerPort: 80
```

Specifies the port used by the application inside the container.

Note: `containerPort` does not expose the application externally. A Service is needed for network access.

## **5. Relationship Between Deployment, ReplicaSet and Pod**

```
Deployment
    |
    v
ReplicaSet
    |
    v
   Pods
    |
    v
Container
```

- Deployment manages ReplicaSets.
- ReplicaSet maintains the desired number of Pods.
- Pods contain containers.
- Containers run the application.

## **6. Important Rules to Remember**

**Rule 1: Selector and labels must match.**

```
selector:
  matchLabels:
    app: nginx
```

```
template:
  metadata:
    labels:
      app: nginx
```

Both use `app: nginx`.

**Rule 2: YAML indentation matters.**

Correct:

```
metadata:
  name: nginx-deployment
```

Incorrect:

```
metadata:
name: nginx-deployment
```

Use spaces instead of tabs.

## **7. Important Kubernetes Commands**

| Command                                        | Purpose                              |
| ---------------------------------------------- | ------------------------------------ |
| `kubectl apply -f deployment.yaml`             | Create or update Deployment          |
| `kubectl get deployments`                      | List Deployments                     |
| `kubectl get replicasets`                      | List ReplicaSets                     |
| `kubectl get pods`                             | List Pods                            |
| `kubectl describe deployment nginx-deployment` | Show Deployment details              |
| `kubectl delete -f deployment.yaml`            | Delete resources defined in the file |

