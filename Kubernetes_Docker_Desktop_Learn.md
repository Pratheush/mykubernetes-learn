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

<img src="file:///D:/Kubernetes%20Learning/kube_screenshot/kubectl%20common%20commands.png" title="" alt="kubectl common commands.png" width="655">

# Kubernetes Architecture

![Kubernetes Architecture (1).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).jpg)

![Kubernetes Architecture (1).webp](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).webp)

### 

![Kubernetes Architecture (3).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(3).jpg)

![Kubernetes Architecture (1).png](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(1).png)

![Kubernetes Architecture (2).jpg](D:\Kubernetes%20Learning\kube_screenshot\Kubernetes%20Architecture%20(2).jpg)

### Kubernetes Control Plane Components :

- 🧠 **API Server** – The brain that talks to users and nodes, handling all requests.

- 📋 **Scheduler** – The planner that decides which pod goes on which node.

- 🕵️ **Controller Manager** – The babysitter that watches and fixes cluster state.

- 📚 **etcd** – The memory that stores all cluster data safely.

- 🔐 **Cloud Controller Manager** – The bridge that connects Kubernetes to your cloud provider.

OR 

- 🧠 **API Server** – The main gatekeeper that handles all communication in the cluster.

- 📦 **Scheduler** – Chooses the best node to run each new pod.

- 👮 **Controller Manager** – Keeps everything running as expected by fixing mismatches.

- 🗄️ **etcd** – Stores all cluster data like a secure, fast database.

- ☁️ **Cloud Controller Manager** – Connects Kubernetes to your cloud provider’s services.

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
   
   PS C:\Users\prath> kubectl exec -it my-deployment /bin/bash
   error: exec [POD] [COMMAND] is not supported anymore. Use exec [POD] -- [COMMAND] instead
   See 'kubectl exec -h' for help and examples

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

---
# ============================================================
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
   
   Before creating or running full-bundle.yaml ::

```powershell
 kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   13h
```

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

🧼 Delete All The Components mentioned In The fully-bundle.yaml:

```powershell
kubectl delete -f full-bundle.yaml
```

You can also split this into separate files if needed, but bundling helps for quick testing and learning.

👉 This manifest gives you a **mini Kubernetes lab**:

- Deployment + Service = web app.

- Job = one-time task.

- CronJob = recurring task.

TO ACCESS INTO BROWSER ::

![port-forwarding first app.png](D:\Kubernetes%20Learning\kube_screenshot\port-forwarding%20first%20app.png)

***

***

![kubernetes nginx hello world (1).png](D:\Kubernetes%20Learning\kube_screenshot\kubernetes%20nginx%20hello%20world%20(1).png)

![kubernetes nginx hello world (3).png](D:\Kubernetes%20Learning\kube_screenshot\kubernetes%20nginx%20hello%20world%20(3).png)

![kubernetes nginx hello world (2).png](D:\Kubernetes%20Learning\kube_screenshot\kubernetes%20nginx%20hello%20world%20(2).png)

`kubectl describe pod` – Shows detailed info about a pod, like its status, events, and container setup.

![evolution of containers.png](D:\Kubernetes%20Learning\kube_screenshot\evolution%20of%20containers.png)

here earlier using virtual machines we ran  different different but this is also consuming lots of memory and space. so later we started using containers . containers using linux-namespaces different process are run  and isolation is maintained and to orchestrate we use kubernetes.

BECAUSE KUBERNETES IS HIGHLY EXTENSIBLE  FEATURES OF KUBERNETES LIKE AUTOSCALING, SELF-HEALING, MONITORING, SCHEDULING, FLEXIBILITY( HIGHLY EXTENSIBLE) ETC...

WHEN WE INSTALL CROSSPLANE ON KUBERNETES WE CAN CONNECT KUBERNETES WITH CLOUD

WHEN WE INSTALL KUBEVIRT ON KUBERNETES WE CAN ORCHESTRATE OR MANAGE VIRTUAL-MACHINES ALSO NOT ONLY CONTAINERS OF APPLICATION.

![why kubernetes.png](D:\Kubernetes%20Learning\kube_screenshot\why%20kubernetes.png)

![internals in container.png](D:\Kubernetes%20Learning\kube_screenshot\internals%20in%20container.png)

IN LINUX CONTAINERS LINUX-NAMESPACE LEVEL ISOLATION BETWEEN CONTAINERS

![kube config file parts.png](D:\Kubernetes%20Learning\kube_screenshot\kube%20config%20file%20parts.png)

![GVK GVR.png](D:\Kubernetes%20Learning\kube_screenshot\GVK%20GVR.png)

Create a file deploy.json

kubectl create deployment nginx --image=nginx --dry-run=client -o json > deploy.json
kubectl run nginx --image=nginx --dry-run=client -o json

```powershell
kubectl run demo --image=nginx --dry-run=client -oyaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: demo
  name: demo
spec:
  containers:
  - image: nginx
    name: demo
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}


kubectl create deploy demo --image=nginx --dry-run=client -oyaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: demo
  name: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: demo
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
```

OR

GVK : Group Version Kind
GVR : Group Version Resources

IN ABOVE COMMAND OUTPUT WE CAN SEE THAT DEPLOYMENT IS A KIND. WHEN WE RUN YAML FILE THEN IT IS KIND

AND WHEN IT IS IN REST-API CALL THEN IT IS RESOURCES (RESOUCE IS PLURAL OF KIND).

IN KUBERNETES THERE ARE LOTS OF GROUPS FOR EXAMPLE IN KUBERNETES THERE IS NETWORKING GROUP AND IN NETWORKING GROUP NETWORKING RELATED KUBERNETES OBJECTS COMES LIKE INGRESS, NETWORK-POLICY

POD IS IN CORE GROUP AND IT IS EMPTY.

DEPLOYMENT IS apps PART . apps IS GROUP AND IN KUBERNETES INSIDE GROUPS THERE ARE RESOURCES LIKE FROM ABOVE THE GIVEN apiVersion APPS IS GROUP
apiVersion: apps/v1

HERE FROM ABOVE apiVersion is only v1 BECAUSE IT IS CORE GROUP MEMBER. CORE GROUP MEANS CORE-SERVICES
apiVersion: v1

NO GROUP IN PODS. POD IS CORE GROUP MEMBER SO VERSION IS V1

SERVICE IS CORE GROUP MEMBER AND KIND IS SERVICE

RESOURCES (REST-API-ENDPOINT ) SERVICES POD

WHEN WE CREATE IN YAML FILE THEN IN kind WE DEFINE AS pod OR service NOT AS pods OR services

RESOURCES ARE PLURAL SO WHEN WE MAKE RESP-API CALLS THEN WE USE OR DEFINE AS i.e. deployments or services or pods

NETWORKING POLICY : GROUP IS NETWORKING(networking.k8s.io)
AND VERSION IS V1
RESOURCE IS NETWORK POLICIES

***

***

WHEN WE CREATE IN YAML FILE THEN IN kind WE DEFINE AS pod OR service NOT AS pods OR services

RESOURCES ARE PLURAL SO WHEN WE MAKE RESP-API CALLS THEN WE USE OR DEFINE AS i.e. deployments or services or pods

***

***

**<mark>YAML, POD, POD LIFECYCLE, INIT CONTAINERS, SIDECAR CONTAINERS</mark>**

![GOAl.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\GOAl.png)

![Imperative Declarative Way.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Imperative%20Declarative%20Way.png)

![understanding Yaml.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\understanding%20Yaml.png)

**YAML :**

![yaml.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\yaml.png)

![understanding yaml2.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\understanding%20yaml2.png)

**MULTI LINE STRINGS**

![multi-line string1.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\multi-line%20string1.png)

for multi line string we can use ">"  like above in mymessage : > multi line statement

**PIPE SYMBOL FOR MULTI LIN STRING**

![multi-line string2.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\multi-line%20string2.png)

multi line string statement with in pipe symbol 

**PLACEHOLDER**

![placeholder.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\placeholder.png)

between curly braces i.e. " {{ }} " Placeholder and  we use  " $ " for environment variable

**SEPARATE**

![separate.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\separate.png)

in yaml file we use separate i.e. "---" to separate two or three or more objects like for example in same yaml file we can create deployment, service, job, cron-job etc using separate.

**BOOLEAN**

![boolean.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\boolean.png)

for boolean values we use yes/no/true/false/on/off

**WORKLOAD IS PODS RUNNING IN NODE**

![kubectl yaml pod naming.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\kubectl%20yaml%20pod%20naming.png)

for pod we need container runtime like docker, containerd, cryo, podman etc in node

to run pod we need container runtime.inside pod single or multiple containers is possible.

we create pod using higher level kubernetes objects like deployment, service, job, statefulset, daemonset

pod is in core group that's why apiVersion is v1 only in yaml file for pod

every pod get their own ip-address we call it as cluster CIDR range.

**CIDR**

![cidr.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\cidr.png)

every pod get their own ip-address we call it as cluster CIDR range.

**LIST OF PODS WITH MORE DETAILS**

![getting pods in full details.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\getting%20pods%20in%20full%20details.png)

"kubectl get pods -owide -A " gives pod list with details

**DECLARATIVE SIMPLE POD YAML FILE EXAMPLE**

simple-pod.yaml :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
  labels:
    purpose: demonstrate-args
spec:
  containers:
  - name: example-container
    image: ubuntu
    command: ["/bin/echo", "Hello"]  
    args: ["Welcome", "to", "Kubesimplify"]
```

kubectl create -f  simple-pod.yaml

kubectl apply -f simple-pod.yaml

we can create only once but we can apply after creating it. or we can directly apply simple-pod.yaml file it will create and run both.

**POD LIFECYCLE**

![pod lifecycle.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\pod%20lifecycle.png)

pv means persistent volume

pvc means persistent volume claim

IN RUNNING LIFECYCLE IF ERROR OCCURS THEN ERROR LIFECYCLE BECOMES THE STATUS OF POD LIFECYCLE

**NAMESPACE**

![creating namespace.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\creating%20namespace.png)

```powershell
## LISTING NAMESPACE
PS C:\Users\prath> kubectl get ns
NAME                 STATUS   AGE
default              Active   26h
kube-node-lease      Active   26h
kube-public          Active   26h
kube-system          Active   26h
local-path-storage   Active   26h




controlplane:~$ kubectl create namespace bootcamp
namespace/bootcamp created
controlplane:~$ kubectl get ns
NAME                 STATUS   AGE
bootcamp             Active   7s
default              Active   11d
kube-node-lease      Active   11d
kube-public          Active   11d
kube-system          Active   11d
local-path-storage   Active   11d



controlplane:~$ kubectl get pod --namespace bootcamp
No resources found in bootcamp namespace.
controlplane:~$ kubectl get pod -n bootcamp
No resources found in bootcamp namespace.
controlplane:~$ kubectl create deploy mynginx --image=nginx --dry-run=client
deployment.apps/mynginx created (dry run)
controlplane:~$ kubectl run mynginx --image=nginx --dry-run=client
pod/mynginx created (dry run)
controlplane:~$ kubectl get pods
No resources found in default namespace.
controlplane:~$ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   11d   v1.33.2
node01         Ready    <none>          11d   v1.33.2
controlplane:~$ kubectl get deployment
No resources found in default namespace.
controlplane:~$ kubectl run mynginx --image=nginx
pod/mynginx created
controlplane:~$ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   11d   v1.33.2
node01         Ready    <none>          11d   v1.33.2
controlplane:~$ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
mynginx   1/1     Running   0          11s
controlplane:~$ kubectl get pods -n bootcamp
No resources found in bootcamp namespace.
controlplane:~$ kubectl run mynginx --image=nginx -n bootcamp
pod/mynginx created
controlplane:~$ kubectl get pods -n bootcamp
NAME      READY   STATUS    RESTARTS   AGE
mynginx   1/1     Running   0          4s
controlplane:~$ kubectl get pods -A
NAMESPACE            NAME                                      READY   STATUS    RESTARTS      AGE
bootcamp             mynginx                                   1/1     Running   0             17s
default              mynginx                                   1/1     Running   0             102s
kube-system          calico-kube-controllers-fdf5f5495-8jbqm   1/1     Running   2 (18m ago)   11d
kube-system          canal-5q8x5                               2/2     Running   2 (18m ago)   11d
kube-system          canal-hvvtk                               2/2     Running   2 (18m ago)   11d
kube-system          coredns-6ff97d97f9-gq4nd                  1/1     Running   1 (18m ago)   11d
kube-system          coredns-6ff97d97f9-hcn7j                  1/1     Running   1 (18m ago)   11d
kube-system          etcd-controlplane                         1/1     Running   2 (18m ago)   11d
kube-system          kube-apiserver-controlplane               1/1     Running   2 (18m ago)   11d
kube-system          kube-controller-manager-controlplane      1/1     Running   2 (18m ago)   11d
kube-system          kube-proxy-7kdz8                          1/1     Running   2 (18m ago)   11d
kube-system          kube-proxy-lg8cx                          1/1     Running   1 (18m ago)   11d
kube-system          kube-scheduler-controlplane               1/1     Running   2 (18m ago)   11d
local-path-storage   local-path-provisioner-5c94487ccb-gmwjg   1/1     Running   2 (18m ago)   11d
controlplane:~$ kubectl get pods --namespace bootcamp
NAME      READY   STATUS    RESTARTS   AGE
mynginx   1/1     Running   0          66s
```

**OBSERVER kubectl descibe pod**

![events cycle.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\events%20cycle.png)

Observer here scheduler assigns workload to best fit worker node.

and kublet handles the rest together kube-proxy

KUBECTL LOG AND DESCRIBE :

```powershell
kubectl describe mynginx(pod name)

