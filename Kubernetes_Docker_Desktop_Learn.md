###FLOW IS:

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

##### PART 3 **<mark>YAML, POD, POD LIFECYCLE, INIT CONTAINERS, SIDECAR CONTAINERS</mark>**

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

##### PART 4 <mark>SIDECAR CONTAINER, QOS, PDB, REQUEST_LIMIT</mark>

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

##### PART 5 **<mark>KUBERNETES SCHEDULING</mark>**

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

##### PART 6 **<mark> DEPLOYMENTS, REPLICASET, PROBES, DEPLOYMENT STRATEGIES</mark>**

# 🧩 ReplicaSet, Deployment & Traffic Splitting — Simple Notes

---

## 🧱 1️⃣ ReplicaController → ReplicaSet → Deployment

### 🔹 ReplicationController

- **Old object**, now rarely used.

- Could maintain fixed replicas of Pods.

- ✅ Deprecated — replaced by **ReplicaSet**.

### 🔹 ReplicaSet

- Maintains **a stable set of identical Pods** (replicas).

- Ensures desired number of Pods are **running at all times**.

- Automatically **recreates Pods** if any crash or are deleted.

- Still used internally by **Deployment**.

> ⚙️ You can create a ReplicaSet directly, but it’s not recommended — use **Deployment** instead.

### 🔹 Deployment

- **Higher-level controller** that manages ReplicaSets.

- Used for **rolling updates**, **rollbacks**, and **scaling** easily.

- When you do `kubectl apply -f deploy.yaml`, Kubernetes actually creates a ReplicaSet inside.

> 🧠 Remember:
> 
> - **Deployment** → manages ReplicaSets
> 
> - **ReplicaSet** → manages Pods

## ⚙️ Example Behavior: ReplicaSet + Pod Labels

### 🧾 nginx-rs.yaml

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

**🧾 pod-rs.yaml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx   # Same label as ReplicaSet selector!
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

🔹 Case 1 — Run ReplicaSet first

```powershell
kubectl apply -f nginx-rs.yaml
kubectl get pods -w
```

✅ 3 Pods created.

Now apply the single Pod:

```powershell
kubectl apply -f pod-rs.yaml
```

Result:

- ReplicaSet sees **4 Pods with same label (app=nginx)**.

- Desired replicas = 3 → **terminates the extra Pod** (your nginx-pod).

🔹 Case 2 — Run Pod first

```powershell
kubectl delete -f nginx-rs.yaml
kubectl apply -f pod-rs.yaml
kubectl apply -f nginx-rs.yaml
```

Result:

- One existing Pod (`nginx-pod`) already running with label `app=nginx`.

- ReplicaSet creates **2 more** to reach total 3 Pods.

- So: 1 manual Pod + 2 ReplicaSet Pods = total 3.

✅ ReplicaSet **manages by label**, not by Pod name.

### 🔹 Debugging tip

If you **change the label** of one of the running Pods (e.g., `app=debug`), ReplicaSet no longer tracks it and creates a **new Pod** to maintain desired count.

**💡 Quick Summary Table**

| Controller                | Manages     | Updates   | Usage                      |
| ------------------------- | ----------- | --------- | -------------------------- |
| **ReplicationController** | Pods        | Manual    | Deprecated                 |
| **ReplicaSet**            | Pods        | Manual    | Still used (by Deployment) |
| **Deployment**            | ReplicaSets | Automatic | Preferred for apps         |

## 🌐 Ingress & Traffic Splitting

### 🔹 What is Traffic Splitting?

Traffic splitting = **divide network traffic** between multiple backends or app versions.  
Used for:

- **Canary releases** (10% → new version, 90% → old version)

- **A/B testing**

- **Gradual rollout**

### 🔹 How it works

- You define **HTTPRoute** or **Ingress** rules.

- Each rule defines **weight** (percentage) for how requests are distributed among services.

🧾 Example (Gateway API)

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: traffic-split
spec:
  parentRefs:
  - name: my-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: app-v1
      weight: 80
    - name: app-v2
      weight: 20
```

📘 Here:

- 80% traffic → `app-v1` Service

- 20% traffic → `app-v2` Service

### 🔹 When to use

- During **rolling updates** or **testing new releases**.

- For **gradual rollout** or **blue-green deployments**.

---

## 🧠 Easy Memory Lines

🧩 **ReplicaSet keeps Pods alive**  
📦 **Deployment manages ReplicaSets**  
⚙️ **Ingress / HTTPRoute manages traffic split**  
🔁 **Traffic splitting = share requests by weight**

---

---

🧩 **ReplicaSet Deletion Policies — Simple Notes**

## 🧱 1️⃣ What Happens When You Delete a ReplicaSet?

When you delete a **ReplicaSet**, Kubernetes uses something called a **propagationPolicy** to decide **what happens to its child Pods**.

You can delete using:

```yaml
curl -X DELETE \
'http://localhost:8080/apis/apps/v1/namespaces/default/replicasets/nginx-rs' \
-d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Foreground"}' \
-H "Content-Type: application/json"
```

or simply using:

```yaml
kubectl delete rs nginx-rs --cascade=foreground
```

🧠 **cascade flag = propagationPolicy**

## ⚙️ 2️⃣ Steps Before Running Deletion (Setup)

1. Create the ReplicaSet:
   
   ```powershell
   kubectl apply -f nginx-rs.yaml
   ```

2. Start proxy to use API directly:
   
   ```powershell
   kubectl proxy --port=8080
   ```

3. Watch what happens:
   
   ```powershell
   kubectl get pods -w
   kubectl get rs -w
   ```

**🚀 3️⃣ The Three Propagation Policies**

### 🔹 **1. Foreground (Parent waits for children to be deleted)**

**Definition:**  
ReplicaSet deletion starts → all child Pods are deleted first → only after all Pods are gone → ReplicaSet object is deleted.

**Example:**

```powershell
curl -X DELETE \
'http://localhost:8080/apis/apps/v1/namespaces/default/replicasets/nginx-rs' \
-d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Foreground"}' \
-H "Content-Type: application/json"
```

HERE IN CURL COMMAND ::
-X DELETE sending deleting request rest call
apis : apps/v1
namespace : default
replicaset : nginx-rs
-d tells data in we are sending ::
        kind : DeleteOptions
        apiVersion : v1
        propagationPolicy : Foreground

Process flow:

```yaml
ReplicaSet (deletion pending)
   ↓
Pods deleted first
   ↓
ReplicaSet deleted last
```

✅ **Use when:** you want a *clean deletion order* — children gone before parent.

🧠 **Easy line:**

> “Foreground = First kids, then parent.”

### 🔹 **2. Background (Parent deleted immediately)**

**Definition:**  
ReplicaSet is deleted immediately, and **Kubernetes garbage collector** later deletes its child Pods in the background.

**Example:**

```powershell
curl -X DELETE \
'http://localhost:8080/apis/apps/v1/namespaces/default/replicasets/nginx-rs' \
-d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Background"}' \
-H "Content-Type: application/json"
```

Process flow:

```yaml
ReplicaSet deleted immediately
   ↓
Pods deleted later (in background)
```

✅ **Use when:** you want *faster deletion* and don’t care about waiting for pods.

🧠 **Easy line:**

> “Background = Parent goes first, kids cleaned up later.”

### 🔹 **3. Orphan (Pods are not deleted)**

**Definition:**  
ReplicaSet is deleted immediately, but its Pods remain running.  
They become **orphaned** — no owner controller.

**Example:**

```powershell
curl -X DELETE \
'http://localhost:8080/apis/apps/v1/namespaces/default/replicasets/nginx-rs' \
-d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Orphan"}' \
-H "Content-Type: application/json"
```

Process flow:

```yaml
ReplicaSet deleted
   ↓
Pods keep running independently
```

✅ **Use when:** you want to keep Pods alive (for debugging or testing).

🧠 **Easy line:**

> “Orphan = Parent gone, kids still running.”

## 📘 4️⃣ Example YAML Files

### `nginx-rs.yaml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
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
        image: nginx:latest
        ports:
        - containerPort: 80
```

pod-rs.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx   # Same label as ReplicaSet selector
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

---

## 🧠 5️⃣ Easy Summary Table

| Propagation Policy | What Happens                       | Parent Deleted | Pods Deleted | Example Use     |
| ------------------ | ---------------------------------- | -------------- | ------------ | --------------- |
| **Foreground**     | Delete Pods first, then ReplicaSet | After Pods     | Yes          | Clean order     |
| **Background**     | Delete ReplicaSet, Pods later      | Immediately    | Yes (later)  | Fast deletion   |
| **Orphan**         | Delete ReplicaSet, keep Pods       | Immediately    | ❌ No         | Keep Pods alive |

## 🪄 6️⃣ Easy Memory Tricks

- 🧩 **Foreground → First kids then parent**

- ⚙️ **Background → Parent first, kids later**

- 👶 **Orphan → Parent gone, kids alone**

- 🧹 **Garbage collector** = the one who cleans up background pods.

kubectl proxy --port=8080

🧠 Simple Explanation:
It creates a secure tunnel between your local machine and the Kubernetes cluster’s API server — so you can interact with the cluster using REST API calls from your browser or tools like curl.

🧪 Example Use Case:
After running this command, you can open your browser and visit:

http://localhost:8080/api/

This will show the Kubernetes API endpoints — useful for debugging, automation, or exploring the cluster.

🔐 Bonus:
It uses your current kubectl authentication, so you don’t need to manually handle tokens or certificates.

## 🚀 **ReplicaSet Scale Down Flow (1 ➡️ 5 easy points)**

When Kubernetes **scales down** a ReplicaSet (removes Pods), it follows this order to decide **which Pods to delete first**:

1️⃣ **Pending Pods first**  
→ If any Pods are stuck in *Pending* state (not running yet), they’re deleted first.

2️⃣ **Pods with deletion annotation (controller.kubernetes.io/pod-deletion-cost)**  
→ Lower deletion-cost Pods are removed first.  
*(Think: lower cost = cheaper to delete)*

3️⃣ **Pods on nodes with more replicas**  
→ Nodes that already have many Pods from this ReplicaSet lose Pods first (for balance).

4️⃣ **Older Pods first**  
→ Pods created earlier are deleted before newer ones.  
*(Think: old leaves fall first 🌿)*

5️⃣ **Otherwise: Random**  
→ If all are equal, Kubernetes randomly deletes one.

🧠 **Memory tip:**  
👉 *Pending → Pod-cost → Node-load → Oldest → Random*  
(or “P–C–N–O–R”)

## ⚙️ **DEPLOYMENTS — Imperative vs Declarative**

### **Imperative (One-line command)**

You directly tell Kubernetes *what to do right now.*

```bash
kubectl create deploy mynginx --image=nginx --replicas=3 --port=80`
```

✅ **Creates** a Deployment named `mynginx`  
✅ Uses `nginx` image  
✅ Runs **3 replicas**  
✅ Exposes **port 80**

🧠 *Tip:* “Imperative = Immediate action.”

### **Declarative (YAML file way)**

You write what you **want** in a file → then **apply** it.

`kubectl create deploy bootcamp --image=nginx --replicas=3 --port=80 --dry-run=client -o yaml > deploy.yaml`

Then you can edit the file and apply it:

`kubectl apply -f deploy.yaml`

🧠 *Tip:* “Declarative = Documented configuration.”

## 📈 **Scaling Commands**

### **Scale Up** (Increase pods)

`kubectl scale deploy bootcamp --replicas=5`

➡️ Increases from 3 → 5 Pods.

### **Scale Down** (Decrease pods)

`kubectl scale deploy bootcamp --replicas=4`

➡️ Reduces Pods from 5 → 4 (follows the scale-down flow).

## ✅ **Quick Summary Note**

| Concept                         | Command                                                              | Meaning                         |
| ------------------------------- | -------------------------------------------------------------------- | ------------------------------- |
| Create Deployment               | `kubectl create deploy mynginx --image=nginx --replicas=3 --port=80` | Imperative creation             |
| Generate YAML (for declarative) | `--dry-run=client -o yaml`                                           | Creates YAML without running it |
| Scale Up                        | `kubectl scale deploy bootcamp --replicas=5`                         | Adds Pods                       |
| Scale Down                      | `kubectl scale deploy bootcamp --replicas=4`                         | Removes Pods                    |
| Apply Config                    | `kubectl apply -f deploy.yaml`                                       | Applies YAML file               |

## 💡 **Pro Tips to Remember**

- 🧩 **Imperative → Do now**, **Declarative → Plan first**

- 📦 Use `kubectl get deploy` to see replicas

- 📊 Use `kubectl get rs` to see which ReplicaSet is controlling your Pods

- 🧹 Scaling down never affects newer Pods unless needed

## 🚀 **Deployment Strategy in Kubernetes (Simplified Note)**

### ⚙️ Command Example:

`kubectl create deploy mynginx --image=nginx --replicas=3 --port=80`

When you create a Deployment like this 👆,  
Kubernetes **automatically adds a default deployment strategy** — even if you don’t specify one.

## 🧩 **Default Strategy: RollingUpdate**

When you check the Deployment YAML (`kubectl get deploy mynginx -o yaml`),  
you’ll see something like:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%
```

✅ This means:

- **type: RollingUpdate** → update Pods one by one (gradually)

- **maxSurge: 25%** → allow up to 25% **extra Pods** (temporary surge) during update

- **maxUnavailable: 25%** → allow up to 25% **Pods to be unavailable** during update

🧠 *Tip:* RollingUpdate = smooth replacement → no downtime (or very minimal)

## 🔄 **Why RollingUpdate is Default**

Because it gives **zero downtime deployment** by:

- Creating new Pods with the updated version

- Slowly removing old Pods

- Keeping enough Pods running so the app is always available

🧠 *Easy phrase:*

> “**RollingUpdate rolls new Pods in, old Pods out — with users never noticing!**”

## 🔧 **If You Specify Another Strategy**

If you explicitly mention strategy in YAML,  
Kubernetes will use **your** settings instead of the default.

Example:

```yaml
strategy:
  type: Recreate
```

➡️ This will **delete all old Pods first**, then create new ones.  
⚠️ Causes **downtime**, but ensures a clean restart (used for apps that can’t run multiple versions together).

🧠 *Tip:*

> “**Recreate = Remove all, then recreate.**”

## ✅ **Quick Comparison Table**

| Strategy Type               | Behavior                                | Downtime          | Use Case                      |
| --------------------------- | --------------------------------------- | ----------------- | ----------------------------- |
| **RollingUpdate (Default)** | Updates Pods one by one                 | ❌ No (or minimal) | Most web apps                 |
| **Recreate**                | Deletes old Pods, then creates new ones | ✅ Yes             | DB changes, version conflicts |

## 💡 **Pro Tips to Remember**

- 🧩 Default strategy = **RollingUpdate**

- 🕒 Use **RollingUpdate** → for continuous availability

- 🧹 Use **Recreate** → only when needed (e.g., incompatible app versions)

- 🧠 Shortcut memory:
  
  > **R-U → RollingUpdate = Reliable & Up**  
  > **Recreate → Restart Everything**

🧩 **RollingUpdate vs Recreate — YAML Comparison**

RollingUpdate Strategy Example :

```yaml
# ==========================================================
# 🌀 STRATEGY 1: RollingUpdate (DEFAULT)
# ----------------------------------------------------------
# ✅ ZERO Downtime
# ✅ Gradually replaces old Pods with new ones
# ✅ Best for web apps or services that must stay available
# ==========================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-rolling
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: RollingUpdate               # Default strategy
    rollingUpdate:
      maxSurge: 25%                   # Allow 25% extra Pods during update
      maxUnavailable: 25%             # Allow 25% Pods to be temporarily unavailable
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Recreate Strategy Example :

```yaml
# ==========================================================
# 🔁 STRATEGY 2: Recreate
# ----------------------------------------------------------
# ⚠️ Downtime occurs
# 🚫 Deletes all old Pods before creating new ones
# ✅ Best for apps that cannot run two versions together
# ==========================================================
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-recreate
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy:
    type: Recreate                    # Deletes old Pods first, then creates new ones
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

## 🧠 **Memory Shortcut**

| Strategy          | Meaning                  | Downtime | Tip                      |
| ----------------- | ------------------------ | -------- | ------------------------ |
| **RollingUpdate** | Gradual pod replacement  | ❌ No     | “Roll = smooth change”   |
| **Recreate**      | Delete → then create new | ✅ Yes    | “Recreate = Restart All” |

**🧾 Kubernetes Deployment — Rollout, Record, and Revision Notes**

⚙️ **Command Example**

`kubectl create deploy bootcamp --image nginx --replicas 3 --port 80`

`kubectl set image deploy bootcamp nginx=nginx:1.14.a --record`

### 💡 What does `--record` do?

- The `--record` flag **adds the command you ran** (like the image update) into the Deployment’s **annotation** under  
  `kubernetes.io/change-cause`.

- It helps you **track what change was made and why**, in rollout history.

- If you **don’t use `--record`**, the “CHANGE-CAUSE” will show `<none>` in rollout history.

🧠 *Simple line to remember:*

> “`--record` = Remember what you did.”

✅ **Example output:**

```yaml
kubectl rollout history deploy/bootcamp
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deploy bootcamp nginx=nginx:1.14.a --record=true
3         kubectl set image deploy bootcamp nginx=nginx:1.14.b --record=true
```

⚠️ Note:  
`--record` is now **deprecated** in newer Kubernetes versions.  
👉 Instead, we manually add a `CHANGE-CAUSE` using an **annotation**.

## 🏷️ **Manual Way (Recommended Now)**

You can manually patch your Deployment to add or update the annotation:

```yaml
kubectl annotate deploy bootcamp kubernetes.io/change-cause="Updated nginx image to 1.14.b"
```

Now when you check:

`kubectl rollout history deploy/bootcamp`

you’ll see your custom message under `CHANGE-CAUSE`.

🧠 *Tip:*

> “Annotation = Manual Record”

🔁 **Deployment Rollout & Revision Concepts**

### 🔹 **Each update = New Revision**

Whenever you:

- Change the image

- Change the strategy

- Change the configuration in spec

Kubernetes creates a **new ReplicaSet** (and new revision).

✅ **Example:**

```yaml
Revision 1 → nginx:latest
Revision 2 → nginx:1.14.a
Revision 3 → nginx:1.14.b
```

You can check details:

`kubectl rollout history deploy/bootcamp --revision=2`

`kubectl rollout status deployment/bootcamp`

## 🧨 **Rollback**

If something goes wrong, you can roll back:

`kubectl rollout undo deployment/bootcamp --to-revision=1`

This brings Deployment back to the image/config of Revision 1.

🧠 *Tip:*

> “Undo → Go back to old version safely.”

🧩 **Relationship Between Deployment, ReplicaSet, and Pods**

`Deployment → creates ReplicaSet → creates Pods`

- **Deployment** = higher-level controller (manages rollout, rollback, scaling, strategy)

- **ReplicaSet** = ensures correct number of Pods are running

- **Pods** = actual running containers

🧠 *Tip:*

> “Deployment is the boss, ReplicaSet is the manager, Pods are the workers.”

## ⚙️ **Scaling and Control**

### **Scale Up / Down**

`kubectl scale deploy/bootcamp --replicas=6`

➡️ Deployment automatically updates ReplicaSet and Pod count.

Pause / Resume Rollout

```yaml
kubectl rollout pause deploy/bootcamp
kubectl rollout resume deploy/bootcamp
```

✅ Use **pause** before applying multiple changes (avoid triggering multiple rollouts).  
✅ Use **resume** to continue rollout once all changes are ready.

🧠 *Tip:*

> “Pause = Stop updates temporarily, Resume = Continue rollout.”

## 🧠 **Quick Summary Table**

| Concept           | Command                                                             | Meaning                         |
| ----------------- | ------------------------------------------------------------------- | ------------------------------- |
| Create Deployment | `kubectl create deploy bootcamp --image=nginx`                      | Creates Deployment & ReplicaSet |
| Set Image         | `kubectl set image deploy bootcamp nginx=nginx:1.14.a --record`     | Updates image & records change  |
| Check History     | `kubectl rollout history deploy/bootcamp`                           | View rollout revisions          |
| Annotate Change   | `kubectl annotate deploy bootcamp kubernetes.io/change-cause="..."` | Manually record change          |
| Rollback          | `kubectl rollout undo deploy/bootcamp --to-revision=2`              | Revert to old revision          |
| Scale             | `kubectl scale deploy/bootcamp --replicas=6`                        | Change number of Pods           |
| Pause / Resume    | `kubectl rollout pause/resume deploy/bootcamp`                      | Temporarily stop/resume rollout |

## 🧠 **Simple Memory Tips**

- 🔸 `--record` → “Remember the change.”

- 🔸 Annotation → “Manual reason for change.”

- 🔸 Deployment → “Boss of ReplicaSet.”

- 🔸 RollingUpdate → “No downtime.”

- 🔸 Recreate → “Downtime but clean start.”

- 🔸 Rollout Undo → “Time travel to old version.”

FOR RECREATE ::

```powershell
controlplane:~/Kubernetes-hindi-bootcamp/part6$ cat recreate.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
      - name: demo
        image: nginx:latest
        ports:
        - containerPort: 80

