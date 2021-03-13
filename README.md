# docker.compose4EC2
this is a simple way to set up an EC2 instance and spinning 2 containers of mysql and wordpress using docker-compose

Pre-requirements:
Configuring the AWS CLI
A pem Key

## Security Group:
create security group
```bash
    aws ec2 create-security-group --group-name example --description "this is an example"
```
the output is the security group id for example: sg-03440f667143e100e 

now we need to add inbound rules(open ports), 22 so you can ssh into the instance and 8080 for wordpress to be accessible
```bash
  aws ec2 authorize-security-group-ingress --group-name example --protocol tcp --port 22 --cidr 0.0.0.0/0 
  aws ec2 authorize-security-group-ingress --group-name example --protocol tcp --port 8080 --cidr 0.0.0.0/0
```
	
show security group rules and get the sc id for example: sg-03440f667143e100e
```bash
  aws ec2 describe-security-groups --group-names example
```

## Amazon Machine Images (AMI):
get the image id for ubnuntu in my case:
Ubuntu Server 20.04 - ami-0d6aecf0f0425f42a
Ubuntu Server 18.04 - ami-0a0d71ff90f62f72a
	
## Launch a New EC2 Instance and waite:

launch a new instance with the ami,sc,type and key
```bash
  aws ec2 run-instances --image-id ami-0a0d71ff90f62f72a --security-group-ids sg-03440f667143e100e --instance-type t2.micro --key-name keyName
```

you can get a list of instances and filters them to check its running
```bash
  aws ec2 describe-instances --output table
```
or
```bash
  aws ec2 describe-instances --output json
```

get the Public Ip Address
```bash
  aws ec2 describe-instances --instance-ids i-0ad4ad0ba6eeea693 --query Reservations[0].Instances[0].PublicIpAddress
```
	
## SSH into the machine and configur it
```bash
ssh -i k.pem ubuntu@15.237.100.253
```

## Install Docker and Docker-compose on the Ubuntu Server:

update
```bash
  sudo apt update
```
Install Docker
```bash
  sudo apt install docker.io
```	
Start and Automate Docker
```bash
  sudo systemctl start docker
  sudo systemctl enable docker
```
Check Docker Version
```bash
  docker --version
```
	
Install docker-compose	
```bash
  sudo apt install curl
```
Download the Latest Docker Version
```bash  
  sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose	
```
Change File Permission
```bash
  sudo chmod +x /usr/local/bin/docker-compose
```
Check Docker Compose Version
```bash
  docker-compose --version
```

## Run the docker-compose
get the file from git and run the docker-compose
```bash
  git clone https://github.com/isaacTadela/docker.compose4EC2.git
  cd docker.compose4EC2/
  sudo docker-compose up
```
check the instance public ip on port 8080 to see the wordpress


## Terminate instance
Do not forget to terminate the instances, time is money
```bash
  aws ec2 terminate-instances --instance-ids i-0ad4ad0ba6eeea693
```
waite a few seconds for the EC2 to be terminated
```bash
  aws ec2 delete-security-group --group-name example
```

## p.s
In case you familiar with Terraform you can do all of the above in one command, 
all the explanations and of course the code in the following link