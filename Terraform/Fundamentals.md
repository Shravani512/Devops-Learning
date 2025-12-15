## Terraform

Terraform is a tool used to create, change, and manage infrastructure using code instead of doing it manually. This concept is called Infrastructure as Code (IaC).
Instead of Clicking in AWS console, Creating EC2, VPC, Load Balancer manually You write code, run a command, and Terraform does everything for you.

Why do we need Terraform?
Problems without Terraform
- Manual setup → time-consuming
- Human errors
- No version control
- Hard to recreate same infra in Dev / QA / Prod
- Difficult rollback

Terraform solves this

Infrastructure is repeatable
- Same code for Dev / QA / Prod
- Easy rollback
- Infra changes are tracked in Git
- Works with multiple cloud providers

Setup terraform on EC2 instance
1. Create EC2 instance to install terraform on it
2. install terraform from hasiCorp official link(https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
3. Install hashicorp official key from same link (commands given)
4. Verify if link is correct ( commands given)
5. Add the official HashiCorp repository to your system.
6. sudo apt update
7. sudo apt-get install terraform
8. Verify terraform --version

Setup terraform on Local
1. Chocolatey on windows

##### HCL (HashiCorp Configuration Language)
HCL is the language used by Terraform to describe infrastructure. A clean, readable way to describe cloud resources using blocks and attributes.
File extension is .tf

Defination:
HCL is a declarative configuration language used by Terraform to define infrastructure using blocks, arguments, and expressions in a human-readable format.

##### Writting HCL

General block syntax
```
block_type "label1" "label2" {
  key = value
}

block_type → what kind of thing (provider, resource, variable, etc.)
labels → names/identifiers
{} → block body
key = value → arguments

```
Most Common 
- Provider block(Tells Terraform which platform to talk to.)
```
provider "aws" {
  region = "ap-south-1"
}

we configure here to use region us-east-2 every time to create aws instance.
```
- Resource block(main.tf)(Used to create infrastructure.)
```
resource "aws_instance" "my_ec2" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
}

resource → creating something
aws_instance → resource type

here - aws        → provider name
       instance   → resource type
How to know which resource types exist?
1. Go to Terraform Registry
2. Open AWS Provider
3. Check Resources section

my_ec2 → local name (used inside Terraform)
Inside {} → configuration
```
- Output Block(Used to display values after terraform apply.)
```
output "public_ip" {
  value = aws_instance.my_ec2.public_ip
}
```

##### Folder structure
```
terraform-project/
│── provider.tf
│── main.tf
│── variables.tf
│── outputs.tf
│── terraform.tfvars

Terraform automatically reads all .tf files in this folder.
```

##### Example- Complete Complete Simple HCL File

```
provider "aws" {
  region = "ap-south-1"
}

--------------------

#defines the input variables (their names, types, and defaults) that are referenced and used in main.tf.
variable "instance_type" {
  type    = string
  default = "t2.micro"
}

variable "ami_id" {
  type = string
}

---------------------

#terraform.tfvars
ami_id        = "ami-12345"
instance_type = "t2.micro"

---------------------

#main.tf
resource "aws_instance" "demo" {
  ami           = var.ami_id
  instance_type = var.instance_type
}

---------------------

output "public_ip" {
  value = aws_instance.demo.public_ip
}
```

### Setps for writting HCL and making it working-
1. Write .tf files
2. Initialize Terraform (terraform init) (in the folder where the main.tf/ resource file is present)
3. terraform validate
4. terraform plan
5. terraform apply
6. terraform destroy

### How to install terraform provider
for eg. we wanted to automate creation of s3_bucket but we do not have aws provider(aws_s3_bucket) for it so have to install.
1. create terraform.tf to write HCL
2. Google search aws provider will get a official page provided by hasicorp in top right corner option of use provider (https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
3. copy and paste it in terraform.tf
4. again do terraform init to install provider
5. check if installed or not at location cd terraform/provider/registry.terraform.io/hashicorp

### Install aws CLI to connect local machine to your aws account
1. google search install aws cli (https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
2. you will get command (curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip")(zip will be downloaded check ls)
3. to unzip download unzip (sudo apt-install unzip)
4. unzip awscliv2.zip
5. sudo ./aws/install
6. aws --version

### Configure AWS CLI
1. command (aws configure)
2. it will ask access key_id
3. go to aws account search (iam)->create user-> attach policies-> search se full access-> create
4. in sercurity credential of that user-> create access key for user-> CLI-> next-> you will get access key and secret access key-> paste it and config done
5. local is connected to aws CLI you can check using command (aws s3 ls) to see all s3 buckets.