kubectl apply -f recreate.yaml

controlplane:~$ kubectl set image deployments/demo-deployment demo=nginx:14.0
deployment.apps/demo-deployment image updated
```

generally we don't do recreate because there is lot of downtime means the application we are running all the pods will terminate and die and then recreate and for new pods may be it will take time for creation.

so there is difference between rollingUpdate and recreate-strategy in recreate there is a downtime.

🧾 Kubernetes PROBES — Simple & Complete Notes

## 🎯 **Why We Need Probes**

When we create a Pod and expose it through a Service,  
Kubernetes starts sending traffic **immediately** — even if:

- the app is **not ready yet**,

- a **file/dependency** is missing,

- or the app has gone into a **deadlock** (e.g., two Pods writing to the same file).

✅ To prevent sending traffic to such **unhealthy** or **unready** Pods,  
Kubernetes gives us **Probes**.

## 🔍 **Types of Probes**

1. **Readiness Probe**

2. **Liveness Probe**

3. **Startup Probe**

4. **(Optional)** gRPC Probe *(for gRPC-based services)*

### 🧩 **1. Readiness Probe**

📌 Checks: **Is the Pod ready to handle traffic?**

- Kubernetes uses this to decide whether a Pod should receive requests.

- If **Readiness fails**, the Pod is **removed** from the Service endpoints — so no traffic goes to it.

- Used to check dependencies like DB, config file, or service latency.

🧠 *Think:*

> “Readiness = Ready for traffic or not?”

### 🧩 **2. Liveness Probe**

📌 Checks: **Is the application still alive and running properly?**

- Even if the app is running, it might be stuck (e.g., deadlock or hung thread).

- Liveness Probe checks if the container is **responsive**.

- If it fails, **kubelet restarts the container automatically**.

🧠 *Think:*

> “Liveness = Alive or dead?”

**Common types:**

- **httpGet** → Checks URL returns success (HTTP 200–399)

- **tcpSocket** → Checks if port is open

- **exec** → Runs custom command inside container

Example:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 15
  timeoutSeconds: 2
  periodSeconds: 5
  failureThreshold: 3
```

### 🧩 **3. Startup Probe**

📌 Checks: **Has the application finished starting up yet?**

- Used when your app takes time to boot up (e.g., Java Spring Boot).

- It runs **before** Liveness and Readiness.

- Until Startup Probe succeeds, Kubernetes **does not run** the other probes.

🧠 *Think:*

> “Startup = Let me wake up first, then check me.”

### 🧩 **4. GRPC Probe** *(Advanced, optional)*

- Used for apps exposing **gRPC endpoints** instead of HTTP.

- Checks health through gRPC service calls.

## 🔄 **Probe Parameter Meanings (Easy Definitions)**

| Parameter               | Meaning                                |
| ----------------------- | -------------------------------------- |
| **initialDelaySeconds** | Wait before running first check        |
| **timeoutSeconds**      | Time to wait for probe response        |
| **periodSeconds**       | How often to run probe                 |
| **successThreshold**    | How many successes to mark as healthy  |
| **failureThreshold**    | How many failures to mark as unhealthy |

🧠 *Tip:*

> “Delay → Timeout → Period → Success → Failure” (D-T-P-S-F = probe sequence memory)

⚙️ **Probe Example Summary (from probe2.yaml)**

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 15
  timeoutSeconds: 2
  periodSeconds: 5
  failureThreshold: 3

readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  timeoutSeconds: 2
  periodSeconds: 5
  successThreshold: 1
  failureThreshold: 3

startupProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5
  failureThreshold: 10
```

## 🧠 **How Probes Work — Flow Explanation (Step-by-Step)**

### 🧩 **1. Pod Creation Phase**

- Pod is scheduled → container starts.

- If **Startup Probe** is defined, Kubernetes waits for it first.

---

### 🧩 **2. Startup Probe (Initialization Phase)**

- Checks until app starts successfully.

- Other probes (Readiness, Liveness) are **paused** until Startup succeeds.

- If Startup fails (exceeds failureThreshold) → container **restarts**.

🧠 *Think:*

> Startup = “Wait until I’m ready to be checked.”

---

### 🧩 **3. Readiness Probe (Traffic Control Phase)**

- After Startup passes, Readiness begins.

- Checks if app is ready to **accept traffic**.

- If success → Pod marked **Ready** → added to Service endpoint → starts receiving traffic.

- If fails → Pod marked **NotReady** → removed from Service temporarily.

🧠 *Think:*

> Readiness = “Am I ready for users?”

---

### 🧩 **4. Liveness Probe (Health Monitoring Phase)**

- Runs continuously to check if app is **healthy and responding**.

- If it fails beyond failureThreshold → kubelet **restarts container**.

- Helps recover from crashes, deadlocks, or hung processes.

🧠 *Think:*

> Liveness = “Still alive?”

---

### 🧩 **5. Continuous Monitoring**

- Kubernetes keeps cycling through these checks:
  
  - **Startup** → once at boot
  
  - **Readiness** → before sending traffic
  
  - **Liveness** → while serving traffic

✅ If any fails, appropriate action taken (pause traffic or restart container).

---

## ⚠️ **Failure Scenario**

Example:  
If `livenessProbe` fails 3 times in a row:

1. kubelet marks container as **Unhealthy**

2. kubelet **restarts** the container

3. New container runs probes again from start

🧠 *Tip:*

> “Fail 3 → Restart Me”

EXAMPLE : probe2.yaml

```yaml
probe2.yaml
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
        image: nginx:latest
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 15
          timeoutSeconds: 2
          periodSeconds: 5
          failureThreshold: 3
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          timeoutSeconds: 2
          periodSeconds: 5
          successThreshold: 1
          failureThreshold: 3
        startupProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 5
          failureThreshold: 10
```

## 🧩 **Quick Practical Commands**

| Purpose                | Command                                      |
| ---------------------- | -------------------------------------------- |
| Apply probe file       | `kubectl apply -f probe2.yaml`               |
| Check pod details      | `kubectl describe pod <pod-name>`            |
| See probe results      | Look under “Liveness” & “Readiness” sections |
| Delete all deployments | `kubectl delete deploy --all`                |
| Delete all pods        | `kubectl delete pods --all`                  |
| Delete all ReplicaSets | `kubectl delete rs --all`                    |

## 🧠 **Easy Summary to Remember**

| Probe         | Checks                  | Action on Fail                   | When It Runs          |
| ------------- | ----------------------- | -------------------------------- | --------------------- |
| **Startup**   | Is app started yet?     | Waits / Restart if exceeds limit | Before others         |
| **Readiness** | Can app accept traffic? | Remove from Service              | After startup success |
| **Liveness**  | Is app alive & healthy? | Restart container                | During runtime        |

🧩 *Flow Memory Line:*

> “Startup → Readiness → Liveness”  
> (Wake up → Accept → Stay alive)

## 💡 **Pro Tips**

- Always use **Startup Probe** for apps with slow boot time (e.g., Spring Boot, Java, .NET).

- Combine **Liveness + Readiness** for full lifecycle protection.

- Use `kubectl describe pod` often to observe **probe failures and restarts** in real time.

- **Too aggressive probe settings** (short delays or timeouts) can cause **unnecessary restarts** — tune them properly.

___

🐳 **Kubernetes Canary Deployment & Gateway API — Simplified Notes**

## 🔹 WHY CANARY DEPLOYMENT?

Canary deployment is used when you want to **gradually roll out a new version** of your app  
to a small set of users before sending it to everyone.

💡 **Idea:** Test new version safely with real traffic → verify → then roll out to all.

## ⚙️ **Example Setup (Without Service Mesh or Gateway API)**

### **Service (canary-svc.yaml)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx           # Common label for both versions
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

**Stable Deployment (deploy-canary.yaml)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-stable
spec:
  replicas: 9
  selector:
    matchLabels:
      app: nginx
      version: "1.17"
  template:
    metadata:
      labels:
        app: nginx
        version: "1.17"
    spec:
      containers:
      - name: nginx
        image: nginx:1.17
        ports:
        - containerPort: 80
```

**Canary Deployment (deploy-canary-v2.yaml)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
      version: "1.18"
  template:
    metadata:
      labels:
        app: nginx
        version: "1.18"
    spec:
      containers:
      - name: nginx
        image: nginx:1.18
        ports:
        - containerPort: 80
```

🧩 **Command Flow**

```powershell
kubectl apply -f canary-svc.yaml
kubectl apply -f deploy-canary.yaml
kubectl apply -f deploy-canary-v2.yaml
```

✅ Verify:

```powershell
kubectl get pods
kubectl get svc
kubectl get ep -oyaml
```

🧠 **Observation:**  
In the Service’s Endpoints list (`kubectl get ep nginx-service -oyaml`):

- You’ll see **1 pod from nginx-canary**

- And **9 pods from nginx-stable**

➡️ So traffic gets split **roughly 10% (canary)** and **90% (stable)**,  
because Kubernetes load balances equally **across all endpoints**.

## 🧠 **Quick Summary — Native Canary Logic (Without Gateway API)**

| Step | Description                                          |
| ---- | ---------------------------------------------------- |
| 1️⃣  | Both deployments share same label `app: nginx`       |
| 2️⃣  | Service selector `app: nginx` matches both versions  |
| 3️⃣  | K8s Service sends requests equally to all Pods       |
| 4️⃣  | Based on replica ratio → 1:9 = 10% traffic to canary |
| 5️⃣  | If canary works fine, gradually scale it up          |
| 6️⃣  | Once stable, remove old deployment                   |

🪄 **Tip:** This is the “native” way to simulate canary rollout without Istio, Linkerd, or Gateway API.

---

---

## 🌉 **GATEWAY API — MODERN & ADVANCED TRAFFIC CONTROL**

### 🔹 What is Gateway API?

Gateway API is the **next-generation Kubernetes networking API**,  
replacing old **Ingress** for **more control, flexibility, and standardization**.

### 💡 **USE CASES of Gateway API**

| Use Case                                  | Description                                                                        |
| ----------------------------------------- | ---------------------------------------------------------------------------------- |
| **Canary Deployment (Traffic Splitting)** | Split HTTP traffic between stable & canary versions (e.g., 90% stable, 10% canary) |
| **Blue-Green Rollouts**                   | Route traffic to “green” version only when ready                                   |
| **HTTP Routing**                          | Match routes based on path, host, headers                                          |
| **HTTP Redirects & Rewrites**             | Redirect users (e.g., `/old → /new`)                                               |
| **Header Modifiers**                      | Add, remove, or update HTTP headers                                                |
| **Request Mirroring**                     | Send a copy of live traffic to another backend (for testing)                       |
| **Traffic Weighting**                     | Weighted routing based on percentages or conditions                                |

⚙️ **Basic HTTPRoute Example (Traffic Splitting)**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
spec:
  parentRefs:
  - name: my-gateway          # Your Gateway object
  rules:
  - backendRefs:
    - name: nginx-stable
      port: 80
      weight: 90
    - name: nginx-canary
      port: 80
      weight: 10
```

📊 **Result:**  
→ 90% traffic → `nginx-stable`  
→ 10% traffic → `nginx-canary`

## 🧠 **Quick Memory Chart**

| Feature   | Native Service Split       | Gateway API Split                             |
| --------- | -------------------------- | --------------------------------------------- |
| Setup     | Easy (via replicas)        | More control & flexibility                    |
| Precision | Rough (based on Pod count) | Exact (percentage weights)                    |
| Features  | Basic load balancing       | Traffic split, mirror, redirect, header rules |
| Ideal for | Simple clusters            | Production-grade traffic control              |

🌉 **Gateway API — Simplified & Practical Explanation**

## 🧩 **1️⃣ What Is Gateway API?**

Gateway API is the **next generation** of Kubernetes networking —  
it’s the **evolution of Ingress**, giving **more power, flexibility, and clarity** for handling traffic.

Think of it as:

> “Ingress 2.0 — cleaner, modular, and smarter way to manage network traffic in Kubernetes.”

## 🚦 **2️⃣ Why Gateway API (vs Ingress)?**

| Ingress                          | Gateway API                                            |
| -------------------------------- | ------------------------------------------------------ |
| One big YAML with complex rules  | Split into smaller, reusable parts                     |
| Hard to manage multiple teams    | Supports **multi-team ownership**                      |
| Limited control (host/path only) | Advanced control (weight, mirror, headers, redirects)  |
| Controller specific              | Standardized API for all (NGINX, Istio, Traefik, etc.) |

💡 **Memory Tip:**  
➡️ *Ingress = simple roads*  
➡️ *Gateway API = smart traffic system with lanes, lights, and rules*

*rules*

---

## 🧱 **3️⃣ Key Building Blocks**

| Object                               | Role                                           | Analogy                          |
| ------------------------------------ | ---------------------------------------------- | -------------------------------- |
| **GatewayClass**                     | Defines type of Gateway (controller used)      | “Car Brand” (NGINX, Istio, etc.) |
| **Gateway**                          | The entry point (like LoadBalancer or Ingress) | “Main Gate” to your cluster      |
| **HTTPRoute / TCPRoute / GRPCRoute** | Defines routing rules                          | “Traffic Rules” (who goes where) |
| **BackendRefs**                      | Points to Services                             | “Destination Parking Slots”      |

## 🧭 **4️⃣ How It Works (Simple Flow)**

`Client → Gateway (entry) → HTTPRoute (rules) → Service (Pods)`

💡 So Gateway decides **which HTTPRoute to follow**,  
and HTTPRoute decides **which Service to send traffic to.**

## 🚀 **5️⃣ Simple Example — Canary Traffic Split**

You have two versions of Nginx:

- Stable → 90%

- Canary → 10%

### 🧩 **Gateway**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: my-gateway
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
```

🧩 **HTTPRoute**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
spec:
  parentRefs:
  - name: my-gateway
  rules:
  - backendRefs:
    - name: nginx-stable
      port: 80
      weight: 90
    - name: nginx-canary
      port: 80
      weight: 10
```

🧠 **How it Works:**

- Gateway listens on port 80 (entry point).

- HTTPRoute attaches to Gateway using `parentRefs`.

- Traffic is split: **90% to stable**, **10% to canary** — exactly (not just by pod ratio).

## 🧠 **6️⃣ Quick Use Cases to Remember**

| Use Case                | Description                                      | Example                       |
| ----------------------- | ------------------------------------------------ | ----------------------------- |
| **Traffic Splitting**   | Split between stable & canary versions           | 90% stable, 10% canary        |
| **Request Mirroring**   | Copy live traffic to another service for testing | Mirror `/api` to test backend |
| **Routing Rules**       | Route based on hostname/path                     | `/v1 → old`, `/v2 → new`      |
| **Redirects/Rewrites**  | Change URLs before forwarding                    | `/home → /index.html`         |
| **Header Modification** | Add/remove headers for auth, tracing             | Add `x-user-id` header        |
| **gRPC Routing**        | Handle gRPC traffic natively                     | gRPC load balancing support   |

## 💡 **7️⃣ Easy Memory Map**

| Concept         | Meaning                              | Keyword                |
| --------------- | ------------------------------------ | ---------------------- |
| **Gateway**     | Entry point                          | “Main gate”            |
| **HTTPRoute**   | Rules                                | “Traffic signs”        |
| **BackendRefs** | Destination                          | “Target services”      |
| **Weight**      | Traffic ratio                        | “90–10 Split”          |
| **ParentRefs**  | Connection between Gateway and Route | “Attach rules to gate” |

## 🪄 **8️⃣ Simple Analogy**

Think of Kubernetes networking like a **city**:

| Component    | Analogy                            |
| ------------ | ---------------------------------- |
| GatewayClass | Type of gate (manual, automatic)   |
| Gateway      | Main entrance to the city          |
| HTTPRoute    | Roads and direction boards         |
| BackendRefs  | Buildings/shops where traffic goes |
| Weight       | How many cars go to each road      |

## 🧱 Example: Routing HTTP Traffic to a Backend Service

### 🎯 Goal

Route HTTP traffic from `http://example.com` to a backend service called `my-service` running on port 8080.

## 🔄 Flow Overview

1. **GatewayClass**: Choose NGINX as the controller.

2. **Gateway**: Define the entry point (like a LoadBalancer).

3. **HTTPRoute**: Set rules to match `example.com` and forward traffic.

4. **BackendRefs**: Point to `my-service`.

📁 YAML Manifests

1️⃣ GatewayClass

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: nginx-gateway-class
spec:
  controllerName: nginx.org/gateway-controller
```

2️⃣ Gateway

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: my-gateway
  namespace: default
spec:
  gatewayClassName: nginx-gateway-class
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    hostname: "example.com"
```

3️⃣ HTTPRoute

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: my-route
  namespace: default
spec:
  parentRefs:
  - name: my-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: my-service
      port: 8080
```

4️⃣ Backend Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  namespace: default
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
```

## 🧪 Commands to Run

### ✅ Step-by-step

```powershell
# 1. Install NGINX Gateway Controller (if not already)
kubectl apply -f https://github.com/nginxinc/kubernetes-gateway/releases/latest/download/deploy.yaml

# 2. Apply GatewayClass
kubectl apply -f gatewayclass.yaml

# 3. Apply Gateway
kubectl apply -f gateway.yaml

# 4. Apply HTTPRoute
kubectl apply -f httproute.yaml

# 5. Apply Backend Service
kubectl apply -f service.yaml

# 6. Verify resources
kubectl get gatewayclass
kubectl get gateway
kubectl get httproute
kubectl get svc
```

## 🧠 What Happens

- Traffic to `example.com` hits the **Gateway**.

- The **HTTPRoute** matches the path `/`.

- Traffic is forwarded to `my-service:8080`.

---

##### PART 7 **<mark>ConfigMaps and Secrets</mark>**

# 🧩 **ConfigMaps & Secrets**

---

## 🔹 **What is a ConfigMap?**

👉 A **ConfigMap** stores **non-confidential configuration data** (key–value pairs) for your application.  
Use it when you want to **separate configuration from code**.

📦 Example: database name, username, environment settings, etc.

⚙️ **Creating a ConfigMap**

```powershell
kubectl create configmap bootcamp-configmap \
  --from-literal=username=saiyam \
  --from-literal=database_name=exampledb
```

✅ **Meaning:**

- Creates a ConfigMap named `bootcamp-configmap`

- Stores two keys:
  
  - `username = saiyam`
  
  - `database_name = exampledb`

🔍 **Check the ConfigMap**

```powershell
kubectl get configmaps
kubectl get configmaps bootcamp-configmap -o yaml
```

## 🧠 **4 Ways to Use a ConfigMap**

| Usage                                    | Description                                 | Example                             |
| ---------------------------------------- | ------------------------------------------- | ----------------------------------- |
| 1️⃣ Inside container `command` or `args` | Pass as input to a command                  | `args: ["--config=$(CONFIG_PATH)"]` |
| 2️⃣ As **Environment Variable**          | Inject values directly                      | `envFrom: configMapRef:`            |
| 3️⃣ As **Mounted File (Volume)**         | Mount config files inside container         | `/etc/config/...`                   |
| 4️⃣ Programmatically (via K8s API)       | App reads ConfigMap using Kubernetes client | Used in advanced apps               |

## 🔄 **ConfigMap Updates**

🧠 Remember:

- If ConfigMap is **used as env variable**, the pod **won’t auto-refresh** → you must **restart the pod**.

- If **mounted as a volume (no subPath)** → updates **automatically reflect** inside the file.

- Controlled by:  
  `configMapAndSecretChangeDetectionStrategy` in **kubelet config**.

- if we use ConfigMap as mounted volume and in that there is no sub-path(like for example there is no /mnt/var etc meaning multiple paths are not there means on one path no other multiple paths ) then in that case configMap changes are reflected in filesystem mounted and in any other cases we have to do restart mechanism. And that setting is configured in configMapAndSecretChangeDetectionStrategy in kubelet setting.

