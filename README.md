# Assignment-DevOps-Containerization-and-Container-Orchestration
DevOps: Containerization and Container Orchestration

````markdown
# 🚀 Complete Step-by-Step Plan

## Overall Architecture

```text
                         GitHub
                            │
                            ▼
                         Jenkins
                            │
                     Build Docker Images
                            │
                            ▼
                      Amazon ECR
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
           Frontend      Backend      Backend
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                      AWS EKS Cluster
                           │
                        Helm Chart
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
      Frontend       Backend Services     MongoDB
          │
          ▼
        Ingress
          │
          ▼
        Users

                    CloudWatch
                 Monitoring + Logs
````

---

# STEP 0 — Understand the Application

The assignment uses a MERN-based StreamingApp consisting of:

```text
authService        → 3001
streamingService   → 3002
adminService       → 3003
chatService        → 3004
frontend           → 3000 → Nginx 80
MongoDB            → 27017
```

MongoDB is shared by the services. The assignment allows MongoDB to be deployed using a StatefulSet/PVC or use a managed MongoDB-compatible endpoint.

---

# STEP 1 — Fork the GitHub Repository

The main project specifies:

```text
github.com/UnpredictablePrashant/StreamingApp
```
<img width="1917" height="965" alt="image" src="https://github.com/user-attachments/assets/8a209bf5-4bbc-4cb6-9fec-2ee004bb966d" />



You need to fork the repository present in above picture into your own GitHub account like below image.



<img width="1917" height="967" alt="image" src="https://github.com/user-attachments/assets/99d6a6eb-daea-45b1-a053-88b877cd9749" />

Then clone the repository into your local system like below example and the same is shown in below picture:

```bash
git clone https://github.com/amyadjs-tech/StreamingApp.git
cd StreamingApp
```

Check:

```bash
git status
```

Then:

```bash
git remote -v
```

You should see your repository.

---

<img width="995" height="742" alt="1 1" src="https://github.com/user-attachments/assets/238e3af0-f1f5-4a12-8362-60135d7f7683" />


# STEP 2 — Understand the Dockerfiles

The detailed assignment says the five services already have Dockerfiles and that you should reuse them rather than rewrite the application. And the same is shown in below picture.

Check:

```bash
find . -name Dockerfile
```

You should identify Dockerfiles for:

```text
backend/authService
backend/streamingService
backend/adminService
backend/chatService
frontend
```

---

<img width="1041" height="857" alt="1 2" src="https://github.com/user-attachments/assets/884efb99-4240-4c86-bc1b-2e9e2ff2fcf4" />


# STEP 3 — Build the Five Docker Images

The assignment requires one image for each component.

For the detailed assignment, the example naming is:

```bash
docker build -t amyadjs-tech/streaming-frontend:1.0.0 ./frontend

docker build -t amyadjs-tech/streaming-auth:1.0.0 ./backend/authService

docker build -t amyadjs-tech/streaming-stream:1.0.0 -f backend/streamingService/Dockerfile ./backend

docker build -t amyadjs-tech/streaming-admin:1.0.0 -f backend/adminService/Dockerfile ./backend

docker build -t amyadjs-tech/streaming-chat:1.0.0 -f backend/chatService/Dockerfile ./backend

```

Check:

```bash
docker images
```

You should have five images.

---

The STEP 3 is shown in below pictures

<img width="1917" height="1021" alt="5" src="https://github.com/user-attachments/assets/e777a173-6e5b-49f1-a11f-e64aad1b46e8" />

<img width="1917" height="1015" alt="11" src="https://github.com/user-attachments/assets/50e67d64-01f5-42df-bd62-add0d02ff841" />

<img width="1917" height="1030" alt="10" src="https://github.com/user-attachments/assets/6f19f3e6-98cf-432b-b355-b9d09e49d578" />

<img width="1917" height="1020" alt="9" src="https://github.com/user-attachments/assets/0ae6de33-d3ed-4df7-a264-00bf5c1233bd" />

<img width="1917" height="980" alt="8" src="https://github.com/user-attachments/assets/fc74e431-4955-4749-9209-1391abd9cb95" />


# STEP 4 — Push Images

The detailed assignment demonstrates Docker Hub for the initial containerization task.

But the main graded project specifically requires Amazon ECR, with a separate ECR repository for each component.

Therefore, for the final graded implementation, we'll use:

```text
Amazon ECR
│
├── streaming-auth
├── streaming-stream
├── streaming-admin
├── streaming-chat
└── streaming-frontend
```

---

# STEP 5 — Configure AWS CLI

Install/configure AWS CLI.

Check:

```bash
aws --version
```

<img width="1917" height="297" alt="21" src="https://github.com/user-attachments/assets/b439668a-0564-4c7c-b79e-5e814777fbe9" />


Configure:

```bash
aws configure
```

Enter:

```text
AWS Access Key ID
AWS Secret Access Key
Default region
Output format
```

For example, if using your EKS region:

```text
us-east-1
```

Verify:

```bash
aws sts get-caller-identity
```

If this returns your AWS account/user information, AWS CLI authentication is working as shown in picture below.

<img width="1917" height="196" alt="22" src="https://github.com/user-attachments/assets/94abe9a9-0ff3-4540-a777-b7f44a2d8598" />

The assignment explicitly requires AWS CLI configuration using your AWS credentials.

---

# STEP 6 — Create ECR Repositories

Create five repositories:

```bash
aws ecr create-repository \
  --repository-name streaming-auth \
  --region us-east-1
