<img title="" src="file:///D:/Kubernetes%20Learning/kube_screenshot/docker-desktop%20kubernetes%20imgs.png" alt="docker-desktop kubernetes imgs.png" width="718">

## **1. Understanding What You’re Seeing**

In Docker Desktop:

- `desktop-control-plane` → This is the main Kubernetes control plane node managed by Docker Desktop (kind-based).

- `kind-cloud-provider` & `kind-registry-mirror` → Support containers for Kubernetes networking and local registry.

- `minikube` → This is a separate Kubernetes cluster you created with `minikube start`, unrelated to Docker Desktop’s built-in cluster.

⚠ **Important:** Docker Desktop’s Kubernetes and Minikube are **two separate clusters**. You need to decide which one to connect to in your terminal.

## 🧭 What Is a Kubernetes Context?

A **context** in Kubernetes defines access parameters for a cluster. It includes:

- **Cluster**: The Kubernetes cluster to connect to.

- **User**: The credentials used to authenticate.

- **Namespace**: The default namespace for commands.

You can switch between multiple clusters or environments (e.g., dev, staging, prod) using contexts.

```powershell
kubectl config get-contexts
```

- Lists all available contexts in your kubeconfig file.

- Shows which cluster you're currently connected to.
  
  ```powershell
  kubectl config use-context docker-desktop
  ```
  
  Switches your active context to `docker-desktop`, which is the local Kubernetes cluster provided by Docker Desktop.

## 🖥️ What Are Nodes?

**Nodes** are the worker machines in a Kubernetes cluster. Each node runs:

- **Pods** (your containers)

- **Kubelet** (agent that communicates with the control plane)

- **Container runtime** (like Docker)

```powershell
kubectl get nodes
```

- Lists all nodes in your cluster.

- Shows their status, roles, and resource availability.

### 🚀 Deployment and Service Lifecycle

🔹 **Create a Deployment:**

```powershell
kubectl create deployment nginx --image=nginx
```

- Creates a **Deployment** named `nginx`.

- Pulls the official `nginx` image from Docker Hub.

- Manages pod replicas and ensures high availability.

🔹 **View All Pods:**

```powershell
kubectl get pods -A
```

- Lists all pods across **all namespaces**.

- Useful for debugging or monitoring system-level pods.

🔹 **Expose Deployment as a Service:**

```powershell
kubectl expose deployment nginx --type=NodePort --port=80
```

- Creates a **Service** named `nginx`.

- Exposes port `80` of the deployment to a random high port on the node (e.g., 30000–32767).

- Allows external access to the pod.

🔹 **Check Service Details:**

```powershell
kubectl get svc nginx
```

* Displays the service's cluster IP, node port, and target port.

🔹 **View Pod YAML:**

```powershell
kubectl get pods nginx-xxxx -o yaml
```

- Shows full configuration of a specific pod in YAML format.

- Useful for inspecting environment variables, volumes, labels, etc.

🔹 **Port Forwarding:**

```powershell
kubectl port-forward deployment/nginx 8080:80
```

- Forwards local port `8080` to port `80` of the `nginx` deployment.

- Lets you access the service via `http://localhost:8080`.

🔹 **Apply a YAML Manifest:**

```powershell
kubectl apply -f my-deployment.yaml
```

- Applies configuration from a YAML file.

- Can create or update resources like deployments, services, config maps, etc.

🔹 **Start Kubernetes API Proxy:**

```powershell
kubectl proxy
```

- Starts a local proxy to the Kubernetes API server.

- Useful for accessing dashboard or API endpoints.

🔹 **Access Kubernetes Dashboard:**

