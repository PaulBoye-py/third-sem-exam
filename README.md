# AltSchool Third Semester Examination Repo

## Setup Details

1. Provision a webapp of your choosing with nginx/httpd frontend proxy and a database (mongo, postgresql etc) backend.
2. Provision the Socks Shop example microservice application - <https://microservices-demo.github.io/>

## Task Instructions

- Everything needs to be deployed using an Infrastructure as Code approach.
- In your solution please emphasize readability, maintainability and DevOps methodologies. We expect a clear way to recreate your setup and will evaluate the project decisions on:
  - Deploy pipeline
  - Metrics
  - Monitoring
  - Logging

- Use Prometheus as a monitoring tool
- Use Ansible or Terraform as the configuration management tool.
- You can use an IaaS provider of your choice.
- The application should run on Kubernetes.

---

## Requirements to run this repo

- A VM control node.
- AWS Account, IAM roles.

You will need programmatic access to interact with AWS outside of the AWS Management Console. Creating an IAM role creates temporary credentials that consist of an access key ID, a secret access key, which you will use to configure AWS CLI. Download and keep the `.csv` file.

---

## How to Setup the VM Control Node

This is the server that will be used to access Jenkins. Create an instance on any Cloud provider of your choice. Configure with the following specifications:

- 4GB Memory
- 2CPUs
- Set a Security group and create ingress rules with the following values:

    | **Type**   | **Protocol** | **Port Range** | **Source** |
    | --------   | ------------ | -------------- | ---------- |
    | Custom TCP | tcp          | 8080           | 0.0.0.0/0  |
    | SSH        | tcp          | 22             | 0.0.0.0/0  |
    | HTTPS      | tcp          | 443            | 0.0.0.0/0  |
    | HTTP       | tcp          | 80             | 0.0.0.0/0  |

Once created, ssh into the server. After this, fork this repository, and clone to your VM using this command:

```git
git clone https://github.com/{your-github-username}/third-sem-exam.git
```

cd into the repo with the command:

```git
cd third-sem-exam
```

In the repository, run this command to make the `script.sh` an executable file:

```sh
sudo chmod +x script.sh
```

Update the libraries and package manager repo, install all necessary dependencies, and start Jenkins:

```sh
./script.sh
```

Before moving to the next step, configure AWS CLI with the following command and insert the necessary information from the `.csv` file you downloaded from AWS IAM.

```sh
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
```

The server's public IP address will be shown after the infrastructure is finished, or you can get it from your cloud provider. SSH into jenkins with this url: <http://public-ip-address>:8080>. Jenkins will display the path to the password to unlock Jenkins. Run the command:

```sh
cat /directory-gotten-from-jenkins/
```

This will display the password to unlock Jenkins at  <http://public-ip-address>:8080>. You can log in to the server using this.

---

## Deploying with Jenkins

Once logged into Jenkins, you will be creating 2 pipelines. But before then, setup your github and AWS credentials on Jenkins.

- The first pipeline will create an EKS Cluster using the terraform configuration files in the [`k8s`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/k8s) file. Set up your Source Code Manager(SCM) as GitHub, select this repository, and select [`eks-Jenkinsfile`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-Jenkinsfile) as the `Jenkinsfile` and continue to build the deployment.

- The second pipeline will create the deployments for nginx-conroller, prometheus, and the apps using files in  [`nginx-ingress`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-ingress), [`nginx-controller`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-controller), [`prometheus`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/prometheus), [`sock-shop`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/sock-shop), and [`voting-app`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/voting-app). Set up your Source Code Manager(SCM) as GitHub, select this repository, and select [`Jenkinsfile`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/Jenkinsfile) as the `Jenkinsfile` and continue to build the deployment.

---

## Folder Structure

I have divided the terraform blocks into different files based on the actions they perform.