```

```bash
aws ecr create-repository \
  --repository-name streaming-stream \
  --region us-east-1
```

```bash
aws ecr create-repository \
  --repository-name streaming-admin \
  --region us-east-1
```

```bash
aws ecr create-repository \
  --repository-name streaming-chat \
  --region us-east-1
```

```bash
aws ecr create-repository \
  --repository-name streaming-frontend \
  --region us-east-1
```

Verify:

```bash
aws ecr describe-repositories --region us-east-1
```
The step 6 is shown in below pictures

<img width="1917" height="1020" alt="24" src="https://github.com/user-attachments/assets/83adea41-8487-4ea6-98ac-78b1a714bad0" />

<img width="1917" height="1021" alt="25" src="https://github.com/user-attachments/assets/3a259de3-d6c7-4577-95c1-88229e3b5fcb" />

<img width="1917" height="387" alt="26" src="https://github.com/user-attachments/assets/7c9e32c3-b2e0-48a3-9d55-68264ba3678d" />

<img width="1917" height="1022" alt="image" src="https://github.com/user-attachments/assets/86d7a430-0370-47e1-809f-0d26399b8849" />

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/e26b07bc-a1db-4232-b146-6a48ce8203a6" />

<img width="1917" height="932" alt="27" src="https://github.com/user-attachments/assets/fc4b3fcc-9abf-4f08-aa01-66d5b14a4b96" />

---

# STEP 7 — Login Docker to ECR

Run:

```bash
aws ecr get-login-password --region us-east-1 | \
docker login --username AWS --password-stdin \
939365917679.dkr.ecr.us-east-1.amazonaws.com
```

The step 7 is shown in below picture

<img width="1917" height="645" alt="image" src="https://github.com/user-attachments/assets/23c8b5a6-d2ae-4cb4-b88a-5b40c227d237" />


# STEP 8 — Tag Images for ECR

---
Here you are taking the images you already built/tagged for Docker Hub and creating an **ECR tag** for each one.

Your values are:

* Docker Hub username: `amyadjsdocker`
* AWS Account ID: `939365917679`
* Region: `us-east-1`
* ECR registry: `939365917679.dkr.ecr.us-east-1.amazonaws.com`

### 1. Tag Auth

```bash
docker tag amyadjsdocker/streaming-auth:1.0.0 \
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
```

### 2. Tag Streaming

```bash
docker tag amyadjsdocker/streaming-stream:1.0.0 \
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0
```

### 3. Tag Admin

```bash
docker tag amyadjsdocker/streaming-admin:1.0.0 \
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0
```

### 4. Tag Chat

```bash
docker tag amyadjsdocker/streaming-chat:1.0.0 \
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0
```

### 5. Tag Frontend

```bash
docker tag amyadjsdocker/streaming-frontend:1.0.0 \
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0
```

### 6. Verify the tags

Run:

```bash
docker images
```

You should now see **both Docker Hub and ECR names** for the same images, for example:

<img width="1917" height="1026" alt="32" src="https://github.com/user-attachments/assets/2f12387f-39c8-4ee3-b55a-5b52f815e3a4" />

<img width="1917" height="1021" alt="33" src="https://github.com/user-attachments/assets/ff2bba9f-1920-4387-8342-b846ddbdd2ae" />

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/9f395116-a465-4977-b2fc-2c89af4a9d32" />

# STEP 9 — Push Images to ECR

AWS account ID:

```text
939365917679
```

### 1. Push Auth

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
```

### 2. Push Streaming

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0
```

### 3. Push Admin

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0
```

### 4. Push Chat

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0
```

### 5. Push Frontend

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0
```

### Or run all five together

```bash
docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0

docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0

docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0

docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0

docker push 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0
```

### Verify all 5 are in ECR

After the pushes finish:

You can also check all repositories at once:

```bash
aws ecr describe-repositories \
  --region us-east-1 \
  --query 'repositories[].repositoryName' \
  --output table
```

You should have:

```text
streaming-auth
streaming-stream
streaming-admin
streaming-chat
streaming-frontend
```

And each should have the `1.0.0` image tag.

Step-9 is shown in below attached pictures

<img width="1917" height="1021" alt="34" src="https://github.com/user-attachments/assets/35b8f388-aa29-4629-a48e-baaa8db58f24" />

<img width="1917" height="1021" alt="35" src="https://github.com/user-attachments/assets/23540247-ce84-49ce-b97a-eb4781459ddc" />

<img width="1917" height="291" alt="image" src="https://github.com/user-attachments/assets/158114b6-bb70-465c-9e92-6a3e4849ee73" />

# STEP 10 — Create EKS Cluster

Yes. **Step 10 is the point where you create the AWS EKS cluster** for your StreamingApp. The main graded project requires an EKS cluster and allows `eksctl` for provisioning. 

