# Managing Hundreds of Clusters Using KOPS

**This is a learning repository for me. This is a work-in-progress repository.**

## Kubernetes Installation Using KOPS on EC2

### Create an EC2 Instance
You can either create an EC2 instance or use your personal laptop. (I used **Ubuntu Linux from AWS**.)

### Dependencies Required
- Python3
- AWS CLI
- kubectl

### Install Dependencies
```sh
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y python3-pip apt-transport-https kubectl
pip3 install awscli --upgrade
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### Install KOPS
```sh
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### IAM User Permissions
Provide the following permissions to your IAM user. If you are using the **admin user**, these permissions are available by default:
- AmazonEC2FullAccess
- AmazonS3FullAccess
- IAMFullAccess
- AmazonVPCFullAccess

### Set Up AWS CLI Configuration
Run the following command and follow the prompts:
```sh
aws configure
```

---

## Kubernetes Cluster Installation
**Please follow the steps carefully and read each command before executing.**

### Create an S3 Bucket for Storing KOPS Objects
```sh
aws s3api create-bucket --bucket kops-ayush-storage --region ap-south-1
```

### Create the Cluster
```sh
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-ayush-storage --zones=ap-south-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro --master-volume-size=8 --node-volume-size=8
```

**Note:** We are using a **local domain**. If you want to use a specific domain, update the above command and configure Route53 accordingly.

### Configure Route53 (If Using a Custom Domain)
```sh
aws route53 create-hosted-zone --name dev.example.com --caller-reference 1
```

### Edit the Cluster Configuration
⚠ **Important:** Edit the configuration as multiple resources are created which **won't fall into the free tier!**
```sh
kops edit cluster demok8scluster.k8s.local
```

### Build the Cluster
```sh
kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-ayush-storage
```
This process will take a few minutes...

### Validate the Cluster Installation
```sh
kops validate cluster demok8scluster.k8s.local
```

---

## ⚠ **WARNING: This setup can generate very high AWS bills!** ⚠
**Using KOPS to manage hundreds of clusters will incur large costs. Always ensure you delete unused resources to avoid excessive charges.**
