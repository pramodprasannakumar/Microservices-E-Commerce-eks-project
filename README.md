# Microservices-E-Commerce-eks-project

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

## 🌐 Step 5: Create Network and Database

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
Apply complete! Resources: 19 added, 0 changed, 0 destroyed.

Outputs:

jumphost_public_ip = "18.208.229.108"
region = "us-east-1"
```
4. The command terraform state list is used to list all resources tracked in your current Terraform state file.
```bash
terraform state list
```
---
