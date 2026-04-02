# 📘 Setup Steps – EKS Datadog Monitoring Project

## 🎯 Objective

To set up monitoring and log collection for a Kubernetes application on AWS EKS using Datadog.

---

## ☁️ Step 1: Create EKS Cluster (Manual Setup)

* Created EKS cluster manually from AWS Console
* Configured node group with EC2 instances
* Verified cluster status and nodes were in **Ready state**

```bash
kubectl get nodes
```

---

## 🔗 Step 2: Connect EC2 to EKS Cluster

* Installed AWS CLI
* Configured credentials using `aws configure`
* Updated kubeconfig

```bash
aws eks update-kubeconfig --name <cluster-name>
kubectl get nodes
```

---

## ⚙️ Step 3: Install kubectl

* Downloaded kubectl binary
* Made it executable
* Verified installation

```bash
kubectl version --client
```

---

## 📦 Step 4: Install Helm

* Installed Helm for Kubernetes package management

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
helm version
```

---

## 📁 Step 5: Create Datadog Namespace

```bash
kubectl create namespace datadog
```

---

## 🔐 Step 6: Create Datadog Secrets

* Created API key and Application key from Datadog dashboard

```bash
kubectl create secret generic datadog-secret \
  --from-literal=api-key=<YOUR_API_KEY> \
  -n datadog

kubectl create secret generic datadog-app-secret \
  --from-literal=app-key=<YOUR_APP_KEY> \
  -n datadog
```

---

## 📄 Step 7: Configure Datadog Values

Created `datadog-values.yaml` with:

* API key and App key references
* Log collection enabled
* Container logs enabled
* Cluster name defined

---

## 🚀 Step 8: Install Datadog Using Helm

```bash
helm repo add datadog https://helm.datadoghq.com
helm repo update

helm upgrade --install datadog-agent datadog/datadog \
  -f datadog-values.yaml \
  -n datadog
```

---

## 🔄 Step 9: Restart Datadog Agents

```bash
kubectl rollout restart daemonset datadog-agent -n datadog
kubectl rollout restart deployment datadog-agent-cluster-agent -n datadog
```

---

## ✅ Step 10: Verify Datadog Installation

```bash
kubectl get pods -n datadog
```

* Verified Datadog agent pods are running

---

## 🌐 Step 11: Deploy NGINX Application

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
```

---

## 📄 Step 12: Convert Deployment to YAML (Best Practice)

```bash
kubectl get deployment nginx -o yaml > nginx-deployment.yaml
kubectl get svc nginx -o yaml > nginx-service.yaml
```

* Cleaned YAML files by removing:

  * status
  * resourceVersion
  * uid
  * creationTimestamp

---

## 🔍 Step 13: Generate Application Traffic

Used port-forward to access service:

```bash
kubectl port-forward svc/nginx 9090:80
```

Then generated requests:

```bash
curl http://localhost:9090
curl http://localhost:9090/test
curl http://localhost:9090/abc
```

---

## 📊 Step 14: Verify Logs in Datadog

* Navigated to Datadog Logs Explorer
* Filtered logs using `service:nginx`

Observed:

* GET / → 200 OK
* GET /test → 404
* GET /abc → 404

---

## ⚠️ Challenges Faced & Fixes

| Issue                   | Fix                     |
| ----------------------- | ----------------------- |
| Helm repo not found     | Added Datadog Helm repo |
| Secret command errors   | Fixed syntax            |
| NodePort not accessible | Used port-forward       |
| Logs not visible        | Generated traffic       |

---

## 🎯 Final Outcome

* Successfully integrated Datadog with EKS
* Monitored Kubernetes cluster and pods
* Collected and analyzed NGINX logs
* Implemented real-world DevOps monitoring setup

---

## 🧠 Key Learnings

* Kubernetes observability
* Helm deployments
* Monitoring tools (Datadog)
* Debugging production issues
* Log analysis in distributed systems

