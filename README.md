# Microservice_Project

 

OnlineBoutique
19/01/2025
Your Name : M Ramesh
Microservice-E-Commerces
A microservice-based e-commerce project divides functionalities like user management, product catalog, shopping cart, order processing, and payment into independent services. Each service is scalable, deployable, and maintainable separately, communicating via REST APIs or message brokers. Databases are decentralized, ensuring better data isolation and performance. Tools like Docker and Kubernetes streamline deployment, while CI/CD pipelines ensure rapid updates. Security, monitoring, and analytics are integrated to enhance reliability and insights.
AWS_Part
Create a t2.large EC2 Instance using AMI image is Ubuntu_24.04 LTS image used this
Create a IAM user (eks_user) and add permissions policies  given below policies add one is create a inline policies

AmazonEC2FullAccess
AmazonEKS_CNI_Policy
AmazonEKSClusterPolicy
AmazonEKSWorkerNodePolicy
AWSCloudFormationFullAccess
IAMFullAccess
Add this Inline policies total policies are 7 

{
    "Version": "2012-10-17",
    "Statement": [
       
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "eks:*",
            "Resource": "*"
        }
    ]
}

Create a Access_Keys Secret_Key
Access_Key : xxxxxxxxxxxxxxxx
Secret_Key : xxxxxxxxxxxxxxxxxxxxxxxxxxxx
Mobaxtrem_Connection
Connection to Server
sudo su
apt update 
Create a  folder mkdir Script
vi command use 1.sh file create aws_cli & kubectl , ek_scli the cli commands
Given below the commands

AWSCLI

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

sudo apt install unzip

unzip awscliv2.zip

sudo ./aws/install

aws configure

KUBECTL

curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin

kubectl version --short --client

EKSCTL

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

And then save the file esc (:wq)
This file is not excitable file use this command chmod -x 1.sh (file name) then file colour is change
Run the file ./1.sh 
The multi commands run
Aws configure command use this connect the aws cli by the access_key & secret_key is given
Then aws cli connect are a eks cluster used this given blow command  
Create EKS_Cluster

                   eksctl create cluster --name=EKS-1 \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup
                      
Create a EKS Cluster and then add iam policies

               eksctl utils associate-iam-oidc-provider \
                 --region us-east-1 \
                --cluster EKS-1 \
                --approve

Create a Node group used this command 

            eksctl create nodegroup --cluster=EKS-1 \
                       --region=us-east-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=Prod_Key \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access

Jenkins_Setup
Install java 
apt install openjdk-17-jre-headless
Install Jenkins (jenkins.io)

          sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
          https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
          echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
           https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
           /etc/apt/sources.list.d/jenkins.list > /dev/null
           
           sudo apt-get update
           sudo apt-get install jenkins -y
           
Connect the jenkins server port :8080

Docker install on server

apt install docker.io  -y

Create a use and password
Dashboard of the jenkins plugins to available plugins 
Install plugins (Docker),(Docker Pipelines)
 Install plugins (Kubernetes),(Kubernetes Cli)
 
Config the plugins go to tools
Docker Install name  ( docker) auto install (latest) and apply , save
Docker credentials add the user name password  ID (docker-cred)add it credentials
Credentials add github user & password or token ID (git-cred) add to create
Create a Multibranch pipeline (Microserver-E-Commerces)
Plugins install Multibranch webhook
Generals go to Branch source add git , github repository and git credentials add and scan by webhook name (ramesh) generate token by script
https://github.com/ramesh1918s/ramesh.git
Webhook token
 http://18.206.120.55:8080/multibranch-webhook-trigger/invoke?token=ramesh
Go to github repo settings webhook then add the token  and http://18.206.120.55:8080/multibranch-webhook-trigger/invoke?token=ramesh
Json type and just add push add the webhook
Server run the pipelines some errors are given
The error troubleshooting errors for docker hub repo and images and script
Go to server terminal vi svc.yml add service account given below
Creating Service Account 

            apiVersion: v1
            kind: ServiceAccount
            metadata:
            name: jenkins
            namespace: webapps

Create a kubectl namespace 

kubectl create namespace webapps  
            kubectl apply -f svc.yml
            
Create Role for K8s by file role.yml

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]

 kubectl apply -f role.yml
 