- When you **change or modify a ConfigMap**, the **application (Pod) does not automatically detect or reload the new values**.  
  In native Kubernetes, you must **restart the Pod** to pick up the updated ConfigMap. Some external tools like **Argo CD (with auto-sync)** or **Config Sync** can automatically synchronize these changes without a manual restart.

- ### 💡 **Why restart?**
  
  - Pods **load ConfigMap values only at startup**.
  
  - After startup, they don’t continuously “watch” for ConfigMap changes.
  
  - So when you update ConfigMap → Pod still uses old data → needs restart.

### ⚙️ **Exception / Special Cases**

- If the ConfigMap is **mounted as a Volume (without subPath)** → the **file on disk updates automatically**.  
  But the **app must reread** that file itself to notice changes.

- If ConfigMap is **used as environment variables**, restart is **always required**.

- Tools like **Argo CD**, **Config Sync**, or **Stakater Reloader** automate the restart/sync process.

### 🪄 **One-Line Memory Trick**

> **“ConfigMap changes don’t auto-reach the Pod — restart POD or use sync tools.”**

## 🔐 **Immutable ConfigMap**

Make a ConfigMap **read-only (can’t modify)**:

`immutable: true`

✅ Prevents accidental changes — you can only **delete and recreate** it.

## 🔒 **What is a Secret?**

A **Secret** is like a ConfigMap but for **sensitive data** (passwords, tokens, certificates).  
Data is **base64 encoded**.

```powershell
kubectl create secret generic db-secret \
  --from-literal=password=mysecretpassword
```

## 🧭 **ConfigMap vs Secret (Quick Table)**

| Feature   | ConfigMap            | Secret                  |
| --------- | -------------------- | ----------------------- |
| Data Type | Non-confidential     | Confidential            |
| Storage   | Plain text           | Base64 encoded          |
| Use For   | App config, settings | Passwords, tokens, keys |
| Editable  | Yes (or immutable)   | Yes (or immutable)      |

## 🧠 **Memory Shortcut**

> **ConfigMap → Configuration (non-secret)**  
> **Secret → Secure (passwords, tokens)**

## 🔁 **Full Process Recap**

| Step | Command                        | Purpose                  |
| ---- | ------------------------------ | ------------------------ |
| 1️⃣  | `kubectl create configmap ...` | Create ConfigMap         |
| 2️⃣  | `kubectl get configmaps`       | Verify it                |
| 3️⃣  | Use it in Pod (env/volume)     | Application reads values |
| 4️⃣  | Restart pod if needed          | To apply new config      |
| 5️⃣  | Optional: `immutable: true`    | Lock the config          |

🧩 **Easy Example in Pod (Using ConfigMap as Env)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: demo
    image: nginx
    envFrom:
    - configMapRef:
        name: bootcamp-configmap
```

✅ This will inject all ConfigMap keys as environment variables in the container.

COMMAND:

```powershell
controlplane:~$ kubectl create configmap bootcamp-configmap --from-literal=username=saiyam --from-literal=database_name=exampledb
configmap/bootcamp-configmap created
controlplane:~$ kubectl get secrets
No resources found in default namespace.
controlplane:~$ kubectl get configmaps
NAME                 DATA   AGE
bootcamp-configmap   2      31s
kube-root-ca.crt     1      29d
controlplane:~$ kubectl get configmaps bootcamp-configmap -oyaml
apiVersion: v1
data:
  database_name: exampledb
  username: saiyam
kind: ConfigMap
metadata:
  creationTimestamp: "2025-10-19T15:33:27Z"
  name: bootcamp-configmap
  namespace: default
  resourceVersion: "4034"
  uid: 15cef121-6975-4be8-8ee1-cfd0111498a2
controlplane:~$
```

## 💡 **One-Line Summary**

> **ConfigMap = Non-secret config data**  
> **Secret = Encoded sensitive data**  
> **Mount or inject → App uses it → Restart if not auto-updated**

**USING CONFIGMAP IN DIFFERENT WAYS**

##### EXAMPLE 1:

## 🧩 **Goal:**

Use **ConfigMap** values **inside container commands or args** — for example, pass config paths or environment variables to your app

## ⚙️ **Step-by-Step Example**

1️⃣ Create a ConfigMap

```powershell
kubectl create configmap app-config --from-literal=CONFIG_PATH=/etc/config/config.yaml
```

✅ This creates:

Key: CONFIG_PATH
Value: /etc/config/config.yaml

2️⃣ Use the ConfigMap in a Pod (in command or args)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo
spec:
  containers:
  - name: demo
    image: busybox
    command: ["sh", "-c"]
    args: ["echo Config file path is: $(CONFIG_PATH)"]

    env:
    - name: CONFIG_PATH
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: CONFIG_PATH
```

### 🧩 **Explanation**

- The `env:` section reads data from ConfigMap → stores it as an environment variable inside the container.

- Then the container’s `args` uses that variable with `$(CONFIG_PATH)`.

When the Pod runs, it executes:

```shell
echo Config file path is: /etc/config/config.yaml
```

### 🪄 **Easy-to-Remember Trick**

> **ConfigMap → env variable → used in command or args.**  
> “First define it, then use it!”

#### **EXAMPLE 2:**

EXAMPLE 1: **ConfigMap CAN BE USED AS ENVIRONMENT VARIABLE**
Creating config map to demonstrates how you can have a user for the database where user is passed in as a config map as an env:

FILE:

```yaml
cm1.yaml :
apiVersion: v1
kind: ConfigMap
metadata:
  name: bootcamp-configmap
data:       # in configMap we specify key-value like this here in first like key is username and value is saiyam
  username: "saiyam"
  database_name: "exampledb"

pod1.yaml :
apiVersion: v1
kind: Pod
metadata:
  name: mysql-pod
spec:
  containers:
  - name: mysql
    image: mysql:5.7
    env:
      - name: MYSQL_USER # environment variable name used for mysql to specify user
        valueFrom:
          configMapKeyRef:
            name: bootcamp-configmap #passing configMap name as reference where key i.e. username can be reffered from the configMap
            key: username
      - name: MYSQL_DATABASE  # environment variable name used for mysql to specify database name
        valueFrom:
          configMapKeyRef:
            name: bootcamp-configmap #passing configMap name as reference where key i.e. db name can be reffered from the configMap
            key: database_name
      - name: MYSQL_PASSWORD
        value: demo123  # Specify a strong password.
      - name: MYSQL_ROOT_PASSWORD
        value: demo345 # You should change this value.
    ports:
      - containerPort: 3306
        name: mysql
    volumeMounts:
      - name: mysql-storage
        mountPath: /var/lib/mysql
  volumes:
    - name: mysql-storage
      emptyDir: {}
```

COMMANDS :

```shell
kubectl apply -f cm1.yaml
kubectl apply -f pod1.yaml

# this will give mysql terminal
kubectl exec -it mysql-pod -- mysql -u root -p

# we can check here that user defined in configMap is created
SELECT user FROM mysql.user;

#we can check here that database name defined in configMap is created
SHOW DATABASES;
```

##### Example 2 : ConfigMap CAN BE USED AS MOUNTED VOLUME

##### demonstrates a practical use case where the dev/prod properties can be defined and used for the application when required. In prod your application should be able to read config from the file located at /etc/config/settings.properties. This could mean parsing the file on startup or dynamically reading values when required.

FILE :

```yaml
cm2.yaml:-
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config-dev
data:
  settings.properties: |
    # Development Configuration
    debug=true
    database_url=http://dev-db.example.com
    featureX_enabled=false

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config-prod
data:
  settings.properties: |   # this seems settings.properties work like a file or create a file with name settings.properties
    # Production Configuration
    debug=false
    database_url=http://prod-db.example.com
    featureX_enabled=true

pod2.yaml;-
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-web-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-web-app
  template:
    metadata:
      labels:
        app: my-web-app
    spec:
      containers:
      - name: web-app-container
        image: nginx
        ports:
        - containerPort: 80
        env:
        - name: ENVIRONMENT
          value: "development"
        volumeMounts:
        - name: config-volume # this config name should be same as mentioned below in volumes
          mountPath: /etc/config # this tells where to mount the configMap location with file name : etc/config/settings.properties
      volumes:
      - name: config-volume
        configMap:
          name: app-config-dev  # this is referenced from configMap name
```

COMMAND :

```shell
kubectl aply -f cm2.yaml
kubectl apply -f pod2.yaml

# give us terminal of app now run ls inside to look into /etc/config to find settings.properties file and run cat command on file to # see all the defiend key-value pairs .
kubectl exec -it <pod-name> -- bash

kubectl exec -it <pod-name> -- cat /etc/config/settings.properties
```

##### Exmaple 3

##### How can you make it run programatically

## 🧩 **Goal**

> ✅ Create a ConfigMap in Kubernetes  
> ✅ Deploy a Pod that runs Python code  
> ✅ Python code connects to Kubernetes API (from inside the cluster)  
> ✅ Reads and prints ConfigMap data dynamically (no manual mounting)

## 🧠 **Simple One-Liner Summary**

> “We built a Python app that runs *inside Kubernetes*, uses the Kubernetes Python client, and reads ConfigMap data **programmatically** using Kubernetes API calls.”

## ⚙️ **Step-by-Step Understanding**

##### **1️⃣ Dockerfile — building our Python image**

```dockerfile
FROM python:3.8-slim
RUN pip install kubernetes
COPY read_config.py /read_config.py
CMD ["python", "/read_config.py"]
```

🧠 Explanation:

- Base image: `python:3.8-slim` (lightweight Python image)

- Installs Kubernetes client library → allows Python to talk to K8s API

- Copies our Python script inside container

- Default command → runs the Python script when container starts

2️⃣ ConfigMap — store simple key/value data

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  example.property: "Hello, world!"
  another.property: "Just another example."
```

🧠 Explanation:

- This ConfigMap stores configuration data our Python app will read.

- Keys = properties, Values = text strings.

3️⃣ Deployment — run the container

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: config-reader-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: config-reader
  template:
    metadata:
      labels:
        app: config-reader
    spec:
      containers:
      - name: config-reader
        image: ttl.sh/hindi-boot:1h
        imagePullPolicy: Always
```

🧠 Explanation:

- Runs one replica of our Python container.

- Uses the temporary image (`ttl.sh` automatically expires after 1 hour).

- No volume mounts or env variables — app reads config using **Kubernetes API**.

### **4️⃣ RBAC — give Pod permission to read ConfigMaps**

#### Role:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: config-reader
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list", "watch"]atch"]
```

RoleBinding:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-configmaps
  namespace: default
subjects:
- kind: ServiceAccount
  name: default 
  namespace: default
roleRef:
  kind: Role
  name: config-reader
  apiGroup: rbac.authorization.k8s.iog-reader
```

🧠 Explanation:

- Kubernetes **does not** let Pods read ConfigMaps programmatically unless they have permission.

- Role → defines what can be accessed (`configmaps`)

- RoleBinding → attaches that Role to the Pod’s default ServiceAccount.

5️⃣ Python Script — read ConfigMap from API

read_config.py:

```python
from kubernetes import client, config

def main():
    config.load_incluster_config()  # Use Pod's in-cluster credentials
    v1 = client.CoreV1Api()
    config_map = v1.read_namespaced_config_map('app-config', 'default')

    print("ConfigMap data:")
    for key, value in config_map.data.items():
        print(f"{key}: {value}")

if __name__ == '__main__':
    main()
```

🧠 Explanation:

- `load_incluster_config()` → auto-loads credentials from ServiceAccount inside the Pod.

- Creates a CoreV1 API client → lets us talk to Kubernetes.

- Reads ConfigMap “app-config” in namespace “default”.

- Prints all key-value pairs to logs.

app.yaml :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  example.property: "Hello, world!"
  another.property: "Just another example."

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: config-reader-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: config-reader
  template:
    metadata:
      labels:
        app: config-reader
    spec:
      containers:
      - name: config-reader
        image: ttl.sh/hindi-boot:1h
        imagePullPolicy: Always

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: config-reader
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-configmaps
  namespace: default
subjects:
- kind: ServiceAccount
  name: default 
  namespace: default
roleRef:
  kind: Role
  name: config-reader
  apiGroup: rbac.authorization.k8s.io
```

🧰 **Commands**

```shell
# 1. Build and push Docker image
docker build -t ttl.sh/hindi-boot:1h .
docker push ttl.sh/hindi-boot:1h

# 2. Apply Kubernetes YAML
kubectl apply -f app.yaml

# 3. View output (Pod logs)
kubectl logs -l app=config-reader
```

🧠 Explanation:

- `ttl.sh` gives a temporary image hosting for 1 hour.

- Once Pod runs, it prints ConfigMap contents inside logs.

## 🪄 **Easy-to-Remember Summary**

| Step | Action                       | Purpose                                |
| ---- | ---------------------------- | -------------------------------------- |
| 1️⃣  | Build Python image           | Script to talk to K8s API              |
| 2️⃣  | Create ConfigMap             | Store key/value data                   |
| 3️⃣  | Create Deployment            | Run container in cluster               |
| 4️⃣  | Add RBAC Role/Binding        | Grant Pod permission to read ConfigMap |
| 5️⃣  | Pod reads via Kubernetes API | Prints data dynamically                |

## 🧩 **Key Takeaways**

- This approach = **programmatic ConfigMap access** (not via file or env).

- Useful when:
  
  - You want **dynamic runtime updates**
  
  - Or app wants to **watch** for ConfigMap changes.

- Needs RBAC permissions (`get`, `list`, `watch`).

## 🎯 **In Short (Easy Line to Remember)**

> “We used a Python Pod that talks directly to Kubernetes API to read ConfigMap data dynamically — not via mounting or env, but using service account + RBAC + in-cluster credentials.”

---

## 🔐 **What Are Kubernetes Secrets?**

✅ **Secrets** are used to store **sensitive data** — like passwords, tokens, API keys, certificates — so we don’t hardcode them inside apps or code.

⚠️ **Important:** By default, secrets are **Base64-encoded**, not encrypted.  
That means — they’re slightly hidden, but not secure enough for production.

## 🧠 **Why We Need External Secret Solutions**

> Kubernetes Secrets alone are **not secure enough**.  
> So we use external tools like:
> 
> - **Secrets Store CSI Driver**
> 
> - **Sealed Secrets**
> 
> - **External Secrets Operator**

These tools integrate with external vaults or encrypt secrets safely.

## 🚀 **1️⃣ Secrets Store CSI Driver**

### 🔹 **Simple Explanation:**

> CSI (Container Storage Interface) Driver mounts secrets **directly from external secret stores (Vault, AWS, Azure, GCP, etc.)** into your Pod as a volume.

### 🧩 **How It Works:**

- CSI driver uses **gRPC** to talk to secret providers (AWS, Vault, etc.).

- Secrets are mounted as **temporary files** inside Pods.

- You can also **sync** them into native Kubernetes Secrets if needed.

### 🌍 **Supported Providers:**

- AWS Secrets Manager

- Azure Key Vault

- GCP Secret Manager

- HashiCorp Vault

- Akeyless, IBM, and others

### 🧠 **Easy Example:**

Imagine you store DB password in **AWS Secrets Manager**.  
The CSI driver mounts that password file into `/mnt/secrets/db-password` inside your Pod.  
No manual secret creation or hardcoding needed.

🪄 **In short:**

> “CSI Driver fetches secrets directly from external vaults and mounts them securely in Pods.”

## 🔑 **2️⃣ Sealed Secrets**

### 🔹 **Simple Explanation:**

> Sealed Secrets encrypt your secrets so you can **store them safely in Git (for GitOps)**.

### 🧩 **How It Works:**

- You install a **Sealed Secrets controller** in your cluster.

- You encrypt your secret locally using a **kubeseal** CLI tool.

- The sealed secret file (safe to commit) goes into Git.

- When applied, the controller decrypts it inside the cluster.

### 🧠 **Easy Example:**

1. Create a normal secret → `kubectl create secret generic mysecret --from-literal=password=abc123`

2. Seal it → `kubeseal --format yaml < mysecret.yaml > sealedsecret.yaml`

3. Commit `sealedsecret.yaml` to Git safely.

4. When you `kubectl apply -f sealedsecret.yaml`, controller decrypts and creates the real secret.

🪄 **In short:**

> “Sealed Secrets = Encrypt once, commit safely, decrypt only inside the cluster.”

## 🔐 Why `mysecret.yaml` Is Not Secure (But Sealed Secrets *Are*)

### ❌ `mysecret.yaml` is **not secure**

- It contains **plaintext or base64-encoded secrets**, which are easily decoded.

- If stored in Git or shared accidentally, it exposes sensitive data.

- Even base64 encoding is **not encryption** — it's just a reversible transformation.

### ✅ `sealedsecret.yaml` **is secure**

- Once you run `kubeseal`, the output is **encrypted using the Sealed Secrets controller's public key**.

- Only the controller inside your Kubernetes cluster can decrypt it using its **private key**.

- You can safely commit `sealedsecret.yaml` to Git — it's **useless outside your cluster**.

## 🛡️ Best Practices for Security

| Practice                                                    | Why It Matters                               |
| ----------------------------------------------------------- | -------------------------------------------- |
| 🔒 Delete `mysecret.yaml` after sealing                     | Prevent accidental leaks or Git commits      |
| 🧪 Use `kubectl create secret ... --dry-run=client -o yaml` | Avoid manually writing secrets               |
| 🗃️ Use `.gitignore` or pre-commit hooks                    | Block unsealed secrets from being committed  |
| 🔍 Audit your repo for secrets                              | Use tools like `git-secrets` or `truffleHog` |

### ✅ Install `kubeseal` (Linux x86_64) in Killercoda

1. **Download the latest release archive**
   
   ```bash
   curl -LO https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.22.0/kubeseal-0.22.0-linux-amd64.tar.gz
   ```

2. **Extract the archive**
   
   ```bash
   tar -xvzf kubeseal-0.22.0-linux-amd64.tar.gz
   ```

3. **Move the binary to your system path**
   
   ```bash
   chmod +x kubeseal
   sudo mv kubeseal /usr/local/bin/
   ```

4. **Verify installation**
   
   ```bash
   kubeseal --version
   ```

### 🧩 Step-by-step fix: Install the Sealed Secrets controller

You can install it using Helm or a direct manifest. Here's both options:

#### ✅ Option 1: Install via Helm (recommended)

```bash
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm repo update
helm install sealed-secrets sealed-secrets/sealed-secrets \  --namespace sealed-secrets --create-namespace
```

This will:

- Create the `sealed-secrets` namespace

- Deploy the controller with the default name `sealed-secrets-controller`

#### ✅ Option 2: Install via kubectl (YAML manifest)

```bash
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.0/controller.yaml
```

### 🔍 Verify installation

After installation, confirm the controller is running:

```bash
kubectl get pods -n sealed-secrets
kubectl get svc -n sealed-secrets
```

You should see a pod and service named `sealed-secrets-controller`.

### 🔐 Then retry your sealing command

```bash
kubeseal \  --controller-name=sealed-secrets-controller \  --controller-namespace=sealed-secrets \  --format=yaml < mysecret.yaml > sealedsecret.yaml
```

## 🔐 Sealed Secrets Workflow Cheat Sheet

| Step                    | Command                                                                                                                             | Purpose                                            |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| 1️⃣ Create Secret       | `vi mysecret.yaml`                                                                                                                  | Define your Kubernetes Secret in YAML format       |
| 2️⃣ Seal the Secret     | `kubeseal --controller-name=sealed-secrets --controller-namespace=sealed-secrets --format=yaml < mysecret.yaml > sealedsecret.yaml` | Encrypt the Secret using Sealed Secrets controller |
| 3️⃣ Apply Sealed Secret | `kubectl apply -f sealedsecret.yaml`                                                                                                | Deploy the sealed secret to your cluster           |

COMMANDS :

```bash
Initialising Kubernetes... done

controlplane:~$ curl -LO https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.22.0/kubeseal-0.22.0-linux-amd64.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 19.5M  100 19.5M    0     0  2915k      0  0:00:06  0:00:06 --:--:-- 3657k
controlplane:~$ tar -xvzf kubeseal-0.22.0-linux-amd64.tar.gz
LICENSE
README.md
kubeseal
controlplane:~$ chmod +x kubeseal
controlplane:~$ sudo mv kubeseal /usr/local/bin/
controlplane:~$ kubeseal --version
kubeseal version: 0.22.0
controlplane:~$ 


