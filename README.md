## Terraform
Installation Terraform
~~~sh
wget https://releases.STRINGicorp.com/terraform/0.12.18/terraform_0.12.18_linux_amd64.zip
unzip terraform_0.12.18_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform version
echo "alias t='terraform'" >> $HOME/.bashrc
~~~
\
Some helps
~~~sh
terraform --help
terraform --help plan
~~~
_ from (downloads): https://www.terraform.io/downloads.html

## Terraform in use
Credential file 'terraform.tfvars' must have couple strings:
~~~yml
# ^ file_name: terraform.tfvars

AWS_ACCESS_KEY="<SOME_HASH_CODE_KEY"
AWS_SECRET_KEY="<some_hash_code_first_key/<some_hash_code_second_key"
~~~
I create one instance of EC2 from AMI by my terraform definition file (correct 0.12 version)
\
If you use some old version of definition file you can fix it by command
~~~sh
terraform init
terraform 0.12upgrade
~~~
~~~py
# ^ file_name: terraform.code.tf

# vars.tf
variable "AWS_ACCESS_KEY" {
}

variable "AWS_SECRET_KEY" {
}

variable "AWS_REGION" {
  default = "us-east-2"
}

variable "AMIS" {
  type = map(string)
  default = {
    # *******************************************
    #   https://cloud-images.ubuntu.com/locator/ec2/
    #   US East (Ohio) => us-east-2
    #   OS        => Ubuntu Server 18.04 LTS (HVM), SSD Volume Type
    #   AMI_ID    => ami-0d5d9d301c853a04a
    #   AMI shortcut (AMAZON MACHINE IMAGE)
    # *******************************************
    us-east-2 = "ami-0d5d9d301c853a04a"
  }
}

# provider.tf
provider "aws" {
  access_key = var.AWS_ACCESS_KEY
  secret_key = var.AWS_SECRET_KEY
  region     = var.AWS_REGION
}

# instance.tf
resource "aws_instance" "TASK_SOME_NAME" {
  ami = var.AMIS[var.AWS_REGION]
  tags = {
    Name = "TASK"
  }
  instance_type = "t2.micro"
  provisioner "local-exec" {
    command = "echo ${aws_instance.TASK_SOME_NAME.private_ip} >> private_ips.txt"
  }
}

output "ip" {
  value = aws_instance.TASK_SOME_NAME.public_ip
}
~~~
After creating this file (terraform.code.tf) use commands
~~~sh
terraform init
terraform apply
# ^ to confirm/refuse type 'yes'/'no' in dialog
~~~
After this you can remove the instance(s) defined below:
~~~sh
terraform destroy
# ^ to confirm/refuse type 'yes'/'no' in dialog
~~~
## AWS CLI version 2
The IAM user to create the Kubernetes cluster must have the following permissions:
~~~lst
AmazonEC2FullAccess
AmazonRoute53FullAccess
AmazonS3FullAccess
IAMFullAccess
AmazonVPCFullAccess
~~~
\
Installation AWS cli Version 2
~~~sh
curl "https://d1vvhvl2y92vvt.cloudfront.net/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws2 --version
aws2 help
# ^ use alias fro simplest input: echo "alias aws='aws2'" >> ~/.bashrc
~~~
_ from: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
\
After creating user on the AWS service (IAM users) do next:
~~~sh
# ^ answer to the questions by input credentials and some fields 
# ~ region: us-east-2, ~ output: json
# ~ aws access key id: ...  ~ aws secret access key: ...
aws2 configure --profile default && cat ~/.aws/*
~~~
\
After this you will see output like this:
~~~yml
# ^ ~/.aws/config
[default]
region=us-east-2
output=json

# ^ ~/.aws/credentials
[default]
aws_access_key_id=<AWSACCESSKEYIDSTRING>
aws_secret_access_key=<AWSSECRETACCESSKEYSTRING>
~~~
\
Using Profiles with the AWS CLI
~~~sh
# ^ describe the ec2 instances
aws ec2 describe-instances --profile user01

# ^ also you can save it
aws ec2 describe-instances --profile user01 > ec2_inst_desc.json

# ^ export user profile
export AWS_PROFILE=user01
~~~
\
For example aws cli can use more then one profile
~~~yml
[default]
region=us-west-2
output=json

[profile user1]
region=us-east-1
output=text
~~~
Also you can set some parameters inline
~~~sh
aws configure set region us-west-2 --profile AWS_PROFILE  

# ^ and check the setting
aws configure get region --profile AWS_PROFILE

# output: us-west-2
~~~

