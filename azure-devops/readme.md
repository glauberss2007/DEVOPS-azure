# Azure DevOps

## Azure Marketplace

- Terraform 1 (https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks)
- Terraform 2 (https://marketplace.visualstudio.com/items?itemName=charleszipp.azure-pipelines-tasks-terraform)
- Aws (https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.aws-vsts-tools)

## Projects
- Build and Push Docker Image
- CI/CD/IAAC AWS Kubernetes Cluster
- CI/CD/IAAC Azure Kubernetes Cluster

### Azure DevOps - Pipelines

Overview:

Object: Quick feedback

Continuous Integration (Code Commit -> Unit Tests -> Code Quality -> Package -> Integration test)

Continuous Deployment (Code Commit -> Unit Tests -> Integration Tests -> Package -> Deploy -> Automated tests)

Continuous Delivery (Code Commit -> Unit Tests -> Integration Tests -> Package -> Deploy -> Automated tests -> testing Approval -> Deploy NEXT -> ...)

Create continuos pipelines - Main tools: Jenkins or Azure DevOps

Requirements: Azure Account = VS Code + Docker


- Step 01 - Getting Started with Azure DevOps - First Project

    Create a new project named "azure-devops-kubernetes-terraform" in azure devops workspace
    
- Step 02 - Setting up Git Repo for Azure DevOps Pipeline

    Selec currency-exhange project from microservices rojects folder and copy to an azure-devops-kubernetes-terraform-pipeline folder.
    
generate a ssh key for git hub:

     ssh-keygen -t rsa -C "glauberss2007@gmail.com"
     
Copy content of .pub generated key to github ssh user configuration in settings options

Initialize git inside pipeline folder:
    
    git init
    
select all
    
    git add *
    git add .
    
Commit with initial message

    git commit -m "First commit"
    
Push local project to remote:

    git push -u origin main
    
- Step 03 - Creating your first Azure DevOps Pipeline

Access you project on azure devops and pipelines -> Pipelines -> Create Pipeline -> Conect to your repository.

Azure devops will indicate some tools to next step, in this case due dockerfile, docker, kubernetes,etc...

Select Starter Pipeline to configure it in an yaml file

Change the name and save and run

Confirm on github and  on azuredevops > pipeline > jobs the progress oon each  step.
    
- Step 04 - Getting Started with Azure DevOps - Agents and Jobs - 1

    You can see execution history on pipeline runs
    
    All the chabges made form remote,origin or other will be hown in azure devops 
    
    Click on edit to see the yaml file:

    
Triggered changes on te master(main) branch:

        trigger:
        - main

The Operational system of agent

        pool:
          vmImage: 'ubuntu-latest'

The agent information is on teh job
        
        ...
        Starting: Initialize job
        Agent name: 'Hosted Agent'
        Agent machine name: 'fv-az40-526'
        Current agent version: '2.179.0'
        Operating System
        Ubuntu
        20.04.1
        LTS
        ...

PS: It is possible to add an custom pool that can represent your data center servers, etc...for this

Azure Devops project -> project Settings -> Agent Pool


Tasks configuration using steps

One line script task:

        steps:
        - script: echo Hello, world, changed!
          displayName: 'Run a one-line script'

Multiple lines script task:

        - script: |
            echo Add other tasks to build, test, and deploy your project.
            echo See https://aka.ms/yaml
          displayName: 'Run a multi-line script'


- Step 05 - Getting Started with Azure DevOps - Agents and Jobs - 2

Adding multiple jobs:

        trigger:
        - main

        pool:
          vmImage: 'ubuntu-latest'

        # Pipelines > stages > jons > tasks(steps)

        jobs:
        - job: Job1
          steps:
          - script: echo Hello, world, changed!
            displayName: 'Run a one-line script'

          - script: |
              echo Add other tasks to build, test, and deploy your project.
              echo See https://aka.ms/yaml
            displayName: 'Run a multi-line script'
        - job: Job2
          steps:
          - script: echo Job2
            displayName: 'Run a one-line script'
            
PS: These 2 job runned in pararel with no dependences and in diferentes agnest (diferente machines)            

- Step 06 - Using dependsOn with Jobs

Configuring dependeces where job 2 depends of job 1 and job 3 depends on job 2:

        trigger:
        - main

        pool:
          vmImage: 'ubuntu-latest'

        # Pipelines > stages > jons > tasks(steps)

        jobs:
        - job: Job1
          steps:
          - script: echo Hello, world, changed!
            displayName: 'Run a one-line script'
          - script: |
              echo Add other tasks to build, test, and deploy your project.
              echo See https://aka.ms/yaml
            displayName: 'Run a multi-line script'
        - job: Job2
          dependsOn: Job1
          steps:
          - script: echo Job2
            displayName: 'Run a one-line script'
        - job: Job3
          dependsOn: Job2
          steps:
          - script: echo Job3!
            displayName: 'Run a one-line script'                        

- Step 07 - Creating Azure DevOps Pipeline for Playing with Stages 
    
Configuring multiple stages:

        trigger:
        - main

        pool:
          vmImage: 'ubuntu-latest'

        stages:
        - stage: Build
          jobs:
          - job: FirstJob
            steps:
            - bash: echo Build FirstJob
          - job: SecondJob
            steps:
            - bash: echo Build SecondJob
        - stage: DevDeploy
          jobs:
            - job: DevDeploy
              steps:
              - bash: echo Build DevDeploy
        - stage: QADeploy
          jobs:
            - job: QADeploy
              steps:
              - bash: echo Build QADeploy
        - stage: ProdDeploy
          jobs:
            - job: ProdDeploy
              steps:
              - bash: echo Build ProdDeploy
    
PS: Default stages proccess depends of prevously one

To apply specific dependences:

    trigger:
    - main

    pool:
      vmImage: 'ubuntu-latest'

    stages:
    - stage: Build
      jobs:
      - job: FirstJob
        steps:
        - bash: echo Build FirstJob
      - job: SecondJob
        steps:
        - bash: echo Build SecondJob
    - stage: DevDeploy
      dependsOn: Build
      jobs:
        - job: DevDeploy
          steps:
          - bash: echo Build DevDeploy
    - stage: QADeploy
      dependsOn: Build
      jobs:
        - job: QADeploy
          steps:
          - bash: echo Build QADeploy
    - stage: ProdDeploy
      dependsOn: 
      - DevDeploy
      - QADeploy
      jobs:
        - job: ProdDeploy
          steps:
          - bash: echo Build ProdDeploy
    
- Step 08 - Playing with Variables and dependsOn for Stages

Pipeline level variable:

    Go on Edit > Variables > new variable

Variable name:
        
        PipelineLevelVariable
       
Variable value:

        PipelineLevelVariableValue
        
Stage leve vriable:
    
      ...
      - stage: DevDeploy
      variables:
        environment: Dev
      dependsOn: Build
      jobs:
        - job: DevDeployJob
          steps:
          - bash: echo $(environment)DeployJob
     ...   
        
- Step 09 - Understanding Azure DevOps Pipeline Variables
- Step 10 - Creating Azure DevOps Tasks for Copy Files and Publish Artifacts
- Step 11 - Running Azure DevOps Jobs on Multiple Agents
- Step 12 - Understanding Azure DevOps Deployment Jobs - Environments and Approvals
- Step 13 - Build and Push Docker Image in Azure DevOps - Part 1
- Step 14 - Build and Push Docker Image in Azure DevOps - Part 2
- Step 15 - Playing with Azure DevOps Releases

### CI, CD, IAAC with Kubernetes on Azure with Azure DevOps - Pipelines
- Step 01 - Review Terraform Configuration for Azure Kubernetes Cluster Creation
- Step 02 - Setting up Client ID, Secret and Public Key for Azure Kubernetes Cluster Creation
- Step 03 - Creating Azure DevOps Pipeline for Azure Kubernetes Cluster IAAC 
- Step 04 - Performing Terraform apply to create Azure Kubernetes Cluster in Azure DevOps
- Step 05 - Connecting to Azure Kubernetes Cluster using Azure CLI
- Step 06 - Creating Azure DevOps Pipeline for Deploying Microservice to Azure Kubernetes
- Step 07 - Creating V2 and Enable Build and Push of Docker Image - Part 1
- Step 08 - Creating V2 and Enable Build and Push of Docker Image - Part 2
- Step 09 - Performing Terraform destroy to delete Azure Kubernetes Cluster in Azure DevOps
- Step 10 - Quick Review of Terraform destroy

### CI, CD, IAAC with Kubernetes on AWS with Azure DevOps - Pipelines
- Step 01 - Review Terraform Configuration for AWS EKS Cluster Creation
- Step 02 - Setup AWS S3 Buckets and Subnet Configuration
- Step 03 - Enable AWS Tools in Azure DevOps and Create Azure DevOps Pipeline
- Step 04 - Performing Terraform apply to create AWS EKS Cluster in Azure DevOps
- Step 05 - Retry Terraform apply for Creating Cluster Binding
- Step 06 - Configure AWS CLI and Setup Kubernetes Connection using Service Account
- Step 07 - Creating Azure DevOps Pipeline for Deploying Microservice to AWS EKS
- Step 08 - Creating V3 and Enable Build and Push of Docker Image - Part 1
- Step 09 - Creating V3 and Enable Build and Push of Docker Image - Part 2
- Step 10 - Performing Terraform destroy to delete AWS EKS Cluster in Azure DevOps - 1
- Step 11 - Performing Terraform destroy to delete AWS EKS Cluster in Azure DevOps - 2

### Azure DevOps - Management Tools
- Step 01 - Getting Started with Azure DevOps with Demo Generator
- Step 02 - Overview of Azure DevOps - Boards, Wiki, Repos and Pipelines
- Step 03 - Exploring Azure DevOps Boards - Epics, Features and User Stories
- Step 04 - Azure DevOps - Boards View vs Backlogs View
- Step 05 - Understanding Sprints in Azure DevOps
- Step 06 - Creating Azure DevOps Queries
- Step 07 - Playing with Azure DevOps Repos
- Step 08 - Quick Review of Azure DevOps Pipelines
- Step 09 - Quick Review of Azure DevOps


## Azure Kubernetes Cluster

Pre-requisites
- Service Account
- SSH Public Key


```
# Create Service Account To Create Azure K8S Cluster using Terraform
az login
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<<azure_subscription_id>>"

# Create Public Key for SSH Access
ssh-keygen -m PEM -t rsa -b 4096 # PEM - Privacy Enhanced Mail - Certificate Format RSA- Encryption Algorithm

# ls /Users/rangakaranam/.ssh/id_rsa.pub

# Get Cluster Credentials
az aks get-credentials --name <<MyManagedCluster>> --resource-group <<MyResourceGroup>>
```


## AWS EKS Kubernetes Cluster

```
aws configure
aws eks --region us-east-1 update-kubeconfig --name in28minutes-cluster 
kubectl get pods
kubectl get svc
kubectl get serviceaccounts
kubectl get serviceaccounts default -o yaml
kubectl get secret default-token-hqkvj -o yaml
kubectl cluster-info
```

# Backup - DO NOT USE

### Manually setting up from local machine

#### Create Service Account For Your Subscription To Create Azure K8S Cluster using Terraform

```
az login
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<<azure_subscription_id>>"
export TF_VAR_client_id=<<service_account_appId>>
export TF_VAR_client_secret=<<service_account_password>>
```

#### Create Public Key for SSH Access

```
ssh-keygen -m PEM -t rsa -b 4096 # PEM - Privacy Enhanced Mail - Certificate Format RSA- Encryption Algorithm
ls /Users/rangakaranam/.ssh/id_rsa.pub
```

#### Create Resource Group, Storage Account and Storage Container

```
az group create -l westeurope -n In28minutesK8sResourceGroup
az storage account create -n In28minutesK8sStorageAccount -g In28minutesK8sResourceGroup -l westeurope --sku Standard_LRS
az storage container create -n devterraformstatestorage --account-name <<storage_account_name>> --account-key <<storage_account_key>>

```

#### Execute Terraform Commands

```
# comment backend
terraform init
terraform apply
# add backend
# terraform init with backend
terraform init -backend-config="storage_account_name=<<storage_account_name>>" -backend-config="container_name=<<storage_container_name>>" -backend-config="access_key=<<storage_account_key>>" -backend-config="key=<<k8s.environment.tfstate>>"

```


#### Set Up kubectl 

```
terraform output kube_config>~/.kube/config
```

#### Launch up 

```
kubectl proxy
open 'http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/overview?namespace=default'
```