controlplane:~$ kubectl get svc -A | grep sealed
controlplane:~$ helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
"sealed-secrets" has been added to your repositories
controlplane:~$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "sealed-secrets" chart repository
Update Complete. ⎈Happy Helming!⎈
controlplane:~$ helm install sealed-secrets sealed-secrets/sealed-secrets \
  --namespace sealed-secrets --create-namespace
NAME: sealed-secrets
LAST DEPLOYED: Wed Nov  5 10:09:05 2025
NAMESPACE: sealed-secrets
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

You should now be able to create sealed secrets.

1. Install the client-side tool (kubeseal) as explained in the docs below:

    https://github.com/bitnami-labs/sealed-secrets#installation-from-source

2. Create a sealed secret file running the command below:

    kubectl create secret generic secret-name --dry-run=client --from-literal=foo=bar -o [json|yaml] | \
    kubeseal \
      --controller-name=sealed-secrets \
      --controller-namespace=sealed-secrets \
      --format yaml > mysealedsecret.[json|yaml]

The file mysealedsecret.[json|yaml] is a commitable file.

If you would rather not need access to the cluster to generate the sealed secret you can run:

    kubeseal \
      --controller-name=sealed-secrets \
      --controller-namespace=sealed-secrets \
      --fetch-cert > mycert.pem

to retrieve the public cert used for encryption and store it locally. You can then run 'kubeseal --cert mycert.pem' instead to use the local cert e.g.

    kubectl create secret generic secret-name --dry-run=client --from-literal=foo=bar -o [json|yaml] | \
    kubeseal \
      --controller-name=sealed-secrets \
      --controller-namespace=sealed-secrets \
      --format [json|yaml] --cert mycert.pem > mysealedsecret.[json|yaml]

3. Apply the sealed secret

    kubectl create -f mysealedsecret.[json|yaml]

Running 'kubectl get secret secret-name -o [json|yaml]' will show the decrypted secret that was generated from the sealed secret.

Both the SealedSecret and generated Secret must have the same name and namespace.




✅ Step 3: Verify the controller is running
kubectl get svc -n sealed-secrets
controlplane:~$ kubectl get svc -n sealed-secrets
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
sealed-secrets           ClusterIP   10.106.140.196   <none>        8080/TCP   69s
sealed-secrets-metrics   ClusterIP   10.111.234.142   <none>        8081/TCP   69s
controlplane:~$ 



controlplane:~$ kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.24.0/controller.yaml
deployment.apps/sealed-secrets-controller created
role.rbac.authorization.k8s.io/sealed-secrets-service-proxier created
rolebinding.rbac.authorization.k8s.io/sealed-secrets-controller created
clusterrolebinding.rbac.authorization.k8s.io/sealed-secrets-controller created
role.rbac.authorization.k8s.io/sealed-secrets-key-admin created
Warning: resource clusterroles/secrets-unsealer is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
clusterrole.rbac.authorization.k8s.io/secrets-unsealer configured
serviceaccount/sealed-secrets-controller created
Warning: resource customresourcedefinitions/sealedsecrets.bitnami.com is missing the kubectl.kubernetes.io/last-applied-configuration annotation which is required by kubectl apply. kubectl apply should only be used on resources created declaratively by either kubectl create --save-config or kubectl apply. The missing annotation will be patched automatically.
customresourcedefinition.apiextensions.k8s.io/sealedsecrets.bitnami.com configured
service/sealed-secrets-controller created
rolebinding.rbac.authorization.k8s.io/sealed-secrets-service-proxier created
controlplane:~$ kubectl get svc -n sealed-secrets
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
sealed-secrets           ClusterIP   10.106.140.196   <none>        8080/TCP   2m25s
sealed-secrets-metrics   ClusterIP   10.111.234.142   <none>        8081/TCP   2m25s
controlplane:~$ kubectl get svc -n sealed-secrets
NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
sealed-secrets           ClusterIP   10.106.140.196   <none>        8080/TCP   2m56s
sealed-secrets-metrics   ClusterIP   10.111.234.142   <none>        8081/TCP   2m56s
controlplane:~$ 



🔐 Step 4: Seal your secret with correct flags
Once confirmed, run:

bash
kubeseal \
  --controller-name=sealed-secrets-controller \
  --controller-namespace=sealed-secrets \
  --format=yaml < mysecret.yaml > sealedsecret.yaml
Let me know if you'd like help verifying the install or customizing the controller setup.



vi mysecret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
  namespace: default
type: Opaque
data:
  username: bXl1c2Vy # base64 encoded
  password: bXlwYXNzd29yZA== # base64 encoded


controlplane:~$ kubeseal \
  --controller-name=sealed-secrets \
  --controller-namespace=sealed-secrets \
  --format=yaml < mysecret.yaml > sealedsecret.yaml
bash: mysecret.yaml: No such file or directory
controlplane:~$ vi mysecret.yaml
controlplane:~$ kubeseal   --controller-name=sealed-secrets   --controller-namespace=sealed-secrets   --format=yaml < mysecret.yaml > sealedsecret.yaml
controlplane:~$ kubeseal --format yaml < mysecret.yaml > sealedsecret.yaml
controlplane:~$ kubectl apply -f sealedsecret.yaml
sealedsecret.bitnami.com/mysecret created
controlplane:~$ 


controlplane:~$ ls
LICENSE  README.md  filesystem  kubeseal-0.22.0-linux-amd64.tar.gz  mysecret.yaml  sealedsecret.yaml
controlplane:~$ cat sealedsecret.yaml 
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  creationTimestamp: null
  name: mysecret
  namespace: default
spec:
  encryptedData:
    password: AgCol3L+KLzjr+jJFv4blwVOIj74+iZxCiXWfTSwbLrSatUMkV1G2r3odNgJepv4LWWj0VeCwhicP+Kmz/fhK8a+0q8i6JecMJzGS9sD4EHZexHwAPCMY+9I4X1NgaUyjsmYM5x4FVwzn6gUxuCfTNNjjEkj2hNJ0New+adxO+kUBC+v6NQE4fg+6D+r7tqZpnS+LCcnU0Nq2bpWeHIWTUB1XQCaLxbknzyoMtAoO1zadTN1DXeTklnn2opuO/Op+27TpfQ627Z9OZFvvZRXxu+UrGCvwN0aLg4TK8oISoNN9afUaDcedglEqgHF43kVn4e3zIVn/jsqNlo+1aW/oFEEFOPdVodpXlxj0GV1d3NJm/18HZflQQm5WdU9Zc9BKxxSH5PjWinAf38TuNJxgRZFvctMoGgk9jTLx3CEZh9oJOcn8R0niprzUNfagIYtNdfC+o2T3FonawU9FpdePpiU3GCuMf15hvK5R/zzofA/VDKnmx84+CA69rC9ruQfy/fmzrCyscOhgfQjGCS6r/bByTYm3x3/qBcxWkE/W3FjmB6GMk6vIHXx/9Wr826KzeXwLibS6q4ZIlFqtJtvwA6XafMaS8JhfdNZzvod4TU7EK01BDwcmtX3FkNrev2+7yEAkVbkKa55bwFlnQQmVkxIU8RhYwD6m8rMFow3akLuclZBsHJTYi0JHELhtUxkWLTdYDRsYhOLlg0D
    username: AgBKUCCkWdL5EPQNWU+q2KKNPJSjnvFmpohhPbwIhz+06j2FojJrpvJ2v+axoYWCW0359Qeq0LKeFbAKj9madBO+cbwznbfynxzfkkju//kvBXfENY9UjuAxTpURiSrguwr5fD6kvoFxXtaXLQhFFz1RIlUrxD1nMm0xagRmFunUdz8arfWn+BiNJEAzSDQRgMQ26e//lzab/YAZ15GrVLdwhAGA6Fdt9LbCS2tVkfzwO6tJjN6jhYW56Y64wO333scid5iu+gmLysjCsQvRGGkuRfvUcniqc4BCl4nlpHpErbMDIoT/fCdKvqmo9dJTBqDLthb4ZVDvE9LF1tptnAbOS3p2YH8XZGLqu9QhixZbAmKOar3H5lRiYN10uQ27c+VXFebJmezH+BzAjdkc/xnGd2YR71Nqjvw9SDPwSyVHkS7Xi9kek9Il8YCziym6I70u8tX7KXfrwQDljycxUwmGuLTNw2kL1mwRDPMIhYGj5U2ke1hKsURTnkoT3kLwqpCerwVTZfHwvZsBYyCDlejoemfoWqeFSd6ueNBBwCwMWHIEhHa3trsC8V+v473/ZN/8gOrSSDImnjVhnDwCjzuL+Ehpq3uWQqOUJgV93TRIINzOjCsZkfm6KY83cg7B1dwGTxFTg5ffv0uICYUwILpTLK7WFN635VW5W3dKc+5WxWykh96LYusvt7ST8Ap/K8houSJYdAQ=
  template:
    metadata:
      creationTimestamp: null
      name: mysecret
      namespace: default
    type: Opaque

controlplane:~$
```

## 🌐 **3️⃣ External Secrets Operator (ESO)**

### 🔹 **Simple Explanation:**

> External Secrets Operator **syncs** secrets automatically from external secret managers into Kubernetes Secrets.

### 🧩 **How It Works:**

- You define an `ExternalSecret` resource in YAML.

- ESO connects to your external provider (Vault, AWS, GCP, etc.).

- It automatically creates/updates the corresponding Kubernetes Secret.

### 🧠 **Easy Example:**

```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: db-secret
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: aws-secrets-store
    kind: SecretStore
  target:
    name: db-secret
  data:
    - secretKey: password
      remoteRef:
        key: my-database-password
```

🪄 **In short:**

> “ESO automatically keeps Kubernetes Secrets synced from AWS, Vault, etc. — no manual creation needed.”

## 🧱 **Types of Kubernetes Secrets (Built-In)**

| Type                                  | Purpose / Use Case                                                                  |
| ------------------------------------- | ----------------------------------------------------------------------------------- |
| `Opaque`                              | Default type, for custom user-defined key-value data                                |
| `kubernetes.io/service-account-token` | Auto-generated token for ServiceAccounts                                            |
| `kubernetes.io/dockercfg`             | For Docker `~/.dockercfg` file (legacy)                                             |
| `kubernetes.io/dockerconfigjson`      | For Docker `~/.docker/config.json` file (used when pulling from private registries) |
| `kubernetes.io/basic-auth`            | Username/password authentication                                                    |
| `kubernetes.io/ssh-auth`              | SSH private key for secure connections                                              |
| `kubernetes.io/tls`                   | Store TLS cert and key for HTTPS                                                    |
| `bootstrap.kubernetes.io/token`       | Internal token used by Kubernetes for node bootstrapping                            |

🧠 **Most Commonly Used Types:**

- `Opaque` → for app credentials

- `dockerconfigjson` → for private image pull secrets

- `tls` → for HTTPS certs

- `service-account-token` → for Pod authentication

## 🪄 **Easy-to-Remember Summary Table**

| Mechanism                     | Purpose                                     | How It Works                               | Best Use Case                         |
| ----------------------------- | ------------------------------------------- | ------------------------------------------ | ------------------------------------- |
| **Secrets Store CSI Driver**  | Fetch secrets directly from external stores | Mounts secrets as volumes                  | Secure runtime fetch from Vaults      |
| **Sealed Secrets**            | Encrypt secrets for GitOps                  | Encrypt → Commit → Decrypt inside cluster  | GitOps environments                   |
| **External Secrets Operator** | Sync secrets automatically from vaults      | Creates/updates K8s Secrets from providers | Continuous sync with AWS, Vault, etc. |

## 🧠 **Simple Memory Line**

> “K8s Secrets are base64, not secure → use CSI Driver (runtime fetch), Sealed Secrets (Git-safe), or External Secrets Operator (auto-sync).”

when we have to pull docker image from private registry then use kubernetes.io/dockercfg or kubernetes.io/dockerconfigjson

majorly we use kubernetes.io/dockercfg or kubernetes.io/dockerconfigjson or Opaque(default) or kubernetes.io/tls

kubernetes.io/service-account-token  this is used when we need token . kubectl create token.

---

### 🔐 What are Kubernetes Secrets?

- **Secrets** store **sensitive data** such as passwords, tokens, SSH keys, TLS certs, and credentials.

- They are **base64-encoded**, **not encrypted** by default (only encoded for transport).

- Better to use **external secret managers** like **Sealed Secrets**, **Secret Store CSI Driver**, or **External Secrets Operator** for real security.

- Use `kubectl create secret` or YAML manifest to define them.

## ⚙️ **Creating Secrets**

### 1️⃣ **Opaque Secret (Default Type)**

Used for arbitrary data like passwords or API keys.

**Command:**

```shell
# imperative way
kubectl create secret generic my-opaque-secret --from-literal=password=supersecret
```

Check it:

`kubectl get secrets
kubectl get secret my-opaque-secret -o yaml`

Decode Base64:

```bash
echo "c3VwZXJzZWNyZXQ=" | base64 -d
# Output: supersecret
```

🧩 *Kubernetes encodes data using base64; we can decode it easily.*

```shell
controlplane:~$ kubectl create secret generic my-opaque-secret --from-literal=password=supersecret
secret/my-opaque-secret created
controlplane:~$ kubectl get secrets
NAME               TYPE     DATA   AGE
my-opaque-secret   Opaque   1      22s
controlplane:~$ kubectl get secrets my-opaque-secret -oyaml
apiVersion: v1
data:
  password: c3VwZXJzZWNyZXQ=
kind: Secret
metadata:
  creationTimestamp: "2025-10-18T14:01:17Z"
  name: my-opaque-secret
  namespace: default
  resourceVersion: "3715"
  uid: 2d7791d9-9087-4813-8e36-39876ae92a8c
type: Opaque
controlplane:~$ echo "c3VwZXJzZWNyZXQ=" | -d base64
-d: command not found
controlplane:~$ echo "c3VwZXJzZWNyZXQ=" |  base64 -d 
supersecret
controlplane:~$ echo "supersecret" | base64
c3VwZXJzZWNyZXQ=
```

FILE: 

2️⃣ **YAML-based Secret**

sec.yaml :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-opaque-secret
type: Opaque
data:
  password: c3VwZXJzZWNyZXQ=   # base64 of 'supersecret'
```

**Apply it:**

`kubectl apply -f sec.yaml`

⚠️ *Do not commit this YAML file to GitHub — it still exposes encoded (not encrypted) secrets.*

### 3️⃣ **Basic Auth Secret**

Used for username/password authentication.

```bash
kubectl create secret generic my-basic-auth-secret \
--from-literal=username=myuser \
--from-literal=password=mypassword \
--type=kubernetes.io/basic-auth
```

📌 *Either `username` or `password` (or both) must be provided.*

### 4️⃣ **SSH Auth Secret**

Used for storing private SSH keys.

```bash
ssh-keygen
kubectl create secret generic my-ssh-key-secret \
--from-file=ssh-privatekey=/root/.ssh/id_ed25519 \
--type=kubernetes.io/ssh-auth
```

COMMAND:

```bash
# if we want to create ssh-key secret
kubectl create secret generic my-ssh-key-secret \
--from-file=ssh-privatekey=/path/to/.ssh/id_rsa \
--type=kubernetes.io/ssh-auth

controlplane:~$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/root/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_ed25519
Your public key has been saved in /root/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:mDR9L4+3sK0wARZHEceUZ42HRyu+GnjHidlQZWIQ9Og root@controlplane
The key's randomart image is:
+--[ED25519 256]--+
|      ..=**+o=+  |
|       + .o+=++. |
|      = . oo+o.  |
|     o = o + .   |
|      o S E o    |
|         o X o   |
|        + B O    |
|         + O .   |
|          +.o    |
+----[SHA256]-----+
controlplane:~$ kubectl create secret generic my-ssh-key-secret --from-file=ssh-privatekey=/root/.ssh/id_ed25519 --type=kubernetes.io/ssh-auth
secret/my-ssh-key-secret created

controlplane:~$ kubectl get secret my-ssh-key-secret -oyaml
apiVersion: v1
data:
  ssh-privatekey: LS0tLS1CRUdJTiBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0KYjNCbGJuTnphQzFyWlhrdGRqRUFBQUFBQkc1dmJtVUFBQUFFYm05dVpRQUFBQUFBQUFBQkFBQUFNd0FBQUF0emMyZ3RaVwpReU5UVXhPUUFBQUNBNCtkaWhHaVFYS0U2blpvRW9UaXJLNTdnYkhDdXkvRXJPZmZhRmxQeGtVUUFBQUpqSW01eVN5SnVjCmtnQUFBQXR6YzJndFpXUXlOVFV4T1FBQUFDQTQrZGloR2lRWEtFNm5ab0VvVGlySzU3Z2JIQ3V5L0VyT2ZmYUZsUHhrVVEKQUFBRURBaHBvajZSVUs0TnFMYS9iRGhEOVYrMCt1ckJuSGdjMTkyc2RQRGxsekp6ajUyS0VhSkJjb1RxZG1nU2hPS3Nybgp1QnNjSzdMOFNzNTk5b1dVL0dSUkFBQUFFWEp2YjNSQVkyOXVkSEp2YkhCc1lXNWxBUUlEQkE9PQotLS0tLUVORCBPUEVOU1NIIFBSSVZBVEUgS0VZLS0tLS0K
kind: Secret
metadata:
  creationTimestamp: "2025-10-18T14:32:52Z"
  name: my-ssh-key-secret
  namespace: default
  resourceVersion: "4453"
  uid: 3c3c911d-7120-4ba5-85f9-a7e5f86715da
type: kubernetes.io/ssh-auth
```

### 5️⃣ **TLS Secret**

Used for certificates (HTTPS).

```bash
# tls secret key : here we need cert file
kubectl create secret tls my-tls-secret \
--cert=/path/to/cert/file \
--key=/path/to/key/file
```

🔑 *Used by Ingress or apps needing TLS communication.*

```bash
kubectl delete deploy --all

kubectl delete cm --all

kubectl delete pods --all
```

COMMANDS:

```bash
# secret name mysql-root-pass we use it in pod3.yaml with refering
kubectl create secret generic mysql-root-pass --from-literal=password='abc123'

# secret name mysql-user-pass we use it in pod3.yaml with refering
kubectl create secret generic mysql-user-pass --from-literal=password='saiyampass'

kubectl get secrets

cat pod3.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
          - name: MYSQL_ROOT_PASSWORD
            valueFrom:
              secretKeyRef: # referencing from secret i.e. name mysql-root-pass for key value i.e. password
                name: mysql-root-pass
                key: password
          - name: MYSQL_USER
            valueFrom:
              configMapKeyRef:  # referencing from configMap i.e. name bootcamp-configmap for key value i.e. username
                name: bootcamp-configmap
                key: username
          - name: MYSQL_PASSWORD
            valueFrom:
              secretKeyRef:     # referencing from secret i.e. name mysql-user-pass for key value i.e. password
                name: mysql-user-pass
                key: password
          - name: MYSQL_DATABASE
            valueFrom:
              configMapKeyRef:  # referencing from configMap i.e. name bootcamp-configmap for key value i.e. database_name
                name: bootcamp-configmap
                key: database_name
        ports:
          - containerPort: 3306
        volumeMounts:
          - name: mysql-storage
            mountPath: /var/lib/mysql
      volumes:
        - name: mysql-storage
          emptyDir: {}


cat cm1.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: bootcamp-configmap
data:
  username: "saiyam"
  database_name: "exampledb"

kubectl apply -f cm1.yaml

kubectl apply -f pod3.yaml

kubectl get pods

# gives mysql terminal then gives password
kubectl exec -it <pod-name> -- mysql -u root -pabc123
```

## ⚡ **Useful Commands Recap**

| Command                                     | Purpose                  |
| ------------------------------------------- | ------------------------ |
| `kubectl create secret generic ...`         | Create generic secret    |
| `kubectl create secret tls ...`             | Create TLS secret        |
| `kubectl create secret docker-registry ...` | Create image pull secret |
| `kubectl get secrets`                       | List all secrets         |
| `kubectl get secret <name> -o yaml`         | Show secret in YAML      |
| `echo "..."                                 | base64 -d`               |
| `kubectl apply -f <file>`                   | Apply YAML               |
| `kubectl delete secret <name>`              | Delete a secret          |

## 🧰 **Types of Secrets (with Usage)**