Since you're using `us-east-1`, run the following.

### 1. Check prerequisites

```bash
aws sts get-caller-identity
```

This should show your AWS account.

Check `eksctl`:

```bash
eksctl version
```

Check `kubectl`:

```bash
kubectl version --client
```

---

### 2. Create the EKS cluster

Run:

```bash
eksctl create cluster \
  --name streaming-cluster \
  --region us-east-1 \
  --nodegroup-name streaming-nodes \
  --node-type t3.medium \
  --nodes 3 \
  --managed
```

### What this creates

Conceptually:

```text
                 AWS EKS
                    │
          ┌─────────┴─────────┐
          │   Control Plane   │
          │   (AWS managed)   │
          └─────────┬─────────┘
                    │
        ┌───────────┼───────────┐
        │           │           │
     Node 1       Node 2      Node 3
    t3.medium    t3.medium   t3.medium
```

You **don't manually create the control-plane node**. AWS manages the EKS control plane. The `--nodes 3` option creates three worker nodes in the managed node group.

The cluster creation can take several minutes.

---

### 3. Verify the cluster

After creation completes:

```bash
eksctl get cluster
```

You should see something similar to:

```text
NAME                REGION
streaming-cluster   us-east-1
```

Then:

```bash
kubectl get nodes
```

Expected:

```text
NAME                            STATUS   ROLES    AGE   VERSION
ip-xxx-xxx-xxx-xxx...           Ready    <none>   ...   ...
ip-xxx-xxx-xxx-xxx...           Ready    <none>   ...   ...
ip-xxx-xxx-xxx-xxx...           Ready    <none>   ...   ...
```

You can also run:

```bash
kubectl get nodes -o wide
```

to see the EC2 instance details.

### 4. Verify Kubernetes connectivity

Run:

```bash
kubectl get pods -A
```

You should see Kubernetes system pods running in namespaces such as `kube-system`.

---

### Your project flow now

You've reached:

```text
GitHub
   ↓
Docker Images
   ↓
Docker Hub ✅
   ↓
ECR ✅
   ↓
EKS ← YOU ARE HERE
   ↓
Helm
   ↓
StreamingApp
```

The detailed assignment then moves into **Kubernetes/Helm deployment**, while the main graded project requires the MERN stack to be deployed on EKS and validated under scaling operations.  

**Before you run the cluster creation command:** make sure you're comfortable with the AWS cost implications. An EKS cluster plus 3 `t3.medium` nodes and associated resources can incur charges while running. Once your project testing/submission is complete, you can delete the cluster.

The step-10 is shown in below 7-pictures

<img width="1917" height="1017" alt="image" src="https://github.com/user-attachments/assets/67a84cf3-e197-4bac-8ed4-295db67e1e53" />

<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/e84b6d7d-f421-4199-8709-6458e028dfae" />

<img width="1917" height="91" alt="image" src="https://github.com/user-attachments/assets/e51c5515-178d-45f1-8181-ac99d09777f1" />

<img width="1917" height="967" alt="image" src="https://github.com/user-attachments/assets/1f2afb7b-09df-492b-b9f3-5e0d17c83690" />

<img width="1917" height="135" alt="image" src="https://github.com/user-attachments/assets/3b38f1d9-386a-4f1a-b4d7-59fc73b704e8" />

<img width="1917" height="102" alt="image" src="https://github.com/user-attachments/assets/154e8a33-d2df-4316-91f6-c23d26b6da86" />

<img width="1917" height="267" alt="image" src="https://github.com/user-attachments/assets/13bf77e9-c257-4f9a-aa1a-1acc30db6c41" />


# STEP 11 — Create Kubernetes Namespace

Here we create a separate Kubernetes namespace called `streaming`** so that the StreamingApp resources can be deployed into that namespace.

### 1. Create the namespace

Run:

```bash
kubectl create namespace streaming
```

Expected:

```text
namespace/streaming created
```

### 2. Verify it

```bash
kubectl get namespaces
```

You should see:

```text
NAME              STATUS   AGE
default           Active   ...
kube-system       Active   ...
kube-public       Active   ...
kube-node-lease   Active   ...
streaming         Active   ...
```

### 3. Verify specifically

```bash
kubectl get namespace streaming
```

Expected:

```text
NAME        STATUS   AGE
streaming   Active   ...
```

### Why are we creating `streaming`?

Your application will have multiple Kubernetes resources:

```text
streaming namespace
│
├── auth Deployment
├── auth Service
├── streaming Deployment
├── streaming Service
├── admin Deployment
├── admin Service
├── chat Deployment
├── chat Service
├── frontend Deployment
├── frontend Service
├── MongoDB
├── ConfigMap
├── Secret
└── Ingress
```

Keeping them under `streaming` makes the application resources easier to manage separately from Kubernetes system resources.

For example:

```bash
kubectl get pods -n streaming
```

```bash
kubectl get services -n streaming
```

```bash
kubectl get deployments -n streaming
```

The step-11 is shown in below picture

<img width="1917" height="977" alt="image" src="https://github.com/user-attachments/assets/0d7b75d8-96ad-41fe-922b-a30b2ae105ad" />


