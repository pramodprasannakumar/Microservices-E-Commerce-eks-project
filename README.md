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

## 🔌 Step 9: Install Required Jenkins Plugins

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Plugins**.
2. Click the **Available** tab.
3. Search and install the following:
   - ✅ **Pipeline: stage view**
   - ✅ **Eclipse Temurin installer**
   - ✅ **SonarQube Scanner**
   - ✅ **Maven Integration**
   - ✅ **NodeJS**
   - ✅ **Docker**
   - ✅ **Docker Commons**
   - ✅ **Docker pipeline**
   - ✅ **Docker API**
   - ✅ **Docker-build-step**
   - ✅ **Amazon ECR**
   - ✅ **Kubernetes Client API**
   - ✅ **Kubernetes**
   - ✅ **Kubernetes Cerdentials**
   - ✅ **Kubernetes CLI**
   - ✅ **Kubernetes Cerdentials Provider**
   - ✅ **Config File Provider**
   - ✅ **OWASP Dependency-check**
   - ✅ **Email Extension Template**
   - ✅ **Prometheus metrics**
4. when installation is compete:
   - ✅ **Restart jenkins when installation is complete and no job are running**

---
## 🌐 Step 10: SonarQube Setup in Browser

1. Open browser and go to:

```bash
http://<EC2 Public IP>:9000
```
Log in with:
      - Username: `admin`
      - Password: `admin` (change after first login)
2. Update your password
  - **Old Password**: `admin`    
  - **New Password**: `yaswanth`
  - **Confirm Password**:`yaswanth`   
  - **update**
## 🧪 SonarQube & Jenkins Integration 
This guide will walk you through integrating **SonarQube** with **Jenkins** for static code analysis, from setting up the project in SonarQube to configuring Jenkins plugins and credentials.

## 🔹 Step 10.1: Create a Project in SonarQube

1. Open **SonarQube Dashboard** in your browser (`http://localhost:9000`).
2. Click **Projects** → **Manually** → **Create Project**.
3. Fill in:
   - **Project Display Name**: `flipkart`
   - **Project Key**: (automatically generated)
   - **Main Branch Name**:`master` //Your Git default branch → `master` So, in SonarQube → set Main branch name to:`master`
4. Click **setup**.
5. Under **Setup your project for Clean as You Code**, choose:
   - **What should be the baseline for new code?** → Select **Use the Getting Started tutorial**
6. Click **Create Project**.

## 🔹 Step 10.2: Generate a Token for Your Project
0. open flipkart project
1. Click **Locally** → **Analyze your project**.
2. When prompted for a token:
   - Click **Generate a project token**.
   - **Token name**: (enter a name, e.g., `flipkart-token`)
   - **Expires in**: `No expiration`
   -  **swiggy-token:**`sqp_cc249cfe9a25cb61880787076049fc7d56310005`
3. Click **Generate**.
4. **Copy the token** – you'll need it in Jenkins.

## Step 11: Add SonarQube Token as Jenkins Credential

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Credentials**.
2. Click **System** → **Global credentials (unrestricted)**.
3. Click **Add Credentials**.
4. Fill in:
   - **Kind**: `Secret text`
   - **Secret**: *(paste your SonarQube token)*
   - **ID**: `sonarqube`
   - **Description**: `sonarqube token`
5. Click **Create**.

---
## Step 12: Configure SonarQube Server in Jenkins

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Configure System**.
2. Scroll down to the **SonarQube servers** section.
3. select Environment variables
4. Click **Add SonarQube** and fill:
   - **Name**: `sonarqube`
   - **Server URL**: `http://localhost:9000` *(or your actual Sonar IP)*
   - **Server Authentication Token**: Select `sonarqube` (from credentials)
5. ✅ Check **Environment variables injection**.
6. Click **Save**.

## Step 13: Configure Webhook in SonarQube

1. Go to **SonarQube Dashboard** → **Administration**
2. Under **Configuration**, click **Webhooks**
3. Click **Create**
4. Fill:
   - **Name**: `jenkins`
   - **Server URL**: `http://localhost:8080/sonarqube-webhook/` *(or your actual jenkins IP)*
5. Click **Create**

This allows SonarQube to notify Jenkins after analysis is complete.

---

## Step 14: Configure Tools

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Tool**.