| Type                                           | Use                                    |
| ---------------------------------------------- | -------------------------------------- |
| `Opaque`                                       | Default, arbitrary key-value data      |
| `kubernetes.io/service-account-token`          | Auto-generated SA tokens               |
| `kubernetes.io/dockercfg` / `dockerconfigjson` | For Docker registry login              |
| `kubernetes.io/basic-auth`                     | Username-password pair                 |
| `kubernetes.io/ssh-auth`                       | SSH private key                        |
| `kubernetes.io/tls`                            | TLS certs & keys                       |
| `bootstrap.kubernetes.io/token`                | Internal use for cluster bootstrapping |

🧠 *Most commonly used:*  
`Opaque`, `dockerconfigjson`, `basic-auth`, `ssh-auth`, and `tls`

## 🔒 **Better Security Options**

| Mechanism                     | What it does                                                                                | Why use it                      |
| ----------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------- |
| **Secret Store CSI Driver**   | Fetches secrets directly from external vaults (AWS, Azure, GCP, HashiCorp Vault) using gRPC | No secrets stored in Kubernetes |
| **Sealed Secrets**            | Encrypts secrets → safe to store in Git → decrypted only in cluster                         | GitOps-safe                     |
| **External Secrets Operator** | Syncs external secrets (from AWS, Vault, etc.) into Kubernetes Secrets automatically        | Automates secret management     |

🧩 *All these methods prevent storing raw secrets inside YAML or Git.*

## 🧠 **Easy-to-Remember Summary**

1. **Secrets = Sensitive data (passwords, tokens, keys).**

2. **Always base64-encoded**, not encrypted → use sealed/external secrets for safety.

3. **Create secrets** using CLI or YAML (base64 required in YAML).

4. **Reference secrets** inside pods with `secretKeyRef`.

5. **For private Docker images**, use `docker-registry` type secret.

6. **Use ConfigMap** for non-sensitive data (e.g., usernames, app settings).

---

### Image pull secrets

🪟 ✅ Docker Build for Windows Platform

`docker buildx build --platform windows/amd64 -t saiyam911/bootcamp-demo:v1 . --push`

🪟 ✅ Docker Build for Linux Platform

`docker buildx build --platform linuxs/amd64 -t saiyam911/bootcamp-demo:v1 . --push`

### 🔍 Breakdown:

- `--platform windows/amd64`: Targets Windows OS with 64-bit architecture. Builds your image for Linux/amd64 (common for Kubernetes nodes).

- `-t saiyam911/bootcamp-demo:v1`: Tags the image.

- `--push`: Pushes the image to Docker Hub.

## ⚠️ Important Notes

- Your **Dockerfile must be compatible with Windows base images**, like:

```dockerfile
FROM mcr.microsoft.com/windows/servercore:ltsc2022
```

- You need **Docker Desktop with Windows containers enabled**.

- Not all registries or Kubernetes clusters support Windows containers — check compatibility before deploying.

```dockerfile
FROM python:3.13.9
RUN pip install kubernetes
COPY read_config.py /read_config.py
CMD ["python", "/read_config.py"]
```

### 🔐 **STEP 2: Expose Environment Variable** `SECRET` **Locally**

Before creating the Kubernetes secret, you need to **set your Docker password** as an environment variable.

#### ✅ Linux/macOS

`export SECRET="your-docker-password"`

**✅ Windows CMD**

`set SECRET=your-docker-password`

✅ Windows PowerShell

`$env:SECRET = "your-docker-password"`

🔐 **STEP 3: Create Docker Registry Secret in Kubernetes**

```powershell
kubectl create secret docker-registry pullsec \
  --docker-username=saiyam911 \
  --docker-password=$SECRET \
  --docker-email=saiyam911@gmail.com
```

✅ What this does:

- Creates a secret named `pullsec` of type `docker-registry`.

- Stores credentials so Kubernetes can **pull your private image**.

📦 **STEP 4: Create Pod Manifest (**`pod.yaml`**)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: bootcamp-demo-pod
spec:
  containers:
  - name: bootcamp-demo
    image: saiyam911/bootcamp-demo:v1
  imagePullSecrets:
  - name: pullsec
```

✅ What this does:

- Defines a Pod using your private image.

- Tells Kubernetes to use the `pullsec` secret to authenticate.

🚀 **STEP 5: Deploy the Pod**

`kubectl apply -f pod.yaml`

🔍 **STEP 6: Verify Pod Status**

`kubectl get pods -o wide`

## 🧠 MEMORY MNEMONIC: **B.E.S.T. P.O.D.**

| Letter | Meaning                                | Command / Concept                           |
| ------ | -------------------------------------- | ------------------------------------------- |
| **B**  | **Build** Docker image                 | `docker buildx ...`                         |
| **E**  | **Export** `SECRET` locally            | `export SECRET=...`                         |
| **S**  | **Secret** creation for registry       | `kubectl create secret docker-registry ...` |
| **T**  | **Tag** image with version             | `-t saiyam911/bootcamp-demo:v1`             |
| **P**  | **Pod** manifest with imagePullSecrets | `pod.yaml`                                  |
| **O**  | **Operate** by applying manifest       | `kubectl apply -f pod.yaml`                 |
| **D**  | **Diagnose** with pod status           | `kubectl get pods -o wide`                  |

---

---

---

##### PART 8 **<mark>STATEFULSETS, SERVICE, INGRESS</mark>**

## 🧠 What Does “Flat Network” Mean in Kubernetes?

A **flat network** in Kubernetes means:

- All **Pods** across all **Nodes** can communicate **directly** with each other.

- There’s **no NAT (Network Address Translation)** between Pods.

- Every Pod gets a **unique IP address** that’s routable within the cluster.

✅ So yes — **Kubernetes assumes a flat, routable network** for Pod-to-Pod communication.

BUT THIS IS NOT SO SECURE.

🔧 How Flat Networking Is Achieved
This is usually handled by a CNI plugin (Container Network Interface), like:

1. Calico
2. Cilium
3. Flannel
4. Weave

These plugins ensure:

* Pod IPs are reachable across nodes.
* Network policies can be enforced.

### ✅ Pod IPs Are Ephemeral

> **Pods get dynamic IPs that change when they restart, so direct Pod IP communication is unreliable.**

🧠 *Remember*: Pod IPs are temporary — use Services for stable access.

### ✅ Services Provide Stable Access

> **Kubernetes Services have stable IPs and use Endpoints to route traffic to Pods via kube-proxy (IPTables or IPVS).**

🧠 *Remember*: Services act like traffic routers — even if Pods die and restart, traffic still flows.

## ✅ Correct Concepts

- **Kubernetes Service is an object**: Yes, it abstracts access to a group of Pods.

- **Service has a stable IP**: Yes, every ClusterIP Service gets a **static internal IP**.

- **Service uses endpoints**: Yes, it dynamically tracks the IPs of matching Pods.

- **Traffic routing survives Pod restarts**: Yes, because the Service updates its endpoints automatically.

## ⚠️ Needs Clarification

### ❌ “IPTables maintains the Service IP”

- **Partially true**: IPTables (or IPVS) is used by kube-proxy to **route traffic** from the Service IP to Pod IPs.

- But IPTables doesn’t “maintain” the IP — Kubernetes assigns the IP, and kube-proxy sets up routing rules.

### ❌ “Service uses endpoint and routes through iptables”

- **Better phrased**: kube-proxy watches Services and Endpoints, and uses IPTables rules to forward traffic from the Service IP to the correct Pod IPs.

## 🧠 Easy-to-Remember Flow

### 🔄 How a Kubernetes Service Works

1. **Service**: Gets a stable ClusterIP (e.g., `10.96.0.1`).

2. **Selector**: Matches Pods with specific labels.

3. **Endpoints**: Tracks Pod IPs dynamically.

4. **kube-proxy**: Uses IPTables or IPVS to route traffic from Service IP to Pod IPs.

5. **Pod dies?** No problem — new Pod is added to Endpoints automatically.

### 🔁 Summary Mnemonic: **P.E.S.T.**

| Letter | Meaning                           |
| ------ | --------------------------------- |
| **P**  | **Pod IPs are Ephemeral**         |
| **E**  | **Endpoints track live Pod IPs**  |
| **S**  | **Service has Stable IP**         |
| **T**  | **Traffic routed via kube-proxy** |

## 🧭 Kubernetes Service Types Explained

| Service Type     | Description                                                                | Use Case                                                                 |
| ---------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **ClusterIP**    | Default type. Exposes service on an internal IP within the cluster.        | Internal communication between pods. Not accessible outside the cluster. |
| **NodePort**     | Exposes service on a static port on each node’s IP.                        | Access from outside the cluster via `<NodeIP>:<NodePort>`.               |
| **LoadBalancer** | Provisions an external load balancer (via cloud provider).                 | Public access to services in cloud environments.                         |
| **ExternalName** | Maps service to a DNS name (no proxying).                                  | Redirects to external services via DNS (e.g., `mydb.example.com`).       |
| **Headless**     | Service without a ClusterIP (`clusterIP: None`).                           | Direct pod discovery for StatefulSets or custom load balancing.          |
| **ExternalDNS**  | Not a Service type, but a controller that manages DNS records dynamically. | Automates DNS updates for services with external access.                 |

## 🧩 1️⃣ Cluster → Nodes → Pods → Containers

Think of Kubernetes like this:

```css
Cluster
 ├── Node 1 (Worker Machine)
 │     ├── Pod A
 │     │     ├── Container 1
 │     │     └── Container 2
 │     └── Pod B
 └── Node 2
       └── Pod C
```

## 🌐 2️⃣ Each Node has its own **unique IP address**

✅ Every **Node** (physical or virtual machine) has its **own unique IP** in the cluster network —  
like different houses having their own street addresses.

🧠 Example:

```shell
Node1 IP: 10.0.0.1
Node2 IP: 10.0.0.2
Node3 IP: 10.0.0.3
```

## 🏠 3️⃣ Each Pod gets its own **unique Pod IP**

- When a Pod is created, Kubernetes (via the CNI plugin like Calico, Flannel, etc.) assigns it a **unique Pod IP**.

- That IP is **different from the Node IP**.

- This **Pod IP** is used by other Pods to talk directly to it.

🧠 Example:

```shell
Pod A IP: 10.244.1.2   (on Node1)
Pod B IP: 10.244.1.3   (on Node1)
Pod C IP: 10.244.2.2   (on Node2)
```

🗣️ So, even though Pod A and Pod B are on the same Node, they have **different Pod IPs**.

## 📦 4️⃣ Inside one Pod → All containers share the **same Pod IP**

- A **Pod** can have **one or more containers**.

- All containers inside that same Pod **share**:
  
  - The same **network namespace**
  
  - The same **Pod IP**
  
  - The same **localhost (127.0.0.1)**

🧠 Example:

```shell
Pod A IP: 10.244.1.2
 ├── Container 1 → localhost:8080
 └── Container 2 → localhost:9090
```

➡️ They can talk to each other using `localhost`, like two apps running on the same machine.

## 🧭 5️⃣ Communication Flow

Let’s see how everything connects:

| Source                             | Destination                   | Connection                    |
| ---------------------------------- | ----------------------------- | ----------------------------- |
| Container → same Pod container     | `localhost:<port>`            | Same Pod IP                   |
| Pod → another Pod (same Node)      | `10.244.x.x`                  | Different Pod IPs             |
| Pod → another Pod (different Node) | `10.244.x.x`                  | Handled by CNI plugin routing |
| Pod → Internet                     | NAT (Node routes traffic out) |                               |

🔗 6️⃣ Visualization (Easy View)

```css
[ Node 1 ]                         [ Node 2 ]
 IP: 10.0.0.1                      IP: 10.0.0.2
 ├── Pod A (10.244.1.2)            ├── Pod C (10.244.2.2)
 │   ├── Container 1               │   ├── Container 1
 │   └── Container 2               │   └── Container 2
 └── Pod B (10.244.1.3)
     └── Container 1
```

👉 Each Pod has its own **Pod IP (unique in the cluster)**  
👉 Containers inside the same Pod share that **Pod IP**  
👉 Nodes have **their own IPs**, separate from Pods.

## 🧠 **Easy Way to Remember**

| Level             | IP Behavior                   | Example      |
| ----------------- | ----------------------------- | ------------ |
| Node              | Has its own unique IP         | `10.0.0.1`   |
| Pod               | Gets unique Pod IP (from CNI) | `10.244.1.2` |
| Containers in Pod | Share Pod IP                  | `localhost`  |

🧩 “**Node IPs are unique per machine, Pod IPs are unique per Pod, and containers share the Pod IP.**”

## 🔥 Bonus Tip

Kubernetes networking follows **"Flat Network" model**:

- Every Pod can talk to every other Pod in the cluster **without NAT**.

- No matter which Node they’re on — this is handled by the **CNI plugin** (like Calico, Flannel, etc.).

**MULTICONTAINER POD**

![multicontainer pod.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\multicontainer%20pod.png)

**HERE THIS IS A MULTI-CONTAINER POD AND ITS NAME IS shared-namespace WITH CONTAINER p1 WITH IMAGE BUSYBOX AND CONTAINER p2 WITH IMAGE NGINX.**

In multi-container pod there are shared namespaces like networking namespaces are shared because we can communicate to localhost within eachother. in a **multi-container Pod**, all containers share the **same network namespace**, so they can communicate with each other via `localhost`, just like processes running on the same machine.

As soon as pod is created then CNI(Container Network Interface) like Calico/ Cilium/ Flannel/ Weave attach IP address with network .

The **CNI plugin assigns a unique IP to the Pod**

### 🧠 Easy Explanation:

- In Kubernetes, **all containers inside a Pod share the same network namespace**.

- So the **Pod gets one IP address**, and all its containers use that IP to communicate.

> 🔄 Think of a Pod like a house with multiple rooms (containers) — the house has one address (Pod IP), and everyone inside shares it.

0.

![multi-container pod1.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\multi-container%20pod1.png)

HERE AS YOU CAN SEE IN THIS NODE POD IS CREATED AND IT HAS THREE CONTAINERS

1. Busybox

2. Nginx

3. Pause container holds network namespace

POD get its IP address and this ip-address connects to eth0@if9 interface . eth0 is pods interface 

1.

![multi-container pod1.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\multi-container%20pod1.png)

2.

![multi-container pod2.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\multi-container%20pod2.png)

3.

![multi-container pod3.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\multi-container%20pod3.png)

> In a multi-container Pod, all containers (e.g., Busybox, Nginx) share the **network namespace** held by the **pause container**. The Pod gets a **single IP address**, connected via a **veth pair**: one end (`eth0@ifX`) inside the Pod, and the other in the **node’s root namespace**. Pod's Unique IP is connected to eth0 interface (eth0@ifX) inside the pod.

🧠 *Remember*: Pause container anchors the network; all containers talk through the same Pod IP via `eth0`.

**Kubernetes creates virtual Ethernet (veth) pairs to connect Pods to the node’s network**, but the exact setup (names, structure) can vary based on the **CNI plugin** used (like Calico, Cilium, etc.).

🧠 *Remember*: veth pairs are Pod’s network cables — their style depends on the CNI electrician.

## 🧱 POD CREATION & VERIFICATION

### ✅ `cat << EOF | kubectl apply -f -`

Creates a Pod with two containers: `busybox` and `nginx`.

```yaml
controlplane:~$ cat << EOF | kubectl apply -f -
> apiVersion: v1
kind: Pod
metadata:
  name: shared-namespace
spec:
  containers:
    - name: p1
      image: busybox
      command: ['/bin/sh', '-c', 'sleep 10000']
    - name: p2
      image: nginx
> EOF
pod/shared-namespace created
controlplane:~$
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-namespace
spec:
  containers:
    - name: p1
      image: busybox
      command: ['/bin/sh', '-c', 'sleep 10000']
    - name: p2
      image: nginx
```

🧠 *Why it works*: `kubectl apply -f -` reads from stdin and applies the manifest.

```powershell
controlplane:~$ kubectl get pods -owide
NAME               READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
shared-namespace   2/2     Running   0          41s   192.168.1.4   node01   <none>           <non


# GETTING INTO NODE01
controlplane:~$ ssh node01
Last login: Mon Feb 10 22:06:42 2025 from 10.244.0.131
```

### ✅ `kubectl get pods -o wide`

Shows Pod status, IP, and node location.

🧠 *Key Insight*: The Pod has **one IP** (`192.168.1.4`) shared by both containers.

**🧠 NETWORK NAMESPACE EXPLORATION**

### ✅ `ssh node01`

Accesses the node where the Pod is running.

```bash
# GETTING THE NETWORK NAMESPACE CREATED ON NODE01
node01:~$ ip netns list
cni-9e5781b9-8548-fb12-f265-5c198bdb1888 (id: 1)
cni-68761b83-4ec5-aa10-1928-1cd3777b24c4 (id: 0)
cni-dac19c53-ffd4-794f-4cc5-27619180ddda (id: 2)
```

### ✅ `ip netns list`

Lists all network namespaces created by CNI plugins.

🧠 *True Statement*: These `cni-*` namespaces are created by CNI (e.g., Calico) to isolate Pod networking.

```bash
node01:~$ kubectl get pods
E1020 08:46:02.840686    7699 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
E1020 08:46:02.840953    7699 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
E1020 08:46:02.842340    7699 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
E1020 08:46:02.842671    7699 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
E1020 08:46:02.844041    7699 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

### ⚠️ `kubectl get pods` (on node directly)

Fails because `kubectl` needs access to the control plane API server, not available on the node by default.

🧠 *True Statement*: You can't run `kubectl` on a node unless it's configured with kubeconfig and access to the API server.

```bash
# LIST NAMESPACE OF NGINX CONTAINER WE CREATED IN THE MULTI-CONTAINER POD
node01:~$ lsns | grep nginx
4026532606 mnt         2  6804 root             nginx: master process nginx -g daemon off;
4026532607 pid         2  6804 root             nginx: master process nginx -g daemon off;
4026532608 cgroup      2  6804 root             nginx: master process nginx -g daemon off;
node01:~$
```

## 🔍 NAMESPACE LINKING TO CONTAINERS

### ✅ `lsns | grep nginx`

Lists namespaces (mnt, pid, cgroup) for the `nginx` process.

🧠 *True Statement*: Each container has its own process, mount, and cgroup namespaces.

```bash
# HOW TO CHECK PAUSE CONTAINER,HERE 6804 IS PROCESS ID FROM ABOVE COMMAND lsns | grep nginx i.e. LISTING NETWORK NAMESPACE OF NGINX THUS GETTING PROCESS
# THIS GIVES LIST OF NAMESPACE REPLATED TO NGINX PROCESS 6804
node01:~$ lsns -p 6804
        NS TYPE   NPROCS   PID USER  COMMAND
4026531834 time      147     1 root  /sbin/init
4026531837 user      147     1 root  /sbin/init
4026532540 net         4  6678 65535 /pause
4026532600 uts         4  6678 65535 /pause
4026532601 ipc         4  6678 65535 /pause
4026532606 mnt         2  6804 root  nginx: master process nginx -g daemon off;
4026532607 pid         2  6804 root  nginx: master process nginx -g daemon off;
4026532608 cgroup      2  6804 root  nginx: master process nginx -g daemon off;
```

### ✅ `lsns -p 6804`

Shows all namespaces for the `nginx` process (PID 6804).

🧠 *Key Insight*: The `net, uts, ipc` namespace is held by the **pause container** (PID 6678), proving that all containers share the **pause container’s network namespace**.

✅ *True Statement*: Pause container anchors the network namespace for the Pod.

```bash
# check the network namespace (this gives list of all network namespaces)
node01:~$ ls -lt /var/run/netns
total 0
-r--r--r-- 1 root root 0 Oct 20 08:42 cni-dac19c53-ffd4-794f-4cc5-27619180ddda
-r--r--r-- 1 root root 0 Oct 20 08:25 cni-68761b83-4ec5-aa10-1928-1cd3777b24c4
-r--r--r-- 1 root root 0 Oct 20 08:25 cni-9e5781b9-8548-fb12-f265-5c198bdb1888
```

## 🔗 NETWORK INTERFACE INSPECTION

### ✅ `ls -lt /var/run/netns`

Lists symbolic links to network namespaces created by CNI.

🧠 *True Statement*: These are used by tools like `ip netns exec` to inspect Pod networking.

