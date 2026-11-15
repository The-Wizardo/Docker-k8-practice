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

# **Kubernetes Notes – Service YAML**

## **1. What is a Kubernetes Service?**

A Kubernetes Service is an object that provides a stable network endpoint to access a group of Pods.

**Why do we need a Service?**

- Pods can be deleted and recreated.
- A recreated Pod may receive a different IP address.
- A Service provides a stable IP address.
- A Service forwards traffic to matching Pods using labels and selectors.

## **2. Service YAML Example**

File name: `service.yaml`

```
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
```

## **3. Explanation of Each YAML Tag**

| Tag          | Explanation                                               |
| ------------ | --------------------------------------------------------- |
| `apiVersion` | Specifies the Kubernetes API version.                     |
| `kind`       | Defines the resource type. Here, Service.                 |
| `metadata`   | Contains resource information.                            |
| `name`       | Defines the Service name.                                 |
| `spec`       | Defines the desired Service configuration.                |
| `type`       | Defines how the Service is exposed.                       |
| `selector`   | Identifies the Pods that receive traffic.                 |
| `ports`      | Defines the Service port configuration.                   |
| `port`       | Port exposed by the Service.                              |
| `targetPort` | Port on the selected Pod/container where traffic is sent. |
| `protocol`   | Network protocol, such as TCP or UDP.                     |

## **4. Types of Kubernetes Services**

Kubernetes provides three commonly used Service types.

### **A. ClusterIP**

- Default Service type.
- Allows communication inside the Kubernetes cluster.
- Not directly accessible from outside the cluster.

Example:

```
spec:
  type: ClusterIP
```

### **B. NodePort**

- Exposes the application through a port on the Kubernetes node.
- Kubernetes assigns a port, usually between `30000–32767`.
- Useful for local testing and basic external access.

Example:

```
spec:
  type: NodePort
```

### **C. LoadBalancer**

- Exposes the application through an external load balancer.
- Commonly used in cloud environments.
- Requires a suitable load balancer implementation.

Example:

```
spec:
  type: LoadBalancer
```

### **Comparison**

| Type         | Purpose                        |
| ------------ | ------------------------------ |
| ClusterIP    | Internal cluster communication |
| NodePort     | Access through a node port     |
| LoadBalancer | External load balancer access  |

## **5. Understand Service Ports**

Example:

```
ports:
  - port: 80
    targetPort: 80
    protocol: TCP
```

| Field        | Meaning                                                                                                   |
| ------------ | --------------------------------------------------------------------------------------------------------- |
| `port`       | Service listens on port 80.                                                                               |
| `targetPort` | Traffic is forwarded to port 80 on the selected Pod.                                                      |
| `protocol`   | Uses TCP communication.                                                                                   |
| `nodePort`   | Optional explicit port exposed on the node. Kubernetes automatically assigns one for NodePort if omitted. |

### **Traffic flow**

```
User
  |
  v
NodePort: 30582
  |
  v
Service: Port 80
  |
  v
Pod: TargetPort 80
  |
  v
Nginx Container
```

## **6. Relationship Between Labels and Selectors**

**Pod labels:**

```
labels:
  app: nginx
```

**Service selector:**

```
selector:
  app: nginx
```

- Label identifies a Pod.
- Selector finds Pods with matching labels.
- Service forwards traffic to the matching Pods.
- If labels don't match, the Service won't select those Pods.

## **7. Important Kubernetes Commands**

| Command                                  | Purpose                          |
| ---------------------------------------- | -------------------------------- |
| `kubectl apply -f service.yaml`          | Create or update Service         |
| `kubectl get services`                   | List Services                    |
| `kubectl get svc`                        | Short command to list Services   |
| `kubectl describe service nginx-service` | Show Service details             |
| `kubectl get endpoints nginx-service`    | Show selected Pod endpoints      |
| `kubectl get pods --show-labels`         | Display Pod labels               |
| `kubectl get pods -l app=nginx`          | Find Pods using a label selector |
| `kubectl delete -f service.yaml`         | Delete Service defined in YAML   |

## **8. Practical Commands We Used**

**Create Deployment:**

```
kubectl apply -f deployment.yaml
```

**Create Service:**

```
kubectl apply -f service.yaml
```

**Check all resources:**

```
kubectl get deployments,pods,services
```

**Check Service endpoints:**

```
kubectl get endpoints nginx-service
```

**Access application locally:**

