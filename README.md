# End-to-End Deployment Guide: Microservices E-Commerce on AWS EKS

## ✅ Step 1: Clone the GitHub Repository

1. Open **VS Code**.
2. Open the terminal in VS Code.
3. Clone the project:

```bash
git clone https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git
```

---

## 🔐 Step 2: Configure AWS Keys

Make sure you have your AWS credentials configured. Run:

```bash
aws configure
```

Enter your:
- Access Key ID
- Secret Access Key
- Region (like `us-east-1`)
- Output format (leave it as `json`)

---

## 📁 Step 3: Navigate into the Project

```bash
ls
cd Microservices-E-Commerce-eks-project
ls
```

---

## ☁️ Step 4: Create S3 Buckets for Terraform State

These buckets will store `terraform.tfstate` files.

```bash
cd s3-buckets/
ls
terraform init
terraform plan
terraform apply -auto-approve
```

---

## 🌐 Step 5: Create Network 

1. Navigate to Terraform EC2 folder:

```bash
cd ../terraform_main_ec2
```

2. Run Terraform:

```bash
terraform init
terraform plan
terraform apply -auto-approve
```
3. example output :
```bash
Apply complete! Resources: 24 added, 0 changed, 0 destroyed.

Outputs:

jumphost_public_ip = "18.208.229.108"
region = "us-east-1"
```
4. The command terraform state list is used to list all resources tracked in your current Terraform state file.
```bash
terraform state list
```
output :
```bash
$ terraform state list
aws_iam_instance_profile.instance-profile
aws_iam_policy.eks_policy
aws_iam_role.iam-role
aws_iam_role_policy_attachment.cloudformation_full_access
aws_iam_role_policy_attachment.ec2_full_access
aws_iam_role_policy_attachment.eks_cluster_policy
aws_iam_role_policy_attachment.eks_policy_attachment
aws_iam_role_policy_attachment.eks_worker_node_policy
aws_iam_role_policy_attachment.iam-policy
aws_iam_role_policy_attachment.iam_full_access
aws_instance.ec2
aws_internet_gateway.igw
aws_route_table.private_rt
aws_route_table.rt
aws_route_table_association.private_rt_association1
aws_route_table_association.private_rt_association2
aws_route_table_association.rt-association
aws_route_table_association.rt-association2
aws_security_group.security-group
aws_subnet.private-subnet1
aws_subnet.private-subnet2
aws_subnet.public-subnet1
aws_subnet.public-subnet2
aws_vpc.vpc
```
---

## 💻 Step 6: Connect to EC2 and Access Jenkins

1. Go to **AWS Console** → **EC2**
2. Click your instance → Connect
3. Once connected, switch to root:

```bash
sudo -i
```

4. DevOps Tool Installation Check & Version Report

```bash
  [Git]="git --version"
  [Java]="java -version"
  [Jenkins]="jenkins --version"
  [Terraform]="terraform -version"
  [Maven]="mvn -v"
  [kubectl]="kubectl version --client --short"
  [eksctl]="eksctl version"
  [Helm]="helm version --short"
  [Docker]="docker --version"
  [Trivy]="trivy --version"
  [SonarQube]="docker ps | grep sonar"
  [Grafana]="kubectl get pods -A | grep grafana"
  [Prometheus]="kubectl get pods -A | grep prometheus"
  [AWS_CLI]="aws --version"
  [MariaDB]="mysql --version"
```

