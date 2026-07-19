# ArgoCD HTTPS Hosting on EKS

1. AWS CLI INSTALLED

   ```bash
   # To install the AWS CLI, run the following commands.
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   ```

2. **eksctl** installed

   ```bash
   # Linux/WSL
   curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   ```

   Check installation:

   ```bash
   eksctl version
   ```

3. **kubectl** installed

   ```bash
   kubectl version
   ```

   [Install Guide](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

4. **Helm** installed

   ```bash
   helm version
   ```

   [Install Guide](https://helm.sh/docs/intro/install/)  

## Step-by-Step Setup

### Step 1: Create EKS Cluster

```bash
# Create EKS Cluster without node group
eksctl create cluster --name argocd-cluster --region us-east-1 --without-nodegroup
```

### Step 2: Verify Cluster Creation

```bash
eksctl get clusters --region us-east-1
```

### Step 3: Associate IAM OIDC Provider

```bash
eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=argocd-cluster --approve
```

### Step 4: Create Node Group

```bash
eksctl create nodegroup \
--cluster=argocd-cluster \
--region=us-east-1 \
--name=argocd-ng \
--node-type=t3.medium \
--nodes=2 \
--nodes-min=1 \
--nodes-max=3 \
--node-volume-size=20 \
--managed
```

### Step 5: Verify Cluster Access

1. Update kubeconfig

    ```bash
    aws eks update-kubeconfig --region us-east-1 --name argocd-cluster
    ```

2. Verify nodes

    ```bash
    kubectl get nodes
    ```

### Step 6: Install ArgoCD

1. Create namespace

    ```bash
    kubectl create namespace argocd
    ```



