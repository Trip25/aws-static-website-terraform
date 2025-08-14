# AWS Static Website Deployment with Terraform

This project provisions a **production-ready static website** hosted on AWS using **Terraform**, **S3**, **CloudFront**, and **CloudWatch**.  
It follows security and performance best practices such as encryption, CloudFront Origin Access Control (OAC), versioning, caching, and monitoring.

---

## 📂 Project Structure

```
aws-static-website-terraform/
│
├── infrastructure/           # Terraform infrastructure code
│   ├── main.tf             # AWS resources definitions
│   ├── variables.tf        # Input variables
│   ├── outputs.tf          # Terraform outputs
│   ├── versions.tf         # Providers and version constraints
│   ├── terraform.tfvars    # Your custom variables
│   └── terraform.tfvars.example  # Example values
│
├── website/                # Website static content
│   ├── index.html
│   ├── error.html
│   ├── styles.css
│   └── assets/
│
├── .github/workflows/     # CI/CD workflows
├── docs/                  # Documentation (optional)
├── .gitignore
└── README.md
```

---

## 🚀 Features

- **Infrastructure as Code** with Terraform
- **S3 bucket** for static site hosting (private access, served via CloudFront)
- **CloudFront CDN** with caching, compression, and HTTP/2 support
- **CloudFront OAC** for secure S3 access
- **S3 encryption** and versioning
- **Custom domain support** via Route53 (optional)
- **CloudWatch** logging and alarms for 4xx/5xx errors
- **Responsive website** starter template included
- **CI/CD ready** with GitHub Actions

---

## 🛠 Prerequisites

Before you start, you’ll need:

- AWS Account (IAM user with programmatic access)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Terraform](https://developer.hashicorp.com/terraform/downloads)
- Git
- macOS terminal or VSCode integrated terminal

On macOS, you can install tools with Homebrew:

```bash
brew install awscli
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

---

## ⚙️ Setup Instructions

1️⃣ **Clone this repository or create from the setup script**
```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd aws-static-website-terraform
```
_Or run the provided `setup.sh` script to generate the structure._

2️⃣ **Configure AWS credentials**

Enter your AWS Access Key, Secret Key, default region (e.g., `eu-west-2`), and output format (`json`).

3️⃣ **Set your Terraform variables**
```bash
cd infrastructure
cp terraform.tfvars.example terraform.tfvars
```

Edit `terraform.tfvars` to match your project:

```hcl
aws_region = "eu-west-2"
environment = "dev"
project_name = "my-static-website"
domain_name = "" # Leave empty for default CloudFront domain
enable_logging = true
price_class = "PriceClass_100"
```

4️⃣ **Initialize and deploy**
```bash
terraform init
terraform plan
terraform apply
```

Type `yes` when prompted.

---

## 🌐 Upload Website Content

After `terraform apply`, note your S3 bucket name and website url from the Terraform output.

Upload your static files:

```bash
# Replace <bucket-name> with output from Terraform
aws s3 cp ../website/index.html s3://<bucket-name>/index.html
aws s3 cp ../website/error.html s3://<bucket-name>/error.html
aws s3 cp ../website/styles.css s3://<bucket-name>/styles.css
aws s3 cp ../website/assets/ s3://<bucket-name>/assets/ --recursive
```

## 🔄 Updating Your Site

1. Edit files in `/website` locally.
2. Re-upload updated files to S3 (same `aws s3 cp` commands).
3. If you use CloudFront, invalidate the cache to see changes immediately:
```bash
aws cloudfront create-invalidation \
    --distribution-id <your-distribution-id> \
    --paths "/*"
```

---

## 🧹 Tearing Down Resources

When you no longer need the site, destroy resources to avoid costs:
```bash
terraform destroy
```

Type `yes` to confirm.

---

## 📌 Notes

- Terraform manages the infrastructure, **not** the content uploads. Consider adding a CI/CD flow if you update content often.
- Enable ACM certificates and customise DNS in Route53 for HTTPS with your own domain.


## Github Actions

```bash
gh secret set AWS_ACCESS_KEY_ID --body "your-access-key"
gh secret set AWS_SECRET_ACCESS_KEY --body "your-secret-key"
gh secret set AWS_REGION --body "eu-west-2"
```

Then configure in github secrets and variables