```
kubectl port-forward service/nginx-service 8080:80
```

Open:

```
http://localhost:8080
```

## **9. Important Concepts to Remember**

1. Deployment manages ReplicaSets.
2. ReplicaSet maintains the desired number of Pods.
3. Pods run containers.
4. Service provides stable network access to Pods.
5. Labels identify Kubernetes resources.
6. Selectors find resources using matching labels.
7. `port` is the Service port.
8. `targetPort` is the destination port on the Pod.
9. `nodePort` is the port exposed on the node.

 Notes Completed

**Next lesson: ConfigMap and Secrets**

We'll learn how to store application configuration and sensitive values separately from application code.

# Kubernetes Notes – ConfigMap

## 1. What is a ConfigMap?

A ConfigMap is a Kubernetes object used to store non-sensitive application configuration separately from application code and Docker images.

### Examples

- Application name
- Environment name
- Application version
- API URLs
- Application settings

### Why do we need ConfigMap?

Suppose we have one application running in three environments:

- Development
- Testing
- Production

We don't need to create three different Docker images just because the configuration is different.

We can use the same Docker image and provide different configurations through ConfigMaps.

**Main advantage:** We can change application configuration without rebuilding the Docker image.

## 2. How ConfigMap works

```text
Local config.json
       |
       v
Kubernetes ConfigMap
       |
       v
Deployment
       |
       v
Pod
       |
       v
Container reads config.json
```

## 3. Create a ConfigMap

### Example: config.json

```json
{
    "APP_NAME": "Calculator Application",
    "ENVIRONMENT": "Development",
    "APP_VERSION": "1.0",
    "SUPPORT_EMAIL": "support@example.com"
}
```

### Command

```cmd
kubectl create configmap calculator-config --from-file=config.json
```

### Explanation

| Command | Explanation |
|---|---|
| `kubectl` | Kubernetes command-line tool |
| `create configmap` | Creates a ConfigMap |
| `calculator-config` | Name of the ConfigMap |
| `--from-file` | Reads configuration from a file |
| `config.json` | Local configuration file |

Expected output:

```text
configmap/calculator-config created
```

## 4. Check ConfigMap

### List ConfigMaps

```cmd
kubectl get configmaps
```

### View ConfigMap details

```cmd
kubectl describe configmap calculator-config
```

### View ConfigMap YAML

```cmd
kubectl get configmap calculator-config -o yaml
```

## 5. Mount ConfigMap inside a Pod

To make the ConfigMap available inside our application container, we use a Volume and VolumeMount.

### Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: calculator-deployment

spec:
  replicas: 1

  selector:
    matchLabels:
      app: calculator

  template:
    metadata:
      labels:
        app: calculator

    spec:
      containers:
        - name: calculator
          image: my-app:1.0
          imagePullPolicy: IfNotPresent

          ports:
            - containerPort: 80

          volumeMounts:
            - name: config-volume
              mountPath: /usr/share/nginx/html/config.json
              subPath: config.json
              readOnly: true

      volumes:
        - name: config-volume
          configMap:
            name: calculator-config