# STEP 12 — Create Kubernetes Deployments

Here you need five Deployments, one for each application service. The assignment guide specifies one Deployment + Service pair for each component. 

Since your ECR account ID is `939365917679`, the five ECR images are:

```text
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0
939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0
```

<img width="1917" height="157" alt="image" src="https://github.com/user-attachments/assets/2e4f2f6e-f871-4b11-9ffa-e79cf4d44711" />


## 1. Create a folder

From your `StreamingApp` directory:

```bash
mkdir -p k8s
cd k8s
```

## 2. Create `auth-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth
  namespace: streaming
spec:
  replicas: 2
  selector:
    matchLabels:
      app: auth
  template:
    metadata:
      labels:
        app: auth
    spec:
      containers:
        - name: auth
          image: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
          ports:
            - containerPort: 3001
```

## 3. Create `streaming-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: streaming
  namespace: streaming
spec:
  replicas: 2
  selector:
    matchLabels:
      app: streaming
  template:
    metadata:
      labels:
        app: streaming
    spec:
      containers:
        - name: streaming
          image: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0
          ports:
            - containerPort: 3002
```

## 4. Create `admin-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: admin
  namespace: streaming
spec:
  replicas: 2
  selector:
    matchLabels:
      app: admin
  template:
    metadata:
      labels:
        app: admin
    spec:
      containers:
        - name: admin
          image: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0
          ports:
            - containerPort: 3003
```

## 5. Create `chat-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: chat
  namespace: streaming
spec:
  replicas: 2
  selector:
    matchLabels:
      app: chat
  template:
    metadata:
      labels:
        app: chat
    spec:
      containers:
        - name: chat
          image: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0
          ports:
            - containerPort: 3004
```

## 6. Create `frontend-deployment.yaml`

The frontend image is served through Nginx, so its container port is `80`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: streaming
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
        - name: frontend
          image: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0
          ports:
            - containerPort: 80
```

### 7. Check your files

```bash
ls
```

You should have:

```text
auth-deployment.yaml
streaming-deployment.yaml
admin-deployment.yaml
chat-deployment.yaml
frontend-deployment.yaml
```

### 8. Apply them

You can apply all five at once:

```bash
kubectl apply -f . -n streaming
```

Then check:

```bash
kubectl get deployments -n streaming
```

and:

```bash
kubectl get pods -n streaming
```

You should eventually see **10 pods** because each Deployment has `replicas: 2`:

```text
auth          2/2
streaming     2/2
admin         2/2
chat          2/2
frontend      2/2
```

The step-12 is shown in below pictures

<img width="1917" height="1022" alt="image" src="https://github.com/user-attachments/assets/5b488d2f-f684-43dd-8a63-e668a37be153" />

<img width="1917" height="1021" alt="image" src="https://github.com/user-attachments/assets/4a3d0b4c-f445-460f-9cf6-b645f5808596" />

<img width="1917" height="905" alt="image" src="https://github.com/user-attachments/assets/74e1de3d-2fad-407d-a337-4b278fa57f3f" />

<img width="1917" height="1022" alt="image" src="https://github.com/user-attachments/assets/32e06506-9a51-4d7d-8d7b-1b5002a27686" />

# STEP 13 — Create Kubernetes Services

Here we need to create five Kubernetes `ClusterIP` Services**, one for each application component. The assignment guide specifies a Deployment + Service pair for each component and says to use ClusterIP Services. 

Create these five files inside your `k8s` directory.

### 1. `auth-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: auth-svc
  namespace: streaming
spec:
  type: ClusterIP
  selector:
    app: auth
  ports:
    - port: 3001
      targetPort: 3001
```

### 2. `streaming-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: streaming-svc
  namespace: streaming
spec:
  type: ClusterIP
  selector:
    app: streaming
  ports:
    - port: 3002
      targetPort: 3002
```

### 3. `admin-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: admin-svc
  namespace: streaming
spec:
  type: ClusterIP
  selector:
    app: admin
  ports:
    - port: 3003
      targetPort: 3003
```

### 4. `chat-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: chat-svc
  namespace: streaming
spec:
  type: ClusterIP
  selector:
    app: chat
  ports:
    - port: 3004
      targetPort: 3004
```

### 5. `frontend-service.yaml`

Your frontend container uses Nginx on port `80`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-svc
  namespace: streaming
spec:
  type: ClusterIP
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 80
```

### Apply all five

From the `k8s` directory:

```bash
kubectl apply -f . -n streaming
```

### Verify

```bash
kubectl get services -n streaming
```

You should see:

```text
NAME             TYPE        CLUSTER-IP
auth-svc         ClusterIP   ...
streaming-svc    ClusterIP   ...
admin-svc        ClusterIP   ...
chat-svc         ClusterIP   ...
frontend-svc     ClusterIP   ...
```

You can also check that the Services are connected to the Pods:

```bash
kubectl get endpoints -n streaming
```

The important relationship is:

```text
auth Deployment
      ↓
   app: auth
      ↓
 auth-svc
      ↓
auth Pods
```

And similarly:

