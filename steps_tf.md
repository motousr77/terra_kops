## Install AWS materials
~~~bash
sudo apt-get install python2-pip
sudo apt-get install python3-pip
sudo pip install awscli
sudo pip3 install awscli
~~~

## Install Kubectl commands
~~~sh
KUBECTL_BIN=kubectl
function install_kubectl {
    if [ -z $(which $KUBECTL_BIN) ]
       then
           curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/$KUBECTL_BIN
           chmod +x ${KUBECTL_BIN}
           sudo mv ${KUBECTL_BIN} /usr/local/bin/${KUBECTL_BIN}
    else
       echo "Kubectl is most likely installed"
    fi
 
}

install_kubectl 
~~~

## Install Kops commands
Kops latest release Version 1.15 WORKING ONLY IN LINUX KERNEL Version LESS then 4.9 ~ Warning!!!
\
In this case use Snap installer where current Kops Version is 1.17.0-alpha.1
~~~sh
snap find kops
sudo snap install kops
~~~
\
And if you have kernel verssio 4+ ( uname -r ) do this
\
_link https://github.com/kubernetes/kops/blob/master/docs/install.md#linux
\
Kubernetes documentation: https://kubernetes.io/docs/getting-started-guides/kops/
~~~sh
function install_kops {
    if [ -z $(which kops) ]
       then
           curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
           chmod +x kops-linux-amd64
           mv kops-linux-amd64 /usr/local/bin/kops
       else
           echo "kops is most likely installed"
       fi
}
 
install_kops
~~~

## Install Terraform commands
~~~bash
TERRAFORM_ZIP_FILE=terraform_0.11.7_linux_amd64.zip
TERRAFORM=https://releases.hashicorp.com/terraform/0.11.7
TERRAFORM_BIN=terraform
 
function install_terraform {
    if [ -z $(which $TERRAFORM_BIN) ]
       then
           wget ${TERRAFORM}/${TERRAFORM_ZIP_FILE}
           unzip ${TERRAFORM_ZIP_FILE}
           sudo mv ${TERRAFORM_BIN} /usr/local/bin/${TERRAFORM_BIN}
           rm -rf ${TERRAFORM_ZIP_FILE}
    else
       echo "Terraform is most likely installed"
    fi
 
}
 
install_terraform 
~~~

# Terraform materials
#### 1) Copy and paste this text to file named: terraform.tfvars
Please replace this three dots with your credentials from AWS console.
These credentials are related to the user you have just created in your AWS account.
~~~bash
vim terraform.tfvars
 ...
AWS_ACCESS_KEY="..."
AWS_SECRET_KEY="..."
...
:wq!
~~~

#### 2) Copy and paste text below to file named: terraform.code.tf
~~~py
# vars.tf
variable "AWS_ACCESS_KEY" {}
variable "AWS_SECRET_KEY" {}
variable "AWS_REGION" {
  default = "eu-central-1"
}
variable "AMIS" {
  type = "map"
  default = {
    # *******************************************
    # https://cloud-images.ubuntu.com/locator/ec2/
    #   Frankfurt => eu-central-1
    #   OS        => UBUNTU Xenial 16.04 LTS
    #   AMI_ID    => ami-245f7fcf
    #   AMI shortcut (AMAZON MACHINE IMAGE)
    # *******************************************
    eu-central-1 = "ami-415b7baa"
  }
}
 
# provider.tf
provider "aws" {
    access_key = "${var.AWS_ACCESS_KEY}"
    secret_key = "${var.AWS_SECRET_KEY}"
    region = "${var.AWS_REGION}"
}
 
# instance.tf
resource "aws_instance" "TASK_SOME_NAME" {
  ami = "${lookup(var.AMIS, var.AWS_REGION)}"
  tags = { Name = "TASK" }
  instance_type = "t2.micro"
  provisioner "local-exec" {
     command = "echo ${aws_instance.TASK_SOME_NAME.private_ip} >> private_ips.txt"
  }
}
output "ip" {
    value = "${aws_instance.TASK_SOME_NAME.public_ip}"
}
~~~

#### 3) If you have just created these two files:
~~~lst
terraform.tfvars
terraform.code.tf
~~~
#### 4) Now you can run:
~~~bash
terraform init
terraform apply
~~~

Once you managed to create your first instance (server/EC2) and you are not using it - please delete it!!!

#### 5) Delete/destroy your instances:
~~~bash
terraform destroy
~~~

# Start Kubernetes cluster - Kops command
Start up Kubernetes cluster by using kops command

Please keep in mind that flags:
~~~sh
--name=kops.some_name.com
--state=s3://kops.some_name.com
--dns-zone=kops.some_name.com
~~~
has to be adjusted to your domain name you are going to buy or get for free.
~~~bash
kops create cluster \
  --name=kops.some_name.com \
  --state=s3://kops.some_name.com \
  --authorization RBAC \
  --zones=eu-central-1a \
  --node-count=2 \
  --node-size=t2.micro \
  --master-size=t2.micro \
  --master-count=1 \
  --dns-zone=kops.some_name.com \
  --out=some_name_terraform \
  --target=terraform \
  --ssh-public-key=~/.ssh/<user_name>.pub
~~~

# Convert terraform v11 code procuded by kops to terraform v12
Convert terraform v11 code procuded by kops to terraform v12 by "terraform 0.12upgrade" command
~~~bash
cd /tmp/terraform_code
terraform init 
# Archlinux installed terraform in v12 
# and still kops is producing code in terraform v11
terraform plan
...errors ...
 
# terraform v11 code can be converted to terrafrom v12 
terraform 0.12upgrade
terraform plan
terraform apply    
~~~