5. Get the initial Jenkins admin password:

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```
- example output :
``` bash
0c39f23132004d508132ae3e0a7c70e4
```

Copy that password!

---

## 🌐 Step 7: Jenkins Setup in Browser

1. Open browser and go to:

```
http://<EC2 Public IP>:8080
```

2. Paste the password from last step.
3. Click **Install suggested plugins**
4. Create first user:

| Field     | Value       |
|-----------|-------------|
| Username  | yaswanth    |
| Password  | yaswanth    |
| Full Name | yaswanth    |
| Email     | yash@example.com |

Click through: **Save and Continue → Save and Finish → Start using Jenkins**

---
## 🔐 Step 8: it is a (Optional) 
## 🔐 Step 8: Add AWS Credentials in Jenkins

1. In Jenkins Dashboard → **Manage Jenkins**
2. Go to: **Credentials → System → Global Credentials (unrestricted)**
3. Click **Add Credentials**

### Add Access Key:
- Kind: Secret Text
- Secret: _your AWS Access Key_
- ID: `accesskey`
- Description: AWS Access Key

### Add Secret Key:
- Kind: Secret Text
- Secret: _your AWS Secret Key_
- ID: `secretkey`
- Description: AWS Secret Key

Click **Save** for both.

---

## 🔌 Step 9: Install Jenkins Plugin

1. Jenkins Dashboard → **Manage Jenkins**
2. Go to: **Plugins**
3. Click **Available plugins**
4. Search for:
   - `pipeline: stage view`
   - `Docker`
   - `Docker Pipeline`
   - `Kubernetes`
   - `Kubernetes CLI`
5. Install it


---

## 🛠️ Step 10: Create a Jenkins Pipeline Job (Create EKS Cluster)

1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `eks-terraform`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `eks-terraform/eks-jenkinsfile`
   - Apply
   - Save
6. click **Build with Parameters**
   - ACTION :
    - Select Terraform action : `apply`
    - **Build** 

- To verify your EKS cluster, connect to your EC2 jumphost server and run:
```bash
aws eks --region us-east-1 update-kubeconfig --name project-eks
kubectl get nodes
```
---

## 🛠️ Step 11: Create a Jenkins Pipeline Job (Create Elastic Container Registry (ecr))

1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `eks-terraform`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `ecr-terraform/ecr-jenkinsfile`
   - Apply
   - Save
6. click **Build with Parameters**
   - ACTION :
    - Select Terraform action : `apply`
    - **Build** 

7. To verify your EKS cluster, connect to your EC2 jumphost server and run:
```bash
aws ecr describe-repositories --region us-east-1
```

8. ✅ Verify Amazon ECR Repositories in AWS Console (us-east-1)
This guide shows how to verify if your ECR repositories exist using the AWS Console UI.

#### 🔹 Navigation Path

**Amazon ECR → Private registry → Repositories**

#### 🛠 Prerequisites

- AWS Console access
- IAM permissions to view Amazon ECR
- Repositories to verify:
  - `emailservice`
  - `checkoutservice`
  - `recommendationservice`
  - `frontend`
  - `paymentservice`
  - `productcatalogservice`
  - `cartservice`
  - `loadgenerator`
  - `currencyservice`
  - `shippingservice`
  - `adservice`

#### 📘 Step-by-Step Instructions

##### 1. Log in to AWS Console  
🔗 [https://us-east-1.console.aws.amazon.com/](https://us-east-1.console.aws.amazon.com/)

##### 2. Go to Elastic Container Registry  
- In the top search bar, type: `ECR`
- Click on **Elastic Container Registry**

##### 3. Navigate to Repositories  
- In the left sidebar, click:  
  **Private registry → Repositories**  
- Or go directly here:  
  🔗 [https://us-east-1.console.aws.amazon.com/ecr/repositories](https://us-east-1.console.aws.amazon.com/ecr/repositories)

##### 4. Verify Repositories  
- Use the search bar to search each repository name:

---
## Step 11: Create a Jenkins Pipeline Job for Build and Push Docker Images to ECR

## 🔐 Step 11.1: Add GitHub PAT to Jenkins Credentials

1. Navigate to **Jenkins Dashboard** → **Manage Jenkins** → **Credentials** → **(global)** → **Global credentials (unrestricted)**.
2. Click **“Add Credentials”**.
3. In the form:
   - **Kind**: `Secret text`
   - **Secret**: `ghp_HKMTPOKYE2LLGuytsimxnnl5d1f73zh`
   - **ID**: `my-git-pattoken`
   - **Description**: `git credentials`
4. Click **“OK”** to save.

### 🚀 Step 17.2: ⚖️ Jenkins Pipeline Setup: Build and Push and update Docker Images to ECR

### 🚀 Step 17.2.1:  📂 Jenkins Pipeline Setup: emailservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `emailservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/emailservice`
   - Apply
   - Save
6. click **Build**
   
### 🚀 Step 17.2.2:  📂 Jenkins Pipeline Setup: checkoutservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `checkoutservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/checkoutservice`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.3:  📂 Jenkins Pipeline Setup: recommendationservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `recommendationservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/recommendationservice`
   - Apply
   - Save
6. click **Build**


### 🚀 Step 17.2.4:  📂 Jenkins Pipeline Setup: frontend
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `frontend`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/frontend`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.5:  📂 Jenkins Pipeline Setup: paymentservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `paymentservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/paymentservice`
   - Apply
   - Save
6. click **Build**



### 🚀 Step 17.2.6:  📂 Jenkins Pipeline Setup: productcatalogservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `productcatalogservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/productcatalogservice`
   - Apply
   - Save
6. click **Build**