```bash
# exec into the namespace or into the pod to see the IP links
# HERE THIS IS EXEC INTO THE NAMESPACE TO SEE THE IP LINKS
# THIS IS ON NODE
node01:~$ ip netns exec cni-dac19c53-ffd4-794f-4cc5-27619180ddda ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
3: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether b2:3c:c6:4a:ce:9f brd ff:ff:ff:ff:ff:ff link-netnsid 0
node01:~$
```

### ✅ `ip netns exec <namespace> ip link`

Shows interfaces inside the Pod's network namespace.

🧠 *Key Insight*: You’ll see `eth0@if9`, which is the Pod’s virtual interface.

✅ *True Statement*: `eth0@if9` is the Pod-side of a **veth pair**.

```bash
# EXEC INTO THE POD TO SEE THE IP LINKS
# OBSERVE THAT eth0@if9 is same IP LINK COMMAND WE RAN ABOVE THERE eth0@if9
controlplane:~$ kubectl exec -it shared-namespace -- ip addr
Defaulted container "p1" out of: p1, p2
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
3: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether b2:3c:c6:4a:ce:9f brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.4/32 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::b03c:c6ff:fe4a:ce9f/64 scope link
       valid_lft forever preferred_lft forever
```

### ✅ `kubectl exec -it shared-namespace -- ip addr`

Executes into the Pod (defaults to container `p1`) and shows IP addresses.

🧠 *True Statement*: The Pod has one IP (`192.168.1.4`) and one interface (`eth0@if9`) shared by both containers.

Now you will see eth@9 -> after @ there will be a number and use that number and you can then search its corresponding link on the node using ip link | grep -A1 ^9 you will be able to see the same network namespace after link These are the veth pairs or based on the CNI

```bash
# WE CAN SEE CORRESPONDING LINK THAT 9 IS FROM eth0@9 INTERFACE SO 9 LINKED TO link-netns cni-dac19c53-ffd4-794f-4cc5-27619180ddda  
node01:~$ ip link | grep -A1 ^9
9: caliede2c6f02d9@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netns cni-dac19c53-ffd4-794f-4cc5-27619180ddda
node01:~$

# @9 IS LINK TO NETWORK NAMESPACE i.e. "cni-dac19c53-ffd4-794f-4cc5-27619180ddda (id: 2)"
# THIS IS ON HOST AND veth pair OF HOST i.e. CALICO PAIR "caliede2c6f02d9@if3" IS LINKED TO NETWORK NAMESPACE "link-netns cni-dac19c53-ffd4-794f-4cc5-27619180ddda"
node01:~$ ip link | grep -A1 ^9
9: caliede2c6f02d9@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether ee:ee:ee:ee:ee:ee brd ff:ff:ff:ff:ff:ff link-netns cni-dac19c53-ffd4-794f-4cc5-27619180ddda
node01:~$ ip netns list
cni-9e5781b9-8548-fb12-f265-5c198bdb1888 (id: 1)
cni-68761b83-4ec5-aa10-1928-1cd3777b24c4 (id: 0)
cni-dac19c53-ffd4-794f-4cc5-27619180ddda (id: 2)
node01:~$
```

## 🔗 HOST-SIDE VETH PAIR

### ✅ `ip link | grep -A1 ^9`

Finds the host-side veth pair (`caliede2c6f02d9@if3`) linked to the Pod’s network namespace.

🧠 *True Statement*: This confirms the veth pair — one end in the Pod (`eth0@if9`), one in the node (`calico...@if3 :: caliede2c6f02d9@if3`) Llinked to network namespace cni-dac19c53-ffd4-794f-4cc5-27619180ddda.

- Every Pod gets a **veth pair**: one end (`eth0@ifX`) inside the Pod, one end (`vethXYZ@ifY`) on the host.

- The host-side veth (e.g., `caliede2c6f02d9@if3`) connects the Pod to the node’s network via the CNI plugin (like Calico).

- `ip link | grep -A1 ^9` helps trace the veth pair by showing interface 9 and the next line (e.g., `eth0@if9` ↔ `veth@if3`).

- The **host-side veth** lives in the **node’s network namespace**, while the other end (`eth0`) lives inside the Pod. So when we say “host-side veth,” we’re referring to the part of the veth pair that connects the Pod to the **node (host) network stack**.

## ✅ FINAL VERDICT ON STATEMENTS

| Statement                                     | Verdict | Explanation                                                   |
| --------------------------------------------- | ------- | ------------------------------------------------------------- |
| Pod has one IP shared by all containers       | ✅       | All containers share the pause container’s network namespace. |
| Pause container holds the network namespace   | ✅       | It’s the anchor for Pod networking.                           |
| Pod IP connects to `eth0@ifX` inside Pod      | ✅       | That’s the Pod-side veth interface.                           |
| Host has matching veth pair in root namespace | ✅       | CNI sets up this pair for routing.                            |
| `kubectl` fails on node without API access    | ✅       | Needs kubeconfig and API server access.                       |
| `ip netns list` shows CNI namespaces          | ✅       | Created by plugins like Calico.                               |
| `lsns -p <PID>` shows container namespaces    | ✅       | Confirms shared or isolated namespaces.                       |

-----

### 🧠 What Is a Pause Container?

In Kubernetes, **every Pod** (except for some very specific cases) includes a **pause container**. This container is not for running your app — it's a tiny container that serves a critical purpose:

- 🧩 **It holds the network namespace** for the Pod.

- 🛡️ **It acts as the parent container** for all other containers in the Pod.

- 📡 It ensures that all containers in the Pod share the same IP address and port space.

- **Every Pod** will have **one pause container**.

- It’s created **automatically** by the kubelet when the Pod starts.

- Your app containers are then launched inside the same namespace as the pause container.

### ❗ Exceptions:

- **Single-container Pods** in some container runtimes (like `containerd` with certain configurations) may optimize away the pause container.

- **Windows Pods** use a different mechanism and may not use a pause container in the same way.

---

**containerd does create a network namespace**, but with help from the **CNI plugin**.

### 🔧 3-Liner Breakdown:

- 🧠 `containerd` itself handles container lifecycle (start, stop, etc.), but **delegates networking** to the CNI plugin.

- 🌐 When a Pod is created, the **CNI plugin** (like Calico, Flannel) sets up the **network namespace** and veth pair.

- 🔗 `containerd` then launches the container inside that **pre-configured network namespace**, linking it to the node.

**🚦 Intra-Node Pod Communication**

![inter node pod communication.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\inter%20node%20pod%20communication.png)

FLOW OF INTRA-NODE POD COMMUNICATION :

![inter node pod communication.png flow.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\inter%20node%20pod%20communication.png%20flow.png)

🚦 Intra-Node Pod Communication:

#### 🧱 Setup:

- Each Pod has its own **network namespace**.

- Each Pod connects to the node via a **veth pair**: `eth0` in Pod ↔ `vethX` in node.

- The node uses a **bridge** (e.g., `br1234`) to connect all Pod interfaces.

### 🔄 Traffic Flow: Pod A → Pod B (Same Node)

1. **Pod A sends packet via** `eth0` 
   → This goes out through its veth pair (`veth1`).

2. **Packet enters the node’s root namespace** 
   → `veth1` connects to the bridge (`br1234`).

3. **Bridge receives the packet** 
   → It checks the **ARP table** to resolve Pod B’s IP to its MAC address.

4. **Bridge forwards packet to Pod B’s veth (**`veth2`**)** 
   → This is the host-side of Pod B’s veth pair.

5. **Packet enters Pod B via** `eth0` 
   → Pod B receives the traffic as if it came from a regular network.

**🌐 Inter-Node Pod Communication : Node-Node Communication**

![node to node communication.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\node%20to%20node%20communication.png)

**DEFAULT GATEWAY :**

![default gateway.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\default%20gateway.png)

**Node to Node Communication flow :**

![node to node communication.png flow.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\node%20to%20node%20communication.png%20flow.png)

🌐 Inter-Node Pod Communication

#### 🧱 Setup:

- Each Pod lives in its own **network namespace** on a **node**.

- Pods connect to the node via **veth pairs** and a **bridge**.

- Nodes communicate over physical interfaces like `eth0`.

### 🔄 Traffic Flow: Pod A (Node 1) → Pod B (Node 2)

1. **Pod A sends packet via** `eth0` 
   → The packet exits Pod A through its veth pair and enters Node 1’s root namespace.

2. **Bridge on Node 1 checks ARP table** 
   → Realizes Pod B is **not on the same node**, so ARP **cannot resolve** it directly.

3. **Node 1 routes packet to default gateway** 
   → Uses routing table to forward packet via `eth0` to Node 2.

4. **Packet travels across the network** 
   → Could be via overlay (VXLAN), underlay (BGP), or direct routing — handled by CNI plugin (e.g., Calico).

5. **Node 2 receives packet on** `eth0` 
   → Routing table and CNI plugin forward it to Pod B’s veth interface.

6. **Pod B receives packet via** `eth0` 
   → Communication complete!

This flow involves **routing**, not just bridging. ARP works only within the same node — for cross-node traffic, routing and CNI plugins take over.

## ⚙️ Simple Flow:

1️⃣ Pod A (on Node1) wants to talk to Pod B (on Node2).  
2️⃣ Pod A sends traffic → Node1’s network interface.  
3️⃣ Node1’s CNI plugin sees Pod B is on Node2.  
4️⃣ Traffic goes through **cluster gateway or overlay network** → Node2.  
5️⃣ Node2’s CNI plugin delivers it to **Pod B**.

## 🧩 **Easy Way to Remember**

👉 “**Same Node → Direct Pod-to-Pod.  
Different Node → Via Gateway (CNI handles routing).**”

BOTH POD HAS THEIR OWN eth0 INTERFACE AND POD IP ADDRESS IS LINKED TO THE eth0

NODE TO NODE COMMUNICATION HAPPENS THROUGH GATEWAY.

1️⃣ Delete old services and pods

```bash
kubectl delete svc --all
kubectl delete pod --all
```

🟢 **Meaning:** Cleans up old Pods and Services to start fresh.

2️⃣ Create a new Pod

```bash
kubectl run nginx --image=nginx -l run=nginx

# example command to see how we can specify --labels and -l flag for label
controlplane:~$ kubectl run nginx --image=nginx --labels=app=nginx
controlplane:~$ kubectl run nginx --image=nginx -l app=nginx

controlplane:~$ kubectl run nginx --image=nginx -l run=nginx
```

🟢 **Meaning:**  
Creates a **Pod named `nginx`** with the image **nginx**, and adds a label **run=nginx** (important for service selector).

3️⃣ Generate a Service YAML (dry run)

```bash
kubectl expose pod nginx --port 80 --dry-run=client -o yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx  # selector : run : nginx tells select those pods which has label run=nginx
status:
  loadBalancer: {}
controlplane:~$
```

🟢 **Meaning:**  
Previews how a **Service** YAML would look before actually creating it.  
The **selector** automatically matches Pod labels (like `run=nginx`).

4️⃣ Create the Service

```bash
kubectl expose pod nginx --port 80
```

🟢 **Meaning:**  
Actually creates a **Service** of type **ClusterIP (default)**, which routes **internal cluster traffic** to the Pod.

5️⃣ Check Services and Endpoints

```bash
kubectl get svc
kubectl get ep
```

🟢 **Meaning:**

- **Service** gets a **ClusterIP** (internal virtual IP).

- **Endpoints (EP)** show which Pods’ IPs are connected to this Service.

- If EP shows `<none>`, the labels don’t match or Pod isn’t ready.

```bash
controlplane:~$ kubectl get ep
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME         ENDPOINTS         AGE
kubernetes   172.30.1.2:6443   6m22s


controlplane:~$ kubectl expose pod nginx --port 80
service/nginx exposed
controlplane:~$

# BY DEFAULT THE SERVICE CREATED IS ClusterIP. ClusterIP IS THAT KIND OF SERVICE WHICH IS USED FOR INTERNAL COMMUNICATION
controlplane:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   16m
nginx        ClusterIP   10.101.128.218   <none>        80/TCP    7m50s
controlplane:~$


controlplane:~$ kubectl run nginx --image=nginx -l run=nginx
pod/nginx created
controlplane:~$ kubectl expose pod nginx --port 80
service/nginx exposed

controlplane:~$ kubectl get ep
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME         ENDPOINTS         AGE
kubernetes   172.30.1.2:6443   6d22h
nginx        <none>            14s


controlplane:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   6d22h
nginx        ClusterIP   10.107.109.85   <none>        80/TCP    33s
```

6️⃣ Test internal communication

```bash
curl <ClusterIP>
```

🟢 **Meaning:**  
The request goes:

```bash
Client → Service (ClusterIP) → Pod (via Endpoint)
```

If you see the **Nginx Welcome Page**, it means the service routing works perfectly 🎉

🧭 **Flow Diagram (Easy Visual)**

```scss
Client Pod
   ↓
Service (ClusterIP) —> virtual internal IP (10.x.x.x)
   ↓
Endpoints —> Pod IP (192.168.x.x:80)
   ↓
Pod —> Container (nginx)
```

### 💡 **Key Points to Remember**

| Concept                      | Simple Explanation                                              |
| ---------------------------- | --------------------------------------------------------------- |
| **Service Type (ClusterIP)** | Default, used for **internal** communication inside the cluster |
| **Selector**                 | Connects Service to Pods with matching labels                   |
| **Endpoints**                | Show actual Pod IPs linked to a Service                         |
| **curl ClusterIP**           | Tests whether Service routes to the Pod correctly               |

COMMANDS CURL ::

```bash
controlplane:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   6d22h
nginx        ClusterIP   10.107.109.85   <none>        80/TCP    33s

# curl 10.107.109.85 this request will go to service and then request will go to pod
controlplane:~$ curl 10.107.109.85
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

controlplane:~$ kubectl get ep
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME         ENDPOINTS         AGE
kubernetes   172.30.1.2:6443   6d22h
nginx        192.168.1.4:80    2m23s
```

✅ **ClusterIP Service (Simple Explanation)**  
ClusterIP is the **default service type** in Kubernetes that gives your app an **internal virtual IP** accessible **only inside the cluster**.  
It acts like an **internal load balancer** — routing traffic from other Pods or Services to your backend Pods.  
🧠 *Example:* `kubectl expose pod nginx --port 80` creates a ClusterIP, and any Pod inside the cluster can access it using the name `nginx` on port 80.

✅ **Headless Service (Simple Explanation)**  
A Headless Service is created by setting `clusterIP: None`, meaning **no virtual IP or load balancing** — clients get **direct Pod IPs** instead.  
Useful when you want **direct access to each Pod**, like for databases (e.g., StatefulSets, Cassandra).  
🧠 *Example:*

```yaml
spec:
  clusterIP: None
```

Now DNS resolves each Pod IP individually — no load balancer, full Pod-level control.

### ⚙️ **1. Two Types of Applications**

- **Stateless:** Data is *not* stored permanently. Pods can be created or deleted anytime without issues.  
  🧠 *Example:* Nginx, frontend, or APIs.

- **Stateful:** Data *must* be saved and consistent. Used in databases like MySQL, PostgreSQL, MongoDB.

### 🚀 **2. Why Stateful Apps Can’t Run Using Deployments**

- Deployments create pods **randomly named** (e.g., `db-7fdgh`, `db-9fj32`).

- Service of type **ClusterIP** sends requests to *any* pod randomly → **data inconsistency**.

- Scaling up/down or deleting pods randomly may break replication or lose data.

💡 *Example:*  
If you write data to `db-1`, read might go to `db-2` (different pod) — data mismatch.

**WHY STATEFULL APPLICATION CANNOT RUN BY DEPLOYMENT ?**
SUPPOSE THROUGH DEPLOYMENT WE CREATED 3 PODS OF DATABASE AND WE CREATED SERVICE OF TYPE ClusterIP AND IMAGINE OUR APPLICATION POD WANTS TO PERFORM CRUD OPERATION ON DATABASE AND WHEN APPLICATION SENDS SIGNAL TO SERVICE TO WRITE DATA THEN THIS SERVICE WILL SEND TRAFFIC TO ANY DATABASE POD AND WE DON'T KNOW ABOUT THE NAME OF DATABASE POD BECAUSE THEY ARE RANDOMLY GENERATED SO HERE THIS WILL CAUSE DATA INCONSISTENCY. SUPPOSE WE SCALED OR UPDATED AND ADD ONE MORE POD AND THAT POD ALSO GET ADDED BY RANDOMLY GENERATED .
IF WE PERFORM DELETE OPERATION THEN IN RANDOM ORDER OPERATION WILL PERFORM

SO WE CAN USE STATEFULSETS. STATEFULSETS ARE ALSO KUBERNETES OBJECTS

### 💾 **3. Solution → Use StatefulSets**

- **Designed for stateful apps** (like databases).

- Provides:
  
  1. **Sticky Identity:** Each pod has a fixed, predictable name (e.g., `db-0`, `db-1`, `db-2`).
  
  2. **Ordered Scaling:** Pods start and stop in order (first `db-0`, then `db-1`, etc.).
  
  3. **Headless Service:** Used so pods have stable network identities.
  
  4. **Persistent Volumes:** Each pod gets its own volume for data storage.

🧩 *Example:*  
If a pod `db-0` restarts, it gets the same name and its same data volume back.

### 🧠 **4. StatefulSets Workflow**

1. `db-0` pod starts → attaches to its persistent volume → ready.

2. Then `db-1` starts, attaches its volume → ready.

3. Finally `db-2` starts.  
   👉 Pods are deleted in *reverse order* (e.g., `db-2`, then `db-1`, then `db-0`).

### 🧩 **5. Advanced: Operators**

- **Operators** automate database setup, replication, and recovery.

- Example: **CloudNativePG Operator** – manages PostgreSQL clusters with *primary/standby replication*.

STATEFULL APPLICATION IS NOT EASY TO RUN ON KUBERNETES BECAUSE WE HAVE TO TAKE CARE OF FEW THINGS WHICH IS CALLED REPLICATION OF DATA.
LIKE IN STATEFULSETS WE CREATED MASTER-SLAVE TYPE ARCHITECTURE THEN THE REPLICATION BETWEEN THEM THAT WE NEED TO MANAGE AND IF NOT THEN THERE ARE OPERATORS

### 💾 **1. Stateful Applications Are Hard to Run**

- Running **stateful apps (databases)** in Kubernetes is **not easy** because you must handle:
  
  - **Data replication** (syncing data across pods)
  
  - **Backups & disaster recovery**
  
  - **Monitoring and failover setup**

- These tasks are complex if done manually.

### 🤖 **2. Operators Make It Easier**

- Now, Kubernetes has **Operators** that **automate database management** (setup, backup, replication, failover).

- Popular examples:
  
  - **Percona Operator** → for MySQL, MongoDB, PostgreSQL
  
  - **KubeDB** → supports multiple databases
  
  - **CloudNativePG** → for PostgreSQL with primary–standby replication

🧠 *Think of Operators as “database admins” for Kubernetes.*

### 🌐 **3. StatefulSets and Headless Service**

- StatefulSets need a **Headless Service** to give **each pod its own stable DNS name**.

- When you set:

```yaml
clusterIP: None
```

- → it becomes a **Headless Service** (no ClusterIP assigned).

- Instead of one IP, **each pod gets its own DNS record** like:

```bash
db-0.mydb.default.svc.cluster.local
db-1.mydb.default.svc.cluster.local
```

✅ This helps pods (like database nodes) communicate directly with each other.

### 🧩 **4. Easy Way to Remember**

| Concept              | Meaning                           | Why Important                           |
| -------------------- | --------------------------------- | --------------------------------------- |
| **StatefulSet**      | Manages stateful apps (databases) | Gives predictable pod names             |
| **Headless Service** | `clusterIP: None`                 | Enables direct pod-to-pod communication |
| **Operator**         | Automates DB management           | Makes running databases easy            |

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  clusterIP: None        # Headless Service
  selector:
    app: mysql
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: "mysql"
  replicas: 3
  selector:
    matchLabels:
      app: mysql
```

🧾 *Each MySQL pod gets a stable name (mysql-0, mysql-1, mysql-2) and connects using its unique DNS.*

### 💾 **Concept Recap — What’s Happening Here**

You are deploying a **Stateful PostgreSQL database** using:

- A **StatefulSet** → manages pods with stable names and persistent storage

- A **Headless Service** → allows direct pod-to-pod communication

- **Persistent Volumes (PV & PVC)** → ensure data remains safe even if a pod is deleted

⚙️ **1. Service (Headless Service)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres
spec:
  ports:
  - port: 5432
  clusterIP: None   # Headless Service — no cluster IP assigned
  selector:
    app: postgres
```

