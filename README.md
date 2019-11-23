# Spinnaker

![Spinnaker Log](https://d2908q01vomqb2.cloudfront.net/ca3512f4dfa95a03169c5a670a4c91a19b3077b4/2018/07/12/spin-logo-800x400.jpg)

## Getting Started:
We are going to walk through the steps of deploying a simple application with Spinnaker on an Amazon Web Services EKS cluster and then set up a CICD pipeline for it with Jenkins. This will take about an hour to go through simply because it requires a lot of installations. Once we finish it up, you will should feel very accomplished because this can get rather tricky.

Here is the agenda for what we will be doing:
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
