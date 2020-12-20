# Terraform

## Projects
- Provision EC2 based HTTP Servers with Load Balancer
- Provision AWS and Azure Kubernetes Clusters (Azure DevOps Pipelines)

## Understanding IaC problem
Requirement:
Microservices has many differentes technologies used, with requiriments and diferents dependences.

Manual approach example:
  
  Provision server > install java > install tomcat > configure tomcat > deploy app

IaC - Benefits on provision server and app configuration steps:

Terraform, Cloud formation:

   Create a template > provision server

Ansible, chef, puppet:

  Install softwares > configure softwares 

Jenkins:

  Deploy app into server...!

Manual approach example:
Provision server > isntall java > isntall tomcat > configure tomcat > deploy app

Requirement:
Microservices has many differentes technologies used, with requiriments and diferents dependences.

IaC - Benefits on provision server and app configuration steps.
Terraform, Cloud formation:
Create a template > provision server

Ansible, chef, puppet:
Install softwares > configure softwares 

Jenkins
Deploy app into server...!

## Steps
- Step 01 - Creating and Initializing First Terraform Project
    Install terraform on oracle cloud: https://docs.cloud.oracle.com/en-us/iaas/Content/API/SDKDocs/terraformgetstarted.htm
    
    Install terraform on AWS: https://askubuntu.com/questions/983351/how-to-install-terraform-in-ubuntu#:~:text=%20Steps%20to%20install%20terraform%20on%20Ubuntu%20%2F,4%20Extract%20the%20downloaded%20file%20archive%0Aunzip...%20More%20
    
Check version:
  
    terraform -v

Create a folder to store terraform files, that have extensios ".tf"
The providers initial specification must be configured (AWS, GCP, Oracle,...), creating an main.tf file content oracle initial provider configuration:

AWS Example:

    # Configure the AWS Provider
    provider "aws" {
      version = "~> 3.0"
      region  = "us-east-1"
    }
    
PS: https://registry.terraform.io/providers/hashicorp/aws/latest/docs    

Oracle example:
    
    variable "region" {}

    provider "oci" {
      auth = "InstancePrincipal"
      region = "${var.region}"
    }
    
    

execute the follow comand in the same folder as the file main.tf is in:

    terraform init
    
- Step 02 - Create Oracle IAM/AWS User Access Key and Secret
To create the IAM to aceess resource use https://docs.cloud.oracle.com/en-us/iaas/big-data/doc/create-iam-users-and-add-them-iam-groups.html
and them add him to administrator greoup identity>groups.
Oracle configuration: https://docs.cloud.oracle.com/en-us/iaas/developer-tutorials/tutorials/tf-provider/01-summary.htm

AWS configuration: https://linuxbeast.com/tutorials/aws/create-a-new-iam-users-on-aws-console/#:~:text=%20How%20to%20Create%20A%20New%20IAM%20Users,to%20add%20tags%20to%20your%20IAM...%20More%20
    
- Step 03 - Configure Terraform Environment Variables for AWS Access Keys
AWS: You will need the information provided in .csv file generated on step02

Execute following comands:
    
    export AWS_ACCESS_KEY_ID="ACCESSKEY"
    
    export AWS_SECRET_ACCESS_KEY="SECRETKEY"

- Step 04 - Creating AWS S3 Buckets with Terraform
- Step 05 - Playing with Terraform State - Desired, Known and Actual
- Step 06 - Playing with Terraform Console
- Step 07 - Creating AWS IAM User with Terraform
- Step 08 - Updating AWS IAM User Name with Terraform
- Step 09 - Understanding Terraform tfstate files in depth
- Step 10 - gitignore Terraform tfstate files
- Step 11 - Refactoring Terraform files - Variables, Main and Outputs
- Step 12 - Creating Terraform Project for Multiple IAM Users
- Step 13 - Playing with Terraform Commands - fmt, show and console
- Step 14 - Recovering from Errors with Terraform
- Step 15 - Understanding Variables in Terraform
- Step 16 - Creating Terraform Project for Understanding List and Map
- Step 17 - Adding Elements - Problem with Terraform Lists
- Step 18 - Creating Terraform Project for Learning Terraform Maps 
- Step 19 - Quick Review of Terraform FAQ
- Step 20 - Understanding Creation of EC2 Instances in AWS Console
- Step 21 - Creating New Terraform Project for AWS EC2 Instances
- Step 22 - Creating New EC2 Key Pair and Setting Up
- Step 23 - Adding AWS EC2 Configuration to Terraform Configuration
- Step 24 - Installing Http Server on EC2 with Terraform - Part 1
- Step 25 - Installing Http Server on EC2 with Terraform - Part 2
- Step 26 - Remove hardcoding of Default VPC with AWS Default VPC
- Step 27 - Remove hardcoding of subnets with Data Providers
- Step 28 - Remove hardcoding of AMI with Data Providers
- Step 29 - Playing with Terraform Graph and Destroy EC2 Instances
- Step 30 - Creating New Terraform Project for AWS EC2 with Load Balancers
- Step 31 - Create Security Group and Classic Load Balancer in Terraform
- Step 32 - Review and Destroy AWS EC2 with Load Balancers
- Step 33 - Creating Terraform Project for Storing Remote State in S3 
- Step 34 - Create Remote Backend Project for Creating S3 Buckets
- Step 35 - Update User Project to use AWS S3 Remote Backend
- Step 36 - Creating multiple environments using Terraform Workspaces
- Step 37 - Creating multiple environments using Terraform Modules



## Commands Executed

```
brew install terraform
terraform --version
terraform version
terraform init
export AWS_ACCESS_KEY_ID=*******
export AWS_SECRET_ACCESS_KEY=*********
terraform plan
terraform console
terraform apply -refresh=false
terraform plan -out iam.tfplan
terraform apply "iam.tfplan"
terraform apply -target=aws_iam_user.my_iam_user
terraform destroy
terraform validate
terraform fmt
terraform show
export TF_VAR_iam_user_name_prefix = FROM_ENV_VARIABLE_IAM_PREFIX
export TF_VAR_iam_user_name_prefix=FROM_ENV_VARIABLE_IAM_PREFIX
terraform plan -refresh=false -var="iam_user_name_prefix=VALUE_FROM_COMMAND_LINE"
terraform apply -target=aws_default_vpc.default
terraform apply -target=data.aws_subnet_ids.default_subnets
terraform apply -target=data.aws_ami_ids.aws_linux_2_latest_ids
terraform apply -target=data.aws_ami.aws_linux_2_latest
terraform workspace show
terraform workspace new prod-env
terraform workspace select default
terraform workspace list
terraform workspace select prod-env
```
