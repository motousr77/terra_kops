## Terraform
Installation Terraform
~~~sh
wget https://releases.hashicorp.com/terraform/0.12.18/terraform_0.12.18_linux_amd64.zip
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

## AWS CLI version 2
#### IAM user permission
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
~~~
_ from: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
\

After creating user on the AWS service (IAM users) do next:
~~~sh
# ^ answer to the questions by input credentials and some fields 
aws2 configure --profile default && cat ~/.aws/*
~~~
\
After this you will see output like this:
~~~yml
# ^ ~/.aws/config
[default]
region=us-east-2c
output=json

# ^ ~/.aws/credentials
[default]
aws_access_key_id=<AWSACCESKEYIAHASH>
aws_secret_access_key=<AWSSECRETACCESSKEYHASH>
~~~
\