2. Scroll to **JDK installations** section:
   - Click **Add JDK**
   - **Name**: `jdk`
   - ✅ Check **Install automatically**
     - Add Installer
       - select `install from adoptium.net`
          - version : `jdk-17.0.8.1+1

3. Scroll to **SonarQube Scanner** section:
   - Click **Add SonarQube Scanner**
   - **Name**: `sonarqube-scanner`
   - ✅ Check **Install automatically**
     - version : `SonarQube Scanner 7.0.1.4817` 
      - it is least version

4. Scroll to **NodeJS installations** section:
   - Click **Add NodeJs**
   - **Name**: `nodejs`
   - ✅ Check **Install automatically**
       - version : `Nodejs 23.7.0` 
          - it is least version

5. Scroll to **Dependency-check installation** section:
   - Click **Add Dependency-check**
   - **Name**: `DP-check`
   - ✅ Check **Install automatically**
     - Add Installer
       - select `install from github.com`
          - version : `dependency-check-12.0.2`
             - it is least version

6. Scroll to **Docker** section:
   - Click **Add Docker**
   - **Name**: `Docker`
   - ✅ Check **Install automatically**
     - Add Installer
       - select `Download from docker.com`
          - version : `least`
             - it is least version

7. Scroll to **Maven** section:
   - Click **Add Maven**
   - **Name**: `maven`
   - ✅ Check **Install automatically**

4. Click **Save**.

---

## 🔹 Step 15: 📧 Jenkins Email Notification Setup with Gmail 
Follow these steps to set up **email notifications in Jenkins using your Gmail account**.


#### 🔐 Step 15.1: Enable 2-Step Verification & App Password in Gmail

1. Go to **[Gmail](https://mail.google.com)**.
2. In the top-right, click **Manage your Google Account**.
3. In the left sidebar, click **Security**.
4. Under **Signing in to Google**, check if **2-Step Verification** is enabled.
   - If **not**, turn it **ON** and complete the setup.
5. In the top Google search bar, type: `App Passwords`
6. Generate an app password:
   - **App Name**: `jenkins`
   - Click **Generate**
   - 🔑 **Copy the generated password**


#### 🔧 Step 15.2: Add Gmail Credentials in Jenkins

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Credentials**
2. Click **System** → **Global credentials (unrestricted)**
3. Click **Add Credentials**
4. Fill the form:
   - **Kind**: `Username with password`
   - **Username**: `yaswanth.arumulla@gmail.com`
   - **Password**: *(paste the app password)*
   - **ID**: `email`
   - **Description**: `email`
5. Click **Create**

#### ⚙️ Step 15.3: Configure Email Settings in Jenkins

1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Configure System**
2. Scroll down to **Extended E-mail Notification**
   - **SMTP Server**: `smtp.gmail.com`
   - **SMTP Port**: `465`
   - Click **Advanced**
   - **Credentials**: Select the `email` credential
   - ✅ **Use SSL**
   - ✅ **Use OAuth 2.0**
   - **Default Content Type**: `html (text/html)`

3. Scroll down to **E-mail Notification**
   - **SMTP Server**: `smtp.gmail.com`
   - Click **Advanced**
     - ✅ **Use SMTP Authentication**
     - **User Name**: `yaswanth.arumulla@gmail.com`
     - **Password**: *(paste app password)*
     - ✅ **Use SSL**
     - **SMTP Port**: `465`
     - **Reply-to Address**: `yaswanth.arumulla@gmail.com`
     - **Charset**: `UTF-8`
     - **Test configuration**:
       - **Test E-mail recipient**: `yaswanth.arumulla@gmail.com`
       - Click **Test Configuration** to verify

#### 🔄 Step 15.4: Set Default Email Triggers in Jenkins

1. Scroll down to **Default Triggers**
2. Click the dropdown and select:
   - ✅ `Always`
   - ✅ `Failure`
   - ✅ `Success`

3. Click **Apply** then **Save**.

#### ✅ Step 15.5: Check Gmail

- Go to your **Gmail inbox** and confirm that a test email has arrived from Jenkins.

- You're now ready to receive Jenkins pipeline notifications via Gmail!

---









---

## 🛠️ Step 16: Create a Jenkins Pipeline Job (Create EKS Cluster)

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

## 🛠️ Step 17: Create a Jenkins Pipeline Job (Create Elastic Container Registry (ecr))

1. Go to Jenkins Dashboard
2. Click **New Item**
3. Name it: `ecr-terraform`
4. Select: **Pipeline**
5. Click **OK**
 - Pipeline:
   - Definition : `Pipeline script from SCM`
   - SCM : `Git`
   - Repositories : `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - Branches to build : `*/master`
   - Script Path : `ecr-terraform/ecr-jenkinfile`
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
## Step 18: Create a Jenkins Pipeline Job for Build and Push Docker Images to ECR