🧠 **Easy to Remember:**  
Headless Service (`clusterIP: None`) → gives **each pod its own DNS** (like `postgres-0.postgres`, `postgres-1.postgres`) for **direct communication**.

🧱 **2. StatefulSet**

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres"       # must match Service name
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_PASSWORD
          value: "example"
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```

🧠 **Easy to Remember:**

- Each pod gets a **unique name** → `postgres-0`, `postgres-1`, `postgres-2`.

- Pods are **created one-by-one** (ordered startup).

- Each pod has its own **Persistent Volume Claim (PVC)** → data stays safe even if pod restarts.

- Pods are **deleted in reverse order** (highest number first).

### 🧩 **3. Command Flow (Step-by-Step)**

| Command                                           | Purpose                                                         |
| ------------------------------------------------- | --------------------------------------------------------------- |
| `kubectl get sc`                                  | Check available StorageClasses                                  |
| `kubectl apply -f svc.yaml`                       | Create Headless Service                                         |
| `kubectl apply -f statefulset.yaml`               | Deploy StatefulSet                                              |
| `kubectl get pods`                                | See pods created in order: postgres-0 → postgres-1 → postgres-2 |
| `kubectl get pv` / `kubectl get pvc`              | Check Persistent Volumes created                                |
| `kubectl scale statefulset postgres --replicas=3` | Scale pods — names stay predictable                             |
| `kubectl exec -it postgres-0 -- psql -U postgres` | Connect inside PostgreSQL pod                                   |
| `kubectl get svc`                                 | Observe service type `ClusterIP`, `CLUSTER-IP: None`            |

### 🧠 **4. Why StatefulSet Is Important**

- **Predictable Pod Names:** helps maintain order (`postgres-0`, `postgres-1`, etc.)

- **Stable Storage:** data persists even after restart

- **Ordered Scaling:** pods start and stop sequentially

- **Headless Service:** allows direct pod communication for database replication

### 💬 **5. Real-world Example**

Think of:

- `postgres-0` → **Master node** (handles writes)

- `postgres-1`, `postgres-2` → **Slave nodes** (replicate data)

If a new pod `postgres-3` joins, it syncs automatically using **replication logic** — giving **high availability**.

### 🔁 **6. Easy Way to Remember**

| Concept                            | Key Point                                          |
| ---------------------------------- | -------------------------------------------------- |
| **StatefulSet**                    | Used for databases or apps needing stable identity |
| **Headless Service**               | `clusterIP: None` → direct pod DNS                 |
| **PVC**                            | Keeps data even if pod is deleted                  |
| **Operator (e.g., CloudNativePG)** | Automates replication, backup, failover            |

**simple visual diagram** showing how the Headless Service connects `postgres-0`, `postgres-1`, and `postgres-2` (master-slave replication flow)

![headless-service-statefulset pod communication.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\headless-service-statefulset%20pod%20communication.png)

## 🧭 **Kubernetes Service DNS — Easy Explanation**

### ✅ What happens:

Each Service and Pod in Kubernetes gets a DNS name automatically through **CoreDNS**.  
Pods use `/etc/resolv.conf` to resolve DNS names.

If you don’t mention the namespace, DNS searches **only inside the Pod’s namespace.**

### 🧩 **Example:**

- **Pod namespace:** `test`

- **Service:** `data` in namespace `prod`

| Query                         | Result      | Why                                |
| ----------------------------- | ----------- | ---------------------------------- |
| `data`                        | ❌ No result | Looks only inside `test` namespace |
| `data.prod`                   | ✅ Works     | Specifies correct namespace        |
| `data.prod.svc.cluster.local` | ✅ Works     | Fully qualified domain name (FQDN) |

⚙️ `/etc/resolv.conf` example:

```pgsql
nameserver 10.32.0.10
search test.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
So when you run:
```

So when you run:

```kotlin
ping data
```

It automatically expands to:

```pgsql
data.test.svc.cluster.local
```

### 🧠 **In short:**

> A Pod can resolve `service-name.namespace.svc.cluster.local` through DNS.  If namespace is not mentioned, it searches in its own namespace.

## 🧾 **DNS Records exist for:**

1. **Services** → create DNS name for internal communication

2. **Pods** → especially useful for **Headless Services**

## 🌐 **NodePort Service — Easy Explanation**

### ✅ What it is:

NodePort exposes your app **outside the cluster** via `<NodeIP>:<NodePort>`  
It maps traffic from a fixed port range **(30000–32767)** on each Node to your Service.

NodePort WE SHOULD NEVER USE IT. BEST USECASE OF NodePort IS LOCAL-TESTING OR TESTING.

💡 Example:

```bash
kubectl run nginx --image=nginx
kubectl expose pod nginx --type=NodePort --port 80
kubectl get svc
```

Output example:

```pgsql
NAME    TYPE       CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.96.10.5   <none>        80:31234/TCP   1m
```

👉 You can now access it using:  
**`http://<NodeIP>:31234`**

⚙️ To check Node IP:

```bash
kubectl get nodes -o wide
```

🧪 To verify NodePort works (in KillerKoda or cluster):

```bash
curl <NodeIP>:<NodePort>
```

🔍 Check NodePort in iptables:

```bash
sudo iptables -t nat -L -n -v | grep -e NodePort -e KUBE

kubectl get svc

sudo iptables -t nat -L -n -v | grep <NodePort from kubectl get svc>
```

You’ll see entries like:

```python
KUBE-NODEPORTS all -- 0.0.0.0/0  match-set KUBE-NODEPORT dst
```

It means:

> All packets coming to NodePort range are accepted and routed to the right Service.

### 🚫 NodePort usage:

- ✅ **Good for:** Local testing, internal cluster debugging.

- ❌ **Not good for:** Production.  
  Use **LoadBalancer** or **Ingress** instead.

### 🧠 **Easy Summary to Remember**

| Concept              | Meaning                                 | Example                       |
| -------------------- | --------------------------------------- | ----------------------------- |
| **ClusterIP**        | Internal access only                    | App → DB                      |
| **NodePort**         | Access from outside via `<NodeIP>:port` | Browser → Node:31234          |
| **LoadBalancer**     | External cloud-managed IP               | Public internet               |
| **Headless Service** | No ClusterIP; direct pod DNS            | StatefulSets                  |
| **DNS name format**  | `svc-name.namespace.svc.cluster.local`  | `data.prod.svc.cluster.local` |

a simple visual diagram showing DNS resolution (Pod → Service DNS) and NodePort traffic flow (User → Node → Service → Pod)

![dns resolution.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\dns%20resolution.png)

### ⚙️ **`kubectl port-forward`**

**Purpose:**  
It forwards a port **from your local machine → to a Pod or Service inside the cluster**.  
Used mainly for **testing or debugging** internal services **without exposing them externally**.

**Example:**

`kubectl port-forward pod/nginx 8080:80`

➡️ This means:  
You can now open your browser and go to `http://localhost:8080`, and it will forward traffic to **port 80** of the **nginx Pod** running inside Kubernetes.

**Use-cases (2–3 lines):**

- Access internal Pods or Services from your laptop without a NodePort or LoadBalancer.

- Useful for **debugging APIs, databases, or web apps** running inside Kubernetes.

- Temporary, safe, and easy — no external exposure.

### 🌐 **Service Type: LoadBalancer**

**Purpose:**  
`LoadBalancer` service type **exposes your app publicly on the Internet** via a **Cloud Provider Load Balancer (AWS ELB, GCP LB, Azure LB, etc.)**.

**Example:**

```bash
kubectl run demo --image=nginx
kubectl expose pod demo --type=LoadBalancer --port=80
kubectl get svc
```

➡️ In a **local cluster (like Minikube or KillerCoda)**, this service stays in **Pending** because **no cloud provider** is available to provision a public IP.

FOR EVERY APPLICATION ENDPOINT WE DON'T HAVE TO CREATE LoadBalancer. IN PRODUCTION LoadBalancer SERVICE IS CREATED LESS BECAUSE IT IS EXPENSIVE.

**How it works (in cloud):**

1. The **Cloud Controller Manager** detects a LoadBalancer-type service.

2. It asks the **cloud provider** to create a **public IP** and **load balancer**.

3. Traffic from users (Internet) → hits **LoadBalancer IP** → routes to **Service** → then to **Pods**.

**Use-cases (few lines):**

- When you want users **outside the cluster** to access your app (e.g., website, API).

- Used in **production for web frontends, APIs, gateways**, etc.

- **Expensive**, so typically one LoadBalancer per external entry point (Ingress often used instead).

### 🧠 **Quick Summary to Remember**

| Command/Concept         | Purpose                               | Example                                        | Easy Tip                               |
| ----------------------- | ------------------------------------- | ---------------------------------------------- | -------------------------------------- |
| `kubectl port-forward`  | Access Pod locally without exposing   | `kubectl port-forward pod/myapp 8080:80`       | For debugging/test                     |
| `Service: LoadBalancer` | Public access via cloud load balancer | `kubectl expose pod myapp --type=LoadBalancer` | For production apps, creates public IP |

### 🌐 **ExternalName Service (Kubernetes)**

**✅ What it is:**  
An **ExternalName Service** lets your **Kubernetes application access services outside the cluster** using a **DNS name instead of an IP address**.

### ⚙️ **Key Points (Simplified and Corrected):**

1. **Access external services** — When your app in Kubernetes needs to talk to something **outside the cluster** (like an external database, API, or cloud service).  
   👉 Example: your app inside Kubernetes connects to `mysql.example.com` hosted externally.

2. **No IP management needed** — It simply **maps a Kubernetes service name to an external DNS name**. No ClusterIP or LoadBalancer is created.

3. **Zero overhead** — It acts as a **DNS alias**. Kubernetes DNS will automatically resolve it to the external domain.

4. **Cross-namespace communication** — It can also be used to simplify access **between namespaces or environments** in large organizations.

5. **Easy updates** — If the external endpoint changes, just update the `ExternalName`—no need to redeploy your apps.

🧩 **Example:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: mysql.example.com
```

Now, inside the cluster —  
Any Pod that connects to `external-db.default.svc.cluster.local` → automatically reaches **`mysql.example.com`** outside the cluster.

### 🧠 **Easy to Remember Summary**

| Feature      | Explanation                           | Example                         |
| ------------ | ------------------------------------- | ------------------------------- |
| Purpose      | Connect to external services (DB/API) | External MySQL or API           |
| Type         | `ExternalName`                        | `spec.type: ExternalName`       |
| How it works | DNS alias, no IP, no proxy            | `externalName: api.company.com` |
| Benefit      | Simple, no config changes in Pods     | Just update DNS name            |

### 🔹 **In short:**

**ExternalName = DNS bridge between cluster and external world.**  
No IPs, no ports — just a clean DNS redirect.

## 🧠 **EASY EXPLANATION (IN SHORT)**

### 🔹 **Goal**

We want an **application Pod (in `application-ns`)** to connect to a **PostgreSQL database Pod (in `database-ns`)**,  
even though they are in **different namespaces**.

Normally, Kubernetes DNS only resolves **within the same namespace** —  
so to connect across namespaces, we use a **Service of type `ExternalName`**.

EXAMPLE ::

```bash
controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ ls
Dockerfile  app.py  apppod.yaml  db.yaml  db_svc.yaml  externam-db_svc.yaml  requirements.txt
controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ cat requirements.txt
psycopg2-binary
controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ cat Dockerfile
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Add files
ADD app.py /app/
ADD requirements.txt /app/

# Install dependencies
RUN pip install -r requirements.txt

# Command to run the application
CMD ["python", "app.py"]


controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ cat app.py
import psycopg2
import os
import sys

def main():
    db_host = os.getenv("DATABASE_HOST")
    db_port = os.getenv("DATABASE_PORT")
    db_user = os.getenv("DATABASE_USER", "postgres")
    db_password = os.getenv("DATABASE_PASSWORD", "example")
    db_name = os.getenv("DATABASE_NAME", "postgres")

    conn_string = f"host={db_host} port={db_port} dbname={db_name} user={db_user} password={db_password}"
    print("Connecting to database\n ->%s" % conn_string)

    try:
        conn = psycopg2.connect(conn_string)
        cursor = conn.cursor()
        print("Connected!\n")
        cursor.execute("SELECT version();")
        version = cursor.fetchone()
        print(f"Database version: {version[0]}")
    except Exception as e:
        print("Unable to connect to the database.")
        print(e)
        sys.exit(1)
    finally:
        if conn:
            conn.close()

if __name__ == "__main__":
    main()

controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$cat apppod.yaml
    apiVersion: v1
kind: Pod
metadata:
  name: my-application
  namespace: application-ns
spec:
  containers:
  - name: app
    image: ttl.sh/saiyamdemo:1h
    env:
      - name: DATABASE_HOST
        value: "external-db-service"  # FROM externam-db_svc.yaml NAME OF SERVICE IS BEING CALLED HERE
      - name: DATABASE_PORT
        value: "5432"
      - name: DATABASE_USER
        value: "postgres"
      - name: DATABASE_PASSWORD
        value: "example"
      - name: DATABASE_NAME
        value: "postgres"

controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ cat db.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-database
  namespace: database-ns
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-database
  template:
    metadata:
      labels:
        app: my-database
    spec:
      containers:
      - name: database
        image: postgres:latest
        env:
          - name: POSTGRES_PASSWORD
            value: "example"
        ports:
        - containerPort: 5432
controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$ cat db_svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-database-service
  namespace: database-ns
spec:
  selector:
    app: my-database
  ports:
  - protocol: TCP
    port: 5432
    targetPort: 5432
controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$

controlplane:~/Kubernetes-hindi-bootcamp/part8/ExternalName$cat externam-db_svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db-service
  namespace: application-ns
spec:
  type: ExternalName  # service type ExternalName
  externalName: my-database-service.database-ns.svc.cluster.local   #<db-service>.<db-namespace>.svc.cluster.local
  ports:
  - port: 5432
```

## ⚙️ **STEP-BY-STEP FLOW**

### 1️⃣ Create namespaces

```bash
kubectl create ns database-ns
kubectl create ns application-ns
```

🟢 *Why:* Separate environments for DB and App, just like real-world setups.

2️⃣ Create PostgreSQL Database (in `database-ns`)

```bash
kubectl apply -f db.yaml
```

🟢 *Creates a database Pod using postgres:latest*  
💬 Comment:

```yaml
# db.yaml → Creates "my-database" pod inside "database-ns"
```

3️⃣ Create a **ClusterIP Service** for Database

```bash
kubectl apply -f db_svc.yaml
```

🟢 *This exposes the DB internally in that namespace.*  
💬 Comment:

```yaml
# db_svc.yaml → Creates Service "my-database-service" in "database-ns"
# It exposes port 5432 so others can connect (inside same namespace)
```

✅ The DNS for this service will be:

```pgsql
my-database-service.database-ns.svc.cluster.local
```

4️⃣ Create an **ExternalName Service** (in `application-ns`)

```bash
kubectl apply -f externam-db_svc.yaml
```

🟢 *This acts as a DNS alias in another namespace.*  
💬 Comment:

```yaml
# externam-db_svc.yaml → Creates "external-db-service" in "application-ns"
# It points to the real DB service's DNS (cross-namespace connection)
```

Now DNS resolution works like this:

```pgsql
external-db-service.application-ns.svc.cluster.local
        ↓
my-database-service.database-ns.svc.cluster.local
```

So any app in `application-ns` talking to `external-db-service`  
will automatically reach the PostgreSQL service in `database-ns`.

### 5️⃣ Build and Push Application Image

```bash
docker build --no-cache --platform=linux/amd64 -t ttl.sh/saiyamdemo:1h .
docker push ttl.sh/saiyamdemo:1h
```

🟢 *Creates your Python app image and pushes to temporary registry.*

6️⃣ Deploy the Application Pod

```bash
kubectl apply -f apppod.yaml
```

💬 Comment:

```yaml
# apppod.yaml → Creates "my-application" pod in "application-ns"
# It uses env vars (DATABASE_HOST, etc.) to connect to "external-db-service"
```

So inside the pod:

```ini
DATABASE_HOST = "external-db-service"
```

→ which resolves to  
`my-database-service.database-ns.svc.cluster.local`

7️⃣ Verify Connection

```bash
kubectl logs my-application -n application-ns
```

🟢 *You should see output like:*

```kotlin
Connecting to database -> host=external-db-service port=5432 ...
Connected! Database version: PostgreSQL 16.x
```

🔁 **FULL CONNECTION FLOW DIAGRAM (Text View)**

```yaml
+------------------------+             +--------------------------+
|  Namespace:            |             |  Namespace:              |
|  application-ns        |             |  database-ns             |
|                        |             |                          |
|  Pod: my-application   |             |  Pod: my-database        |
|  (uses psycopg2 app)   |             |  (Postgres DB)           |
|  ↓                     |             |  ↑                       |
|  ExternalName Service  |────────────▶|  ClusterIP Service       |
|  external-db-service   |             |  my-database-service     |
|  (maps to DNS)         |             |  (exposes port 5432)     |
+------------------------+             +--------------------------+

Flow:
my-application → external-db-service → my-database-service.database-ns → PostgreSQL DB
```

## 🧩 **EASY SUMMARY TABLE**

| Component             | Namespace        | Type                   | Purpose                     |
| --------------------- | ---------------- | ---------------------- | --------------------------- |
| `my-database`         | `database-ns`    | Pod                    | Runs PostgreSQL DB          |
| `my-database-service` | `database-ns`    | Service (ClusterIP)    | Exposes DB within namespace |
| `external-db-service` | `application-ns` | Service (ExternalName) | Connects across namespaces  |
| `my-application`      | `application-ns` | Pod                    | Python app connecting to DB |

## 🧠 **Easy-to-Remember Summary:**

> **ExternalName = DNS bridge between namespaces or external services.**  
> It doesn’t create IPs — it just **redirects requests via DNS name resolution.**

## 🧠 WHAT IS INGRESS?

> **Ingress** is a Kubernetes **resource** used to manage **external (HTTP/HTTPS) access** to your internal services.  
> It acts like a **smart entry gate** that routes traffic based on **hostnames** and **paths** (e.g., `/app1`, `/app2`) to the right service inside the cluster.

## ⚙️ WHY WE NEED AN INGRESS CONTROLLER

> Ingress by itself only defines **rules**.  
> To actually make it **work**, we need an **Ingress Controller** — a component that reads Ingress rules and configures a real HTTP reverse proxy (like NGINX, Kong, Traefik).

### Common Ingress Controllers:

1. **NGINX Ingress Controller** (most popular and open-source)

2. **Kong Ingress Controller**

3. **Traefik Ingress Controller**

You can deploy **any** of these in your Kubernetes cluster depending on your needs.

🔁 TRAFFIC FLOW (EASY TO REMEMBER)

```scss
USER → INGRESS CONTROLLER → INGRESS → SERVICE (ClusterIP) → POD
```

**Flow :**

USER SENDS REQUEST TO INGRESS-CONTROLLER AND REQUEST GOES TO INGRESS AND FROM INGRESS TO CLUSTERIP SERVICE AND THEN REQUEST GOES TO POD

### In Production:

The **Ingress Controller** is often exposed as a **LoadBalancer** (for external users).  
Each **Service** inside the cluster remains **ClusterIP**, accessible only internally.

## 💡 EXAMPLE SCENARIO

Let’s say you have two applications inside your cluster:

- `/app1` → `service1`

- `/app2` → `service2`

You want:

```bash
https://example.com/app1 → goes to service1
https://example.com/app2 → goes to service2
```

You can’t do this with **NodePort** or **LoadBalancer** alone,  
but you **can** with **Ingress** — using **path-based routing**.

🧩 YAML EXAMPLE

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: service1
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: service2
            port:
              number: 80
```

✅ **Explanation (line by line):**

- `host: example.com` → main domain user will hit

- `/app1` → route traffic to `service1`

- `/app2` → route traffic to `service2`

- **Ingress Controller (e.g. NGINX)** reads this and sets routing rules.

## 🧭 KEY CONCEPTS (EASY TO REMEMBER)

| Term                     | Meaning                                    | Example                       |
| ------------------------ | ------------------------------------------ | ----------------------------- |
| **Ingress**              | Set of routing rules                       | “If /app1 → service1”         |
| **Ingress Controller**   | The proxy engine that enforces those rules | NGINX, Kong, Traefik          |
| **ClusterIP Service**    | Internal-only endpoint for Pods            | Used by Ingress to reach Pods |
| **LoadBalancer Service** | Exposes Ingress Controller to the internet | External IP for users         |