```

### Explanation of important fields

| Field | Explanation |
|---|---|
| `volumes` | Defines the volume available to the Pod |
| `name` | Name assigned to the volume |
| `configMap` | Specifies that the volume uses a ConfigMap |
| `name: calculator-config` | References the existing ConfigMap |
| `volumeMounts` | Defines where the volume is attached inside the container |
| `mountPath` | Destination path inside the container |
| `subPath` | Mounts only the specified file |
| `readOnly` | Prevents modification through this mount |

### Important paths

Local file:

```text
config.json
```

File inside the container:

```text
/usr/share/nginx/html/config.json
```

The application can now read the configuration file from the container.

## 6. Verify ConfigMap inside the Pod

First, check the Pod:

```cmd
kubectl get pods
```

Then execute the following command:

```cmd
kubectl exec YOUR-POD-NAME -- cat /usr/share/nginx/html/config.json
```

### Explanation

| Command | Purpose |
|---|---|
| `kubectl exec` | Executes a command inside a container |
| `YOUR-POD-NAME` | Name of the running Pod |
| `cat` | Displays the file contents |
| File path | Location of config.json inside the container |

This verifies that the ConfigMap file is available inside the application container.

## 7. Update an Existing ConfigMap

Suppose we want to change the environment from Development to Production.

### Original configuration

```json
{
    "APP_NAME": "Calculator Application",
    "ENVIRONMENT": "Development",
    "APP_VERSION": "1.0",
    "SUPPORT_EMAIL": "support@example.com"
}
```

### Updated configuration

```json
{
    "APP_NAME": "Calculator Application",
    "ENVIRONMENT": "Production",
    "APP_VERSION": "1.0",
    "SUPPORT_EMAIL": "jawad@example.com"
}
```

Save the updated local `config.json`.

### Update the existing ConfigMap

```cmd
kubectl create configmap calculator-config --from-file=config.json --dry-run=client -o yaml | kubectl apply -f -
```

### Explanation of the command

| Command part | Purpose |
|---|---|
| `create configmap` | Generates a ConfigMap definition |
| `--from-file=config.json` | Reads the updated local file |
| `--dry-run=client` | Generates the resource without creating it directly |
| `-o yaml` | Outputs the generated definition as YAML |
| `\|` | Passes the generated YAML to the next command |
| `kubectl apply -f -` | Applies the YAML received through standard input |

This updates the existing ConfigMap instead of creating a duplicate.

Verify:

```cmd
kubectl describe configmap calculator-config
```

## 8. Restart Deployment after ConfigMap update

In our practical, we mounted the ConfigMap using `subPath`.

A file mounted using `subPath` does not automatically receive ConfigMap updates.

Therefore, restart the Deployment:

```cmd
kubectl rollout restart deployment calculator-deployment
```

Check rollout status:

```cmd
kubectl rollout status deployment calculator-deployment
```

Check the Pods:

```cmd
kubectl get pods
```

Verify the updated configuration:

```cmd
kubectl exec YOUR-NEW-POD-NAME -- cat /usr/share/nginx/html/config.json
```

Expected value:

```json
"ENVIRONMENT": "Production"
```

### Important concepts

- Normal ConfigMap volume mounts receive updates eventually.
- ConfigMap values used as environment variables do not automatically update inside an already-running container.
- ConfigMap files mounted through `subPath` do not automatically receive updates.
- Restarting the Deployment creates new Pods that use the updated configuration.

## 9. Important ConfigMap commands

| Command | Purpose |
|---|---|
| `kubectl get configmaps` | List ConfigMaps |
| `kubectl describe configmap calculator-config` | View details |
| `kubectl get configmap calculator-config -o yaml` | View YAML |
| `kubectl delete configmap calculator-config` | Delete a ConfigMap |
| `kubectl rollout restart deployment calculator-deployment` | Restart Deployment |
| `kubectl get pods` | Check Pods |

---

# Kubernetes Notes – Secret

## 1. What is a Kubernetes Secret?

A Secret is a Kubernetes object intended to store sensitive information.

### Examples

- Database username
- Database password
- API keys
- Authentication tokens
- Certificates
- Private keys

Instead of writing passwords directly inside application code, we can store them in a Kubernetes Secret.

**Main advantage:** Sensitive configuration can be managed separately from application code and Docker images.

## 2. Create a Kubernetes Secret

For our practical, we created a Secret named `calculator-secret`.

### Command

```cmd
kubectl create secret generic calculator-secret --from-literal=DB_USERNAME=admin --from-literal=DB_PASSWORD=MyPassword123
```

### Explanation

| Command | Explanation |
|---|---|
| `kubectl` | Kubernetes command-line tool |
| `create secret` | Creates a Secret |
| `generic` | Creates a general-purpose Secret |
| `calculator-secret` | Name of the Secret |
| `--from-literal` | Provides key-value data directly |
| `DB_USERNAME` | Username key |
| `DB_PASSWORD` | Password key |

Expected output:

```text
secret/calculator-secret created
```

## 3. Check Kubernetes Secrets

### List Secrets

```cmd
kubectl get secrets
```

### View Secret details

```cmd
kubectl describe secret calculator-secret
```

Example output:

```text
Name: calculator-secret
Namespace: default
Type: Opaque

Data
====
DB_PASSWORD: 13 bytes
DB_USERNAME: 5 bytes
```

Kubernetes displays the size of each value instead of displaying the actual password.

### View Secret YAML

```cmd
kubectl get secret calculator-secret -o yaml
```

Example:

```yaml
apiVersion: v1
kind: Secret

metadata:
  name: calculator-secret

type: Opaque

data:
  DB_USERNAME: YWRtaW4=