### 🔐 Step 18.1: Add GitHub PAT to Jenkins Credentials

1. Navigate to **Jenkins Dashboard** → **Manage Jenkins** → **Credentials** → **(global)** → **Global credentials (unrestricted)**.
2. Click **“Add Credentials”**.
3. In the form:
   - **Kind**: `Secret text`
   - **Secret**: `ghp_HKMTPOKYE2LLGuytsimxnnl5d1f73zh`
   - **ID**: `my-git-pattoken`
   - **Description**: `git credentials`
4. Click **“OK”** to save.

### 🚀 Step 12.2: ⚖️ Jenkins Pipeline Setup: Build and Push and update Docker Images to ECR

### 🚀 Step 12.2.1:  📂 Jenkins Pipeline Setup: emailservice
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
   
### 🚀 Step 12.2.2:  📂 Jenkins Pipeline Setup: checkoutservice
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

### 🚀 Step 12.2.3:  📂 Jenkins Pipeline Setup: recommendationservice
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


### 🚀 Step 12.2.4:  📂 Jenkins Pipeline Setup: frontend
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

### 🚀 Step 12.2.5:  📂 Jenkins Pipeline Setup: paymentservice
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



### 🚀 Step 12.2.6:  📂 Jenkins Pipeline Setup: productcatalogservice
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


### 🚀 Step 12.2.7:  📂 Jenkins Pipeline Setup: cartservice
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

### 🚀 Step 12.2.8:  📂 Jenkins Pipeline Setup: loadgenerator
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

### 🚀 Step 12.2.9:  📂 Jenkins Pipeline Setup: currencyservice
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

### 🚀 Step 12.2.10:  📂 Jenkins Pipeline Setup: shippingservice
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

### 🚀 Step 12.2.11:  📂 Jenkins Pipeline Setup: adservice
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

---
## 🖥️ step 13 : 🎉 Install ArgoCD in Jumphost EC2

### 13.1: Create Namespace for ArgoCD

```bash
kubectl create namespace argocd
```

### 13.2: Install ArgoCD in the Created Namespace

```bash
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 13.3: Verify the Installation

```bash
kubectl get pods -n argocd
```

Ensure all pods are in `Running` state.

### 13.4: Validate the Cluster

Check your nodes and create a test pod if necessary:

```bash
kubectl get nodes
```

### 13.5: List All ArgoCD Resources

```bash
kubectl get all -n argocd
```

Sample output:

```
NAME                                                    READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                     1/1     Running   0          106m
pod/argocd-applicationset-controller-787bfd9669-4mxq6   1/1     Running   0          106m
pod/argocd-dex-server-bb76f899c-slg7k                   1/1     Running   0          106m
pod/argocd-notifications-controller-5557f7bb5b-84cjr    1/1     Running   0          106m
pod/argocd-redis-b5d6bf5f5-482qq                        1/1     Running   0          106m
pod/argocd-repo-server-56998dcf9c-c75wk                 1/1     Running   0          106m
pod/argocd-server-5985b6cf6f-zzgx8                      1/1     Running   0          106m
```
### 14.6: 🚀 Expose ArgoCD Server Using LoadBalancer

### 14.6.1: Edit the ArgoCD Server Service

```bash
kubectl edit svc argocd-server -n argocd
```

### 14.6.2: Change the Service Type

Find this line:

```yaml
type: ClusterIP
```

Change it to:

```yaml
type: LoadBalancer
```

Save and exit (`:wq` for `vi`).

### 14.6.3: Get the External Load Balancer DNS

```bash
kubectl get svc argocd-server -n argocd
```

Sample output:

```bash
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP                           PORT(S)                          AGE
argocd-server   LoadBalancer   172.20.1.100   a1b2c3d4e5f6.elb.amazonaws.com        80:31234/TCP,443:31356/TCP       2m
```

### 14.6.4: Access the ArgoCD UI

Use the DNS:

```bash
https://<EXTERNAL-IP>.amazonaws.com
```

---

### 14.7: 🔐 Get the Initial ArgoCD Admin Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo
```

### Login Details:

* **Username:** `admin`
* **Password:** (The output of the above command)

---

## Step 15:  Deploying with ArgoCD and Configuring Route 53 (Step-by-Step)

### Step 15.1: Create Namespace in EKS (from Jumphost EC2)
Run these commands on your jumphost EC2 server:
```bash
kubectl create namespace dev
kubectl get namespaces
```