```powershell
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

- Opens the Kubernetes Dashboard via the proxy.

- Requires the dashboard to be installed in your cluster.

🧹 How to Stop and Delete Deployment & Service

🔹 **Delete the Deployment:**

```powershell
kubectl delete deployment nginx
```

🔹 Delete the Service:

```powershell
kubectl delete svc nginx
```

This will:

- Stop all pods managed by the deployment.

- Remove the service and its exposed port.

🛑 **<mark>How to Shut Down Kubernetes on Docker Desktop</mark>**

Since you're using **Docker Desktop**, Kubernetes runs as a local cluster. To shut it down:

### 🔹 Option 1: Disable Kubernetes from Docker Desktop UI

1. Open Docker Desktop.

2. Go to **Settings** → **Kubernetes**.

3. Uncheck **Enable Kubernetes**.

4. Click **Apply & Restart**.

This will:

- Stop the Kubernetes cluster.

- Remove all running containers and services.

### 🔹 Option 2: Stop Docker Desktop

Simply quitting Docker Desktop will also stop the Kubernetes cluster.

![kubectl common commands.png](D:\Kubernetes%20Learning\kube_screenshot\kubectl%20common%20commands.png)

# Kubernetes Architecture

![Kubernetes Architecture (1).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).jpg)

![Kubernetes Architecture (1).webp](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).webp)

### 

![Kubernetes Architecture (3).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(3).jpg)

![Kubernetes Architecture (1).png](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).png)

![Kubernetes Architecture (2).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(2).jpg)

### 🧩 **Worker Node Components**

- **Kubelet** 🧭: Ensures containers are running as defined by the control plane.

- **Container Runtime** 🐳: Runs and manages containers (e.g., Docker, containerd).

- **Kube-Proxy** 🔀: Handles networking and load balancing for services.

- **Pods** 📦: Smallest deployable units that run your app containers.

- **Node OS** 🖥️: The operating system that hosts all these components.

INSIDE WORKER NODE

1. KUBELET (agent that communicates with the control plane)

2. KUBE-PROXY

3. K8s OBJECTS

4. CONTAINER RUNTIME

### 📦 **Kubernetes OR K8s Objects Inside a Worker Node**

- **Pod** 🧱: The basic unit that runs one or more containers.

- **ReplicaSet** 🔁: Ensures the desired number of pod replicas are running.

- **Deployment** 🚀: Manages updates and rollbacks for ReplicaSets and Pods.

- **DaemonSet** 🛡️: Ensures a copy of a pod runs on every node.

- **StatefulSet** 🗂️: Manages pods with persistent identity and storage.

- **Job** ⏳: Runs pods to completion for batch tasks.

- **CronJob** ⏰: Schedules jobs to run periodically.

These objects are **scheduled by the control plane**, but they **live and run on the worker nodes**.

✅ **Service** is a Kubernetes object.

- **Type**: Native Kubernetes object

- **Purpose**: Exposes a set of Pods as a network service

- **Created by**: You (via YAML or CLI)

- **Lives on**: Control plane (definition), but affects networking across nodes

- **Types**:
  
  - `ClusterIP` (default, internal only)
  
  - `NodePort` (exposes on node IP)
  
  - `LoadBalancer` (cloud provider integration)
  
  - `ExternalName` (DNS alias)

> Think of a **Service** as a stable front door to your Pods, even if Pods come and go.
> 
> #### ❌ **Logs** — Not a Kubernetes object.
> 
> - **Type**: Output generated by containers (not an object)
> 
> - **Accessed via**: `kubectl logs <pod-name>`
> 
> - **Stored in**: Node filesystem or external logging systems (e.g., Fluentd, Loki, ELK)
> 
> - **Managed by**: Logging agents, not Kubernetes itself
> 
> > Logs are **data**, not objects. Kubernetes helps you access them, but doesn’t manage them as first-class citizens.

![Screenshot 2025-08-16 124136.png](D:\Kubernetes%20Learning\kube_screenshot\Screenshot%202025-08-16%20124136.png)

### 🔄 **Flow Summary**

1. **You define objects** (like Deployments, Jobs) via YAML or CLI.

2. **Control plane schedules them** to worker nodes.

3. **Worker nodes run the actual pods** that do the work.

## 🚀 1. **Deployment**

### ✅ Purpose:

- Manages Pods and ReplicaSets

- Ensures desired number of Pods are running

📄 YAML Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

🛠️ Commands:

```powershell
kubectl apply -f deployment.yaml       # Create
kubectl rollout pause deployment my-deployment   # Pause updates
kubectl rollout resume deployment my-deployment  # Resume updates
kubectl scale deployment my-deployment --replicas=0  # Stop (scale to 0)
kubectl delete deployment my-deployment          # Delete
```

## 🌐 2. **Service**

### ✅ Purpose:

- Exposes Pods to internal or external traffic

- Keeps a stable endpoint even if Pods change

📄 YAML Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

🛠️ Commands:

```powershell
kubectl apply -f service.yaml           # Create
kubectl delete service my-service       # Delete
```

Services don’t “pause” or “stop” like Deployments. You delete or update them.

## ⏱️ 3. **Job**

### ✅ Purpose:

- Runs Pods to completion (e.g., batch tasks)

- Doesn’t restart unless specified

📄 YAML Example:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "Hello from Kubernetes Job"]
      restartPolicy: Never
```