kubectl logs mynginx


## THIS WILL DO FOLLOW UP LOG TO CHECK AND DEBUG
kubectl logs -f mynginx


kubectl get pods
kubectl get pods -owide


kubectl get pods -oyaml
```

**INIT CONTAINERS :**

![inti containers.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\inti%20containers.png)

![init containers 1.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\init%20containers%201.png)

### 🧠 Simple Definition

**Init containers** are special containers inside a Pod that:

- Run **first**, one at a time.

- Do **setup tasks** like waiting for a database, pulling config files, or setting permissions.

- Must **finish successfully** before your main app container starts.

They’re perfect for jobs that need to be done **once**, before your app runs.

### 📦 Real-Life Analogy

Imagine you're launching a web app:

- You need to **wait for the database** to be ready.

- You want to **download some config files** from GitHub.

Instead of putting all that logic inside your main app container, you use **init containers** to handle it cleanly.

🧾 Example YAML (Annotated for Memory)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  initContainers:
  - name: wait-for-db
    image: busybox
    command: ['sh', '-c', 'until nc -z db-service 5432; do echo waiting; sleep 2; done;']
  - name: fetch-config
    image: alpine/git
    command: ['git', 'clone', 'https://github.com/myorg/configs.git', '/app/config']
  containers:
  - name: myapp
    image: myapp:latest
    ports:
    - containerPort: 80
```

🧩 **What’s happening here:**

- `wait-for-db`: waits until the database is reachable.

- `fetch-config`: clones config files from GitHub.

- Only after both finish, the main app (`myapp`) starts.

**EXAMPLE OF CONFIGMAP SHOWING  HOW TO SETUP WITH '>' MULTI-LINE MESSAGE**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
 name: example-configmap-folded
data:
 mymessage: >
   Hello, this is a folded
   multi-line message.
```

**EXAMPLE OF DECLARATIVE JOB :**

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: nginx-job
spec:
  template:
    spec:
      containers:
      - name: nginx
        image: nginx
        command: ["sleep", "5"]  
      restartPolicy: OnFailure  
  backoffLimit: 4 
```

WHEN WE DECLARE HIGHER SECTION OF KUBERNETES OBJECTS LIKE DEPLOYMENT, JOB, POD, SERVICE, DAEMONSET, STATEFULSET THERE IN 'spec' SECTION  WE SPECIFY 'template' INSIDE spec. YOU CAN SEE IN THE ABOVE GIVEN EXAMPLE.

INSIDE TEMPLATE SECTION THERE IS 'spec' SECTION WHERE WE DEFINE CONTAINERS.

**EXAMPLE OF INIT CONTAINER :**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bootcamp-pod
spec:
  volumes:
  - name: shared-data
    emptyDir: {}
  initContainers:
  - name: bootcamp-init
    image: busybox
    command: ['sh', '-c', 'wget -O /usr/share/data/index.html http://kubesimplify.com']
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/data
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
```

This is an example of Init-Container . here we are creating simple pod and its name is bootcamp-pod. Inside we specified emptyDir and its name is shared-data imagine there is a filesystem and we create a directory and now two or more containers are there and now they can access it mount it using mountPath inside the container's volumeMounts section with name specified and mountPath.

under emptyDir two or more containers can access same directory.

This init-container is fetching " [http://kubesim](http://kubesim)"  and output is stored into index.html file which is kept in /usr/share/data folder and the mounted path is shared-data which is emptyDir which containers has shared access.

### multiple init container

mymulti-initcontainer.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo-2
spec:
  initContainers:
  - name: check-db-service
    image: busybox
    command: ['sh', '-c', 'until nslookup db.default.svc.cluster.local; do echo waiting for db service; sleep 2; done;']
  - name: check-myservice
    image: busybox
    command: ['sh', '-c', 'until nslookup myservice.default.svc.cluster.local; do echo waiting for myservice service; sleep 2; done;']
  containers:
  - name: main-container
    image: busybox
    command: ['sleep', '3600']
```

kubectl apply -f mymulti-initcontainer.yaml

kubectl get pods ::

pod init-demo-2 is not running as two init-containers are checking for two services 1. db service and 2. myservice as soon as these two services verifies that these two services are running then the two init -containers will check for running two services and if two services are running then main-container will start and running thus showing pod init-demo-2 as in running status.

```powershell
controlplane:~$ kubectl get pods
NAME           READY   STATUS     RESTARTS   AGE
bootcamp-pod   1/1     Running    0          6m24s
init-demo-2    0/1     Init:0/2   0          31s
```

kubectl describe pod init-demo-2

```powershell
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  41s   default-scheduler  Successfully assigned default/init-demo-2 to node01
  Normal  Pulling    40s   kubelet            Pulling image "busybox"
  Normal  Pulled     39s   kubelet            Successfully pulled image "busybox" in 782ms (782ms including waiting). Image size: 2223685 bytes.
  Normal  Created    39s   kubelet            Created container: check-db-service
  Normal  Started    39s   kubelet            Started container check-db-service
```

as we can see that init-demo-2 has not started yet because inside inti-demo-2 two init-containers are there which is checking up for two services first 1. db service and 2. myservice and if service is not found then sleep for 2 second and then two init-containers check again for running services so unless and until these two init-containers find the services running the two init-containers will not complete and that is why main-container has not started yet.

myservices.yaml file:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: db
spec:
  selector:
    app: demo1
  ports:
  - protocol: TCP
    port: 3306
    targetPort: 3306
---
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  selector:
    app: demo2
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

kubectl apply -f myservice.yaml 

as soon as we run the above services db and myservice will run thus the above mentioned two inti-containers  i.e. 1. check-db-service 2. check-myservice wil complete and thus the main-container will start.

Thus showing status of pod as running in init-demo-2

```powershell
controlplane:~$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
bootcamp-pod   1/1     Running   0          33m
init-demo-2    1/1     Running   0          27m
```

### SIDECAR container pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  volumes:
  - name: shared-data
    emptyDir: {}
  initContainers:
  - name: meminfo-container
    image: alpine
    restartPolicy: Always
    command: ['sh', '-c', 'sleep 5; while true; do cat /proc/meminfo > /usr/share/data/index.html; sleep 10; done;']
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/data
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
```

*Officially in kubernetes 1.29 in sidecar containers we can set restartPolicy Always in initContainers.*

🧳 What Is a Sidecar Container?

a **sidecar container** like a **helper buddy** riding alongside the main container in the same Pod. It runs **at the same time** as the main container and **shares the same network and storage**.

the sidecar (helper container) rides alongside, doing useful tasks like logging, proxying, or syncing data.

✅ Sidecar Container Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-sidecar
spec:
  containers:
    - name: main-app
      image: my-app:latest
      ports:
        - containerPort: 8080
    - name: log-agent
      image: fluentd:latest
      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/app
  volumes:
    - name: shared-logs
      emptyDir: {}
```

🔹`main-app`: Your core application
🔹 `log-agent`: Sidecar that collects logs from the shared volume

## 🧨 How Is It Different from an Init Container?

| Feature               | **Sidecar Container**                         | **Init Container**                              |
| --------------------- | --------------------------------------------- | ----------------------------------------------- |
| **Runs When**         | Runs **alongside** main container             | Runs **before** main container starts           |
| **Purpose**           | Supports runtime tasks (logging, proxy, etc.) | Prepares environment (setup, checks, downloads) |
| **Lifecycle**         | Lives as long as the Pod                      | Exits after its job is done                     |
| **Access to Volumes** | ✅ Shared volumes                              | ✅ Shared volumes                                |
| **Networking**        | ✅ Shares Pod IP                               | ✅ Shares Pod IP                                 |

SERVICE MESH :

sidecar container Usecases::

1. Authentication

2. Mutual TLS

3. HealthCheck

4. Tracing

5. Metrics

6. Security

![Service Mesh.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Service%20Mesh.png)

![SideCar Container.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\SideCar%20Container.png)

USE_CASES of SideCar Containers like as logging, Proxy, Metrics collector

![sidecar container usecases.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\sidecar%20container%20usecases.png)

Concept of Service Mesh Implementing other features like authentication, mutual tls, healthcheck, tracing, metrics, security

![sidecar container as proxy.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\sidecar%20container%20as%20proxy.png)

main-container A communicates with main-container B through proxy.  Request comes from user to Kubernetes to sidecar then authenticates, Then main-container A communicates sidecar-a and sidecar-a communicates to sidecar-b and then communicates to sidecar-b communicates to main-container B . this way security is enhanced.

THREE TYPE OF PATTERNS :

1. Sidecar container

2. Ambassador Pattern

3. Adapter Pattern

![service mesh does.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\service%20mesh%20does.png)

Before 1.29 This was like this with the sidecar container:

![Problem With this SideCar Container.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Problem%20With%20this%20SideCar%20Container.png)

Problem is that there is no sequence i.e. what if main-container starts first and does the job and finish job and complete the task and exit but still the sidecar container has not started yet which is suppose to run along and collect the metrics or log or anything as supporting job then its a problem.