### Step 15.2: Create New Applicatio with ArgoCD
1. Open the **ArgoCD UI** in your browser.
2. Click **+ NEW APP**.
3. Fill in the following:
   - **Application Name:** `project`
   - **Project Name:** `default`
   - **Sync Policy:** `Automatic`
   - **Repository URL:** `https://github.com/arumullayaswanth/Microservices-E-Commerce-eks-project.git`
   - **Revision:** `HEAD`
   - **Path:** `kubernetes-files`
   - **Cluster URL:** `https://kubernetes.default.svc`
   - **Namespace:** `dev`
4. Click **Create**.


## Step 17: Create a Jenkins Pipeline Job for Backend and frondend & Route 53 Setup

## Enable HTTPS for aluru.site with AWS Classic Load Balancer (CLB)

This guide explains how to configure HTTPS for your domain `aluru.site` using AWS Classic Load Balancer (CLB), Route 53, and AWS Certificate Manager (ACM).

---

## ✅ Prerequisites

* A working application (e.g., on EC2 or Kubernetes).
* A registered domain: `aluru.site`
* Domain is managed in **Route 53** as a **Public Hosted Zone**.
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
* Your Classic Load Balancer is running and serving HTTP on port 80 or 8080.

---

## 📌 Step 1: Request a Public Certificate in ACM

1. Go to **AWS Certificate Manager** (ACM).
2. Click **Request Certificate**.
3. Choose **Request a Public Certificate**.
4. Enter domain:

   * `aluru.site`
   * `www.aluru.site` (optional)
5. Choose **DNS validation**.
6. Click **Request**.
7. After request:

   * Click **Create DNS record in Route 53**.
   * ACM will create the `_acme-challenge` CNAME record.
8. Wait a few minutes until status becomes **Issued**.

---

## 📌 Step 2: Add HTTPS Listener to CLB

1. Go to **EC2 Console > Load Balancers**.
2. Select your **Classic Load Balancer**.
3. Go to **Listeners** tab.
4. Click **Add Listener** (or edit existing 443):

   * Protocol: **HTTPS**
   * Load Balancer Port: **443**
   * Instance Protocol: **HTTP** (or HTTPS if applicable)
   * Instance Port: **80** (or 8080 if your app runs there)
   * SSL Certificate: Choose the one for `aluru.site`
   * Security Policy: Select **ELBSecurityPolicy-2021-06**
5. Click **Save**.

---

## 📌 Step 3: Update Security Group Rules

Go to your EC2 or Load Balancer **Security Group**:

* Add **Inbound Rule**:

  * Type: HTTPS
  * Protocol: TCP
  * Port: 443
  * Source: 0.0.0.0/0

Ensure existing rules allow HTTP (port 80) or your backend port.

---

## 📌 Step 4: Configure DNS in Route 53
1. In ArgoCD UI, open your `project` application.
2. Click on **frontend** and copy the hostname (e.g.,
   `acfb06fba08834577a50e43724d328e3-1568967602.us-east-1.elb.amazonaws.com`).
   
1. Go to **Route 53 > Hosted Zones**.
2. Select `aluru.site`.
3. Click **Create Record**:

   * Record name: leave blank (for root domain)
   * Record type: **A – Routes traffic to an IPv4 address and AWS resource**
   * Alias: **Yes**
   * **Alias target:** Choose Application and Classic Load Balancer
   * Region: **US East (N. Virginia)**
   * **Alias target value:** Paste the frontend load balancer DNS (from step 2)

4. Click **Create Record**.

---

## 📌 Step 5: Test Your Setup

### Using Browser

Visit:

```
https://aluru.site
```

You should see your application load securely over HTTPS.

### Using curl

```bash
curl -v https://aluru.site
```

Expect HTTP 200 OK or the actual page content.

---

## 🛠 Troubleshooting

* **HTTPS times out?**

  * Check port 443 is open in Security Group.
  * Make sure your app is reachable from the CLB.
  * ACM certificate must be in **Issued** status.
* **HTTP works but HTTPS doesn't?**

  * Listener or certificate may not be configured properly.
  * Check the load balancer health check passes.

---

## 📌 Optional: HTTP to HTTPS Redirect

This must be implemented in your application or with a reverse proxy like **NGINX**.

Example NGINX config:

```nginx
server {
  listen 80;
  server_name aluru.site;
  return 301 https://$host$request_uri;
}
```

---

You now have secure HTTPS traffic configured for `aluru.site`! ✅

```bash
terraform destroy -auto-approve
terraform destroy -auto-approve --force
```


























   