- The [k8s](https://github.com/PaulBoye-py/third-sem-exam/tree/main/k8s) folder contains the terraform files to provide my k8s cluster called `demo`. Under the `k8s` folder, there are 11 terraform files, each performing different tasks.

  - [`provider.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/provider.tf) file sets `aws` as my provider, with a corresponding region, allowing Terraform to be able to interact with `aws`.

  - [`vpc-igw.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/vpc-igw.tf) file creates a VPC on `aws` called `main` and creates an internet gateway for my `main` vpc, allowing communication between my vpc and the internet.

  - [`my-subnets.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/my-subnets.tf) creates 2 private subnets, and 2 public subnets within `main` vpc.

  - [`nat-gateway.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/nat-gateway.tf) creates a `nat` gateway for communication between between instances in my `private subnets` and external services.

  - [`routes.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/routes.tf) creates private and public route tables for my `main` vpc. It also associates the private subnets with the private route tables, and public subnets with the public route tables.

  - [`main-eks.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/main-eks.tf) creates my eks cluster called `demo`, and attaches an IAM role policy to it.

  - [`eks-nodes.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/eks-nodes.tf) creates the nodes within my `demo` cluster and configures them.

  - [`iam-oidc.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/iam-oidc.tf) provides an IAM OpenID Connect provider.

  - [`iam-test.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/iam-test.tf) provides and attaches IAM roles and policies.

  - [`eks-iam-autoscaler.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/eks-iam-autoscaler.tf) contains configuration for autoscaling the cluster.

  - [`jenkins`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/jenkins)- I ended up merging all the Jenkins pipeline code to a central file in the root directory.

- The [nginx-ingress](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-ingress) contains the nginx ingress configuration files for the 2 applications which were deployed.

  - [`ingress-rule.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-ingress/ingress-rule.tf) contains the k8s nginx ingress for the `sock-shop` and `azure-vote-front` frontend services which were gotten online.

  - [`ingress-rule.yaml`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-ingress/ingress-rule.yaml) is the same as above, just used it to test locally on minikube.

  - [`provide.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-ingress/provide.tf) interacts with the resources provided by k8s.

- The [nginx-controller](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-controller) contains files create the nginx controller.

  - [`helm-nginx.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-controller/helm-nginx.tf) creates a k8s namespace called `nginx-namespace`, an instance of a helm chart running in a my k8s `main` cluster.

  - [`providers-nginx.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-controller/providers-nginx.tf) interacts with the resources provided by k8s.

  - [`values.yaml`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-controller/values.yaml) contains the default config for my nginx controller.

- The [prometheus](https://github.com/PaulBoye-py/third-sem-exam/tree/main/prometheus) contains the files to create and setup prometheus.

  - [`helm-prone`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/helm-prome.tf) creates a k8s namespace called `kube-namespace`, an instance of a helm chart running in a my k8s `main` cluster.

  - [`providers-prome.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/providers-prome.tf) interacts with the resources provided by k8s.

  - [`values.yaml`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/values.yaml) contains the configurations for Helm, including the default desired states.

- The [sock-shop](https://github.com/PaulBoye-py/third-sem-exam/tree/main/sock-shop) contains the `yaml` file for the deployment of the `sock-shop` app and its associated services.

- The [voting-app](https://github.com/PaulBoye-py/third-sem-exam/tree/main/voting-app) contains the `yaml` file for the deployment of the `Azure Voting App` and its associated services.

- [eks-Jenkinsfile](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-Jenkinsfile) creates the pipeline for creating and destroying the k8s clusters.

- [Jenkinsfile](https://github.com/PaulBoye-py/third-sem-exam/blob/main/Jenkinsfile) creates the pipeline for creating the deployments for nginx-conroller, prometheus, and the apps.

- [script.sh](https://github.com/PaulBoye-py/third-sem-exam/blob/main/script.sh) installs necessary packages and repos needed for the Jenkins server to run.

---

## Resources that helped me

- [AltSchool Africa LMS](https://www.altschoolafrica.com/)

- [How to request an SSL Certificate from ACM.](https://youtu.be/RRdYFwlCHic)

- [Jenkins for beginners.](<https://youtu.be/8IWH1cYVZt4> )

- [Amazon Elastic Kubernetes Service (EKS) with Terraform.](<https://www.youtube.com/playlistlist=PLiMWaCMwGJXkbN7J_j3qFEZVBacdoYCPJ> )

- [Terraform Documentation](https://developer.hashicorp.com/terraform)

- [Jenkins Documentation](https://www.jenkins.io/doc/)

- [AWS Documentation](https://docs.aws.amazon.com/)

- [Helm Documentation](https://helm.sh/docs/)

- [Prometheus Documentation](https://prometheus.io/docs/introduction/overview/)

- My instructors in AltSchool - Abu Ango, Kunrad, Val.

- My friends and circle members in AltSchool - Tuyo, Teddy, Pat, Mayowa, Dami.