### Multi container pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  volumes:
  - name: shared-data
    emptyDir: {}
  containers:
  - name: meminfo-container
    image: alpine
    restartPolicy: Always
    command: ['sh', '-c', 'sleep 5; while true; do cat /proc/meminfo > /usr/share/data/index.html; sleep 10; done;']
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/data
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
```

here sidecar container reads memory info and output into html file and mounts into /usr/share/data and thus main-container nginx-container has the html file we can see that file then.

![problem with sidecar container1.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\problem%20with%20sidecar%20container1.png)

![problem with sidecar container2.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\problem%20with%20sidecar%20container2.png)

![problem with sidecar container3.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\problem%20with%20sidecar%20container3.png)

![SideCar container first class citizen.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\SideCar%20container%20first%20class%20citizen.png)

![4 container running in pod.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\4%20container%20running%20in%20pod.png)

![4 container running in pod1.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\4%20container%20running%20in%20pod1.png)

HERE FOUR CONTAINER ARE RUNNING IN THE POD FIRST TWO CONTAINER RAN AND EXIT AND THIRD CONTAINER RUN AND RESTART AGAIN AND RUNNING THUS SUPPORTING AND FOURTH CONTAINER IS RUNNING 

### 📘 Kubernetes Workload Object Structure Cheat Sheet

| **Kind**      | **API Version** | **Pod Template Path**                            | **Notes**                                                     |
| ------------- | --------------- | ------------------------------------------------ | ------------------------------------------------------------- |
| `Deployment`  | `apps/v1`       | `spec.template.spec.containers`                  | Supports rolling updates, replica sets                        |
| `StatefulSet` | `apps/v1`       | `spec.template.spec.containers`                  | Adds stable network IDs, persistent storage                   |
| `DaemonSet`   | `apps/v1`       | `spec.template.spec.containers`                  | Ensures one Pod per node                                      |
| `Job`         | `batch/v1`      | `spec.template.spec.containers`                  | Runs to completion, one-off tasks                             |
| `CronJob`     | `batch/v1`      | `spec.jobTemplate.spec.template.spec.containers` | 🧠 Extra nesting: wraps a Job template inside a Cron schedule |

![Kuberenetes Object Structure.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Kuberenetes%20Object%20Structure.png)

![Kubernetes Object Structure1.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Kubernetes%20Object%20Structure1.png)

![Kubernetes Object Structure2.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\Kubernetes%20Object%20Structure2.png)

### 🧠 Mnemonic for Quick Recall

> **D-S-D-J** → `spec.template.spec.containers` 
> 
> **C**ronJob → `spec.jobTemplate.spec.template.spec.containers`

**visual YAML reference card that compares the core structure of Kubernetes workload objects, expanded with common fields like `restartPolicy`, `volumes`, and `initContainers`.**

## 🧾 Kubernetes Workload Reference Card (Expanded)

### ✅ Deployment / StatefulSet / DaemonSet / Job

![visual reference deployment job statefulset daemonset.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\visual%20reference%20deployment%20job%20statefulset%20daemonset.png)

**🕒 CronJob (Extra Nesting)**

![visual reference of cronjob.png](D:\Kubernetes%20Learning\kube_screenshot\YAML,%20POD,%20POD%20LIFECYCLE,%20INIT%20CONTAINERS,%20SIDECAR%20CONTAINERS\visual%20reference%20of%20cronjob.png)

## 🧠 Quick Reference Table

| **Kind**    | **Pod Template Path**                            | **restartPolicy** | **initContainers** | **volumes** |
| ----------- | ------------------------------------------------ | ----------------- | ------------------ | ----------- |
| Deployment  | `spec.template.spec.containers`                  | `Always`          | ✅ Supported        | ✅ Supported |
| StatefulSet | `spec.template.spec.containers`                  | `Always`          | ✅ Supported        | ✅ Supported |
| DaemonSet   | `spec.template.spec.containers`                  | `Always`          | ✅ Supported        | ✅ Supported |
| Job         | `spec.template.spec.containers`                  | `OnFailure/Never` | ✅ Supported        | ✅ Supported |
| CronJob     | `spec.jobTemplate.spec.template.spec.containers` | `OnFailure/Never` | ✅ Supported        | ✅ Supported |

*****

***

****

<mark>SIDECAR CONTAINER, QOS, PDB, REQUEST_LIMIT</mark>

USECASE OF SIDECAR CONTAINER ::

OBSERVE ServiceAccount created, Role with Pod get and watch permission as access is created and allowed. RoleBinding to bind the role and serviceAccount.

```yaml
# This is the identity the Pods will run as.
apiVersion: v1
kind: ServiceAccount
metadata:
  name: demo-kubectl-sidecar
  namespace: default
---
# This defines the namespace-scope permissions granted.
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: demo-kubectl-sidecar
  namespace: default
rules:
- apiGroups:
  - ''
  resources:
  - pods
  verbs:
  - get
  - watch
---
# This joins the ServiceAccount to the Role above.
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: demo-kubectl-sidecar
  namespace: default
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: demo-kubectl-sidecar
subjects:
- kind: ServiceAccount
  name: demo-kubectl-sidecar
---
# This defines the cluster-scope permissions granted.
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: demo-kubectl-sidecar
rules:
- apiGroups:
  - ''
  resources:
  - nodes
  verbs:
  - get
  - watch
---
# This joins the ServiceAccount to the ClusterRole above.
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: demo-kubectl-sidecar
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: demo-kubectl-sidecar
subjects:
- kind: ServiceAccount
  name: demo-kubectl-sidecar
  namespace: default
---
# This is the actual workload.
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-kubectl-sidecar
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-kubectl-sidecar
  template:
    metadata:
      labels:
        app: demo-kubectl-sidecar
    spec:
      serviceAccountName: demo-kubectl-sidecar
      securityContext:
        # Set this to any valid GID, and two things happen:
        #   1) The volume "content" is group-owned by this GID.
        #   2) This GID is added to each container.
        fsGroup: 9376
      containers:
      - name: server
        image: nginx
        volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: content
          readOnly: true
      initContainers:
      - name: sidecar
        image: thockin/kubectl-sidecar:v1.31.3-1
        restartPolicy: Always
        env:
          - name: MYPOD
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: MYNS
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
          - name: MYNODE
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
        args:
          - bash
          - -c
          - |
            while true; do
              kubectl -n $MYNS get pod $MYPOD -o json | jq '.status' > /data/this-pod-status.json
              kubectl get node $MYNODE -o json | jq '.status' > /data/this-node-status.json
              sleep 30
            done
        volumeMounts:
        - name: content
          mountPath: /data
        securityContext:
          # This doesn't need to run as root.
          runAsUser: 9376
          runAsGroup: 9376
      volumes:
      - name: content
        emptyDir: {}
      terminationGracePeriodSeconds: 5
```

```dockerfile
# Copyright 2024, Tim Hockin
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Debian is very standard but has lots of tools.
# Alpine is less expansive, but smaller.
FROM debian AS base

# We can be sloppy about layers because this is a multi-stage build.
RUN apt-get -y -qq -o Dpkg::Use-Pty=0 update
RUN apt-get -y -qq -o Dpkg::Use-Pty=0 -y upgrade

# Install the tools you need
RUN apt-get -y -qq -o Dpkg::Use-Pty=0 -y install \
    bash \
    grep \
    sed \
    gawk \
    coreutils \
    jq \
    diffutils