```text
streaming-svc → streaming Pods
admin-svc     → admin Pods
chat-svc      → chat Pods
frontend-svc  → frontend Pods
```

The guide later uses these Service names for the Ingress routing, including `/api/auth`, `/api/streaming`, `/api/admin`, `/api/chat`, and `/` for the frontend. 

**Next in your sequence:** Step 14 should be the **ConfigMap and Secret**, because the assignment requires non-secret configuration in ConfigMaps and sensitive values such as JWT/AWS credentials in Secrets. 

The step-13 is shown in below pictures

<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/36dd5e31-1d62-4ed8-82f2-bea6542cc09c" />

<img width="1917" height="1026" alt="image" src="https://github.com/user-attachments/assets/b116c1ef-e65c-440e-a29e-2e6fb5e2463a" />

<img width="1917" height="1020" alt="image" src="https://github.com/user-attachments/assets/9d99c380-262e-4dfa-8615-e56809c9557e" />

# STEP 14 — Create ConfigMap

Here we need to create a ConfigMap for non-sensitive configuration.** The assignment specifically gives examples such as `PORT`, `CLIENT_URLS`, `AWS_REGION`, and the MongoDB host. 

Since your namespace is `streaming`, create:

```text
k8s/configmap.yaml
```

### `configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: streaming-config
  namespace: streaming
data:
  AWS_REGION: us-east-1
  MONGO_HOST: mongo
```

You can create the file:

```bash
vi configmap.yaml
```

Paste the YAML, then save:

**Enter Esc button then type :wq then press Enter**

### Apply the ConfigMap

```bash
kubectl apply -f configmap.yaml
```

Expected:

```text
configmap/streaming-config created
```

### Verify

```bash
kubectl get configmap -n streaming
```

You should see:

```text
NAME               DATA   AGE
streaming-config   2      ...
```

To see its contents:

```bash
kubectl describe configmap streaming-config -n streaming
```

You should see:

```text
AWS_REGION:   us-east-1
MONGO_HOST:   mongo
```

### Important

Don't put sensitive values such as:

```text
JWT_SECRET
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

in this ConfigMap. The assignment specifically separates non-secret configuration in ConfigMaps from sensitive values in Kubernetes Secrets. 

Also, `MONGO_HOST: mongo` assumes that your MongoDB Kubernetes service will eventually be named **`mongo`**. The detailed guide uses MongoDB as a shared database and allows it to be deployed as a StatefulSet with persistent storage. 

**Next Step 15:** create the Kubernetes **Secret** for the sensitive configuration.

The step-14 is shown in below pictures

<img width="1917" height="767" alt="image" src="https://github.com/user-attachments/assets/0327b661-eccf-4b0b-88e9-00d44acc1a48" />

# STEP 15 — Create Secret

**Step 15 is the Kubernetes Secret configuration.** Your structure is correct.

However, **do not put your real AWS access key or secret key directly into a Git-tracked YAML file**.

### STEP 15 — Create Secret

Create:

```text
k8s/secret.yaml
```

For now, you can use:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: streaming-secret
  namespace: streaming
type: Opaque
stringData:
  JWT_SECRET: your-secret
  AWS_ACCESS_KEY_ID: your-key
  AWS_SECRET_ACCESS_KEY: your-secret-key
```

### Apply it

```bash
kubectl apply -f k8s/secret.yaml
```

Verify:

```bash
kubectl get secret -n streaming
```

You should see:

```text
NAME               TYPE     DATA   AGE
streaming-secret   Opaque   3      ...
```

To inspect the Secret metadata:

```bash
kubectl describe secret streaming-secret -n streaming
```

Kubernetes will **not display the actual Secret values** in the normal `describe` output.

### Important for your project

Your assignment requires sensitive configuration such as `JWT_SECRET` and AWS credentials to be handled through a Kubernetes Secret. 

For the actual AWS deployment, we'll make the setup safer by **not committing real credentials to GitHub**.

**Next step after this: STEP 16 — MongoDB StatefulSet + Service**, because your ConfigMap currently has:

```yaml
MONGO_HOST: mongo
```

So we'll create the MongoDB service with the name `mongo` to match it.

The step-15 is shown in below picture

<img width="1917" height="511" alt="image" src="https://github.com/user-attachments/assets/5b646e6e-4fd6-4f47-bfa4-0226f104feb7" />


# STEP 16 — Deploy MongoDB

For **STEP 16**, since your current ConfigMap has:

```yaml
MONGO_HOST: mongo
```

we should create a MongoDB **StatefulSet + PVC + Service** in the `streaming` namespace. The assignment guide explicitly allows MongoDB to be deployed as a StatefulSet with persistent storage. 

### STEP 16.1 — Create `mongo.yaml`

Create:

```text
k8s/mongo.yaml
```

Use:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongo
  namespace: streaming
spec:
  serviceName: mongo
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:6
          ports:
            - containerPort: 27017
          volumeMounts:
            - name: mongo-data
              mountPath: /data/db

  volumeClaimTemplates:
    - metadata:
        name: mongo-data
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 5Gi

---
apiVersion: v1
kind: Service
metadata:
  name: mongo
  namespace: streaming
spec:
  clusterIP: None
  selector:
    app: mongo
  ports:
    - port: 27017
      targetPort: 27017
```

