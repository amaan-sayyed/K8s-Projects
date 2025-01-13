# 📦 Three Tier Architecture Deployment on AWS EKS

Stan's Robot Shop is a sample microservice application you can use as a sandbox to test and learn containerised application orchestration and monitoring techniques. It is not intended to be a comprehensive reference example of how to write a microservices application, although you will better understand some of those concepts by playing with Stan's Robot Shop. To be clear, the error handling is patchy and there is not any security built into the application.

<!-- You can get more detailed information from my [blog post](https://www.instana.com/blog/stans-robot-shop-sample-microservice-application/) about this sample microservice application. -->

This sample microservice application has been built using these technologies:
- NodeJS ([Express](http://expressjs.com/))
- Java ([Spring Boot](https://spring.io/))
- Python ([Flask](http://flask.pocoo.org))
- Golang
- PHP (Apache)
- MongoDB
- Redis
- MySQL ([Maxmind](http://www.maxmind.com) data)
- RabbitMQ
- Nginx
- AngularJS (1.x)

<!-- The various services in the sample application already include all required Instana components installed and configured. The Instana components provide automatic instrumentation for complete end to end [tracing](https://docs.instana.io/core_concepts/tracing/), as well as complete visibility into time series metrics for all the technologies.

To see the application performance results in the Instana dashboard, you will first need an Instana account. Don't worry a [trial account](https://instana.com/trial?utm_source=github&utm_medium=robot_shop) is free. -->

<!-- ## Build from Source
To optionally build from source (you will need a newish version of Docker to do this) use Docker Compose. Optionally edit the `.env` file to specify an alternative image registry and version tag; see the official [documentation](https://docs.docker.com/compose/env-file/) for more information.

To download the tracing module for Nginx, it needs a valid Instana agent key. Set this in the environment before starting the build.

```shell
$ export INSTANA_AGENT_KEY="<your agent key>"
```

Now build all the images.

```shell
$ docker-compose build
```

If you modified the `.env` file and changed the image registry, you need to push the images to that registry

```shell
$ docker-compose push
``` -->

<!-- ## Run Locally
You can run it locally for testing.

If you did not build from source, don't worry all the images are on Docker Hub. Just pull down those images first using:

```shell
$ docker-compose pull
```

Fire up Stan's Robot Shop with:

```shell
$ docker-compose up
```

If you want to fire up some load as well:

```shell
$ docker-compose -f docker-compose.yaml -f docker-compose-load.yaml up
```

If you are running it locally on a Linux host you can also run the Instana [agent](https://docs.instana.io/quick_start/agent_setup/container/docker/) locally, unfortunately the agent is currently not supported on Mac.

There is also only limited support on ARM architectures at the moment. -->

<!-- ## Marathon / DCOS

The manifests for robotshop are in the *DCOS/* directory. These manifests were built using a fresh install of DCOS 1.11.0. They should work on a vanilla HA or single instance install.

You may install Instana via the DCOS package manager, instructions are here: https://github.com/dcos/examples/tree/master/instana-agent/1.9 -->

<!-- ## Kubernetes
You can run Kubernetes locally using [minikube](https://github.com/kubernetes/minikube) or on one of the many cloud providers.

The Docker container images are all available on [Docker Hub](https://hub.docker.com/u/robotshop/).

Install Stan's Robot Shop to your Kubernetes cluster using the [Helm](K8s/helm/README.md) chart.

To deploy the Instana agent to Kubernetes, just use the [helm](https://github.com/instana/helm-charts) chart. -->

<!-- ## Accessing the Store
If you are running the store locally via *docker-compose up* then, the store front is available on localhost port 8080 [http://localhost:8080](http://localhost:8080/)

If you are running the store on Kubernetes via minikube then, find the IP address of Minikube and the Node Port of the web service.

```shell
$ minikube ip
$ kubectl get svc web
```

If you are using a cloud Kubernetes / Openshift / Mesosphere then it will be available on the load balancer of that system. -->

<!-- ## Load Generation
A separate load generation utility is provided in the `load-gen` directory. This is not automatically run when the application is started. The load generator is built with Python and [Locust](https://locust.io). The `build.sh` script builds the Docker image, optionally taking *push* as the first argument to also push the image to the registry. The registry and tag settings are loaded from the `.env` file in the parent directory. The script `load-gen.sh` runs the image, it takes a number of command line arguments. You could run the container inside an orchestration system (K8s) as well if you want to, an example descriptor is provided in K8s directory. For End-user Monitoring ,load is not automatically generated but by navigating through the Robotshop from the browser .For more details see the [README](load-gen/README.md) in the load-gen directory.   -->

<!-- ## Website Monitoring / End-User Monitoring

### Docker Compose

To enable Website Monioring / End-User Monitoring (EUM) see the official [documentation](https://docs.instana.io/website_monitoring/) for how to create a configuration. There is no need to inject the JavaScript fragment into the page, this will be handled automatically. Just make a note of the unique key and set the environment variable `INSTANA_EUM_KEY` and `INSTANA_EUM_REPORTING_URL` for the web image within `docker-compose.yaml`.

### Kubernetes

The Helm chart for installing Stan's Robot Shop supports setting the key and endpoint url required for website monitoring, see the [README](K8s/helm/README.md). -->

<!-- ## Prometheus

The cart and payment services both have Prometheus metric endpoints. These are accessible on `/metrics`. The cart service provides:

* Counter of the number of items added to the cart

The payment services provides:

* Counter of the number of items perchased
* Histogram of the total number of items in each cart
* Histogram of the total value of each cart

To test the metrics use:

```shell
$ curl http://<host>:8080/api/cart/metrics
$ curl http://<host>:8080/api/payment/metrics
``` -->
---

## 🛠️ **Prerequisites**

Before starting, make sure you have:

- **AWS Account**
- **AWS CLI** installed
- **eksctl** installed
- **kubectl** installed
- **Helm** installed
- **SSH Client**

---

## 🚀 **Steps to Deploy the 3-Tier Architecture**

### **Step 1: Create an IAM User in AWS**

1. Log in to the **AWS Console**.
2. Search for **IAM** and navigate to the IAM Dashboard.
3. Go to **Users** → **Create User**.
4. Provide a name and select **Programmatic Access**.
5. Attach the **AdministratorAccess** policy (for learning purposes).
6. Download the **Access Key** and **Secret Key** as a `.csv` file.

---

### **Step 2: Create an EC2 Instance**

1. Go to the **EC2 Dashboard** in AWS.
2. Click **Launch Instance** and select an **Ubuntu** AMI.
3. Choose an instance type like `t2.medium`.
4. Configure security groups to allow **SSH** access.
5. Launch the instance and connect using **SSH**.

---

### **Step 3: Install Required Tools on the EC2 Instance**

After connecting to the EC2 instance, run the following commands:

#### **Install AWS CLI**

```bash
sudo apt update
sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

#### **Install eksctl**

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

#### **Install kubectl**

```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.28.3/2023-11-14/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl
export PATH=$HOME/bin:$PATH
kubectl version --client
```

#### **Install Helm**

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

---

### **Step 4: Set Up an EKS Cluster**

#### **Configure AWS Settings**

```bash
aws configure
```

#### **Create an EKS Cluster**

```bash
eksctl create cluster --name demo-cluster-three-tier-1 --region us-west-2
```

---

### **Step 5: Configure IAM OIDC Provider**

#### **Create an EKS Cluster**

```bash
export cluster_name=demo-cluster-three-tier-1
```

#### **Check if OIDC Provider Exists**

```bash
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d "/" -f5)
aws iam list-open-id-connect-providers | grep $oidc_id
```

#### **Associate OIDC Provider**

```bash
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```

---

### **Step 6: Set Up the AWS Load Balancer Controller**

#### **Download the IAM Policy**

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json
```

#### **Create an IAM Policy**

```bash
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```

#### **Create IAM Role**

```bash
eksctl create iamserviceaccount \
   --cluster demo-cluster-three-tier-1 \
   --namespace kube-system \
   --name aws-load-balancer-controller \
   --role-name AmazonEKSLoadBalancerControllerRole \
   --attach-policy-arn arn:aws:iam::<YOUR-AWS-ACCOUNT-ID>:policy/AWSLoadBalancerControllerIAMPolicy \
   --approve
```

---

### **Step 7: Deploy the ALB Controller**

#### **Add the Helm Repository**

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

#### **Deploy the ALB Controller**

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=demo-cluster-three-tier-1
```

---

### **Step 8: Deploy the EBS CSI Plugin**

#### **Create an IAM Role**

```bash
eksctl create iamserviceaccount \
   --name ebs-csi-controller-sa \
   --namespace kube-system \
   --cluster demo-cluster-three-tier-1 \
   --role-name AmazonEKS_EBS_CSI_DriverRole \
   --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
   --approve
```

#### **Install the EBS CSI Plugin**

```bash
eksctl create addon --name aws-ebs-csi-driver --cluster demo-cluster-three-tier-1 --service-account-role-arn arn:aws:iam::<AWS-ACCOUNT-ID>:role/AmazonEKS_EBS_CSI_DriverRole --force
```

---

### **Step 9: Deploy the Application Using Helm**

#### **Navigate to the Helm Chart Directory**

```bash
cd helm
kubectl create ns robot-shop
```

#### **Deploy the Helm Chart**

```bash
helm install robot-shop --namespace robot-shop .
```

#### **Check the Pods and Services**

```bash
kubectl get pods -n robot-shop
kubectl get svc -n robot-shop
```

#### **Apply Ingress**

```bash
kubectl apply -f ingress.yaml
```

---

### **Step 10: Delete the EKS Cluster**

```bash
eksctl delete cluster --name demo-cluster-three-tier-1 --region us-west-2
```

---

### **🔗 For a Detailed Step-by-Step Explanation**

For a more detailed and step-by-step guide, including explanations and troubleshooting tips, please check out my blog:
<br>[Step-by-Step Guide to 3-Tier Architecture Deployment on AWS EKS via Helm](https://amaansayyed.hashnode.dev/step-by-step-guide-to-3-tier-architecture-deployment-on-aws-eks-via-helm)

---

Feel free to contribute or raise issues to improve this guide. 😊