RUN rm -rf /var/lib/apt/lists/*

# Update this as needed.
FROM registry.k8s.io/kubernetes/kubectl:{ARG_VERSION} AS kubectl

FROM scratch

COPY --from=base / /
COPY --from=kubectl /bin/kubectl /bin/kubectl

# This image has no ENTRYPOINT or CMD.
```

copying base as debian and updating and upgrading and installing important tools needed and then  from another base kubectl copying kubectl binary and creating image with it.

```powershell
kubectl port-forward deployment/demo-kubectl-sidecar 8080:80
curl http://localhost:8080/this-pod-status.json
curl http://localhost:8080/this-node-status.json
```

## Pause container

In Kubernetes when you launch a pod, there is also a pause container that gets spinned up. You can find the pause container file

```yaml
ctr --namespace k8s.io containers list | grep pause
```

You're running a `ctr` command to list containers in the `k8s.io` namespace and filter for the `pause` container, which is typically used by Kubernetes to hold the network namespace for pods.

- `ctr`: CLI for containerd.

- `--namespace k8s.io`: Targets the Kubernetes namespace within containerd.

- `containers list`: Lists all containers.

- `grep pause`: Filters for the `pause` container, which is used by Kubernetes to maintain pod networking.

Pause Container holds network namespace and does IP-address presservation

and helps in interaction between the containers within the pod and also helps in lifecycle management of pod, forwards signal from one container to another container.

pause container also tells activity of pod i.e. if pause container is running then it means our pod is active even if the container inside pod has died . pod is active because pause-container is running.

In ideal scenario when container inside the pod restarts i.e. container restarts then in ideal scenario ip-address changes but here in this case ip-address of the pod stays same i.e. ip-address does not change and this happens because of pause-container.

every pod gets their own ip-address and inside pod a container is running and container has also ip-address and when container dies and restart in the kubernetes then ip-address of the pod doesn't change.

pause containers listens signals of SIGTERM, SIGINT, SIGCHILD

***

***

**🛡️ Kubernetes User Namespace – Simplified Explanation**

### 🔍 What is a User Namespace?

A **User Namespace** in Kubernetes allows containers to run as **root inside the pod**, but be mapped to a **non-root user on the host**. This isolates container privileges from the host system.

### 🧠 Key Concepts (Simplified)

- **Inside the Pod:**
  
  - Containers often run as **root (UID 0)** by default.
  
  - There may be multiple user/group IDs (UIDs/GIDs) used inside the container.

- **On the Host:**
  
  - With **User Namespace**, these container UIDs/GIDs are **mapped to different, non-root IDs** on the host.
  
  - This means even if a container runs as root inside, it has **limited permissions** on the host.

- **Isolation Benefit:**
  
  - Prevents containers from accessing sensitive host resources.
  
  - If an attacker breaks into a container, they only get access to a **restricted user space**, not full host privileges.

### 🆕 What’s New in Kubernetes v1.30?

- You can now **define custom UID/GID ranges** for pods.

- Each pod can be assigned a **unique user/group mapping** on the host.

- This improves **multi-tenant security** and **fine-grained access control**.

### 🧾 Analogy with Linux

Just like in Linux:

- You create **users and groups**.

- Assign **permissions** to files and processes.

In Kubernetes:

- Each pod has a **user namespace map**.

- This map controls what the container user can access **on the host**.

### ✅ Why It Matters

| Feature                             | Benefit                                             |
| ----------------------------------- | --------------------------------------------------- |
| Root in container, non-root on host | Limits host access even if container is compromised |
| Custom UID/GID mapping              | Better isolation between pods                       |
| Namespace map                       | Fine-grained control over permissions               |
| Security hardening                  | Reduces risk of privilege escalation                |

### 🧠 Easy Mnemonic to Remember

**"Root in Pod ≠ Root on Host"**

> Even if the pod thinks it's powerful, the host sees it as a harmless guest.

**🧾 Example: Pod with Custom UID/GID Mapping**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: userns-demo
spec:
  hostUsers: false  # 👈 Enables user namespace isolation
  securityContext:
    runAsUser: 1000       # 👤 UID inside the container
    runAsGroup: 3000      # 👥 GID inside the container
    fsGroup: 2000         # 📁 Group ID for mounted volumes
    supplementalGroups:   # ➕ Additional group IDs
      - 4000
  containers:
    - name: demo-container
      image: busybox
      command: ["sh", "-c", "id && sleep 3600"]
      securityContext:
        allowPrivilegeEscalation: false  # 🚫 Prevent privilege escalation
```

### 🧠 Quick Summary

- Inside the pod, processes run as UID 1000 and GID 3000.

- On the host, these are mapped to different, non-root IDs.

- Even if the container thinks it’s root, the host sees it as a restricted user.

- This limits damage if the container is compromised.

### 🔍 Key Fields Explained

| Field                      | Purpose                                                                |
| -------------------------- | ---------------------------------------------------------------------- |
| `hostUsers: false`         | Enables user namespace mapping (required for custom UID/GID isolation) |
| `runAsUser`                | Sets the UID for processes inside the container                        |
| `runAsGroup`               | Sets the primary GID for the container                                 |
| `fsGroup`                  | Applies GID to mounted volumes                                         |
| `supplementalGroups`       | Adds extra group IDs for access control                                |
| `allowPrivilegeEscalation` | Prevents container from gaining extra privileges                       |

**<mark>🚨 Pod Disruptions in Kubernetes – Simplified Guide</mark>**

Kubernetes pods can be disrupted in two ways:

### 1️⃣ **Voluntary Disruptions**

These are planned actions initiated by users or controllers.

**Examples:**

- Updating or deleting a Deployment

- Manually deleting a Pod

- Draining a node (`kubectl drain node01 --ignore-daemonsets`)

- Cluster autoscaling or upgrades

> ⚠️ **Important:** 
> Deleting a Deployment or Pod **bypasses** the Pod Disruption Budget (PDB). So even if a PDB is defined, it won’t protect against manual deletions.

### 2️⃣ **Involuntary Disruptions**

These are unexpected failures or accidents.

**Examples:**

- Hardware failure or power outage

- Cloud provider backup failure or natural disaster

- Cluster crash or accidental VM deletion

- Kernel panic or resource exhaustion

> 🧠 **Note:** 
> PDBs **do not protect** against involuntary disruptions. They only apply to **voluntary evictions**.

## 

## 🛡️ How to Mitigate Involuntary Disruptions

To maintain high availability:

- ✅ **Increase pod replicas** 
  More replicas = more resilience.

- ✅ **Use anti-affinity rules** 
  Spread pods across nodes so one failure doesn’t take down all replicas.

- ✅ **Specify resource requests** 
  Ensures pods get scheduled on nodes with enough capacity.

### 🧠 Anti-Affinity Example

Imagine a cluster with 4 nodes.
You create a Deployment with 4 replicas and set anti-affinity rules.
Kubernetes will try to place **one replica per node**.
If 3 nodes fail, **at least one pod remains running**, ensuring service continuity.

## 📊 Pod Disruption Budget (PDB)

A **PDB** defines how many pods can be disrupted **at the same time** during voluntary actions.

**PodDisruptionBudget (PDB):** Ensures minimum number of pods available during upgrades.

EXAMPLE YAML :

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 2  # At least 2 pods must be running
  selector:
    matchLabels:
      app: my-app
```

If your Deployment has 3 replicas and `minAvailable: 2`, then **only 1 pod** can be evicted at a time.

### 🧠 Why PDB Matters

Without a PDB:

- A node drain could evict all pods at once.

- If traffic is high, this could cause **service downtime**.

With a PDB:

- Kubernetes checks if eviction violates the budget.

- If it does, it **blocks the eviction**:

## 🔁 RollingUpdate Strategy

When updating a Deployment:

- `maxUnavailable: 25%` → Max pods that can be down during update

- `maxSurge: 25%` → Extra pods that can be created temporarily

> 🎯 **RollingUpdate works with PDB** to ensure updates don’t break availability.

## 🧠 Easy Mnemonics to Remember

- **PDB = Pod Disruption Barrier** 
  Prevents too many pods from going down at once.

- **Anti-Affinity = Spread to Survive** 
  One pod per node = better fault tolerance.

- **Voluntary = Planned** | **Involuntary = Disaster**

- **SIGTERM = Soft Goodbye** 
  Eviction sends SIGTERM before pod shuts down.

![PDB1.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\PDB1.png)

![PDB2.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\PDB2.png)

![PDB3.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\PDB3.png)

DEPLOYMENT.YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

PDB.YAML:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: nginx-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: nginx
```

```powershell
kubectl delete deploy deployment-name

kubectl describe deploy deployment-nam
e
# this command is to update nginx image from nginx:1.14.2
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1

kubectl get pods -w

kubectl get pods

kubectl describe deploy deployment-name

kubectl get pdb

kubectl delete -f pdb.yaml

after updating 2 to 3 minimumAvailable
kubectl apply -f pdb.yaml



kubectl apply -f deploy.yaml

kubectl get deploy

kubectl get pods


This will also tells where or on which node it is running
kubectl get pods -owide

kubectl get nodes

kubectl drain node01(this node01 we got from above command kubectl get nodes)


kubectl drain node01 --ignore-daemonset
```

## 🧠 What’s Happening?

When you run:

```yaml
kubectl drain node01 --ignore-daemonsets
```

Kubernetes:

- **Evicts all pods** from `node01` (except daemonsets).

- **Marks node01 as unschedulable**, meaning **no new pods will be scheduled** on it.

Your pods are pending because:

- The scheduler is trying to place them.

- But `node01` is **cordoned** (unschedulable).

- And there may be **no other available nodes** with enough resources.

## ✅ How to Fix It

### 1️⃣ **Uncordon the Node**

Allow scheduling again:

```yaml
kubectl uncordon node01
```

This will make `node01` schedulable again, and your pending pods should start running—assuming it has enough resources.

### 2️⃣ **Check Node Status**

Verify that the node is ready:

```yaml
kubectl get nodes
```

Look for `STATUS = Ready`. If it’s `NotReady`, the node might be down or misconfigured.

### 3️⃣ **Check Pod Events**

Inspect why pods are pending:

```yaml
kubectl describe pod <pod-name>
```

Look at the **Events** section. Common issues:

- `Insufficient CPU` or `memory`

- `NodeSelector` or `Taints` mismatch

- `PersistentVolumeClaim` not bound

4️⃣ **Check Resource Availability**

```yaml
kubectl describe node node01
```

Look at `Allocatable` vs `Used` resources. If the node is low on CPU/memory, pods won’t schedule.

## 🧠 Quick Recap

| Action                  | Result                                                |
| ----------------------- | ----------------------------------------------------- |
| `kubectl drain`         | Evicts pods + marks node unschedulable                |
| `kubectl uncordon`      | Makes node schedulable again                          |
| Pods stuck in `Pending` | Likely due to unschedulable node or lack of resources |
| Use `describe pod`      | To find exact reason for pending status               |

**<mark>⚙️ RESOURCE REQUESTS & LIMITS – SIMPLIFIED</mark>**

### 🔹 What Are They?

- **Request** = Minimum CPU/Memory guaranteed to the container.

- **Limit** = Maximum CPU/Memory the container is allowed to use.

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

### 🧠 Why They Matter

- **Requests** are used by the **scheduler** to place pods on nodes with enough resources.

- **Limits** are enforced at runtime by the **kubelet** and **Linux kernel (via cgroups)**.

## 🧮 RESERVED CPU – EXPLAINED

Reserved CPU = The CPU amount guaranteed to a container via its **request**.

### 🔧 Example:

If you set `cpu: "500m"` as a request, Kubernetes reserves **0.5 CPU cores** on the node for that container. Even if the container is idle, that CPU is blocked for others.

## 🧠 CPU Throttling (Linux CFS)

- If a container tries to use more CPU than its **limit**, Linux's **Completely Fair Scheduler (CFS)** kicks in.

- It **throttles** the container—allowing short bursts, then slowing it down.

- This prevents noisy neighbors from hogging CPU.

NOISY NEIGHBOUR LIKE IN A NODE ONE APP IS USING MORE CPU OR CPU UTILIZATION KEEP ON INCREASING OR ANOTHER APP NEEDS MORE CPU THERE DEFINING LIMIT TO CPU IS GOOD

## 📊 OVER vs UNDER UTILIZATION

| Scenario          | Description                                |
| ----------------- | ------------------------------------------ |
| Over-utilization  | App uses 90% of 100MB request consistently |
| Under-utilization | App only needs 100MB but you give 1GB      |
| Result            | Wasted resources → higher cost             |

* IF RESOURCE REQUEST SUPPOSE OUR APPLICATION IS SMALL SO IT NEEDS 100MB BUT IT USES 80%-90% ALWAYS THEN WE ARE OVER-UTILIZE.
  SUPPOSE WE KNOW OUR APPLICATION WON'T USE MUCH, ONLY 100MB IS IT ALL NEEDED BUT WE GIVE 1GB THEN WE CALL IT UNDER-UTILIZATION.

* SUPPOSE YOU ASSIGNED 2 CPU AND 1GB MEMORY SCHEDULED WITH 3 REPLICAS BUT YOU ARE NOT USING CPU AND MEMORY RESERVED THIS WAY IT IS WASTING AND BECOMES COSTLY INCREASING COST BECAUSE THE MORE REPLICAS YOU CREATED THE CPU AND MEMORY IS GETTING WASTED THIS WAY COSTING IS INCREASING AND THEN WE NEED MORE NODES THEN MORE VIRTUAL RESOURCES ARE USED THIS WAY COST IS INCREASING

* WE SHOULD ALWAYS DEFINE RESOURCE REQUEST/LIMIT

## 🧠 QoS CLASSES – QUALITY OF SERVICE

| Class          | When It Applies                              | Eviction Priority |
| -------------- | -------------------------------------------- | ----------------- |
| **Guaranteed** | Request = Limit for both CPU & Memory        | ❌ Least likely    |
| **Burstable**  | Only one of CPU/Memory has request/limit set | ⚠️ Medium risk    |
| **BestEffort** | No request or limit defined                  | ✅ First to evict  |

> 🎯 In production, always define **both request and limit** for CPU & Memory to ensure **Guaranteed QoS**.

* WHEN CPU AND MEMORY RESOURCE REQUEST IS EQUAL TO LIMIT THEN IT IS GURANTEED QUALITY OF SERVICES CLASS

* WHEN CPU/MEMORY RESOURCE REQUEST/LIMIT DEFINED (ANYONE NOT DEFINED OR ONE IS NOT DEFINED AND OTHER IS DEFINED)  THEN IT IS BURSTABLE QUALITY OF SERVICES CLASS 

* WHEN CPU AND MEMORY RESOURCE REQUEST AND LIMIT NOT DEFINED THEN IT BEST EFFORT QUALITY OF SERVICES CLASS

***<u>IMPORTANT NOTES:</u>***

WHEN K8's RUN OUT OF RESOURCES THE EVICT FIRST BEST EFFORT, THEN BURSTABLE, THEN GURANTEED DURING POD EVICTION ON THE BASIS OF QUALITY OF SERVICES POD EVICT OCCURS LIKE WHEN NODE DRAIN.

MEANING WHENEVER NODE COMES UNDER PRESSURE THEN GURANTEED QUALITY OF SERVICES RESOURCE WILL NOT BE EVICTED. THAT IS WHY WE MUST DEFINE MEMORY AND CPU RESOURCE REQUEST LIMIT

SCHEDULER HAS TO EVICT POD AND POD EVICTION IS DONE BASED ON QUALITY OF SERVICES.

IN PRODUCTION WE SHOULD SPECIFY RESOURCE REQUEST LIMIT OR ELSE WE SHOULD SPECIFY LIMITRANGE FOR NAMESPACE 

## 🧾 LimitRange – Namespace Defaults

Use `LimitRange` to enforce default resource settings in a namespace.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: example-limitrange
  namespace: default
spec:
  limits:
  - type: Container
    default:
      cpu: "300m"
      memory: "200Mi"
    defaultRequest:
      cpu: "200m"
      memory: "100Mi"
```

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: example-limitrange
  namespace: default
spec:
  limits:
  - type: Pod
    max:
      cpu: "2"
      memory: "1Gi"
    min:
      cpu: "200m"
      memory: "100Mi"
  - type: Container
    max:
      cpu: "1"
      memory: "500Mi"
    min:
      cpu: "100m"
      memory: "50Mi"
    default:
      cpu: "300m"
      memory: "200Mi"
    defaultRequest:
      cpu: "200m"
      memory: "100Mi"
```

Above LimitRange is set up for default namespace.

### 🔍 Key Points:

- `limits` is now a **list** (`- type: Container`)

- All resource settings (`default`, `defaultRequest`, `min`, `max`) are nested under that list item

## 🧠 Quick Tip to Remember

> **LimitRange = list of rules per resource type** 
> Always use `- type: Container` or `- type: Pod` under `limits`

## 📦 Downward API – Self-Awareness for Pods

Allows containers to access their own metadata without querying the API server.

DOWNWARD API 
IT IS SOMETIMES USEFUL FOR A CONTAINER TO HAVE INFORMATION ABOUT ITSELF WITHOUT BEING OVERLY COUPLED TO KUBERNETES. THE DOWNWARD API ALLOWS CONTAINERS TO CONSUME INFORMATION ABOUT THEMSELVES OR THE CLUSTER WITHOUT USING THE KUBERNETES CLIENT OR API SERVER

```yaml
env:
  - name: MY_NODE_NAME
    valueFrom:
      fieldRef:
        fieldPath: spec.nodeName
```

EXAMPLE : downwardapipod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-envars-fieldref
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      env:
        - name: MY_NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: MY_POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: MY_POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: MY_POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: MY_POD_SERVICE_ACCOUNT
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName
  restartPolicy: Never
```

Other useful fields:

- `metadata.name` → Pod name

- `metadata.namespace` → Namespace

- `status.podIP` → Pod IP

- `spec.serviceAccountName` → Service account

My Practical with DownwardApi ::

```powershell
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl apply -f downwardapipod.yaml 
pod/nginx-envars-fieldref created
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl get pods
NAME                    READY   STATUS              RESTARTS   AGE
nginx-envars-fieldref   0/1     ContainerCreating   0          6s
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl exec -it nginx-envars-fieldref printenv
error: exec [POD] [COMMAND] is not supported anymore. Use exec [POD] -- [COMMAND] instead
See 'kubectl exec -h' for help and examples
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl exec -it nginx-envars-fieldref -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx-envars-fieldref
NGINX_VERSION=1.29.1
NJS_VERSION=0.9.1
NJS_RELEASE=1~bookworm
PKG_RELEASE=1~bookworm
DYNPKG_RELEASE=1~bookworm
MY_NODE_NAME=node01
MY_POD_NAME=nginx-envars-fieldref
MY_POD_NAMESPACE=default
MY_POD_IP=192.168.1.4
MY_POD_SERVICE_ACCOUNT=default
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
TERM=xterm
HOME=/root
```

## ⚖️ FAIR SHARE SCHEDULING – EXPLAINED

When multiple containers request CPU but no limits are set:

- Linux CFS divides CPU **proportionally** based on requests.

- Example: Pod A requests 600m, Pod B requests 300m → CPU split 2:1.
  
  based on request Linux CFS divides CPU 2:1

## 🧱 RESOURCE QUOTA AT NODE LEVEL

While Kubernetes doesn’t directly enforce quotas **per node**, you can:

- Use **requests** to control scheduling.

- Use **limits** to prevent overuse.

- Use **ResourceQuota** at **namespace level** to cap total usage.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    requests.cpu: "2"
    requests.memory: "4Gi"
    limits.cpu: "4"
    limits.memory: "8Gi"
```

## 🧠 Final Cheat Sheet

| Concept        | Key Point                            |
| -------------- | ------------------------------------ |
| Request        | Used for scheduling                  |
| Limit          | Enforced at runtime                  |
| Guaranteed QoS | Request = Limit for CPU & Memory     |
| Burstable QoS  | Only one defined or unequal          |
| BestEffort QoS | Nothing defined                      |
| Throttling     | Happens when CPU usage exceeds limit |
| Downward API   | Pod introspection without API calls  |
| LimitRange     | Default resource rules per namespace |
| ResourceQuota  | Caps total usage per namespace       |

In **guaranteed.yaml** both cpu and memory is specified and here request and limits of resources are same

Guaranteed Example: myguaranteed.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-guaranteed
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: "256Mi"
        cpu: "500m"
      limits:
        memory: "256Mi"
        cpu: "500m"
```

In **burstable.yaml** only one of the two is specified i.e. either cpu or memory. here request and limits  are not set same for the resource.

Burstable Example: myburstable.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-burstable
spec:
  containers:
  - name: nginx
    image: nginx
    resources:
      requests:
        memory: "256Mi"
      limits:
        memory: "512Mi"
```

In **besteffort.yaml** no resource i.e. no cpu and no memory is specified

BestEffort Example : mybesteffort.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-besteffort
spec:
  containers:
  - name: nginx
    image: nginx
```

MY PRACTICAL :: usage of drain command and uncordon command

```powershell
controlplane:~$ kubectl run mynginx --image=nginx
controlplane:~$ kubectl drain node01 --force --ignore-daemonsets
node/node01 cordoned
Warning: deleting Pods that declare no controller: default/mynginx; ignoring DaemonSet-managed Pods: kube-system/canal-hvvtk, kube-system/kube-proxy-lg8cx
evicting pod default/mynginx
pod/mynginx evicted
node/node01 drained
controlplane:~$ kubectl get nodes
NAME           STATUS                     ROLES           AGE   VERSION
controlplane   Ready                      control-plane   27d   v1.33.2
node01         Ready,SchedulingDisabled   <none>          27d   v1.33.2
controlplane:~$ kubectl get pods       
No resources found in default namespace.
controlplane:~$ kubectl run mynginx --image=nginix
pod/mynginx created
controlplane:~$ kubectl get pods -owide
NAME      READY   STATUS              RESTARTS   AGE   IP       NODE           NOMINATED NODE   READINESS GATES
mynginx   0/1     ContainerCreating   0          10s   <none>   controlplane   <none>           <none>
controlplane:~$ kubectl delete pod mynginx
pod "mynginx" deleted
controlplane:~$ kubectl uncordon node01
node/node01 uncordoned
controlplane:~$ kubectl run mynginx --image=nginix
pod/mynginx created
controlplane:~$ kubectl get pods -owide
NAME      READY   STATUS              RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
mynginx   0/1     ContainerCreating   0          9s    <none>   node01   <none>           <none>
controlplane:~$
```

EXAMPLE : USAGE OF LIMITRANGE : 

SINCE LIMITRANGE IS SET UP IN DEFAULT NAMESPACE QOS i.e. Quality of Service class IS Burstable.

ALSO CHECK RESOURCE SECTION :

BOTH RESOURCE SECTION & QOS RESULTS ARE SHOWN IN SCREENSHOT

```powershell
controlplane:~/Kubernetes-hindi-bootcamp/part4$ ls
1          besteffort.yaml  deploy.yaml          guranteed.yaml   pdb.yaml
README.md  burstable.yaml   downwardapipod.yaml  limitrange.yaml  pod.yaml
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl apply -f limitrange.yaml 
limitrange/example-limitrange created
controlplane:~/Kubernetes-hindi-bootcamp/part4$ cat limitrange.yaml 
apiVersion: v1
kind: LimitRange
metadata:
  name: example-limitrange
  namespace: default
spec:
  limits:
  - type: Pod
    max:
      cpu: "2"
      memory: "1Gi"
    min:
      cpu: "200m"
      memory: "100Mi"
  - type: Container
    max:
      cpu: "1"
      memory: "500Mi"
    min:
      cpu: "100m"
      memory: "50Mi"
    default:
      cpu: "300m"
      memory: "200Mi"
    defaultRequest:
      cpu: "200m"
      memory: "100Mi"

controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl get ns
NAME                 STATUS   AGE
default              Active   27d
kube-node-lease      Active   27d
kube-public          Active   27d
kube-system          Active   27d
local-path-storage   Active   27d
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl run mynginx --image=nginx
pod/mynginx created
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl get pods -owide
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
mynginx   1/1     Running   0          11s   192.168.1.7   node01   <none>           <none>
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl get limitrange
NAME                 CREATED AT
example-limitrange   2025-09-16T03:14:58Z
controlplane:~/Kubernetes-hindi-bootcamp/part4$ kubectl get pod mynginx -oyaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    cni.projectcalico.org/containerID: 0e481d97f6853cd947bf29e4dc8d30e441e97200c0c7261a55c4c56940d9875d
    cni.projectcalico.org/podIP: 192.168.1.7/32
    cni.projectcalico.org/podIPs: 192.168.1.7/32
    kubernetes.io/limit-ranger: 'LimitRanger plugin set: cpu, memory request for container
      mynginx; cpu, memory limit for container mynginx'
  creationTimestamp: "2025-09-16T03:24:54Z"
  generation: 1
  labels:
    run: mynginx
  name: mynginx
  namespace: default
  resourceVersion: "5857"
  uid: 0f16f7ba-ea0e-4ffd-9079-e31750b2b739
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: mynginx
    resources:
      limits:
        cpu: 300m
        memory: 200Mi
      requests:
        cpu: 200m
        memory: 100Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-twqvn
      readOnly: true
  dnsPolicy: ClusterFirst
```

My PRACTICAL : COMMANDS EXECUTED :

```powershell
# creating pod nginx name with nginx image
kubectl run nginx --image=nginx

kubectl get pods


# check resources: {} section
kubectl get pod nginx -oyaml

kubectl delete pod nginx

kubectl get pods 

kubectl apply -f limitrange.yaml

kubectl get limitrange 

# check resources: {} section again this time after DEFINING limitrange
kubectl get pods nginx -oyaml


kubectl apply -f besteffort.yaml

kubectl get pods
# check what is setup for qosClass: Burstable, Burstable is showing because we already set LimitRange policy for the namespace
kubectl get pod <pod-name> -oyaml

kubectl delete -f limitrange.yaml

kubectl delete -f besteffort.yaml

kubectl apply -f besteffort.yaml

# check for qosClass: BestEffort because here resource section is empty
kubectl get pod <pod-name> -oyaml

kubectl apply -f guaranteed.yaml

# check for qosClass: BestEffort because here resource section is empty
kubectl get pod <pod-name> -oyaml

kubectl apply -f downwardapi.yaml

kubectl get pods

# to access inside pod here exec -it then pod-name then command-name that we want to execute inside pod
kubectl exec -it <pod-name> -- printenv

# execute shell inside pod
kubectl exec -it <pod-name> -- bash
```

CHECKING RESOURCE FOR POD CREATED AFTER SETTING LIMITRANGE INTO DEFAULT NAMESPACE

![check resource.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\check%20resource.png)

CHECKING QOS FOR POD CREATED AFTER SETTING LIMITRANGE INTO DEFAULT NAMESPACE

![check qos.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\check%20qos.png)

FINDING BEST FIT NODE FOR POD :

![scheduling pod fiting node.png](D:\Kubernetes%20Learning\kube_screenshot\part%204\scheduling%20pod%20fiting%20node.png)

***

***

**<mark>KUBERNETES SCHEDULING</mark>**

**<mark>🚀 Kubernetes Scheduling Fundamentals</mark>**

### 🔍 Scheduler Decision Factors

The **Kubernetes Scheduler** decides **which node runs a pod**.  
Factors considered:

- **Resource Availability**: CPU, memory

- **Affinity / Anti-Affinity**: Place pods together or apart i.e. separately

- **Taints & Tolerations**: Control which pods can run on which nodes i.e. prevent or allow pods on specific nodes.

👉 By default, the **default scheduler** runs, but we can use multiple schedulers or a descheduler.

🏷️ Labels & Selectors

### ✅ What Are Labels?

- **Key-value pairs** in the **metadata** of an object (Pod, Node, Deployment, Service, etc.).

- Used for **identification, grouping, and selection**.

```yaml
metadata:
  labels:
    app: nginx
    tier: frontend
```

we label pod, node, deployment etc that we create in kubernetes ecosystem for clear understanding that what is this application, what are its labels, what is this node for and what are its labels

example we labeled a node wasm, gpu and xyz now we know that wasm workload wil run here in this node and gpu based workload will run here in this node labeled with gpu .
we can label lots of nodes as gpu or wasm etc.

we can do lots of things on label. we can create policies

Kyverno is a tool we can install in kubernetes using Kyverno we can create policies like that if an object has no label then it will not be created.

### Why Labels?

- Help us **organize resources**.

- Example: If a node is labeled `gpu=true`, GPU workloads can be scheduled there.

- We can attach multiple labels: `gpu=true`, `wasm=true`, `env=prod`.

👉 **Labels = tags for organizing.**

```powershell
# Show pod labels
kubectl get pods --show-labels

# Run a pod without label
kubectl run mynginx --image=nginx

# Run a pod with label
kubectl run demonginx --image=nginx --labels="demo=mynginx"

kubectl run demonginx2 -l="demo=mynginx" --image=nginx

# label is key-value pair and here labeling the pod with key as live and value is mydemonginx
kubectl label pod mynginx live=mydemonginx

# Add label later
kubectl label pod mynginx live=mydemonginx

# Filter pods by label
kubectl get pods -l app=testing

# Show pods where label app != nginx
kubectl get pods -l app!=nginx
```

labels and selectors add meaning to the kubernetes object

selectors selecting on the based of labels

### Label Selectors

deployments and services are labeled and seletected in same way i.e. service is also labeled as and selected as with matching label like deployment.
using nodeselector we can alter scheduling

labels and selectors add meaning to the kubernetes object.

selectors selecting on the based of labels.

1. **Equality-based**

```yaml
selector:
  matchLabels:
    component: redis
```

👉 Selects pods with `component=redis`.

2. **Set-based**

```yaml
selector:
  matchExpressions:
    - { key: tier, operator: In, values: [cache] }
    - { key: environment, operator: NotIn, values: [dev] }
```

👉 Selects pods with `tier=cache` and `environment != dev`.

📌 Operators: `In`, `NotIn`, `Exists`, `DoesNotExist`.

```yaml
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```

here matchLabels:component redis is equity based label
here under matchExpressions set based labels with key and operator and values set and in operator values can be In, NotIn, exists

key can be anything,

so this is telling  select the pod whose label's key is tier or environment and checks that tier or environment exist, in or notIn as per the operator value check in the values set i.e. cache or dev

## 3. Labels in Scheduling

- **NodeSelector** = schedule pods on specific nodes.  
  📌 Example:

```yaml
spec:
  nodeSelector:
    gpu: "true"
```

👉 This pod will only run on nodes labeled `gpu=true`.

## 4. Admission, Authentication, Authorization

- **Authentication**: Who are you?

- **Authorization**: Are you allowed to do this? (RBAC)

- **Admission**(Policies, quotas, limits): Should the object be allowed in the cluster?

👉 At admission phase, policies are checked.

### Policies with Kyverno

- Example: “**Validation Policy**: Block objects without labels i.e. If an object has no label, **deny creation**.”

- Or: “If an object has no label, **add a default label**.” (mutating webhook).

📌 **Mutation = automatically modifying an object before saving.**

mutation means when request comes it checks for something whether its there or not and then we add something our own into the object and then apply it so there with using policy we change it

## 5. Annotations vs Labels

- **Labels**: used for selection and grouping.

- **Annotations**: store extra information (not used for selection).
  
  - Examples: testing notes, cert-manager configs, service mesh metadata.

👉 Think:

- **Labels = searchable tags.**

- **Annotations = sticky notes (extra info).**

## 🧾 Annotations vs Labels

| Feature | Labels                | Annotations                    |
| ------- | --------------------- | ------------------------------ |
| Purpose | Selection, grouping   | Metadata, extra info           |
| Used in | Scheduling, selectors | Ingress, cert-manager, testing |
| Format  | Key-value             | Key-value                      |

```powershell
kubectl describe pod

kubectl run mynginx --image=nginx

kubectl run demo2nginx -l="app=testing" --image=nginx

kubectl get pods --show-labels

# label is key-value pair and here labeling the pod with key as live and value is mydemonginx
kubectl label pod mynginx app=testing

kubectl get pods --show-labels

# this will list pod where label app is not nginx
kubectl get pod -l app!=nginx


# list all the resource there you can see short names like for deployment deploy , for pod po etc..
kubectl api-resources

kubectl create deploy demo --image=nginx -n bootcamp

kubectl get deploy --show-labels -n bootcamp

kubectl label deploy <deploy-name> app1=demo1 -n bootcamp

kubectl create ns bootcamp

kubectl create deploy demong --image=nginx --replicas 3 -n bootcamp

kubectl create deploy demong --replicas 3 -n bootcamp --image=nginx

# list all the pods with label in braces i.e. either test or bootcamp. here we used set operator.
kubectl get pods -l 'app in (test,bootcamp)'

kubectl get pods --show-labels

kubectl get pods --show-labels -n bootcamp

kubectl get deploy --show-labels -n bootcamp

# list all the namespaces
kubectl get ns
```

## 6. Namespaces

### What are Namespaces?

- **Logical groups** of resources.

- Like folders in a computer – they separate and organize workloads.

- Useful for multi-team environments.

Namespaces: we deploy resources in namespaces
grouping resources together.
namespaces are like logical entity mainly for grouping resources together

📌 Example:

```powershell
kubectl create ns dev
kubectl create ns bootcamp
```

👉 Deployments in `dev` namespace are isolated from `bootcamp`.

commands:

```powershell
# List namespaces
kubectl get ns

# Create namespace
kubectl create ns abc

# Run pod in specific namespace
kubectl run nginx --image=nginx -n abc

# List pods with wide info (node, IP)
kubectl get pods -o wide -n abc

# Change default namespace for context
kubectl config set-context --current --namespace=dev
```

### 

## 🧠 Kubernetes Deployment & Scheduling – Simplified Notes

### 🧩 Scenario Recap

You have:

- **DeployA** with **4 replicas** → goes to **NamespaceA**

- **DeployB** with **2 replicas** → goes to **NamespaceB**

- Two virtual machines: **V1M** and **V2M**

- An application is deployed across these VMs

**Pod distribution:**

- DeployA → 2 pods on V1M, 2 pods on V2M

- DeployB → 1 pod on V1M, 1 pod on V2M
  
  when scheduler schedules it doesn't know about namespaces. scheduler schedules based on resources (like cpu, memory, space etc) stuff like that.
  because nodes doesnot know about namespaces and namespaces cannot exist at two places so namespaces are like logical entity which group resources together
  but we can make scheduler aware based on the limitrange, resource-quota and limitrange and resource-quota checked at admission phase

### 📦 What is a Namespace?

- A **namespace** is a **logical grouping** of Kubernetes resources.

- Think of it like folders on your computer: NamespaceA and NamespaceB are separate folders.

- You can have DeployA in NamespaceA and DeployB in NamespaceB — they don’t interfere with each other.

📝 **Key Point:** Namespaces help organize and isolate resources, but they are **not tied to physical nodes** like V1M or V2M.

### ⚙️ What Does the Scheduler Do?

- The **Kubernetes scheduler** decides **where** to place pods (on which node).

- It looks at **available resources** like CPU, memory, disk space — **not namespaces**.

- Scheduler doesn’t care if the pod is from NamespaceA or NamespaceB — it just finds the best node to run it.

🧠 **Example:** If V1M has more free memory, the scheduler might place a pod there, even if it’s from NamespaceB.

### 🛡️ How Do Namespaces Affect Scheduling?

- **By default**, namespaces don’t affect scheduling.

- But you can **influence scheduling** using:
  
  - **ResourceQuota**: Limits total CPU/memory usage in a namespace.
  
  - **LimitRange**: Sets default/request/limit values for pods in a namespace.

These are checked during the **admission phase** (before the pod is scheduled).

🧠 **Example:** If NamespaceB has a ResourceQuota of 1 CPU and DeployB tries to use 2 CPUs, it will be rejected.

### 🔐 Authorization: Who Can Do What?

- Kubernetes uses **RBAC (Role-Based Access Control)** to check:
  
  - Who is trying to deploy?
  
  - Which namespace are they deploying to?
  
  - Do they have permission?

🧠 **Example:** A developer with access to NamespaceA cannot deploy to NamespaceB unless they have the right role.

### 🧾 Summary Cheat Sheet

| Concept       | What It Means                              | Example                               |
| ------------- | ------------------------------------------ | ------------------------------------- |
| Namespace     | Logical grouping of resources              | DeployA in NamespaceA                 |
| Scheduler     | Places pods on nodes based on resources    | Pod goes to V1M if it has free memory |
| ResourceQuota | Limits total resource usage in a namespace | Max 2 CPUs for NamespaceB             |
| LimitRange    | Sets default/request/limit for pods        | Pod must request ≤ 500Mi memory       |
| RBAC          | Controls who can do what                   | Dev can deploy only in NamespaceA     |

### 🧠 Easy Mnemonic to Remember

**"NS-RL-SR"** → *Namespace, RBAC, LimitRange, Scheduler, ResourceQuota*

- **N**amespace: Logical grouping

- **S**cheduler: Places pods based on resources

- **R**BAC: Checks permissions

- **L**imitRange: Pod-level limits

- **R**esourceQuota: Namespace-level limits

***Namespaces group resources logically to isolate, organize, and manage access within a shared Kubernetes cluster.***

### Networking in Namespaces

- **By default, pods in different namespaces can communicate.**

- Example:

```powershell
kubectl exec -it nginx -- curl <pod-ip-in-other-namespace>
```

👉 To block communication, use **NetworkPolicies**.

**COMMUNICATION BETWEEN POD's FROM TWO DIFFERENT NAMESPACE**

```powershell
# we are creating a namespace abc
kubectl create ns abc

# running a pod in default namespace. when -n flag is not mentioned then pod will be created in default namespace
kubectl run nginx --image nginx

# creating and running a pod in abc namespace
kubectl run nginx --image nginx -n abc

# listing all the pods with details like pod created in which node and ip of pod
kubectl get pods -owide

# listing all the pods from abc namespace with node and ip info
kubectl get pods -owide -n abc

# here i am exec into nginx running in default namespace from there i am running curl on pod which is running in abc namespace. this proves that eventhough namespace is different two pods can communicate with other pod.
kubectl exec -it nginx -- curl <ip-of another pod from abc - namespace>

kubectl exec -it -n abc bootcamp-7c8698ff84-5pfwc -- curl 192.168.0.4

SO BY DEFAULT PODS CAN COMMUNICATE WITH EACHOTHER

# listing all namespace
kubectl get ns

# creating dev namespace
kubectl create ns dev

# creating bootcamp namespace
kubectl create ns bootcamp

# creating deployment demo in bootcamp namespace using -n flag
kubectl create deploy demo -n bootcamp --image=nginx

# creating deployment demo in dev namespace using -n flag
kubectl create deploy demo -n dev --image=nginx

# setting current namespace dev from default namespace
kubectl config set-context --current --namespace=dev
```

### ResourceQuota

- Restricts total **resources per namespace**.  
  
  👉 Prevents namespace from consuming too many resources.📌 Example: `resourcequota.yaml`

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: example-quota
  namespace: example-namespace
spec:
  hard:
    requests.cpu: "500m" # total amount of CPU that can be requested
    requests.memory: "200Gi" # total amount of memory that can be requested
    limits.cpu: "1" # total amount of CPU limit across all pods
    limits.memory: 400Gi # total amount of memory limit across all pods
    pods: "10" # total number of pods that can be created
```

### LimitRange

- Defines **min, max, and default** resources per **Pod or Container**.

📌 Example: `limitrange.yaml`

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: example-limitrange
  namespace: example-namespace
spec:
  limits:
  - type: Pod
    max:
      cpu: "2" # max CPU per Pod
      memory: 2Gi # max memory per Pod
    min:
      cpu: "200m" # min CPU per Pod
      memory: 200Mi # min memory per Pod
  - type: Container
    default:
      cpu: "100m" # default CPU request for any container
      memory: 100Mi # default memory request for any container
    defaultRequest:
      cpu: "100m" # default CPU limit for any container
      memory: 100Mi # default memory limit for any container
    max:
      cpu: "1" # max CPU per Container
      memory: 1Gi # max memory per Container
    min:
      cpu: "100m" # min CPU per Container
      memory: 100Mi # min memory per Container
```

### ResourceQuota + LimitRange in Action

📌 Oversized Pod example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: oversized-pod
  namespace: example-namespace
spec:
  containers:
  - name: busybox
    image: busybox
    resources:
      requests:
        memory: "600Mi"
        cpu: "600m"
      limits:
        memory: "600Mi"
        cpu: "600m"
```

👉 If namespace quota allows only `500m` CPU, pod creation **fails with Forbidden error**.

Commands : USAGE Resourcequot, LimitRange

```powershell
# creating example namespace
kubectl create ns example-namespace

# creating resourcequota in example-namespace but before that first create example-namespace
kubectl create -f resourcequota.yaml

kubectl apply -f resourcequota.yaml

kubectl get resourcequota -n example-namespace

kubectl delete -f resourcequota.yaml

# getting all ResourceQuota
kubectl get ResourceQuota -A


# creating limitrange
kubectl create -f limitrange.yaml


# this will show ResourceQuota but here we can see what request and limit range are set
kubectl get ResourceQuota -A

kubectl get limitrange -A  

# applying pod oversize i.e. by resourcequota request memory size is 500 but here in this pod-oversize request memory is 600. this will give forbidden error because of that. this was checked at admission phase before creating the pod
kubectl apply -f pod-oversize.yaml                                                

# ERROR BECAUSE LIMIT WE PUT IN example-namespace by resourcequota.yaml
controlplane:~/Kubernetes-hindi-bootcamp/part5$ kubectl apply -f pod-oversize.yaml
Error from server (Forbidden): error when creating "pod-oversize.yaml": pods "oversized-pod" is forbidden: exceeded quota: example-quota, requested: requests.cpu=600m, used: requests.cpu=0, limited: requests.cpu=500m
```

**Diagram:**

flow: User → API Server → Admission → Scheduler → Node) to visualize the whole lifecycle of pod scheduling + policy enforcement

```yaml
User (kubectl / API request)
        |
        v
Kubernetes API Server
        |
        v
Authentication (Who are you?)
        |
        v
Authorization (Are you allowed?)
        |
        v
Admission Controllers (policies, quotas, mutations)
        |
        v
etcd (stores object spec) + Scheduler (decides Node)
        |
        v
Kubelet on Node (creates Pod → pulls image → runs container)
```

**NODENAME & NODESELECTOR**

```powershell
kubectl run nginx --image=nginx --dry-run=client -o yaml
```

| Component           | Meaning                                                                |
| ------------------- | ---------------------------------------------------------------------- |
| `kubectl run nginx` | Creates a Pod named `nginx`                                            |
| `--image=nginx`     | Uses the official `nginx` container image                              |
| `--dry-run=client`  | Simulates the command on the client side without sending it to the API |
| `-o yaml`           | Outputs the result in YAML format                                      |

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  nodeName: node01
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

using `nodeName: node01`, which **hard-codes** the Pod to run on a specific node. If you want more flexibility and **schedule Pods based on node labels**, you should use `nodeSelector`.

since we add nodeName in yaml file of pod and specify on which node pod will run this will bypass scheduler
add nodeName in spec section with value as node01

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mynginx
  name: nginx
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - image: nginx
    name: mynginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

### 🔍 How It Works:

- `nodeSelector` matches the Pod to nodes that have the label `disktype=ssd`.

- You must ensure that your target node (e.g., `node01`) has this label:

```yaml
kubectl label nodes node01 disktype=ssd
```

This way, the Pod will be scheduled on any node with that label—not just `node01`. It’s cleaner and more scalable.

```powershell
controlplane:~$ cat nodeselectorpod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: mynginx
  name: mynginx
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - image: nginx
    name: mynginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always

controlplane:~$ kubectl apply -f nodeselectorpod.yaml 
pod/mynginx created
controlplane:~$ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
mynginx   1/1     Running   0          7s
nginx     1/1     Running   0          24m
controlplane:~$ kubectl get pods -owide
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
mynginx   1/1     Running   0          12s   192.168.1.5   node01   <none>           <none>
nginx     1/1     Running   0          24m   192.168.1.4   node01   <none>           <none>
controlplane:~$ kubectl get pods -owide --show-labels
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES   LABELS
mynginx   1/1     Running   0          65s   192.168.1.5   node01   <none>           <none>            run=mynginx
nginx     1/1     Running   0          25m   192.168.1.4   node01   <none>           <none>            run=nginx
```

## 🧠 Tips to Remember

- **Labels = Identity**, **Selectors = Filters**, **searchable tags.**

- **nodeSelector = flexible scheduling**, **nodeName = fixed scheduling**

- **Kyverno = policy enforcer**

- **Annotations = extra info, not for selection**

- **Namespaces = logical grouping, not physical isolation**

- **ResourceQuota = total limits or total budget per namespace.**

- **LimitRange = per-pod/container limits**

- **Admission phase = checkpoint before creation.**

- **Mutation = auto-fix object before saving.**

***

***

- In Kubernetes, **nodes need to tell the control plane “I’m alive”** → this is called a **node heartbeat**.

- Earlier (before Kubernetes v1.13), these heartbeats were stored as updates to the **Node object** in the `kube-system` namespace.

- Now, Kubernetes uses a **dedicated namespace** called `kube-node-lease`.

- Inside this namespace, there is a **Lease object** for each node.

- These Lease objects store lightweight “heartbeat” signals.

- Advantage: Updating a small Lease object is **faster and more scalable** than updating the entire Node object.

- This means **quicker failure detection** and **less load** on the API server when you have thousands of nodes.

***

***

### What is kube-scheduler?

- **kube-scheduler** = default Kubernetes scheduler.

- Job: **decide which node will run a Pod.**

### Scheduling process:

1. **Filter (Feasibility):**
   
   - Find all nodes that meet Pod’s requirements (CPU, memory, affinity/anti-affinity, taints/tolerations, etc.).
   
   - These are called **feasible nodes**.

2. **Score:**
   
   - Each feasible node is given a **score** (based on policies like resource usage, spreading, etc.).
   
   - The **highest scoring node** is chosen.

3. **Bind:**
   
   - Scheduler sends its decision to the **API server** in a process called **binding**.
   
   - Then the **kubelet** on that node creates the Pod.

### Scheduler Performance Tuning (for large clusters):

- In **small clusters**, scheduler can check *all* nodes → accurate but slower for large clusters.

- In **large clusters**, we can tune:
  
  - **Percentage of nodes to check** (instead of all).
  
  - **Parallelism** (how many scheduling operations can run at once).
  
  - **Profiles/Plugins** (customize scheduling behavior).

👉 This balances **latency vs accuracy**:

- **Latency** = how fast Pods get scheduled.

- **Accuracy** = how optimal the placement is.

📌 **Memory Tip:**  
Think of kube-scheduler as a **job recruiter**:

- First filter out candidates who don’t meet requirements.

- Then score the shortlisted candidates.

- Pick the top one and hire (bind).

### 🔹 `percentageOfNodesToScore`

- Defines what **percentage of cluster nodes** the scheduler checks before scoring.

- Example: If set to **50**, the scheduler only checks **half of the nodes** (randomly selected), instead of all.

- 👉 **Memory tip:** *“More nodes checked = better accuracy, fewer nodes checked = faster scheduling.”*

### 🔹 Common Tuning Parameters

1. **`percentageOfNodesToScore`**
   
   - Default: **50** (checks 50% of nodes).
   
   - Range: **1–100**.
   
   - Example:
     
     - Cluster has 1,000 nodes.
     
     - `percentageOfNodesToScore = 30` → only **300 nodes** will be evaluated.

2. **`parallelism`**
   
   - Controls how many Pods can be scheduled in **parallel**.
   
   - Higher value → faster scheduling, but more CPU usage.

3. **`profiles` (scheduling profiles)**
   
   - You can define multiple scheduling profiles with **different rules/policies**.
   
   - Example: One profile optimized for **batch jobs**, another for **latency-sensitive apps**.

4. **`plugins`**
   
   - Extend scheduling with custom logic (e.g., prefer GPU nodes, spread across zones).

✅ **Super-simple Example**:

- Cluster size = 1,000 nodes.

- `percentageOfNodesToScore = 50` → scheduler checks **500 nodes**, not all 1,000.

- Saves time but still likely finds a good fit.

***

### 🔹 Kubernetes Scheduling & Binding Workflow

1. **Queue**
   
   - All unscheduled Pods wait in a **queue**.
   
   - Scheduler picks them one by one (or in batches).

2. **Filter**
   
   - Scheduler **filters out nodes** that cannot run the Pod.
   
   - Uses **plugins, predicates, filters**.
   
   - Result: a list of **feasible nodes**.
   
   - Example: Out of 10 nodes, only 3 support WebAssembly → those 3 are feasible.

3. **Score**
   
   - Scheduler **ranks feasible nodes**.
   
   - Higher score = better placement.
   
   - Factors:
     
     - Node already has the Pod’s image → **faster startup** → higher score.
     
     - Node has more free resources → higher score.
     
     - Affinity / anti-affinity → adjusted score.
     
     - Taints & tolerations → can allow or block.

4. **Binding**
   
   - Scheduler picks the **highest-scoring node**.
   
   - Sends the decision to the **API Server** (called **binding**).
   
   - The **kubelet** on that node then creates the Pod.

✅ **Corrected Example:**

- Cluster = 10 nodes.

- Pod = WASM workload.

- **Filter:** Only 3 nodes support WASM → feasible nodes = Node2, Node5, Node8.

- **Score:**
  
  - Node2: already has WASM image → score 9.
  
  - Node5: lots of free CPU/memory → score 7.
  
  - Node8: limited resources → score 4.

- **Binding:** Pod scheduled to **Node2** (highest score = 9).

📌 **Memory Tip:**  
Think like a **job interview**:

- Queue = candidates waiting.

- Filter = who is eligible.

- Score = who is best fit.

- Binding = hire the top candidate.

***

***

🔹 Pod Scheduling & Scheduling Gates

### What are Scheduling Gates?

- **SchedulingGates** are markers that **hold back a Pod from being scheduled**.

- Until all SchedulingGates are cleared, the Pod **cannot be scheduled**.

- Used to **pause scheduling** until some condition (like autoscaler action, resource availability, or policy checks) is satisfied.

**Pod Scheduling Readiness Flow**

```yaml
Pod Created
     ↓
Check SchedulingGates
     ↓
IF empty → Pod is ready for scheduling → Pod scheduled → Running
IF not empty → Pod status = SchedulingGated (stuck until gates are removed)
```

### Why use Scheduling Gates?

- Reduce unnecessary **load on the scheduler**.

- Example:
  
  - Cluster Autoscaler is adding new nodes.
  
  - Without gates → Scheduler keeps retrying Pods on unavailable nodes (wasteful loop).
  
  - With gates → Pods wait until gates are cleared → Scheduler only works when resources are ready.

- Gates can be **added/removed via Admission Webhooks** or manually.

Example Pod with Scheduling Gate : podschedulereadiness.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pod
spec:
  schedulingGates:
  - name: saiyam
  containers:
  - name: pause
    image: registry.k8s.io/pause:3.6
```

**commands:**

```powershell
# Apply pod with a scheduling gate
kubectl apply -f podschedulereadiness.yaml

# Pod will be stuck in SchedulingGated state
kubectl get pods

# Remove SchedulingGates (edit pod spec and delete the gate entry)
kubectl edit pod demo-pod     

# OR using kubectl edit or vi edit manually
# remove SchedulingGates with name saiyam.
vi podschedulereadiness

# OR re-apply YAML without schedulingGates
kubectl apply -f podschedulereadiness.yaml

# Now the Pod will be scheduled and Running
kubectl get pods
```

✅ **Corrected Notes:**

- `SchedulingGated` status = Pod is waiting because a scheduling gate is present.

- Once you **remove the scheduling gate**, Pod becomes **SchedulingReady** → then scheduler places it on a node.

📌 **Memory Tip:**  
Think of a **traffic light** 🚦:

- Red light = SchedulingGate present (Pod cannot move).

- Green light = SchedulingGate removed → Pod moves forward to scheduling.

***

***

# 🔹 What is `topologySpreadConstraints`?

- It’s a **Pod scheduling rule** in Kubernetes.

- It tells the scheduler **how to spread pods evenly** across nodes, zones, racks, etc.

- Goal = **high availability (HA)** and **fault tolerance** by avoiding putting all pods in one place.

👉 Example: 3 replicas of your app → 3 nodes in 3 zones → each pod goes to a different zone.

# 🔹 How does it work?

When you create a Deployment (or StatefulSet, Job, etc.), you can add:

```yaml
topologySpreadConstraints:
- maxSkew: <number>
  topologyKey: <node label>
  whenUnsatisfiable: <action>
  labelSelector: <pod selector>
```

The scheduler looks at:

1. **labelSelector** → which pods are part of this group.

2. **topologyKey** → which node label to use (e.g., `kubernetes.io/hostname` = node, `topology.kubernetes.io/zone` = zone).

3. **maxSkew** → how evenly pods should be spread.

4. **whenUnsatisfiable** → what to do if spreading is not possible.

# 🔹 Fields Explained (Easy)

### ✅ `maxSkew`

- Means “how much imbalance is allowed between groups”.

- Must be ≥ 1.

- Example: `maxSkew: 1` → if one node has 2 pods, others must have either 1 or 2 (difference ≤1).

### ✅ `topologyKey`

- Which **node label** to use for spreading.

- Common values:
  
  - `kubernetes.io/hostname` → spread across **nodes**.
  
  - `topology.kubernetes.io/zone` → spread across **zones**.
  
  - `topology.kubernetes.io/region` → spread across **regions**.

check labels :

```powershell
kubectl get nodes --show-labels
```

### ✅ `whenUnsatisfiable`

- What to do if pods can’t be evenly spread.

- Options:
  
  - **DoNotSchedule** → scheduler refuses to place the pod.
  
  - **ScheduleAnyway** → scheduler places the pod even if balance is broken.

### ✅ `labelSelector`

- Tells which pods this rule applies to.

- Usually matches your Deployment label (`app: demo-app`).

**🔹 Example: Deployment with Spread**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-app
spec:
  replicas: 6
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: app-container
        image: nginx
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: "kubernetes.io/hostname"
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: demo-app
```

# 🔹 Walkthrough (Your Example with 3 Nodes)

1. Cluster: 3 nodes (zoneA, zoneB, zoneC).

2. Deployment: 3 replicas.

3. `topologyKey: zone` + `maxSkew: 1`.
   
   - Pod1 → node1 (zoneA).
   
   - Pod2 → node2 (zoneB).
   
   - Pod3 → node3 (zoneC).
   
   - Perfect distribution ✅.

Scale up to 6:

- Scheduler ensures ~2 pods per zone.

- Max difference allowed = 1 (`maxSkew: 1`).

If one node is cordoned/unavailable:

- Scheduler may block new pods (`DoNotSchedule`) OR place them anyway (`ScheduleAnyway`).

**Commands-Recap**

```powershell
# Check node labels (zones/hostnames)
kubectl get nodes --show-labels

# Apply deployment
kubectl apply -f topology.yaml

# Check pod distribution
kubectl get pods -o wide

# Scale up
kubectl scale deploy demo-app --replicas=6

# Block scheduling on a node
kubectl cordon controlplane

# Unblock scheduling
kubectl uncordon controlplane   

kubectl get pods -owide
```

# 🧠 Easy-to-Remember Trick

- **maxSkew = imbalance allowed** (like tolerance).

- **topologyKey = spread dimension** (node/zone/region).

- **whenUnsatisfiable = action if stuck**.

- **labelSelector = which pods to count**.

👉 Think of it like **seating friends in a theater 🎭**:

- Spread them across rows (`topologyKey`).

- Don’t let one row get too crowded (`maxSkew`).

- If full: either block entry (`DoNotSchedule`) or let them sit anywhere (`ScheduleAnyway`).

***

***

# 🔹 What is `PriorityClass`?

- A **32-bit integer** that decides **importance of a pod**.

- **Higher number = higher priority**.

- If the cluster is out of resources, **low-priority pods are evicted** to make room for high-priority ones.

👉 Used for **critical apps** that must run even if it means killing other workloads.

# 🔹 Default Priority

- If you don’t set any priority, pods run with **default (normal) priority**.

- System already has two special priorities:
  
  - `system-cluster-critical` → for core cluster services (e.g. kube-dns).
  
  - `system-node-critical` → for node-level critical services (e.g. kubelet helper pods).

⚠️ Never use these for your own apps. They’re reserved for Kubernetes components.

# 🔹 Example Scenario

Cluster has 4 nodes:

- node01 → 4 pods

- node02 → 5 pods

- node03 → 3 pods

- node04 → 5 pods

All pods = **default priority**.  
Now → you need to run a **mission-critical app**.

- If there are no free resources → scheduler checks priorities.

- Pods with **lower priority** will be evicted to make space.

- Your mission-critical pod runs because it has **higher PriorityClass**.

# 🔹 Create a PriorityClass

priorityclass.yaml :

```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: mission-critical-priority
value: 1000000   # Higher than normal
globalDefault: false
description: "Priority class for mission-critical workloads"
```

🔹 Use PriorityClass in a Pod/Deployment :

priority-pod.yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: critical-app
spec:
  priorityClassName: mission-critical-priority
  containers:
  - name: nginx
    image: nginx
```

👉 observe **priorityClassName** used is same from above that we created. priorityClassName should match when we use in Pod/Deployment.

COMMANDS :

```powershell
kubectl apply -f priorityclass.yaml

kubectl get priorityclass -A

# this pod is with priorityclass so this pod will run even if the resource are not available and when we scale up this pod then also it will run even if the resources are not available by evicting the low priority pods
kubectl apply -f priority-pod.yaml

kubectl get pod
```

# 🔹 Key Points to Remember

- **Bigger value = more important**.

- Used when **resources are tight** → evicts lower-priority pods.

- Don’t use system critical priorities → make your own.

- Normal apps = default priority.

# 🧠 Memory Trick

Think of it like **hospital emergency triage 🚑**:

- Normal patients wait in line (default priority).

- Critical patient (mission-critical app) gets treated first, even if others are sent away (pods evicted).

***

***

# 🔹 What is Pod Overhead?

- Some runtimes (like **gVisor, Kata Containers, Firecracker**) use **extra CPU/memory** for isolation and security.

- This extra cost = **Pod Overhead**.

- Kubernetes adds this overhead to the Pod’s requests → so scheduler accounts for **pod request + runtime overhead** together.

# 🔹 Why is it Needed?

Without Pod Overhead:

- Scheduler may think enough resources are free.

- But runtime itself consumes extra → leading to **overcommit & crashes**.

With Pod Overhead:

- Scheduler makes **accurate decisions**.

- Ensures node doesn’t get overloaded.

# 🔹 How It Works

1. Define a **RuntimeClass** with overhead.

2. Pod uses that RuntimeClass.

3. Scheduler = Pod Requests + Overhead.

# 🔹 Example

**RuntimeClass with overhead:**

```yaml
apiVersion: node.k8s.io/v1
kind: RuntimeClass
metadata:
  name: kata
handler: kata
overhead:
  podFixed:
    memory: "120Mi"
    cpu: "100m"
```

Pod using RuntimeClass:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  runtimeClassName: kata
  containers:
  - name: app
    image: nginx
    resources:
      requests:
        cpu: "200m"
        memory: "256Mi"
```

🔹 Total resources scheduler sees =  
200m (container) + 100m (overhead) = **300m CPU**  
256Mi (container) + 120Mi (overhead) = **376Mi memory**

# 🔹 Simple Use Cases

- **Secure runtimes** (gVisor/Kata/Firecracker).

- **Multi-tenant clusters** → prevent noisy-neighbor issues.

- **Accurate resource accounting** for autoscalers.

# 🧠 Easy Memory Trick

Think of Pod Overhead like **packing your suitcase ✈️**:

- Clothes = your app’s resource requests.

- Bag weight (overhead) = runtime cost.

- At airport (scheduler), total = clothes + bag.

***

***

NodeAffinity is used for altering scheduling
Taints & Tolerations is used for altering scheduling
nodeselector for altering scheduling
nodename for altering scheduling

```powershell
kubectl delete deploy --all

kubectl delete deploy -A
```

# 📝 Node Affinity — Simple Notes

🔹 **What it is?**

- Like `nodeSelector`, but more powerful.

- Lets you tell **which nodes your pod can run on** based on **node labels**.

- Two modes:
  
  1. **requiredDuringSchedulingIgnoredDuringExecution** → **HARD rule**. Must match.
  
  2. **preferredDuringSchedulingIgnoredDuringExecution** → **SOFT rule**. Best effort.

🔹 **Key Points to Remember**

- **Required** = If no node matches → Pod stays **Pending**.

- **Preferred** = Pod schedules anyway, but will **prefer labeled nodes** if available.

- “IgnoredDuringExecution” → means labels are only checked **at scheduling time**, not later.

**Example yaml NodeAffinity :**

nodeaffinity.yaml : 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mycontainer
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: "topology.kubernetes.io/region"
            operator: In
            values:
            - "us-east-1"         # MUST be on node with this region
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: "disktype"
            operator: In
            values:
            - "ssd"               # If multiple nodes match, prefer SSD
```

🔹 **Demo Flow (Commands)**

1. Apply pod with node affinity:
   
   ```powershell
   # Apply pod with node affinity:
   kubectl apply -f nodeaffinity.yaml
   
   # Pod will be Pending because no node has matching labels:
   kubectl get pods
   
   kubectl describe pod mypod
   
   kubectl get nodes --show-labels
   
   # Label a node:
   kubectl label node node01 topology.kubernetes.io/region=us-east-1
   
   kubectl label node controlplane topology.kubernetes.io/region=us-east-1
   
   kubectl get nodes --show-labels
   
   # Pod should now schedule on that node.
   # Add preferred label:
   kubectl label node node01 disktype=ssd
   ```

👉 Pod prefers SSD nodes if multiple nodes match.

- Flow simplified: first pod pending → then add labels → then pod runs.

🔹 **Quick Memory Trick**

- **Required = MUST HAVE label.**

- **Preferred = NICE TO HAVE label.**

- **IgnoredDuringExecution = only checked at START, not later.**

Node Affinity is a property of Pods that attracts them to a set of nodes(either as a preference or a hard requirement).

---

---

# 📝 Taints & Tolerations — Easy Notes

🔹 **Definition**

- **Taint = repel pods** (mark node as “restricted”).

- **Toleration = allow pod** to accept that taint and run there.

- Together, they control **which pods can run on which nodes**.

🔹 **Operators**

- `Equal` → match key/value exactly.

- `Exists` → key must exist, value ignored.

- `NoValueReq` → (default) tolerates taint just by key.

🔹 **Effects**

- `NoSchedule` → Pod **won’t schedule** here unless it tolerates taint.

- `PreferNoSchedule` → Pod tries to avoid, but may schedule if no other choice.

- `NoExecute` → Existing pods **without toleration are evicted**, new pods not scheduled.

🔹 **Use Cases**

1. **Dedicated Nodes** → e.g. GPU nodes only for ML pods.

2. **Special Hardware** → e.g. SSD-only workloads.

3. **Taint-based Evictions (NoExecute)** → push pods away from unhealthy nodes.

A cluster with controlplane and three nodes and one node is setup with taint(foo=bar:NoSchedule). scheduler schedules pod and checks taints i.e. foo=bar:NoSchedule here a POD with toleration i.e. Key:"foo", operator:"Equal" and value:"bar" and effect:"NoSchedule" will schedule on this node which is tainted with foo=bar:NoSchedule.

if node is tainted with NoExecute Effect then suppose if a node has two running pods and there is no toleration and suppose node is tainted with Effect NoExecute then pod running from earlier will be evicted if tainted and only matching toleration specified pod will run at the node.

🔹 **Example Pod (Toleration)**

toleration.yaml :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-tolerations
spec:
  containers:
  - name: nginx
 image: nginx
  tolerations:
  - key: "app"
 operator: "Equal"
 value: "demo"
 effect: "NoSchedule"
```

🔹 **Correct Demo Flow (Commands)**

Commands :

```powershell
# Apply pod with toleration
kubectl apply -f toleration.yaml

kubectl get pods

kubectl delete -f toleration.yaml

# till now there is no taint on our nodes so lets taint our node01
# Taint a node
kubectl taint nodes node01 app=demo:NoSchedule

# Now only pods with matching toleration can run there.

kubectl get nodes

kubectl get pods

kubectl cordon controlplane

# Test scheduling without toleration
kubectl run abc --image=nginx

kubectl get nodes

# only node01 where pod can schedule since controlplane is cordoned
# but node01 is tainted  so pod with toleration matching to node01 taint will run
kubectl get pods

kubectl describe pod abc

# Schedule with toleration
# node01 is tainted with app=demo:NoSchedule so a matching pod with toleration will run on node01
kubectl apply -f toleration.yaml

kubectl get pods

# here node is tainted with effect NoExecute so earlier running pod will evvict and pod with toleration where effect does not matchup
kubectl taint nodes node01 app=demo:NoExecute

kubectl get pods

kubectl get pods -owide
```

Now only pods with matching toleration can run there.

3. Test scheduling without toleration:
   
   ```powershell
   kubectl run abc --image=nginx
   kubectl get pods
   kubectl describe pod abc   # should stay Pending (no matching toleration)
   ```

4. Schedule with toleration:

```powershell
 kubectl apply -f toleration.yaml
kubectl get pods -o wide   # this pod should run on node01
```

5. Try **NoExecute effect**:

```powershell
kubectl taint nodes node01 app=demo:NoExecute --overwrite
kubectl get pods -o wide   # old pods w/o toleration are evicted
```

`--overwrite` is needed if you reapply taint with new effect.

`Equal` isn’t always used → in practice `Exists` is common.

🔹 **Quick Memory Trick**

- **Taint = Blocker** 🚫

- **Toleration = Pass/Key** ✅

- **NoSchedule = Won’t start**

- **PreferNoSchedule = Avoid if possible**

- **NoExecute = Evict running pods too**

___

___

---

**<mark>PART-6 : DEPLOYMENTS, REPLICASET, PROBES, DEPLOYMENT STRATEGIES</mark>**
