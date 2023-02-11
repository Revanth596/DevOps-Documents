# Project Setup using below tools
1) Maven
2) Git Hub
3) Jenkins
4) Docker
5) Kubernetes

# Step-1 : Jenkins Server Setup #
1) Create Ubuntu VM using AWS EC2 <br/>
2) Enable SSH & 8080 Ports in Ec2 Security Group <br/>
3) Install Java & Jenkins using below commands <br/>
$ sudo apt-get update <br/>
$ sudo apt-get install default-jdk <br/>
$ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add - <br/>
$ sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list' <br/>
$ sudo apt-get update <br/>
$ sudo apt-get install jenkins <br/>
$ sudo systemctl status jenkins <br/>
3) Copy jenkins admin pwd
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
4) Open jenkins server in browser using VM public ip
           URL : http://public-ip:8080/
5) Create Admin Account & Install Required Plugins in Jenkins


# Step - 2 : Install Maven & Git in Jenkins #
1) install maven with below command <br/>
$ sudo apt install maven -y <br/> <br/>
2) install git with below command <br/>
$ sudo apt install git -y <br/> <br/>

# Step - 3 : Setup Docker in Jenkins #
1) install docker <br/>
$ curl -fsSL get.docker.com | /bin/bash <br/>

2) Add Jenkins user to docker group <br/>
$ sudo usermod -aG docker jenkins  <br/>

3) Restart Jenkins  <br/>
$ sudo systemctl restart jenkins <br/>

# Step-4 :: Create EKS Management Host in AWS #

1) Launch new EC2 VM ( Ubuntu )	  
2) Connect to machine and install kubectl using below commands  
	$ curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl <br/>
	$ chmod +x ./kubectl <br/>
	$ sudo mv ./kubectl /usr/local/bin <br/>
	$ kubectl version --short --client <br/>

3) Install AWS CLI latest version using below commands

	$ sudo apt install unzip <br/>
	$ cd  <br/>
	$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" <br/>
	$ unzip awscliv2.zip <br/>
	$ sudo ./aws/install <br/>
	$ aws --version <br/>

4) Install eksctl using below commands
	$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp <br/>
	$ sudo mv /tmp/eksctl /usr/local/bin <br/>
	$ eksctl version <br/>


# Step-5 :: Create IAM role & attach to EKS Management Host #

1) Create New Role using IAM service ( Select Usecase - ec2 )
	
2) Add below permissions for the role
		IAM - fullaccess <br/>
		VPC - fullaccess <br/>
		ec2 - fullaccess  <br/>
		cloudfomration - fullaccess  <br/>
		administrator - acces <br/>
		
3) Enter Role Name (eksroleec2)
4) Attach created role to EKS Management Host (Select EC2 => Click on Security => attach IAM role we have created)
  
# Step-6 :: Create EKS Cluster using eksctl #
Syntax: 

eksctl create cluster --name cluster-name  \
--region region-name \
--node-type instance-type \
--nodes-min 2 \
--nodes-max 2 \ 
--zones <AZ-1>,<AZ-2>

Example: $ eksctl create cluster --name ashokit-cluster1 --region us-east-1 --node-type t2.medium  --zones us-east-1a,us-east-1b

Note: Cluster creation will take 5 to 10 mins of time (we have to wait)

$ kubectl get nodes  

# Step-7 :: Install AWS CLI in JENKINS Server #

URL : https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html 

$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" <br/>
$ unzip awscliv2.zip <br/>
$ sudo ./aws/install <br/>
$ aws --version <br/>
 
# Step-8 :: Install Kubectl in JENKINS Server #
=> Execute below commands in Jenkins server
$ curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin 
$ kubectl version --short --client

# Step-9 :: Update EKS Cluster Config File in Jenkins Server #
1) Execute below command in Eks Management host & copy kube config file data
$ cat .kube/config 

2) Execute below commands in Jenkins Server and paste kube config file
$ cd /var/lib/jenkins 
$ sudo mkdir .kube 
$ sudo vi .kube/config 

3) check eks nodes 
$ kubectl get nodes 

Note: We should be able to see EKS cluster nodes here.

#####################################
#Step-10 : Create Jenkins Pipeline #
#####################################
