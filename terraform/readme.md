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
            name = "my_iam_user_${count.index}"
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

If you run an tf with an erro you just need to rerun the correct one with correct information to update it.

- Step 15 - Understanding Variables in Terraform
    
Create a variable:

    variable "iam_user_name_prefix" {
      default = "my_iam_user"
    }
    
using the variable:

     resource "aws_iam_user" "my_iam_user" {
        count = 3
        name = "${var.iam_user_name_prefix}_${count.index}"
    }
    
Then apply,
    
    terraform apply
    
You can use the variable types:
    
    variable "iam_user_name_prefix" {
      type = string #any, number, bool, list, map, set, object, tuple
      default = "my_iam_user"
    }

and use validate to confirm

    terraform validate
    
PS: terrafor will ask you for the variables name prefixo if not specified.    

Export an variable:

Linux:
    
    export TF_VAR_iam_user_name_prefix = FROM_ENV_VARIABLE_IAM_PREFIX
    
Windows
    
    setx TF_VAR_iam_user_name_prefix = FROM_ENV_VARIABLE_IAM_PREFIX
    
or

    set TF_VAR_iam_user_name_prefix = FROM_ENV_VARIABLE_IAM_PREFIX

The priority variables are:
    
    1º Variables declared using comando line
    2º Variables set inside .tfvars file
    3º Variables sei inside .tf file

Use to check the variables applied:

    terraform plan -refresh=false
    
Apply new variables from a .tfvars file

    terraform aply -var-file="some-name.tfvars"
    
PS: Variables is very important due it makes the envoronment dinamic and that is normally used such development, homologation and production.

- Step 16 - Creating Terraform Project for Understanding List and Map
    
Create the project 03 and use the project 02 file.

create a new variable in main.tf file,

      variable "names" {
      default = ["ravs", "tom", "jane"]
      #default = ["ranga", "tom", "jane"]
    }

    provider "aws" {
      region  = "us-east-1"
      version = "~> 2.46"
    }

    resource "aws_iam_user" "my_iam_users" {
      #count = length(var.names)
      #name  = var.names[count.index]
      for_each = toset(var.names)
      name = each.value
    }
    
Create a list of names    

Set all the names as a list of variables,

    variable "names" {
      default = ["ravs", "tom", "jane"]
     }
    
Then execute it using lenght method,

    resource "aws_iam_user" "my_iam_users" {
      count = length(var.names)
      name  = var.names[count.index]
    }

On terraform console you can use some comand like:

    >length(var.names)
    >reverse(var.names)
    >distinct(var.names)
    >toset(var.names)

To concate, use same type:

    >concat(var.names, ["new_value"])
    
Search by content

    >contains(var.names,"ranga")
    
Sorting

    >sort(var.names)
    
Ranging select range(begin,end,step_size)

    >range(1,10)
    >range(1,10,2)
    
More functions can be founded in https://www.terraform.io/docs/configuration/functions.html

- Step 17 - Adding Elements - Problem with Terraform Lists
  The type of container to be used is very important
  
  Add a new user on first index of a list will change all the others,
  
  In this case other types to store tha values are recomended.
  
  So, lets use a diferent store type and destroy the current one,
  
      terraform destroy -refresh=false
      
  Modify the .tf file to
  
      resource "aws_iam_user" "my_iam_users" {
        for_each = toset(var.names)
        name  = each.value
      }
  
  list is used when teh position is important
  for_each is used when the data is the important

- Step 18 - Creating Terraform Project for Learning Terraform Maps 
    
    Maps is used to associate a resource to an argument such user to his country etc...
    
Create a variable map of string eith other map,

    variable "users" {
      default = {
        ravs : { country : "Netherlands", department : "ABC" },
        tom : { country : "US", department : "DEF" },
        jane : { country : "India", department : "XYZ" }
      }
    }
    