vi bind.ym 

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins
kubectl apply -f bind.yml 
Generate token using service account in the namespace
Create Token
vi sec.yml 
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins

kubectl apply -f sec.yml -n webapps and then mysecret 

kubectl describe secret mysecretname -n webapps

Generate a secret token 
eyJhbGciOiJSUzI1NiIsImtpZCI6Ik94RWZQeHNQQXUxYVo0TmNLMGoyeXNyWWxlVmNoMDJHVmtFcGM2RUNBbVkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJ3ZWJhcHBzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im15c2VjcmV0bmFtZSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJqZW5raW5zIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMmU4ZjY3NzMtOGMxOC00NzBhLWJkYWUtZjU0YTlhOGIyNzFkIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OndlYmFwcHM6amVua2lucyJ9.a-z917ZaiTtqlmOr-khe4ye-FTIg4DaDKaxo8ivdVc6aZ_8fjl3nCWGocF2OzMMkLM1H-puUFlqNSCuydvFwvDuMHX0k0DPIqVAVK1CzhV8DQX890IiV4-QbGTt2m1uq13bVoAk5H6RXMEeBR-TRvlvmSZC0tJbYXmG-xyp0v11dX-q8_e3-hii27oqq_hkSfYqZ_ghhUy1uNlJUQj9iqphwcLJU4w5c0Jcu-oHlUfu5WnTIGYe7z8cjwcxONSe2SvrQ17ObZMfM1JCFv1YCR7g7OucbMQR_eTsBvZUUYI_nuSzosjPgSxrVDPGGkkbVSNrwql2utW73DXKVadRVQw

Go to Jenkins Server new pipeline create for ram (pipeline)
General for script go for script syntax then configure kubernetes cli credentials
Add the credentials and add secret text  add the service account token 
    eyJhbGciOiJSUzI1NiIsImtpZCI6Ik94RWZQeHNQQXUxYVo0TmNLMGoyeXNyWWxlVmNoMDJHVmtFcGM2RUNBbVkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJ3ZWJhcHBzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im15c2VjcmV0bmFtZSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJqZW5raW5zIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMmU4ZjY3NzMtOGMxOC00NzBhLWJkYWUtZjU0YTlhOGIyNzFkIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OndlYmFwcHM6amVua2lucyJ9.a-z917ZaiTtqlmOr-khe4ye-FTIg4DaDKaxo8ivdVc6aZ_8fjl3nCWGocF2OzMMkLM1H-puUFlqNSCuydvFwvDuMHX0k0DPIqVAVK1CzhV8DQX890IiV4-QbGTt2m1uq13bVoAk5H6RXMEeBR-TRvlvmSZC0tJbYXmG-xyp0v11dX-q8_e3-hii27oqq_hkSfYqZ_ghhUy1uNlJUQj9iqphwcLJU4w5c0Jcu-oHlUfu5WnTIGYe7z8cjwcxONSe2SvrQ17ObZMfM1JCFv1YCR7g7OucbMQR_eTsBvZUUYI_nuSzosjPgSxrVDPGGkkbVSNrwql2utW73DXKVadRVQw

ID k8s-token and  credentials add kubernetes endpoint add given below
https://63B402FC68BA6190735998B14D920C06.gr7.us-east-1.eks.amazonaws.com
  Cluster name (EKS-1)
Namespace (webapps)
Generate a script pipeline 
withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://63B402FC68BA6190735998B14D920C06.gr7.us-east-1.eks.amazonaws.com']]) {
    // some block
}
Add the script  ram pipeline script 
Full script are given below
Add and save run the multibranch pipeline successfully completed
pipeline {
    agent any

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://63B402FC68BA6190735998B14D920C06.gr7.us-east-1.eks.amazonaws.com']]) {
                    sh "kubectl apply -f deployment-service.yml"
                    
                }
            }
        }
        
        stage('verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://63B402FC68BA6190735998B14D920C06.gr7.us-east-1.eks.amazonaws.com']]) {
                    sh "kubectl get svc -n webapps"
                }
            }
        }
    }
}


The Completed the Project to access the port is load balance port 
For eg 

                          a7e746ea4bf464155b6a8677a02f52ea-384069033.us-east-1.elb.amazonaws.com



 




                    Successfully completed Project for Microservice-E-Commerces
   

                                      Thank You 😀
