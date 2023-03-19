# AltSchool Third Semester Examination Repo

## Folder Structure

I have divided the terraform modules and files into different files based on the actions they perform.

- The [`k8s`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/k8s) folder contains the terraform files to provide my k8s cluster called `demo`. Under the `eks-cluster` folder, there are 11 terraform files, each performing different tasks.

  - [`provider.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/provider.tf) file sets `aws` as my provider, with a corresponding region, allowing Terraform to be able to interact with `aws`.

  - [`vpc-igw.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/vpc-igw.tf) file creates a VPC on `aws` called `main` and creates an internet gateway for my `main` vpc, allowing communication between my vpc and the internet.

  - [`my-subnets.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/my-subnets.tf) creates 2 private subnets, and 2 public subnets within `main` vpc.

    -[`nat-gateway.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/nat-gateway.tf) creates a `nat` gateway for communication between between instances in my `private subnets` and external services.

  - [`routes.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/routes.tf) creates private and public route tables for my `main` vpc. It also associates the private subnets with the private route tables, and public subnets with the public route tables.

  - [`main-eks.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/k8s/main-eks.tf) creates my eks cluster called `demo`, and attaches an IAM role policy to it.

  - [`7-nodes.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/7-nodes.tf) creates the nodes within my `demo` cluster and configures them.

  - [`8-iam-oidc.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/8-iam-oidc.tf) provides an IAM OpenID Connect provider.

  - [`9-iam-test.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/9-iam-test.tf) provides and attaches IAM roles and policies.

  - [`10-iam-autoscaler.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/10-iam-autoscaler.tf) contains configuration for autoscaling the cluster.

  - [`jenkins`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/eks-cluster/jenkins)- I ended up merging all the Jenkins pipeline code to a central file in the root directory.

- The [ingress-rule](https://github.com/PaulBoye-py/third-sem-exam/tree/main/ingress-rule) contains the nginx ingress configuration files for the 2 applications which were deployed.

  - [`ingress-rule.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/ingress-rule/ingress-rule.tf) contains the k8s nginx ingress for the `sock-shop` and `azure-vote-front` frontend services which were gotten online.

  - [`ingress-rule.yaml`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/ingress-rule/ingress-rule.yaml) is the same as above, just used it to test locally on minikube.

  - [`provide.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/ingress-rule/provide.tf) interacts with the resources provided by k8s.

- The [`nginx-controller`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-controller) contains files create the nginx controller.

  - [`helm-nginx.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-controller/helm-nginx.tf) creates a k8s namespace called `nginx-namespace`, an instance of a helm chart running in a my k8s `main` cluster.

  - [`providers-nginx.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/nginx-controller/providers-nginx.tf) interacts with the resources provided by k8s.

  - [`values.yaml`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/nginx-controller/values.yaml) contains the default config for my nginx controller.

- The [`prometheus`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/prometheus) contains the files to create and setup prometheus.

  - [`helm-prone`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/helm-prome.tf) creates a k8s namespace called `kube-namespace`, an instance of a helm chart running in a my k8s `main` cluster.

  - [`providers-prome.tf`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/providers-prome.tf) interacts with the resources provided by k8s.

  - [`values.yaml`](https://github.com/PaulBoye-py/third-sem-exam/blob/main/prometheus/values.yaml) contains the configurations for Helm, including the default desired states.

- The [`sock-shop`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/sock-shop) contains the `yaml` file for the deployment of the `sock-shop` app and its associated services.

- The [`voting-app`](https://github.com/PaulBoye-py/third-sem-exam/tree/main/voting-app) contains the `yaml` file for the deployment of the `Azure Voting App` and its associated services.

- The [`terraform-template`] file was used to test locally.

- [`eks-Jenkinsfile`] creates the pipeline for creating and destroying the k8s clusters.

- [`Jenkinsfile`] creates the pipeline for creating the deployments for nginx-conroller, prometheus, and the apps.