Selectin user as key and country,department as tags,

      resource "aws_iam_user" "my_iam_users" {
        for_each = var.users
        name     = each.key
        tags = {
          #country: each.value
          country : each.value.country
          department : each.value.department
        }
      }


destroy it before continue:
  
    terraform destroy
    
- Step 19 - Quick Review of Terraform FAQ

review STATE

  KNOWN -> present in your terraform.tfstate
  DESIRED -> Declared on .tf file
  ACTUAL -> Presented on teh cloud (AWS, Oracle, GCP,...)
      
 terraform.tfsate store details of resources (on AWS) and found details dependencies
 
 BEST Practicies:
 
  Group Resources having similar lifecycles and create separate terraform projects for them
  
  Use visual code to expanded project in a tree and also create a backup folder with copy of original projects. This is importat to avoid errors.
      
- Step 20 - Understanding Creation of EC2 Instances in AWS Console
  
  Manual creation of an EC2
  
- Step 21 - Creating New Terraform Project for AWS EC2 Instances

Creating the security group:

      // provider configuration
      provider "aws" {
      region = "us-east-2"
      }

      // HTTP Server -> SG
      // SG -> 80 TCP, 22 TCP, CIDR ["0.0.0.0/0"]

      resource "aws_security_group" "http_server_sg" {
      name   = "http_server_sg"
      vpc_id = "vpc-7638861d"

      ingress {
      from_port   = 80
      to_port     = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      }

      ingress {
      from_port   = 22
      to_port     = 22
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      }

      egress {
      from_port   = 0
      to_port     = 0
      protocol    = -1
      cidr_blocks = ["0.0.0.0/0"]
      }

      tags = {
      name = "http_server_sg"
      }
    }

 More argumetns: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group
 
- Step 22 - Creating New EC2 Key Pair and Setting Up
 
Creating a key pair using console and store it in your locar user folder with owner read access only   

- Step 23 - Adding AWS EC2 Configuration to Terraform Configuration

Add the follow lines in main.tf

      // Create an EC2 instance (hard code)
      resource "aws_instance" "http_server" {
        ami= "ami-09558250a3419e7d0"
        key_name= "local_glauber"
        instance_type= "t2.micro"
        vpc_security_group_ids = [aws_security_group.http_server_sg.id]
        subnet_id= "subnet-db83c497"
    }

- Step 24 - Installing Http Server on EC2 with Terraform - Part 1
  
Insert in aws_isntance resource:

    variable "aws_key_pair" {
      default = "~/aws/aws_keys/"name.pem"
    }
    
    ...
    
    connection {
        type = "ssh"
        host = self.public_ip
        user = "ec2-user"
        private_key = file(var.aws_key_pair)
    }
    
    provisioner "remote-exec" {
        inline = [
          //install httpd
          "sudo yum isntall httpd -y",
          //start
          "sudo service httpd start",
          //copy a file
          "echo Welcome to Glauber GitHub - Vurtua Server is at ${self.public_dns} | sudo tee /var/www/html/index.html"
        ]
    }

- Step 25 - Installing Http Server on EC2 with Terraform - Part 2
  
    Execute the terraform file and confirm taht the http service is accessible over internet (configured directly from terraform)
    
Good Practice: Imumutable servers    

  Wrong: provision Server -> Tweak 1 (script 1) -> Tweak 2 (script 2) -> Tweak 3 (script 3) -> Current State
  
  Correct(Immutable concept): Provision Server v1 -> Provision Server v2 -> Confirm Server v2 -> Remover Server v1 -> ...

- Step 26 - Remove hardcoding of Default VPC with AWS Default VPC

Using data provider instead fof hardcode:

Configure default vpc

      resource "aws_default_aws" "default" {
      
      }

Then apply and use the variables generated on .tfstate to change hardcoe values to respective variable on AWS-DEFAULT_AWS.

- Step 27 - Remove hardcoding of subnets with Data Providers

Configure data provider:

    data "aws_subnet_ids" "default_subnets" {
      vpc_id = aws_default_vpc.default.id
    }