## 🚀 SIMPLE FLOW (VISUAL TEXT VIEW)

```bash
             +-------------------+
User  --->   |  Ingress-Controller  |  (LoadBalancer / NGINX)
             +---------┬-----------+
                       |
                       ▼
             +-------------------+
             |     Ingress       |  (Rules)
             |  /app1 → svc1     |
             |  /app2 → svc2     |
             +---------┬-----------+
                       |
                       ▼
             +-------------------+
             | ClusterIP Service |
             | svc1  svc2        |
             +---------┬-----------+
                       |
                       ▼
             +-------------------+
             |      Pods         |
             | app1   app2       |
             +-------------------+
```

###### THIS FLOW IS BEST FOR PRODUCTION SETUP BECAUSE WE DEPLOY INGRESS-CONTROLLER AS LoadBalancer AND THEN FOR ACCESS WE HAVE DIFFERENT-DIFFERENT CLUSTERIP SERVICE AND WE CREATE DIFFERENT DIFFERENT INGRESS RESOURCE

## ⚙️ PATH-BASED ROUTING — WHY INGRESS IS NEEDED

> **Normal Services (ClusterIP/NodePort)** can’t handle URL path rules like `/app1` or `/app2`.  
> They only expose ports, not request paths.

**Ingress**, however, can inspect HTTP requests and route based on:

- **Path** (`/app1`, `/app2`)

- **Hostnames** (`api.example.com`, `shop.example.com`)

That’s why **path-based routing** is only possible through **Ingress**.

## 🧠 SUMMARY — EASY TO REMEMBER

| Feature          | Description                                           |
| ---------------- | ----------------------------------------------------- |
| Ingress          | Gateway for external HTTP/HTTPS traffic               |
| Needs Controller | Must have an Ingress Controller like NGINX            |
| Flow             | User → Ingress Controller → Ingress → ClusterIP → Pod |
| Benefit          | Host & Path-based routing, SSL termination            |
| Best Use         | Production environments with multiple services        |

# EXPLAIN WHY CHATGPT WITH EXAMPLE EASY TO UNDERSTAND

RULES > PATH BASED ROUTING IS NOT POSSIBLE.
PATH BASED ROUTING IS NOT POSSIBLE WHEN WE USE STANDARD SERVICE

WE CAN DO PATH BASED ROUTING USING INGRESS
HOST > REACHABLE > DNS OF NGINX INGRESS CONTROLLER

**EXAMPLE ::**

```yaml
deploy.yaml :
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
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
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: config-volume
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf  # Ensure this matches the filename in the ConfigMap
      volumes:
      - name: config-volume
        configMap:
          name: nginx-config  # nginx-config is the configmap we create from nginx.conf file runing kubectl create configmap nginx-config --from-file=nginx.conf


# we can use different-different services in service section of ingress.yaml so this is called path based routing.
ingress.yaml :
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: bootcamp
spec:
  ingressClassName: nginx
  rules:
  - host: "kubernetes.hindi.bootcamp"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
      - path: /public
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80



nginx.conf :
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        location /public {
            return 200 'Access to public granted!';
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
}

# if location is / i.e. root address then index.html or index.htm
# if location is /public then return 200 with access to public granted
# if for error-page then error-code like 500... with location 50x.html

svc.yaml :
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
```

## 🧠 WHAT YOU’RE DOING HERE — SIMPLE SUMMARY

You’re deploying an **NGINX web app** inside Kubernetes and exposing it:

1. **Internally** → via **ClusterIP Service**

2. **Externally (HTTP access)** → via **Ingress + Ingress Controller** (without NodePort/LoadBalancer manually)

## ⚙️ STEP-BY-STEP FLOW (EASY TO REMEMBER)

### 1️⃣ Create ConfigMap for `nginx.conf`

```bash
kubectl create configmap nginx-config --from-file=nginx.conf
```

- Stores your custom NGINX configuration.

- The `Deployment` mounts this ConfigMap at `/etc/nginx/nginx.conf`.

### 2️⃣ Deploy the NGINX Pod (Deployment)

```bash
kubectl apply -f deploy.yaml
```

- Runs one replica of NGINX.

- Mounts your `nginx.conf` file.

- Exposes port **80** inside the Pod.

### 3️⃣ Create a ClusterIP Service

```bash
kubectl apply -f svc.yaml
kubectl get svc
```

- The **ClusterIP** makes the Pod accessible **inside the cluster**.

- You can test internally:

```bash
curl <ClusterIP>
curl <ClusterIP>/public


curl <service-cluster-ip-of-nginx-service-from kubectl get svc>

curl <service-cluster-ip-of-nginx-service-from kubectl get svc>/public
```

✅ `/` → serves index.html  
✅ `/public` → “Access to public granted!”



### 4️⃣ Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.9.4/deploy/static/provider/cloud/deploy.yaml

```

- Deploys **Ingress Controller** (as a LoadBalancer or NodePort depending on environment).

- This controller is what actually listens to user HTTP requests.



### 5️⃣ Add entry in `/etc/hosts` (for local testing)

```bash
# we will get the ip of the pod running on the node or control-plane
kubectl get pod -owide

# this will give control-plane and node ip-address
kubectl get node -owide

vi /etc/hosts
```

Add this line:

```css
<node-ip-address> kubernetes.hindi.bootcamp
```

- Maps your custom domain (`kubernetes.hindi.bootcamp`) to your cluster node.

- This domain name **"kubernetes.hindi.bootcamp"** must match the **host** in your `ingress.yaml`.



### 6️⃣ Apply Ingress Resource

```bash
kubectl apply -f ingress.yaml
kubectl get ing
```

- Ingress defines rules for how incoming HTTP traffic is routed.

- Based on **path** or **host**, it sends traffic to the correct **Service** (ClusterIP).



### 7️⃣ Test it

```bash
curl kubernetes.hindi.bootcamp
curl kubernetes.hindi.bootcamp/public
```

✅ `/` → shows NGINX index page  
✅ `/public` → returns “Access to public granted!”



#### 💡 HOW IT WORKS (EASY FLOW TO REMEMBER)

```sql
User → Ingress Controller → Ingress → ClusterIP Service → Pod

```

## 🌍 IN PRODUCTION (CLOUD ENVIRONMENT)

Your statement was **almost correct**, here’s the **accurate version** 👇

✅ **Corrected Explanation:**

> In cloud setups, when you deploy the **Ingress Controller**, the **Service type: LoadBalancer** (automatically created by the controller) requests a **public IP** from the cloud provider (like AWS ELB, GCP LoadBalancer, Azure LB).  
> This public IP or DNS name is managed by the **Cloud Controller Manager (CCM)**.  
> Then, all your **Ingress resources** use that same Ingress Controller’s IP/DNS to handle external traffic.



#### 🔁 PRODUCTION FLOW

```java
User → Cloud LoadBalancer (public IP) → Ingress Controller (inside cluster)
     → Ingress Resource → ClusterIP Service → Pods

```

### 🧭 HOSTNAME CONFIGURATION

- The **LoadBalancer** in the cloud gives a **public DNS name**, like:

```bash
a1234567890abcdef.elb.amazonaws.com
```

* You create a **CNAME record** in your domain:

```bash
app.mycompany.com → a1234567890abcdef.elb.amazonaws.com
```

* In your **Ingress**, the host will be:

```bash
host: app.mycompany.com
```

✅ Now, external users hit `https://app.mycompany.com`,  
and the traffic automatically routes through the **LoadBalancer → Ingress Controller → Ingress → Service → Pod**.



## ⚙️ WHY PATH-BASED ROUTING WORKS HERE

> Standard Services (ClusterIP / NodePort) can’t do **path-based routing** (`/app1`, `/app2`)  
> because they work only at **TCP/port level**.  
> Ingress works at **HTTP layer (Layer 7)** — it can route by path or host.



## 🧠 SUMMARY — EASY TO REMEMBER

| Concept                  | Description                                | Example                        |
| ------------------------ | ------------------------------------------ | ------------------------------ |
| **Ingress**              | Routes external HTTP/S traffic to services | `/`, `/public`                 |
| **Ingress Controller**   | Proxy that implements ingress rules        | NGINX, Traefik                 |
| **ClusterIP Service**    | Internal access to Pods                    | Used by Ingress                |
| **LoadBalancer Service** | Cloud-managed external IP                  | Exposes Ingress Controller     |
| **/etc/hosts (local)**   | Maps custom host → node IP                 | For testing without DNS        |
| **Path-based Routing**   | Routes by URL path                         | `/app1` → svc1, `/app2` → svc2 |



✅ **In short (Easy line to memorize):**

> Ingress = Smart Gateway  
> Ingress Controller = Traffic Manager  
> ClusterIP = Internal connector  
> LoadBalancer = Cloud entry point



full flow:
User → Cloud LoadBalancer → Ingress Controller → Ingress → ClusterIP Service → Pod (with / and /public paths)



![Kubernetes HTTP Request Routing Flow1.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\Kubernetes%20HTTP%20Request%20Routing%20Flow1.png)



# ✅ **Corrected & Clean, Easy Explanation of ExternalDNS**

### **What is ExternalDNS?**

ExternalDNS is a Kubernetes addon that **automatically creates and updates DNS records** in your **cloud provider’s DNS** (like Route53, Cloud DNS, Azure DNS) based on Kubernetes objects such as **Service** and **Ingress**.

ExternalDNS : CloudManagedDNS : Every Cloud Provider creates their own implementation. ExternalDNS is Kubernetes project

ExternalDNS monitors or scans Service or Ingress and its pod implementation is Provider specific 

POD(POD Implementation is PROVIDER SPECIFIC) > SCAN SVC, INGRESS ] EXTERNAL DNS WILL SYNCHRONIZE WITH PROVIDER DNS

### **Why do we use ExternalDNS?**

- To **avoid manual DNS management**

- To automatically map your **domain → LoadBalancer IP**

- Makes DNS updates **automatic & consistent**



### ✔ Corrected

**ExternalDNS automatically watches Kubernetes Services and Ingress objects and creates/updates DNS records in your cloud DNS provider (AWS, GCP, Azure, etc.).**  
No manual IP entry is required.



### ✔ Corrected

With ExternalDNS, **you do NOT manually add DNS records**.  
ExternalDNS detects your **Service of type LoadBalancer** and automatically creates a DNS record like:

```scss
myapp.example.com → 34.12.55.10  (LoadBalancer IP)
```

### ✔ Corrected

When you create an **Ingress** with a `host:` field, ExternalDNS auto-creates the DNS entry.  
Example:

```makefile
host: app.example.com
```

ExternalDNS does:

```bash
app.example.com → LoadBalancer IP of Ingress Controller
```

### ✔ Corrected

Yes — **ExternalDNS supports multiple DNS providers** (AWS Route53, Google Cloud DNS, Azure DNS, Cloudflare, etc.) through plugins.



# 🎯 **Short, Easy-To-Remember Summary**

### **ExternalDNS = Auto-DNS Manager for Kubernetes**

- Watches **Service** & **Ingress**

- Automatically creates or updates DNS records

- No need to manually manage DNS in AWS/GCP/Azure

- Works with LoadBalancer Services & Ingress



### 🎯 **Simple Flow to Remember**

```markdown
1. Deploy application (Pods)
2. Create Service (ClusterIP or LoadBalancer)
3. Create Ingress with host: app.example.com
4. ExternalDNS detects it
5. ExternalDNS creates DNS record in cloud DNS
6. User → DNS → LoadBalancer → Ingress → Service → Pod

```



# 🟦 **Simple Example (Very Easy)**

### **Ingress YAML**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    external-dns.alpha.kubernetes.io/hostname: app.example.com
spec:
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80

```

What ExternalDNS does automatically

```php
Creates DNS entry:
app.example.com → <LoadBalancer IP of Ingress Controller>

```

#### 🟦 **Sample ExternalDNS Deployment Snippet**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: external-dns
spec:
  template:
    spec:
      containers:
      - name: external-dns
        image: k8s.gcr.io/external-dns/external-dns:latest
        args:
        - --provider=aws
        - --registry=txt
        - --domain-filter=example.com
        - --source=ingress

```

# 🟩 **Easy Commands to Deploy & Check**

##### **Check ExternalDNS logs**

```bash
kubectl logs -f deployment/external-dns
```

##### Check DNS it created

```nginx
nslookup app.example.com
```

##### Check Ingress

```bash
kubectl get ingress
```

##### Check LoadBalancer IP

```bash
kubectl get svc --namespace ingress-nginx
```

⭐ **Final 10-Second Memory Trick**

```markdown
Create Ingress → ExternalDNS sees it → DNS record created → Domain works automatically.

```

# ✅ **Scenario 1: Using ExternalDNS with Ingress (MOST COMMON)**

➡ **Ingress MUST have an Ingress Controller**  
➡ ExternalDNS only manages DNS — NOT routing  
➡ Ingress Controller handles the actual traffic

### ⭐ Required components:

- Ingress Controller (NGINX, AWS ALB, GKE Ingress, etc.)

- ExternalDNS

```csharp
Ingress → Requires Ingress Controller → Gets LB IP or DNS → ExternalDNS creates DNS record

```

### Example:

You create: `Ingress host: app.example.com`

ExternalDNS will only create DNS entry like:

`app.example.com → ALB / NGINX LB IP`

But routing inside the cluster is done by your **Ingress Controller**.

✔ Yes, you MUST install Ingress Controller here.



# ✅ **Scenario 2: Using ExternalDNS with Service type=LoadBalancer (NO Ingress)**

➡ In this case, YOU DO NOT NEED an Ingress Controller.

Why?  
Because the Service itself gets a public IP from cloud provider.

ExternalDNS will create:

`myapp.example.com → <LoadBalancer Service IP>`

Flow:

`Service (LoadBalancer) → Cloud LB IP → ExternalDNS creates DNS record`

No Ingress involved → No Ingress Controller required.



#### 🎯 **Simple Rule to Remember**

```perl
If you use Ingress → you MUST install an Ingress Controller.
If you only use LoadBalancer Services → No Ingress Controller needed.
```



# 📌 Practical Example Decision Table

| You create DNS for…  | Need Ingress Controller? | Need ExternalDNS?   |
| -------------------- | ------------------------ | ------------------- |
| Ingress              | ✅ YES                    | ✅ YES               |
| LoadBalancer Service | ❌ NO                     | ✅ YES               |
| ClusterIP Service    | ❌ NO                     | ❌ NO (no public IP) |



# ⭐ Full Simple Picture

### **Case A: Ingress + ExternalDNS (most used)**

```sql
User → DNS → LoadBalancer → Ingress Controller → Ingress → Service → Pod
```

### Case B: Only LoadBalancer + ExternalDNS

```sql
User → DNS → LoadBalancer → Service → Pod
```

### ✅ Final Summary You Can Remember in 5 Seconds

```makefile
Ingress = needs Ingress Controller.
ExternalDNS only creates DNS records.
```



# ✅ **Corrected Concept Summary (Very Simple)**

### **What is ExternalDNS?**

ExternalDNS is a **Kubernetes project** that automatically **creates DNS records** in your **cloud provider’s DNS** (AWS Route53, Google Cloud DNS, Azure DNS, Cloudflare, Civo DNS, etc.) based on **Services** or **Ingress**.



# 🎯 **ONE-LINER RULES (Easy to Remember)**

### ⭐ “ExternalDNS watches Kubernetes resources → creates/updates DNS automatically in cloud DNS.”

### ⭐ “Service/Ingress tell ExternalDNS what hostname to create using annotations or host fields.”

### ⭐ “Provider implementation + authentication varies per cloud (token, IAM role, API key).”

### ⭐ “ExternalDNS ≠ Ingress Controller. It only manages DNS, NOT traffic routing.”



# ✅ **Corrected Version of Your Statements**

### ✔ Corrected Statement 1

“ExternalDNS is a Kubernetes project; every cloud provider has its own DNS implementation.”

### ✔ Corrected Statement 2

“ExternalDNS scans/watches Services and Ingresses and syncs them with the cloud DNS provider.”

### ✔ Corrected Statement 3

“The ExternalDNS Pod behaves differently depending on which provider is chosen (provider-specific API calls).”

### ✔ Corrected Statement 4

“You declare hostnames using annotations on Services or hosts in Ingress; ExternalDNS converts them into real DNS records.”

### ✔ Corrected Statement 5

“Each cloud provider requires its own authentication method (API key, token, IAM role, service account, etc).”



🧠 **VERY EASY FLOW TO REMEMBER**

```pgsql
You create Service/Ingress → ExternalDNS sees it → Talks to cloud DNS → Creates DNS record automatically

```

`You create Service/Ingress → ExternalDNS sees it → Talks to cloud DNS → Creates DNS record automatically`

Cloud provider → route traffic to → LoadBalancer / Ingress → Service → Pod

ExternalDNS only manages the DNS part.



# 📌 **Why ExternalDNS is needed?**

Without ExternalDNS:

- You manually create DNS A/CNAME records in Route53, CloudDNS, etc.

With ExternalDNS:

- DNS is created automatically when Kubernetes Service/Ingress is created.



```yaml
externaldnspod.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx2
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx2
  annotations:
    # # ExternalDNS AUTOMATICALLY READS THIS "my-app2.saiyampathak.com"
    external-dns.alpha.kubernetes.io/hostname: my-app2.saiyampathak.com
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80



externalDNS :
apiVersion: v1
kind: ServiceAccount
metadata:
  name: external-dns
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: external-dns
rules:
- apiGroups: [""]
  resources: ["services","endpoints","pods"]
  verbs: ["get","watch","list"]
- apiGroups: ["extensions","networking.k8s.io"]
  resources: ["ingresses"]
  verbs: ["get","watch","list"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: external-dns-viewer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: external-dns
subjects:
- kind: ServiceAccount
  name: external-dns
  namespace: default
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: external-dns
spec:
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: external-dns
  template:
    metadata:
      labels:
        app: external-dns
    spec:
      serviceAccountName: external-dns
      containers:
      - name: external-dns
        image: registry.k8s.io/external-dns/external-dns:v0.14.1
        args:
        - --source=service
        - --source=ingress # ingress is also possible
        - --provider=civo
        env:
        - name: CIVO_TOKEN
          value: ""
```

# 📝 **Your YAML Explained in Super-Simple Notes**

2. ServiceAccount  : Provides identity to the ExternalDNS pod

3. ClusterRole: Allows ExternalDNS to read services, ingress, endpoints, nodes

4. ClusterRoleBinding : Connects the ServiceAccount with the Role permissions

### **4. Deployment (ExternalDNS Pod)**

Important fields:

#### `--source=service`

→ Watch LoadBalancer Services  
→ Create DNS record for service annotation

#### `--source=ingress`

→ Watch Ingress hosts  
→ Create DNS records for ingress hosts

#### `--provider=civo`

→ Use Civo cloud DNS  
(You can use aws, gcp, azure, cloudflare, etc.)

#### `env: CIVO_TOKEN`

→ API key for provider



📌 **Your Example Service (nginx2)**

`external-dns.alpha.kubernetes.io/hostname: my-app2.saiyampathak.com`

This tells ExternalDNS:

`Please create DNS record "my-app2.saiyampathak.com → LoadBalancer IP"`

As soon as the LB comes up, ExternalDNS updates DNS automatically.



# 🧾 **FINAL NOTES FOR STUDY (Simplest Possible)**

### ✔ **What ExternalDNS does**

Automatically creates DNS records for Service/Ingress in your cloud DNS.

### ✔ **What it watches**

- Services (LoadBalancer)

- Ingress (hosts)

### ✔ **How it knows what DNS to create**

- Service annotations

- Ingress hosts

### ✔ **Provider-specific implementation**

Every cloud provider (AWS, GCP, Azure, Cloudflare, Civo, DigitalOcean…) has a different API.

### ✔ **Authentication**

Token/API key required and passed to ExternalDNS pod.

### ✔ **Traffic routing**

ExternalDNS **does not route traffic**—only DNS.  
Routing is done by:

- LoadBalancer Service

- Ingress Controller

### ✔ **Key command patterns**

- Deploy ExternalDNS Deployment

- Add annotation to Services

- Add host to Ingress



FLOW EXTERNAL DNS :

![externalDNS.png](D:\Kubernetes%20Learning\kube_screenshot\part%208\externalDNS.png)