🛠️ Commands:

```powershell
kubectl apply -f job.yaml              # Create
kubectl delete job my-job              # Delete
```

Jobs run once and finish. You don’t pause/stop them mid-run unless you delete them.

![Summary Table about Deployment Service Job.png](D:\Kubernetes%20Learning\kube_screenshot\Summary%20Table%20about%20Deployment%20Service%20Job.png)

🚀 1. **Deployment YAML Explained**

```yaml
apiVersion: apps/v1          # Tells Kubernetes this is a Deployment object
kind: Deployment             # Specifies the type of object
metadata:
  name: my-deployment        # Name of the Deployment
spec:
  replicas: 3                # Number of Pods to run
  selector:
    matchLabels:
      app: my-app            # Label used to find matching Pods
  template:                  # Template for the Pods to be created
    metadata:
      labels:
        app: my-app          # Label assigned to each Pod
    spec:
      containers:
      - name: nginx          # Name of the container
        image: nginx:latest  # Docker image to run
```

### 🔑 Key Concepts:

- `metadata.name`: Unique name of the Deployment.

- `spec.selector.matchLabels` and `template.metadata.labels`: **Must match** — this links the Deployment to the Pods it manages.

- `containers.image`: The actual app or service you want to run.

🌐 2. **Service YAML Explained**

```yaml
apiVersion: v1               # Tells Kubernetes this is a Service object
kind: Service                # Specifies the type of object
metadata:
  name: my-service           # Name of the Service
spec:
  selector:
    app: my-app              # Matches Pods with this label
  ports:
  - protocol: TCP
    port: 80                 # Port exposed by the Service
    targetPort: 80           # Port inside the Pod
  type: ClusterIP            # Internal service (default)
```

### 🔑 Key Concepts:

- `metadata.name`: Name of the Service.

- `spec.selector.app`: **Must match the Pod label** (`app: my-app`) from the Deployment.

- `port` vs `targetPort`: External vs internal port mapping.

> ✅ **To link Service to Deployment**, the `selector.app` in Service must match the `labels.app` in Deployment.

⏱️ 3. **Job YAML Explained**

```yaml
apiVersion: batch/v1         # Tells Kubernetes this is a Job object
kind: Job                    # Specifies the type of object
metadata:
  name: my-job               # Name of the Job
spec:
  template:                  # Pod template for the Job
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "Hello from Kubernetes Job"]
      restartPolicy: Never   # Don't restart the Pod if it finishes
```

### 🔑 Key Concepts:

- `metadata.name`: Name of the Job.

- `containers.command`: What the Job should do.

- `restartPolicy`: Set to `Never` for one-time jobs.

> ✅ **Jobs are independent** — they don’t need Services or Deployments unless you want them to access other Pods.

![deployment service job connection link.png](D:\Kubernetes%20Learning\kube_screenshot\deployment%20service%20job%20connection%20link.png)

## 🧪 Example Flow

1. **Deployment** creates Pods with label `app: my-app`.

2. **Service** uses `selector.app: my-app` to route traffic to those Pods.

3. **Job** runs a one-time task, like printing a message or doing a batch job.

***

***

**<mark>🔍 Verify Kubernetes is Running</mark>**
Open your terminal and run:

```powershell
kubectl cluster-info
```

You should see something like:

```powershell
Kubernetes control plane is running at https://localhost:6443


 kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:54819
CoreDNS is running at https://127.0.0.1:54819/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

**To check the node:**

```powershell
kubectl get nodes
```

**📦 View Running Pods**

```powershell
kubectl get pods --all-namespaces
```

This shows all pods, including system components like kube-apisever ,etcd , etc.

**<mark>🛠 Intermediate Level: Deploy and Manage Workloads</mark>**

4. 🚀 Deploy a Sample App (e.g., NGINX)
   Create a file called : nginx-deployment.yaml

5. ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     replicas: 2
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
           image: nginx:1.14.2
           ports:
           - containerPort: 80
   ```
   
   Apply it:
   
   ```powershell
   kubectl apply -f nginx-deployment.yaml
   ```
   
   Check deployment:
   
   ```powershell
   kubectl get deployments
   ```

