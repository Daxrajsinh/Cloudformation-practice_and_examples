# Cloudformation-practice_and_examples
This repo. contains Cloudformation practice that I did and the examples explaining the simple working of it.
The project demonstrates how to use a **single CloudFormation template** to create **multiple independent stacks** (e.g., dev and prod) using parameters.

---

## 📌 What This Project Covers

- CloudFormation **templates vs stacks**
- Creating **multiple stacks from the same template**
- Parameterized infrastructure
- EC2 instance creation
- S3 bucket creation
- Custom VPC, Subnet, and Security Group
- Debugging common CloudFormation errors

---

## 🏗️ Architecture (Simple)

Each stack creates:

- **S3 Bucket** (environment-specific)
- **Custom VPC**
- **Public Subnet**
- **Security Group** (SSH allowed)
- **EC2 Instance**

Each stack is:

- Independent
- Parameter-driven
- Fully managed by CloudFormation

---

## 📂 Repository Structure
```
.
├── example.yaml     # CloudFormation template
└── README.md        # Project documentation
```

---

## 📄 CloudFormation Template

**File:** `example.yaml`

The template uses parameters to support multiple environments:

### Parameters

- `EnvironmentName` – dev or prod
- `InstanceType` – EC2 instance size
- `KeyName` – Existing EC2 Key Pair for SSH

---

## 🚀 How to Run This Project

### 1️⃣ Prerequisites

- AWS account
- AWS CLI installed and configured
- Existing EC2 Key Pair
- Region: `us-east-1` (recommended)

Make use of AWS Configure:
```bash
aws configure
```

Verify AWS CLI:
```bash
aws sts get-caller-identity
```
---

### 2️⃣ Create Dev Stack
```bash
aws cloudformation create-stack \
  --stack-name dev-stack \
  --template-body file://example.yaml \
  --parameters \
    ParameterKey=EnvironmentName,ParameterValue=dev \
    ParameterKey=InstanceType,ParameterValue=t2.micro \
    ParameterKey=KeyName,ParameterValue=<YOUR_KEYPAIR_NAME>
```

Check status:
```bash
aws cloudformation describe-stacks \
  --stack-name dev-stack \
  --query "Stacks[0].StackStatus"
```

Expected:
```
CREATE_COMPLETE
```

---

### 3️⃣ Create Prod Stack (Same Template)
```bash
aws cloudformation create-stack \
  --stack-name prod-stack \
  --template-body file://example.yaml \
  --parameters \
    ParameterKey=EnvironmentName,ParameterValue=prod \
    ParameterKey=InstanceType,ParameterValue=t3.micro \
    ParameterKey=KeyName,ParameterValue=<YOUR_KEYPAIR_NAME>
```

---

## 🔍 Verifying Resources

### Check EC2 Instances
```bash
aws ec2 describe-instances \
  --query "Reservations[*].Instances[*].Tags"
```

### Check S3 Buckets
```bash
aws s3 ls
```

---

## 🧹 Cleanup (Important)

Delete stacks to avoid unnecessary AWS charges.
```bash
aws cloudformation delete-stack --stack-name dev-stack
aws cloudformation delete-stack --stack-name prod-stack
```

---

## 🧠 Key Learnings

- A **template** is just a blueprint (YAML file)
- A **stack** is a live execution of that template (Instance of template running basically)
- One template → multiple stacks
- EC2 must be launched inside a subnet
- Security Group and Subnet must belong to the same VPC
- CloudFormation stack events are critical for debugging

---

## ✍️ Author
**Daxrajsinh Jadeja**