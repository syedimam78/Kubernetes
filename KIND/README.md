

## 🍎 **macOS – Kind 2-Node Cluster Setup**

### 🧰 Prerequisites

* Docker Desktop installed and running
* Homebrew installed

### 🚀 Commands

```bash
# Step 1: Install Kind and kubectl
brew install kind kubectl

# Step 2: Verify installation
kind version
kubectl version --client

# Step 3: Create Kind config file manually
vi kind-2node.yaml
```

### 📝 Inside `vi` (press `i` to insert)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
```

➡️ Press `Esc`, then type `:wq` and hit `Enter` to save and exit.

---

### Continue:

```bash
# Step 4: Create the cluster
kind create cluster --name kind-2node --config kind-2node.yaml

# Step 5: Verify cluster is running
kubectl cluster-info
kubectl get nodes
```

### 🧹 Delete cluster

```bash
kind delete cluster --name kind-2node
```

---

## 🪟 **Windows (WSL2 – Ubuntu or Debian)**

### 🧰 Prerequisites

* Docker Desktop (with WSL integration)
* Working WSL2 terminal (e.g., Ubuntu)

### 🚀 Commands

```bash
# Step 1: Update system
sudo apt update && sudo apt install -y curl apt-transport-https ca-certificates

# Step 2: Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Step 3: Install kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Step 4: Verify installation
kind version
kubectl version --client

# Step 5: Create Kind config file
vi kind-2node.yaml
```

### 📝 Inside `vi` (press `i`)

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
```

➡️ Then `Esc`, type `:wq`, and hit `Enter`.

---

### Continue:

```bash
# Step 6: Create the cluster
kind create cluster --name kind-2node --config kind-2node.yaml

# Step 7: Verify
kubectl cluster-info
kubectl get nodes
```

### 🧹 Delete

```bash
kind delete cluster --name kind-2node
```

---

Would you like me to add **Ingress (NGINX) setup** next — so you can expose services from this Kind cluster (it’s optional but very useful for labs)?