### STEP 16.2 — Apply it

```bash
kubectl apply -f mongo.yaml
```

### STEP 16.3 — Check MongoDB

```bash
kubectl get statefulset -n streaming
```

Then:

```bash
kubectl get pods -n streaming
```

You should eventually see:

```text
mongo-0    1/1    Running
```

Check the PVC:

```bash
kubectl get pvc -n streaming
```

You should see something similar to:

```text
mongo-data-mongo-0    Bound
```

### STEP 16.4 — Verify the MongoDB Service

```bash
kubectl get svc -n streaming
```

You should see:

```text
mongo
```

Your applications can then connect internally using:

```text
mongodb://mongo:27017
```

or the Kubernetes DNS name:

```text
mongo.streaming.svc
```

**Important:** Because your ConfigMap already says:

```yaml
MONGO_HOST: mongo
```

the Service name `mongo` above matches your configuration.

After MongoDB is `Running` and the PVC is `Bound`, we can move to **STEP 17 — connect the five application Deployments to the ConfigMap and Secret**.

The step-16 is shown in below pictures

<img width="1917" height="1022" alt="60" src="https://github.com/user-attachments/assets/fc773653-53bb-4610-bd0d-0c4c376cdb2f" />

<img width="1917" height="1022" alt="61" src="https://github.com/user-attachments/assets/20a5481e-6fa1-4dd4-b1ab-400b9d253e78" />

<img width="1917" height="234" alt="58" src="https://github.com/user-attachments/assets/965a4be4-ccbb-4040-9f7d-81044ad76f0e" />

<img width="1917" height="512" alt="59" src="https://github.com/user-attachments/assets/093cc161-d309-4e28-bbad-d32fe94c9273" />

# STEP 17 — Add Readiness and Liveness Probes

Step 17.1 — Check your current Deployments

From:

cd ~/StreamingApp/k8s

run:

kubectl get deployments -n streaming

You should see something like:

NAME         READY   UP-TO-DATE   AVAILABLE
admin        ...
auth         ...
chat         ...
frontend     ...
streaming    ...

The step-17.1 is shown in below pictures

<img width="1917" height="167" alt="66" src="https://github.com/user-attachments/assets/577a9a31-2a91-4a1e-bba6-a1dc793b6c6a" />

Step 17.2 — Check the current Deployment YAML

Let's start with auth.

Run:

kubectl get deployment auth -n streaming -o yaml

This will show your current configuration.

You can also save it if needed:

kubectl get deployment auth -n streaming -o yaml > auth-current.yaml

<img width="1917" height="1020" alt="67" src="https://github.com/user-attachments/assets/b2ad4d4d-bbab-4f73-b0e7-80828c89f602" />

<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/149c6d88-ce7a-470c-9ab9-e3b513ef9104" />


Step 17.3 — Find the application's health endpoint

Before adding:

readinessProbe:
livenessProbe:

we need to know whether your application has something such as:

/health

or:

/healthz

or another endpoint.

Because this is your actual application, let's inspect the source code rather than assuming.

From the project root:

cd ~/StreamingApp

Run:

grep -RniE "health|healthcheck|/health|/healthz" backend/authService

If you get output showing something like:

app.get('/health', ...)

then we know /health is available.

If there is no health endpoint, don't add a guessed /health probe yet.

The step 17.3 is shown in below pictures

<img width="1917" height="1020" alt="63" src="https://github.com/user-attachments/assets/6d9dc562-a7af-411f-9081-dd73b7e35220" />

Step 17.4 — Check the other services

Run these one by one:

Streaming
grep -RniE "health|healthcheck|/health|/healthz" backend/streamingService
Admin
grep -RniE "health|healthcheck|/health|/healthz" backend/adminService
Chat
grep -RniE "health|healthcheck|/health|/healthz" backend/chatService
Frontend
grep -RniE "health|healthcheck|/health|/healthz" frontend

<img width="1917" height="1020" alt="63" src="https://github.com/user-attachments/assets/5393516e-72bd-4518-9195-b2cba5dd4d7f" />

Step 17.5 — Understand what we are looking for

Suppose Auth contains:

app.get('/health', (req, res) => {
    res.status(200).send('OK');
});

Then its probes can use:

readinessProbe:
  httpGet:
    path: /health
    port: 3001
  initialDelaySeconds: 10
  periodSeconds: 10

livenessProbe:
  httpGet:
    path: /health
    port: 3001
  initialDelaySeconds: 30
  periodSeconds: 20

The meaning is:

Readiness

Pod starts
   ↓
/health returns 200?
   ↓
YES → Pod receives traffic
NO  → Pod does not receive traffic

Liveness

Pod is running
   ↓
/health returns successfully?
   ↓
YES → keep Pod running
NO  → Kubernetes restarts container

<img width="1917" height="1027" alt="64" src="https://github.com/user-attachments/assets/f4aa44ba-6844-49ca-9d4d-eb9f8c932fe6" />

<img width="1917" height="1021" alt="65" src="https://github.com/user-attachments/assets/3b4ae761-b42a-473e-bcd4-7717a71dae53" />