### 🚀 Step 17.2.7:  📂 Jenkins Pipeline Setup: cartservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `cartservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/cartservice`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.8:  📂 Jenkins Pipeline Setup: loadgenerator
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `loadgenerator`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/loadgenerator`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.9:  📂 Jenkins Pipeline Setup: currencyservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `currencyservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/currencyservice`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.10:  📂 Jenkins Pipeline Setup: shippingservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `shippingservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/shippingservice`
   - Apply
   - Save
6. click **Build**

### 🚀 Step 17.2.11:  📂 Jenkins Pipeline Setup: adservice
1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `adservice`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `jenkinsfiles/adservice`
   - Apply
   - Save
6. click **Build**

---

### 16.6: 🚀 Expose ArgoCD Server Using LoadBalancer

### 16.6.1: Edit the ArgoCD Server Service

```bash
kubectl edit svc argocd-server -n argocd
```

### 16.6.2: Change the Service Type

Find this line:

```yaml
type: ClusterIP
```

Change it to:

```yaml
type: LoadBalancer
```

Save and exit (`:wq` for `vi`).

### 16.6.3: Get the External Load Balancer DNS

```bash
kubectl get svc argocd-server -n argocd
```

Sample output:

```bash
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP                           PORT(S)                          AGE
argocd-server   LoadBalancer   172.20.1.100   a1b2c3d4e5f6.elb.amazonaws.com        80:31234/TCP,443:31356/TCP       2m
```

### 16.6.4: Access the ArgoCD UI

Use the DNS:

```bash
https://<EXTERNAL-IP>.amazonaws.com
```

---

### 16.7: 🔐 Get the Initial ArgoCD Admin Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

### Login Details:

* **Username:** `admin`
* **Password:** (The output of the above command)

---

## Step 19:  Deploying with ArgoCD and Configuring Route 53 (Step-by-Step)

### Step 19.1: Create Namespace in EKS (from Jumphost EC2)
Run these commands on your jumphost EC2 server:
```bash
kubectl create namespace dev
kubectl get namespaces
```

### Step 19.2: Create New Applicatio with ArgoCD
1. Open the **ArgoCD UI** in your browser.
2. Click **+ NEW APP**.
3. Fill in the following:
   - **Application Name:** `project`
   - **Project Name:** `default`
   - **Sync Policy:** `Automatic`
   - **Repository URL:** `https://github.com/arumullayaswanth/Fullstack-nodejs-aws-eks-project.git`
   - **Revision:** `HEAD`
   - **Path:** `kubernetes-files`
   - **Cluster URL:** `https://kubernetes.default.svc`
   - **Namespace:** `dev`
4. Click **Create**.


## Step 17: Create a Jenkins Pipeline Job for Backend and frondend & Route 53 Setup

### Prerequisites
1. Go to AWS Route 53
2. Create a Hosted Zone:
   - Domain: `aluru.site`
   - Type: Public Hosted Zone
3. Update Hostinger Nameservers:
   - Paste the 4 NS records from Route 53 into Hostinger:
     - ns-865.awsdns-84.net
     - ns-1995.awsdns-97.co.uk
     - ns-1418.awsdns-59.org
     - ns-265.awsdns-73.com

### Step 19.3: Configure Route 53 for Backend
1. In ArgoCD UI, open your `project` application.
2. Click on **backend** and copy the hostname (e.g.,
   `acfb06fba08834577a50e43724d328e3-1568967602.us-east-1.elb.amazonaws.com`).
3. Go to **AWS Route 53** > **Hosted zones** > open your hosted zone (e.g., `aluru.site`).
4. Click **Create record** and fill in:
   - **Record type:** `A – Routes traffic to an IPv4 address and some AWS resources`
   - **Alias:** `Yes`
   - **Alias target:** Choose Application and Classic Load Balancer
   - **Region:** `US East (N. Virginia)`
   - **Alias target value:** Paste the backend load balancer DNS (from step 2)
5. Click **Create record**.

### 6. 🔐 Enable HTTPS with ACM and Route 53 (Step-by-Step)

#### 6.1: Request a Public Certificate in ACM
1. Go to **AWS Certificate Manager (ACM)** in the AWS Console.
2. Click **Request a certificate**.
3. Select **Request a public certificate** and click **Next**.
4. Enter your domain name: `aluru.site` (and optionally `www.aluru.site`).
5. Choose **DNS validation (recommended)**.
6. Click **Request**.

#### 6.22: Validate Domain in Route 53
1. In ACM, go to **Certificates** and select your new certificate.
2. Under the domain, click **Create DNS record in Amazon Route 53**.
3. Select your hosted zone: `aluru.site`.
4. Click **Create record**.
5. Wait a few minutes for validation to complete (Status: **Issued**).

#### 6.3: Add HTTPS Listener to Load Balancer
1. Go to **EC2 Console** → **Load Balancers** → select your frontend ALB (e.g., `frontend-alb`).
2. Go to the **Listeners** tab.
3. Click **Add listener**:
   - **Protocol:** HTTPS
   - **Port:** 443
   - **Action:** Forward to your web target group
   - **Security policy:** ELBSecurityPolicy-2021-06 (or latest)
   - **Select ACM Certificate:** Choose the one for `aluru.site`
4. Click **Add**.

#### 6.4: Access Your Application
1. Open your browser and go to: https://aluru.site
2. Your application should now be accessible over HTTPS!


   