then apply with -target...

go to terraform console

    terraform console
    
    > data.aws_subnet_ids.default_subnets
    
Confirm the index of you subnet and change the hardcode

    > tolist(data.aws_subnet_ids.default_subnets.ids)[0]
    
- Step 28 - Remove hardcoding of AMI with Data Providers

configure data provider:

    data "aws_ami_ids" "aws-linux-2-latest_ids" {
      owners = ["amazon"]
    }  
    
Then insert    
    
    data "aws_ami" "aws-linux-2-latest" {
      most_recent = true
      owners = ["amazon"]
      filter{
        name = "name"
        values = ["amzn2-ami-hbm-*"]
    }
    
Apply and confirm using terraform console

    terraform apply
    terraform console
    > data.aws_ami.aws_linux_2_latest

- Step 29 - Playing with Terraform Graph and Destroy EC2 Instances
  
  Execute the graph comand:
  
      terraform graph
      
Copy the file and past it on "graphviz online" https://dreampuf.github.io/ to see a resource diagram with all resources dependences...

Create variables.tf and data-providers.tf files and move respective functions from main.tf to them in order to make terrafomrm project clean

Confirm that no chages occurs using

    terraform apply
    
or

    terraform plan


- Step 30 - Creating New Terraform Project for AWS EC2 with Load Balancers

Creating multiples http_servers under a load balancer using terraform

Using the previous prject main.tf file, change:

    resource ... "http_server" to "http_servers"
    
and subnet sintaxe to 
    
    for_each = data.aws_subnet_ids.default_subnets.ids
    subnet_id = each.value
    
    tags = {
      name : "http_servers_${each.value}"
    }
    
and tehn destroy previous project , init the new one and apply it twice using target in the secondtry

confirm that they are runin using the public DNS to access then via brownser

- Step 31 - Create Security Group and Classic Load Balancer in Terraform

change security group renaming it to elb_sg, remove the ingress of 22 port and tag.

Create the load balancer resource

    resource "aws_elb" "elb" {
      name = "elb"
      subnets = data.aws_subnet_ids.default_subnets.ids
      security_groups = [aws_security_group.elb_sg.id]
      instances = values(aws_instance.http_servers).*.id

      listener {
        instance_port = 80
        instance_protocol = "http"
        lb_port = 80
        lb_rotocol = "http"
      }

then apply it

PS: use documents and terraform console to test the functions return values

- Step 32 - Review and Destroy AWS EC2 with Load Balancers

    terraform destroy
  
- Step 33 - Creating Terraform Project for Storing Remote State in S3 
  
Start from a basic main.tf and output.tf

Initialize and aplly for this new project

    terraform init
    
    terrafomr apply
    
Confirm terraform.tf file created

Create a folder backend_state and users folder

Move terraforms to users

- Step 34 - Create Remote Backend Project for Creating S3 Buckets

create a s3 bcket

    //S3 Bucket       
    resource "aws_s3_bucket" "enterrise_backend_state" {
      bucket = "dev-application-backend-state-glaubersoares"
      
      lifecycle{
        prevent_destroy = true
      }
      
      versioning{
        enabled=true
      }
      
      server_side_encryption_configuration {
        rule{
          apply_server_side_encryption_by_default{
            sse_algorithm = "AES256"
          }
        }
      } 
     }
     
     //Locking - Dynamo DB (only one user editing terraform)
     
     resource "aws_dynamodb_table" "enterrise_backend_lock" {
      name = "dev_application_locks"
      billing_mode = "PAY_PER_REQUEST"
      
      hash_key = "LockID"
      attribute {
        name = "LockID"
        type = "S"
      }
     }
these changes must be done inside main.tf at backend-state folder

then init it, validate and apply

- Step 35 - Update User Project to use AWS S3 Remote Backend

After create the S3 bucket and dynamodb the store it on s3 addinf follow lines on main.tf



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
