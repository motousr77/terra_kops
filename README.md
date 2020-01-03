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