```

The actual values under `data` are Base64 encoded.

## 4. What is Base64 encoding?

Base64 converts data into a different textual representation.

Example:

```text
Original value: admin
Base64 value:   YWRtaW4=
```

Base64 is encoding, not encryption.

Anyone who has permission to read the Secret can decode its values.

### Decode a Secret value in Windows CMD

```cmd
powershell -Command "[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String((kubectl get secret calculator-secret -o jsonpath='{.data.DB_USERNAME}')))"
```

Expected output:

```text
admin
```

## 5. How Secret reaches an application

There are two common methods:

1. Environment variables
2. Volume mounts

### Method 1: Environment variables

```text
Kubernetes Secret
       |
       v
Deployment
       |
       v
Pod environment variables
       |
       v
Application reads the values
```

### Method 2: Volume Mount

```text
Kubernetes Secret
       |
       v
Deployment
       |
       v
Pod volume
       |
       v
Secret files inside container
```

We will practise both methods in the next practical lesson.

## 6. Important Secret commands

| Command | Purpose |
|---|---|
| `kubectl get secrets` | List all Secrets |
| `kubectl get secret calculator-secret` | Get a particular Secret |
| `kubectl describe secret calculator-secret` | View Secret details |
| `kubectl get secret calculator-secret -o yaml` | View Secret YAML |
| `kubectl delete secret calculator-secret` | Delete a Secret |

## 7. Important security rules

1. Never hardcode passwords inside application source code.
2. Do not commit real passwords or API keys to GitHub.
3. Restrict Secret access using Kubernetes RBAC.
4. Enable encryption at rest for Secrets.
5. Avoid printing Secret values in application logs.
6. Use proper secret-management solutions for production systems when required.

**Important:** Kubernetes Secret values are Base64 encoded by default. Base64 is not encryption, and Kubernetes does not automatically guarantee encryption at rest.

---

# Kubernetes Notes – Difference Between ConfigMap and Secret

## 1. Main difference

| Feature | ConfigMap | Secret |
|---|---|---|
| Purpose | Stores normal configuration | Stores sensitive information |
| Application name | Yes | No |
| Environment name | Yes | No |
| Application version | Yes | No |
| Database password | No | Yes |
| API key | No | Yes |
| Default representation | Plain text | Base64 encoded |
| Encryption at rest | Not by default | Not by default |
| Environment variables | Supported | Supported |
| Volume mounts | Supported | Supported |

## 2. Real-world example

Imagine an application that connects to a database.

| Application configuration | Resource |
|---|---|
| Application name | ConfigMap |
| Environment name | ConfigMap |
| Application version | ConfigMap |
| Database URL without credentials | ConfigMap |
| Database username | Secret |
| Database password | Secret |
| API authentication token | Secret |

## 3. Remember this

```text
ConfigMap = Non-sensitive configuration

Secret = Sensitive configuration
```

Both ConfigMaps and Secrets can be consumed by Pods through environment variables or volume mounts.

---

# Important concepts to remember

1. ConfigMap stores non-sensitive configuration.
2. Secret is intended for sensitive information.
3. Both resources are managed separately from the Docker image.
4. A Deployment can provide ConfigMap or Secret data to its Pods.
5. ConfigMaps can be created using local files.
6. Secrets can be created using literal values or files.
7. Base64 is encoding, not encryption.
8. RBAC controls who can access Kubernetes resources.
9. Updating a ConfigMap does not always update a running application immediately.
10. A Deployment restart creates new Pods that can consume updated configuration.

---

# Practical Commands – Quick Reference

## ConfigMap

```cmd
kubectl create configmap calculator-config --from-file=config.json

kubectl get configmaps

kubectl describe configmap calculator-config

kubectl get configmap calculator-config -o yaml

kubectl create configmap calculator-config --from-file=config.json --dry-run=client -o yaml | kubectl apply -f -

kubectl rollout restart deployment calculator-deployment
```

## Secret

```cmd
kubectl create secret generic calculator-secret --from-literal=DB_USERNAME=admin --from-literal=DB_PASSWORD=MyPassword123

kubectl get secrets

kubectl describe secret calculator-secret

kubectl get secret calculator-secret -o yaml
```

---

# Next Lesson – Using Secrets in a Deployment

In the next practical, we will:

1. Add Secret values to the existing Calculator Deployment.
2. Pass database username and password as environment variables.
3. Inspect environment variable names inside the Pod.
4. Understand how an application reads those variables.
5. Learn the difference between `env` and `envFrom`.

**End of Notes – ConfigMap and Secret**