<img width="1917" height="841" alt="image" src="https://github.com/user-attachments/assets/99862279-24dc-4ed9-ae3a-8fb3d49e37ba" />


# STEP 18 — Create Helm Chart

Now we convert the Kubernetes manifests into Helm templates.

Required structure:

```text
streamingapp/
│
├── Chart.yaml
├── values.yaml
│
└── templates/
    ├── auth-deployment.yaml
    ├── auth-service.yaml
    ├── streaming-deployment.yaml
    ├── streaming-service.yaml
    ├── admin-deployment.yaml
    ├── chat-deployment.yaml
    ├── frontend-deployment.yaml
    ├── mongo-statefulset.yaml
    ├── configmap.yaml
    ├── secret.yaml
    └── ingress.yaml
```

This structure comes directly from the assignment guide.

---

The step-18 is shown in below picture

<img width="907" height="612" alt="image" src="https://github.com/user-attachments/assets/cba65c81-1301-45bf-9ec5-9c852454649f" />

<img width="1917" height="1027" alt="image" src="https://github.com/user-attachments/assets/9443b2a4-aa88-4bdb-8dd8-23189b2d8c0b" />

<img width="1917" height="1016" alt="image" src="https://github.com/user-attachments/assets/7a4512e1-4929-4619-a92c-4df6b319158a" />

<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/7f22ccad-5fab-4b45-99df-14f7cc4a457d" />

<img width="1917" height="1025" alt="image" src="https://github.com/user-attachments/assets/d25f771f-fb4e-4734-9b53-f8d44d8f587d" />



# STEP 19 — Create values.yaml

Instead of hardcoding values in Kubernetes YAML, we'll put configurable values into:

```text
values.yaml
```

For example:

```yaml
namespace: streaming

images:
  auth: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-auth:1.0.0
  streaming: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-stream:1.0.0
  admin: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-admin:1.0.0
  chat: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-chat:1.0.0
  frontend: 939365917679.dkr.ecr.us-east-1.amazonaws.com/streaming-frontend:1.0.0

replicas:
  auth: 2
  streaming: 2
  admin: 2
  chat: 2
  frontend: 2

mongo:
  image: mongo:latest
  port: 27017
  database: streamingapp

services:
  auth:
    port: 3001
  streaming:
    port: 3002
  admin:
    port: 3003
  chat:
    port: 3004
  frontend:
    port: 80
```

The assignment's example also uses configurable image tags, replicas, ports, MongoDB storage and Ingress host.

---

The step-19 is shown in below pictures

<img width="1917" height="932" alt="image" src="https://github.com/user-attachments/assets/6cda1f31-01d7-4fb6-b2e8-60619b9d2d27" />


# STEP 20 — Create Ingress

The assignment wants one external host with different paths:

```text
/              → frontend
/api/auth      → auth
/api/streaming → streaming
/api/admin     → admin
/api/chat      → chat
```

Architecture:

```text
                 Ingress
                    │
             streamingapp.local
                    │
        ┌───────────┼───────────┐
        │           │           │
        ▼           ▼           ▼
        /        /api/auth    /api/chat
        │           │           │
    frontend       auth        chat
```

---

# STEP 21 — Install Helm Chart

First validate:

```bash
helm lint ./streamingapp
```

Then:

```bash
helm install streamingapp ./streamingapp \
  --namespace streaming \
  --create-namespace
```

The assignment specifies `helm install` as the deployment mechanism.

Check:

```bash
helm list -n streaming
```

---

# STEP 22 — Verify Kubernetes

Run:

```bash
kubectl get pods -n streaming
```

Then:

```bash
kubectl get svc -n streaming
```

Then:

```bash
kubectl get ingress -n streaming
```

And:

```bash
kubectl get all -n streaming
```

You want to see the application components running and ready.

---

# STEP 23 — Demonstrate Scaling

This is one of the important graded requirements.

For example:

```bash
kubectl scale deployment streaming \
  --replicas=4 \
  -n streaming
```

Then:

```bash
kubectl get pods -n streaming
```

You should see the number of replicas increase.

The assignment explicitly gives the example of scaling the streaming Deployment to four replicas.

---

# STEP 24 — Demonstrate Rolling Update

Change an image version:

```bash
helm upgrade streamingapp ./streamingapp \
  --namespace streaming \
  --set services.auth.tag=1.0.1
```

Then:

```bash
kubectl rollout status deployment/auth \
  -n streaming
```

The assignment requires a rolling update with:

```yaml
maxUnavailable: 0
maxSurge: 1
```

so that the update is performed without downtime.

---

# STEP 25 — Test Self-Healing

Find a pod:

```bash
kubectl get pods -n streaming
```

Delete one:

```bash
kubectl delete pod POD_NAME -n streaming
```

Then:

```bash
kubectl get pods -n streaming -w
```

Kubernetes should automatically create a replacement pod.

The assignment specifically requires demonstrating that deleting a pod causes the Deployment to self-heal without user impact.

---

# STEP 26 — Perform Application Smoke Tests

You need to demonstrate:

### 1. Login

```text
Register
   ↓
Login
   ↓
JWT received
```

