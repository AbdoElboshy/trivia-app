# README: Installing and Configuring Infrastructure for This Project

This document provides detailed instructions on how to set up the AWS infrastructure, install required packages, and connect the project to GitHub. Follow these steps to ensure your environment is properly configured from start to finish.

---

## 1. Prerequisites

- An AWS account with permissions to create and manage resources such as:
  - AWS CloudFormation  
  - AWS Lambda  
  - Amazon API Gateway  
  - Amazon DynamoDB  
  - AWS Step Functions  
  - Amazon S3  
- A GitHub account to host and manage your repository.  
- (If working locally) AWS CLI, AWS SAM CLI, Git, Node.js, and npm installed.  
- (If you prefer EC2) An EC2 instance (Amazon Linux 2 recommended) with access to the internet and an associated IAM role that grants necessary AWS permissions.

---

## 2. Launch and Prepare an EC2 Instance (Optional)

If you prefer using EC2 instead of your local machine:

1. Log into your AWS Management Console and go to “EC2.”  
2. Choose “Launch instances” and select “Amazon Linux 2 AMI.”  
3. Configure instance details (such as instance type: t2.micro or t3.micro).  
4. Create or select a security group allowing inbound SSH (port 22) and any other necessary ports.  
5. Assign an IAM role with permissions for S3, CloudFormation, Lambda, DynamoDB, and other required services.  
6. Launch the instance.

After launch, connect to your instance via SSH:

```bash
ssh -i /path/to/private-key.pem ec2-user@<public-ec2-dns>
```

---

## 3. Installing Required Packages

Whether you are on EC2 (Amazon Linux 2) or another environment, you need the following packages and tools. Below are example commands for Amazon Linux 2.

### 3.1 System Update

```bash
sudo yum update -y
```

### 3.2 Install Curl and Unzip

```bash
sudo yum install -y curl unzip
```

### 3.3 Install Node.js and npm

Use either NodeSource or Node Version Manager (nvm). The following commands use NodeSource’s RPM repository for Node.js 16.x:

```bash
curl -fsSL https://rpm.nodesource.com/setup_16.x | sudo bash -
sudo yum install -y nodejs
```

You can verify installation:

```bash
node -v
npm -v
```

### 3.4 Install Git

```bash
sudo yum install -y git
git --version  # Verify installation
```

### 3.5 Install AWS CLI v2

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

aws --version  # Verify
```

### 3.6 (Optional) Install AWS SAM CLI

```bash
curl -Lo sam_install.sh https://raw.githubusercontent.com/aws/aws-sam-cli/main/installer.sh
chmod +x sam_install.sh
./sam_install.sh --update

echo "export PATH=$PATH:~/.sam-installation/bin" >> ~/.bashrc
source ~/.bashrc

sam --version  # Verify
```

---

## 4. Configuring AWS CLI Credentials (If Needed)

If you are on an EC2 instance with a proper IAM role, you might skip this step. For local development, configure credentials:

```bash
aws configure
```

- AWS Access Key ID  
- AWS Secret Access Key  
- Default region (e.g., us-west-2)  
- Default output format (json recommended)

Verify you are authenticated:

```bash
aws sts get-caller-identity
```

---

## 5. Cloning and Setting Up the Project from GitHub

1. **Clone the repository**:

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
```

2. **Install any project-specific libraries** (for example, if you have a package.json in the project root or a front-end directory):

```bash
# Adjust to your project's directory structure
npm install
```

---

## 6. Building and Deploying the Backend with AWS SAM (If Available)

If your project includes a SAM template (typically named `template.yaml` or `template.yml`), navigate to that folder:

```bash
cd ~/environment/<repo-name>/  # or wherever your template file is located
sam build
sam deploy --guided
```

During deployment:
- Enter a Stack Name (e.g., `my-app-stack`).
- Specify the AWS region (e.g., `us-west-2`).
- Accept defaults or customize as needed.

After successful deployment, your CLI output includes important ARNs and endpoints for services like:
- API Gateway  
- Lambda functions  
- S3 buckets  

---

## 7. Deploying the Frontend to Amazon S3 (Optional)

If the project includes a front-end (e.g., React) that needs to be hosted:

1. **Build the Frontend**:

```bash
cd front-end-react/
npm install
npm run build
```

2. **Create an S3 Bucket**:

```bash
aws s3 mb s3://<unique-bucket-name> --region <aws-region>
```

3. **Upload the Build Output**:

```bash
aws s3 sync --acl public-read build s3://<unique-bucket-name>
```

Verify by visiting:
```
https://<unique-bucket-name>.s3.<aws-region>.amazonaws.com/index.html
```

---

## 8. Connecting Your Project to GitHub

1. **Initialize Git (if not already initialized):**

```bash
git init
git add .
git commit -m "Initial commit"
```

2. **Link to a Remote Repository** (If you created a new repository on GitHub):

```bash
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

3. **Setup Branches, Pull Requests, and CI/CD** (Optional):  
   - Create feature branches, open pull requests on GitHub, and merge changes once approved.  
   - Consider GitHub Actions for automated building, testing, and deployment.

---

## 9. Testing

- **Local Testing**:  
  - For serverless backends, consider using SAM’s `sam local start-api` or `sam local invoke` commands if your application is compatible.  
  - For front-end applications, run `npm start` in the front-end directory.

- **Post-Deployment Testing**:  
  - Use `curl` or a browser to test API Gateway endpoints.  
  - Verify front-end functionality on the S3 bucket URL.

---

## 10. Clean Up

To avoid ongoing costs:
1. Delete the CloudFormation stack if you created one with SAM:

```bash
aws cloudformation delete-stack --stack-name <my-app-stack>
```

2. Remove S3 buckets you no longer need:

```bash
aws s3 rb s3://<unique-bucket-name> --force
```

3. Terminate EC2 instances if you launched any specifically for this project.

---

## 11. Additional Resources

- [AWS Documentation](https://docs.aws.amazon.com/index.html)  
- [AWS Serverless Application Model (SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam.html)  
- [GitHub Actions Documentation](https://docs.github.com/actions)  
- [Node.js Official Website](https://nodejs.org/en/)  

---

**Author**: AbdulRahman Mostafa
**Version**: 1.0  
