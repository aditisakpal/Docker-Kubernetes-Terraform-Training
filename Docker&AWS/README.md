# DOCKER 
## Web Scraping in Jupyter with Docker + SQLite

This project scrapes data from the web using Python (`requests` + `BeautifulSoup`), stores it in a local SQLite database, and runs inside a Docker container with Jupyter Notebook.

---

##  Project Structure

```
py-docker/
├── Dockerfile
├── requirements.txt
├── scraping.ipynb
└── scraped_data.db      ← Created automatically when you run the notebook
```

---

##  Getting Started

### 1. Clone or Download the Repo

```bash
git clone https://github.com/your-username/py-docker.git
cd py-docker
```

Or just download the folder directly.

---

### 2. Build the Docker Image

Make sure Docker Desktop is running.

```bash
docker build -t py-jupyter-notebook .
```

---

### 3. Run the Container and Open Jupyter Notebook

#### On Windows (CMD):

```bash
docker run -p 8888:8888 -v "%cd%":/home/jovyan/work py-jupyter-notebook
```


---

##  What the Notebook Does

- Sends HTTP requests to a website
- Parses HTML with BeautifulSoup
- Extracts relevant data (titles, links, etc.)
- Saves the data into an SQLite database: `scraped_data.db`

---

##  Tech Stack

- Python 3
- Jupyter Notebook
- SQLite (via Python’s built-in `sqlite3`)
- Docker
- Libraries:
  - `requests`
  - `beautifulsoup4`

---

##  Database

The database is stored as `scraped_data.db` in the working directory and is created automatically by the notebook if it doesn’t exist.

You can open the `.db` file using any SQLite viewer or directly in Python.

---

##  Notes

- No need to install `sqlite3` in `requirements.txt` – it comes with Python.
- `re` is also built-in, so don’t include it in the requirements file.
- Only install what’s needed via pip (e.g. `requests`, `beautifulsoup4`).

---
# MiniKube 

##  Web Scraping with Jupyter Notebook on Minikube

This project scrapes web data using Python and stores it in an SQLite database — all running inside a Dockerized Jupyter Notebook deployed to Kubernetes via **Minikube**.

---

##  Tech Stack

- Python 3
- Jupyter Notebook
- Docker
- Kubernetes (Minikube)
- SQLite
- Libraries:
  - `requests`
  - `beautifulsoup4`

---

##  Folder Structure

```
py-docker/
├── Dockerfile
├── requirements.txt
├── scraping.ipynb
├── scraped_data.db       ← Created at runtime
├── jupyter-deployment.yaml
└── jupyter-service.yaml
```

---

##  How to Run on Minikube

### 1. Start Minikube

```bash
minikube start
```

If you're on Windows, make sure Docker is your driver:

```bash
minikube start --driver=docker
```

---

### 2. Enable Minikube's Docker Env

This lets you use your local Docker daemon **inside Minikube**:

```bash
eval $(minikube docker-env)
```

(Use `minikube docker-env --shell=cmd` if you’re on Windows CMD)

---

### 3. Build the Docker Image Inside Minikube

```bash
docker build -t py-jupyter-notebook .
```

---

### 4. Deploy to Kubernetes

Apply the deployment and service files:

```bash
kubectl apply -f jupyter-deployment.yaml
kubectl apply -f jupyter-service.yaml
```

---

### 5. Access the Jupyter Notebook

Expose the service to your browser:

```bash
minikube service jupyter-service
```

This will open Jupyter in your browser via NodePort.

---

##  YAML Files

### `jupyter-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jupyter-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jupyter
  template:
    metadata:
      labels:
        app: jupyter
    spec:
      containers:
      - name: jupyter
        image: py-jupyter-notebook
        ports:
        - containerPort: 8888
        volumeMounts:
        - name: notebook-data
          mountPath: /home/jovyan/work
      volumes:
      - name: notebook-data
        emptyDir: {}
```

### `jupyter-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: jupyter-service
spec:
  type: NodePort
  selector:
    app: jupyter
  ports:
    - protocol: TCP
      port: 8888
      targetPort: 8888
```

---

##  Saving the SQLite DB

> Note: `scraped_data.db` is stored inside the pod. If you want it to persist:
- Mount a host path
- Use a PersistentVolumeClaim
- Or download/export the `.db` file via Jupyter interface

---

##  Extras

-  Built-in support for SQLite (no need to install it!)
-  Fully containerized workflow
-  Use Kubernetes to scale or restart Jupyter as needed

---

##  What the Notebook Does

- Sends HTTP requests to a website
- Parses HTML using BeautifulSoup
- Extracts data like titles/links
- Stores results in `scraped_data.db`

---

# Jupyter Notebook Deployment on AWS EKS

This project sets up a Kubernetes cluster using Amazon EKS and deploys a Jupyter Notebook server to it using a Docker container and kubectl.

## Prerequisites

- AWS CLI configured with appropriate IAM permissions
- eksctl installed
- kubectl installed
- Docker installed
- IAM permissions for EKS, EC2, and IAM roles
- Proper VPC setup or permissions to let eksctl create one

## Required IAM Policies

Ensure the following IAM permissions are attached to your user or role:

```json
{
  "Effect": "Allow",
  "Action": [
    "iam:CreateRole",
    "iam:AttachRolePolicy",
    "iam:PutRolePolicy",
    "iam:PassRole",
    "iam:TagRole",
    "iam:GetRole",
    "iam:ListRoleTags"
  ],
  "Resource": "*"
}
```

## Additional managed AWS policies to attach:
	•	AmazonEKSClusterPolicy
	•	AmazonEKSWorkerNodePolicy
	•	AmazonEKSServicePolicy
	•	AmazonEC2ContainerRegistryFullAccess
	•	AmazonS3ReadOnlyAccess
	•	AWSLambda_FullAccess
	•	AWSLambdaBasicExecutionRole
	•	IAMUserChangePassword

## Steps to Deploy

### 1. Create EKS Cluster

eksctl create cluster --name jupyter-cluster --region us-east-1

If the cluster already exists, delete the old one:

eksctl delete cluster --name jupyter-cluster --region us-east-1

### 2. Configure kubectl to Use the Cluster

aws eks update-kubeconfig --region us-east-1 --name jupyter-cluster

### 3. Verify Cluster Access

kubectl get nodes

### 4. Deploy Jupyter Notebook

kubectl apply -f deployment.yaml


---

## Troubleshooting

### Common Errors
- “AlreadyExistsException”: A cluster or stack with the same name already exists. Use eksctl delete cluster to remove it first.
- “UnauthorizedOperation” or “UnauthorizedTaggingOperation”: Make sure the IAM user has the correct permissions.
- “Connection refused on port 8443”: The cluster is either not ready, or kubectl is not properly configured.

---

## Cleanup

To delete the EKS cluster and associated resources:

eksctl delete cluster --name jupyter-cluster --region us-east-1