6. **🌐 Expose the Deployment**

create nginx-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

Apply it:

```powershell
kubectl apply -f nginx-service.yaml
```

Access it via:

```powershell
minikube service nginx-service
```

This opens the service in your browser if you're using Minikube.

***

***

🧠 Advanced Level: Debugging, Logs, and Cleanup

6. 📋 View Logs
   
   ```bash
   kubectl logs -l app=nginx
   ```

7. 🧪 Exec into a Pod
   
   ```powershell
   kubectl exec -it <pod-name> -- /bin/bash
   ```
   
   To get pod name:
   
   ```powershell
   kubectl get pods
   ```

8. 🧹 Cleanup Resources

```powershell
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

🧰 Bonus: Tips for Docker Desktop Kubernetes
•     You can use 'kubectl config view'  to inspect your kubeconfig.

```powershell
kubectl config view
```

•     Docker Desktop uses a context called docker-desktop . Switch to it if needed:

```powershell
kubectl config use-context docker-desktop
```

***

***

**<mark>A single YAML manifest that bundles:</mark>**

- **Deployment** → Runs an Nginx webserver.

- **Service** → Exposes that Nginx app.

- **Job** → Runs once to print `"Hello from Job"`.

- **CronJob** → Prints `"Hello from CronJob"` every **2 minutes**.

Each section includes comments to help you understand and tweak it easily.

```yaml

# ============================================================
# 1. Deployment: Runs an Nginx web server
# ============================================================
# ---------------- Deployment ----------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 2  # Number of Pods to run
  selector:
    matchLabels:
      app: my-app  # Label used to match Pods
  template:
    metadata:
      labels:
        app: my-app  # Label assigned to Pods
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

---
# ============================================================
# 2. Service: Exposes the Nginx deployment
# ============================================================
#  change type from ClusterIP to NodePort 
#   accessible via localhost:<nodeport>
# ---------------- Service ----------------
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app  # Must match Deployment's Pod label
  ports:
  - protocol: TCP
    port: 80         # Port exposed by Service
    targetPort: 80   # Port inside the Pod
  type: ClusterIP    # Internal access only/Accessible via localhost:<nodeport>

--# ============================================================
# 3. Job: Runs once to print "Hello from Job"
# ============================================================-
# ---------------- Job ----------------
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
      - name: hello
        image: busybox
        command: ["echo", "Hello from Kubernetes Job"]
      restartPolicy: Never  # Don't restart once done
  backoffLimit: 2  # Retry if fails (max 2 retries)   

---
# ============================================================
# 4. CronJob: Runs every 2 minutes, prints "Hello from CronJob"
# ============================================================
# ---------------- CronJob ----------------
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/2 * * * *"  # Every 2 minutes (CRON syntax)
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "Hello from CronJob"]
          restartPolicy: Never
```

🛠️ How to Use These

1. ✅ Create All:

```powershell
kubectl apply -f full-bundle.yaml
```

2. ✅ Check resources:

```powershell
kubectl get all
```

3. ✅ Test service (find NodePort):

```powershell
kubectl get svc my-service
```

Then open `http://localhost:<NODEPORT>` in your browser.

4. ✅ Check Job logs:

```powershell
kubectl logs job/my-job
```

5. ✅ Check CronJob created jobs:

```powershell
kubectl get jobs
```

Get logs of latest:

```powershell
kubectl logs job/<cronjob-created-job-name>
kubectl logs job/hello-cronjob
```



🧼 Delete Individually:

```powershell
kubectl delete deployment my-deployment
kubectl delete service my-service
kubectl delete job my-job
kubectl delete cronjob hello-cronjob
```

You can also split this into separate files if needed, but bundling helps for quick testing and learning.



👉 This manifest gives you a **mini Kubernetes lab**:

- Deployment + Service = web app.

- Job = one-time task.

- CronJob = recurring task.


