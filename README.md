# AWS Ansible Build Manage

### Prerequisites
* Make an AWS Account
* Install Ansible and Ansible EC2 module dependencies
* Create an IAM role and obtain your access and secret keys
* Generate a public/private key pair

### Objective using Ansible
* Create a security group for the environment and add appropriate rules
* Launch an EC2 instance based on the type and region
* Create a ansible playbook to create an AWS EC2 instance
* Create a ansible playbook to stop an AWS EC2 instance
* Create a ansible playbook to terminate an AWS EC2 instance

### Installation of Ansible & Dependencies on Ubuntu
```
> sudo apt install python
> sudo apt install python-pip
> pip install python-boto python-boto3 ansible

```

#### Note: Both boto & boto3 packages are needed 

### Storing your AWS Access & Secret Keys using Ansible vault

after creating the IAM account and creating an IAM user that have access to EC2 instances. Need to store the AWS access/secret keys.

```
> ansible-vault create aws_keys.yml

```
Inside the aws_keys.yml file contains the following:

```
> ec2_access_key: <AWS ACCESS KEY>
> ec2_secret_key: <AWS SECRET KEY>
```
