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
edit main.tf file:

    # plan - execute
    resource "aws_s3_bucket" "my_s3_bucket"{
      bucket = "my-s3-bucket-infocs-glauber-1"
    }

command to confirme what terraform will do:

    terraform plan
    
execute the terraform planed (30 s):

    terraform apply
    
Then you can confirm that the new s3 ucket resource was created
    
- Step 05 - Playing with Terraform State - Desired, Known and Actual
  
  Terraform check if there any changes on main.tf and also create a json file with more details of resources applied
  How it owrks:
  
  # STAGES
  # DESIRED (main.tf) - KNOWN (terraform.tfstate) - ACTUAL (changes in the cloud)
  
  Edit bucket s3 name on terraform nad apply
  
  enablin versions on bucket editing main.tf:

# plan - execute
    resource "aws_s3_bucket" "my_s3_bucket"{
      bucket = "my-s3-bucket-infocs-glauber-2"
      versioning {
        enabled = true
        }
    }
   
- Step 06 - Playing with Terraform Console
Using terraform console

    terraform console
    
Use the typeOfResoucer.nameOfResource on terraform console

get information about an resource (bucket):

    > aws_s3_bucket.my_s3_bucket

check more abaout an specific feature that has his description inside an square bracket (zero because it is the first element)

    > aws_s3_bucket.my_s3_bucket.versioning[0].enabled
    
to get informtion directly after main.tf file executes, insert follow line in main.tf:

    output "my_s3_bucket_versioning" {
      value = aws_s3_bucket.my_s3_bucket.versioning[0].enabled
    }
    
then

    terraform apply -refresh=false

for complete details edit main.tf:

      output "my_s3_bucket_details" {
      value = aws_s3_bucket.my_s3_bucket
    }
    
then use -refresh=false parameter to do not refresh all jn file against real infra (faster excution)    

    terraform apply -refresh=false
    
- Step 07 - Creating AWS IAM User with Terraform
add in min.tf file:

    resource "aws_iam_user" "my_iam_user" {
      name = "my_iam_user_glauber"
    }

then use the follow comando to stroe the plan result in a locla file

    terraform plan -out iam.tfplan
    
after confirm the planned you can apply it executing the generated tfplan file:

    terraform apply "iam.tfplan"
    
 teh execution is faster then without and plan file because it doens not compare
 
 add output line in tho show iam user details
 
      output "my_iam_user_complete_details" {
        value = aws_s3_bucket.my_iam_user
    }
 
 tehn
 
    terraform apply -refresh=false

check details form terrafior console:

    terraform console
    
    > aws_iam_user.my_iam_user

- Step 08 - Updating AWS IAM User Name with Terraform

Terrafor is vey intelignet, it means, depending on resource, the rename will operate in diferent ways

rename a bucket will delete the acua one and rcreate another

rename a user will change it instead of recrete as a bucket resource

to execute an iam name change only in one resource of main.tf file, you must update it with the new name:

    resource "aws_iam_user" "my_iam_user" {
      name = "my_iam_user_glaubersoares"
    }

then,

    terraform apply -target=aws_iam_user.my_iam_user

confirm with yes

- Step 09 - Understanding Terraform tfstate files in depth

terraform.tfstate is the actual last applyed chenges

terraform.tfstate.backup is the previous confguration file

renaming both file "terraform.tfstate.001" and "terraform.tfstate.backup.001", terraform lost his "KNOW" state

so if you execute 

    terraform plan
    
it will return the creation of bucket and iam already existent.

Without the KNOWN file terraform cant map the resource nformation with the one in te clouyd, and them, will try to create the resources again.
The KNOWN file has datails needed to access and edit the resources in cloud.

- Step 10 - gitignore Terraform tfstate files
Terraform never can be part of an git version control.

It is very important to create a "gitignore" file in teh same folder of tfsates files

# terraform excludes
*.tfstate
*.tfstate.backup
.terraform

The terrform state has sensitive information so you must no share it
  
- Step 11 - Refactoring Terraform files - Variables, Main and Outputs
  
  You can have multiples .tf files with any names and concate them

to delete the resources created use the following comand

    terraform destroy
    
- Step 12 - Creating Terraform Project for Multiple IAM Users
a new terraform project only needs a new folder

create a new folder "02" for this project

    insert provider informations
    
use the previous main.tf only with provider

use index and count to create multiples resources:

      resource "aws_iam_user" "my_iam_user" {
        count = 2
        name = "my_iam_user_${cunt.index}"
    }
    
then, i the folder of the new project execute

    terraform init
    
    terraform apply
    
PS: use terraform document to knows the resources types and their arguments as well how use it in the link https://registry.terraform.io/browse/providers

increase the number of users editing main.tf file

        resource "aws_iam_user" "my_iam_user" {
            count = 3
            name = "my_iam_user_${cunt.index}"
    }

then user "terraform apply" to confirm the one more user was created.

- Step 13 - Playing with Terraform Commands - fmt, show and console

open users on terraform console

    terraform console
    
    > aws_iam_user.my_iam_users
    
or access a specific user onfiguraation using index    

    > aws_iam_user.my_iam_users[0].arn
    
to confirm and validate the sintaxe of desired file use

    terraform validate
    
to format it in a clean code use (2 spaces or tab):

    terraform fmt
    
show what is in the satege

    terraform show
    
specifies the output of terraform file

    terraform plan -o iam.tfplan
    
and the apply with

    terraform apply iam.tfplan
    
Check the manual in https://www.terraform.io/docs/commands/apply.html to see more details of parameters    

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