### 2. Upload

```text
Admin
  ↓
Upload video
  ↓
Upload thumbnail
```

### 3. Playback

```text
Catalogue
   ↓
Select video
   ↓
Video streams
```

### 4. Chat

Open two browser tabs:

```text
Browser Tab 1
      │
      ▼
    Chat
      │
      ▼
Browser Tab 2
```

Message sent from one tab should appear in the other.

These are explicitly listed as smoke tests in the assignment.

---

# STEP 27 — Jenkins CI Pipeline

Now implement the CI portion of the main graded project.

The assignment requires Jenkins to build each Docker image and push it to ECR, with automatic triggering on new Git commits.

Pipeline:

```text
Developer
    │
    ▼
GitHub commit
    │
    ▼
Jenkins
    │
    ├── Checkout
    │
    ├── Build auth
    ├── Build streaming
    ├── Build admin
    ├── Build chat
    ├── Build frontend
    │
    ├── Login ECR
    │
    └── Push images
             │
             ▼
            ECR
```

We'll create:

```text
Jenkinsfile
```

in your GitHub repository.

---

# STEP 28 — CloudWatch Monitoring

The main graded project requires Amazon CloudWatch to collect metrics and configure alarms.

We'll configure monitoring for things such as:

```text
CPU
Memory
Pod/cluster health
Application metrics
```

and create appropriate alarms.

---

# STEP 29 — Centralized Logging

The project also requires centralized application logging using CloudWatch Logs or a comparable centralized logging solution.

The flow becomes:

```text
Application
    │
    ▼
Container logs
    │
    ▼
Kubernetes / AWS logging
    │
    ▼
CloudWatch Logs
```

---

# STEP 30 — Documentation

Create:

```text
README.md
```

It should contain:

```text
1. Project overview
2. Architecture
3. Prerequisites
4. AWS configuration
5. Docker build
6. ECR setup
7. EKS setup
8. Helm installation
9. Ingress setup
10. Scaling
11. Rolling update
12. Smoke testing
13. Monitoring
14. Logging
15. Troubleshooting
16. Cleanup
```

The project explicitly requires architecture diagrams, deployment steps, configuration details and scripts to be documented in GitHub.

---

# STEP 31 — Take Screenshots

For your submission, capture at least:

### EKS

```bash
kubectl get nodes
```

### Pods

```bash
kubectl get pods -n streaming
```

### Services

```bash
kubectl get svc -n streaming
```

### Ingress

```bash
kubectl get ingress -n streaming
```

### Scaling

```bash
kubectl get pods -n streaming
```

before and after scaling.

### Rolling Update

```bash
kubectl rollout status deployment/auth -n streaming
```

### Application

Screenshots showing:

```text
Login
Upload
Video playback
Chat in two tabs
```

The detailed assignment specifically asks for screenshots/short clips showing Kubernetes resources and the running application's login, upload and chat functionality.

---

# STEP 32 — Push Everything to GitHub

Finally:

```bash
git add .
```

```bash
git commit -m "Complete orchestration and scaling project"
```

```bash
git push origin main
```

Your repository should contain:

```text
StreamingApp/
├── backend/
├── frontend/
├── streamingapp/
├── Jenkinsfile
├── README.md
├── architecture.png
└── screenshots/
```

---

# STEP 33 — Final Validation Checklist

Before submission, verify:

```text
☑ 5 Docker images built
☑ 5 images pushed to ECR
☑ EKS cluster running
☑ 5 application Deployments
☑ Services configured
☑ ConfigMap configured
☑ Secret configured
☑ MongoDB running with storage
☑ Readiness probes
☑ Liveness probes
☑ Helm chart working
☑ Ingress working
☑ Frontend accessible
☑ Login working
☑ Upload working
☑ Video playback working
☑ Chat working
☑ Scaling demonstrated
☑ Rolling update demonstrated
☑ Pod self-healing demonstrated
☑ Jenkins pipeline working
☑ CloudWatch monitoring
☑ Centralized logging
☑ README completed
☑ Architecture diagram
☑ GitHub repository updated
```

The detailed rubric allocates points across containerization, Kubernetes manifests, Helm, Ingress, scaling/updates and verification, with containerization/Kubernetes/Helm each carrying 20% and the remaining areas covering Ingress, scaling/updates and smoke testing.

---

# ⭐ Let's Do It Practically

Since this is a hands-on project, **don't try to execute all 33 steps at once**.

We'll do it in this order:

**Part 1:** GitHub + inspect application
**Part 2:** Dockerize/build the 5 services
**Part 3:** ECR
**Part 4:** EKS
**Part 5:** Kubernetes YAML
**Part 6:** MongoDB + ConfigMap + Secret
**Part 7:** Helm
**Part 8:** Ingress
**Part 9:** Scaling + rolling update + self-healing
**Part 10:** Jenkins CI → ECR
**Part 11:** CloudWatch
**Part 12:** Documentation + screenshots + final GitHub submission

**Start with Part 1.** Once you have cloned/forked the `StreamingApp` repository, we can inspect its actual folder structure and use the exact commands and files for Part 1 and Part 2.

```
```

