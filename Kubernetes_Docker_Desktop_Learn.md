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
