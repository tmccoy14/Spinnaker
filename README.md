# Spinnaker

![Spinnaker Log](https://d2908q01vomqb2.cloudfront.net/ca3512f4dfa95a03169c5a670a4c91a19b3077b4/2018/07/12/spin-logo-800x400.jpg)

## Getting Started:
We are going to walk through the steps of deploying a simple application with Spinnaker on an Amazon Web Services EKS cluster and then set up a CICD pipeline for it with Jenkins. This will take about an hour to go through simply because it requires a lot of installations. I will be referencing this tutorial from [Spinnaker](https://www.spinnaker.io/guides/tutorials/codelabs/hello-deployment/) but I wanted to created my own steps because they leave out some important detalis and troubleshooting advice. Once we finish this up, you should feel very accomplished because this can get rather tricky. Lets get started!

### Spinnaker
- [Create EC2 Instance](#create-ec2-instace)
- [Install Halyard](#install-halyard)
- [Set up K8s for Amazon EKS](#set-up-k8s-for-amazon-eks)
- [Choose Spinnaker Install Environment](#choose-spinnaker-install-environment)
- [Create External Storage](#create-external-storage)
- [Deploy Spinnaker](#deploy-spinnaker)

### Jenkins
- [Install Jenkins](#install-jenkins)
- [Configure Jenkins](#configure-jenkins)
- [Set Up Repo](#set-up-repo)

### Configure Spinnaker and Jenkins
- [Configure Spinnaker and Jenkins](#configure-spinnaker-and-jenkins)
---

## Create EC2 Instace
The first thing that we are going to do is spin up our Ubuntu EC2 instance. This can also be done locally if preferred, but I wanted to get all of this installed and working on its own machine. You can use all of the free configurations for the Ubuntu instance. Once it's configured ands spun up, ssh into it and we can begin the installation.

## Install Halyard
You should now be logged in to your new Ubuntu instance and ready to install Halyard. Halyard is a CLI tool that manages the lifecycle of your Spinnaker deployments.

Get the latest version of Halyard:
```
curl -O https://raw.githubusercontent.com/spinnaker/halyard/master/install/debian/InstallHalyard.sh
```
Install Halyard:
```
sudo bash InstallHalyard.sh
```
When prompted for a non-root user enter ```ubuntu```

Once the installation is over, run ```hal -v``` to ensure that it was installed properly

## Set Up K8s For Amazon EKS
Before we can set up our AWS EKS cluster, we need to install and configure the AWS CLI.

Updated the package repository:
```
sudo apt-get update
```
Install the AWS CLI:
```
sudo apt-get install awscli
```
Install Python PIP:
```
sudo apt-get install python3-pip
```
Install AWS CLI using PIP:
```
pip3 install awscli 
```
Configure your AWS credentials:
```
aws configure
```

Now you're ready to set up the AWS EKS cluster - ensure you have cloned this repository for the necessary yaml files.

This command will create a two-subnet VPC, IAM roles, instance profiles, security groups, and an EKS cluster 

(This will take about 15 minutes):
```
aws cloudformation deploy --stack-name spinnaker-managing-infrastructure-setup --template-file managing.yaml \
--parameter-overrides UseAccessKeyForAuthentication=false EksClusterName=spinnaker-cluster --capabilities CAPABILITY_NAMED_IAM
```

Once your patience has been tested, we need to make some environment variables for later use:
```
VPC_ID=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`VpcId`].OutputValue' --output text)
CONTROL_PLANE_SG=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`SecurityGroups`].OutputValue' --output text)
AUTH_ARN=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`AuthArn`].OutputValue' --output text)
SUBNETS=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`SubnetIds`].OutputValue' --output text)
MANAGING_ACCOUNT_ID=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`ManagingAccountId`].OutputValue' --output text)
EKS_CLUSTER_ENDPOINT=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`EksClusterEndpoint`].OutputValue' --output text)
EKS_CLUSTER_NAME=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`EksClusterName`].OutputValue' --output text)
EKS_CLUSTER_CA_DATA=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`EksClusterCA`].OutputValue' --output text)
SPINNAKER_INSTANCE_PROFILE_ARN=$(aws cloudformation describe-stacks --stack-name spinnaker-managing-infrastructure-setup --query 'Stacks[0].Outputs[?OutputKey==`SpinnakerInstanceProfileArn`].OutputValue' --output text)
```

Now, we are going to create an IAM role that can be assumed by Spinnaker:
```
aws cloudformation deploy --stack-name spinnaker-managed-infrastructure-setup --template-file managed.yaml \
--parameter-overrides AuthArn=$AUTH_ARN ManagingAccountId=$MANAGING_ACCOUNT_ID --capabilities CAPABILITY_NAMED_IAM
```




## Choose Spinnaker Install Environment

## Create External Storage

## Deploy Spinnaker
