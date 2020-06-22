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

If you cat aws_keys.yml file, you will see its encrypted:

```
thompsonl@thompsonl-VirtualBox:~/Desktop/AWS_Ansible_Config_2$ cat aws_keys.yml 
$ANSIBLE_VAULT;1.1;AES256
35346539363230313434626239306263333764383237633537663830343935623030663530326632
3032336466393666633432343039653736343334363163340a393837613166353033373961393132
64373265353564616465323365383631633736653933323864653130323563656235383330353935
3261623063363734390a353637663835383665366434303633653430353561626165613832623931
64313030356539396635356433343035636665383835353538613463343435393330396663323737
35343835653434653839653233356339306532646434623430393537626637353032386462383633
30343336663434373961373430366563373738303934613635666236643339653062326135373031
35623933636538366163353639613134333035323864356638626336323030643963623233613334
3033

```

To edit the aws_keys.yml via ansible-vault:

```
> ansible-vault edit aws_keys.yml 
Vault password: (provide your vault password)
```

### Setting up the hosts file for Ansible

```
> vi hosts
```

Contents inside hosts file to handle new EC2 instance

```
[local]
localhost

[webserver]
localhost
```
### Building the AWS EC2 instance via Ansible

Create a new file called aws_provisioning.yml with the following:

```
---
- hosts: local
  connection: local
  gather_facts: False
  vars:
    instance_type: t2.micro
    security_group: webserver_ansible_sg
    image: ami-b63769a1
    keypair: ansible_lennoxt
    region: us-east-1
    count: 1
  vars_files:
    - aws_keys.yml
```

### Creating the EC2 security group

Add the following code to the aws_provisioning.yml file

```
  tasks:
      - name: Create a security group
        ec2_group:
          name: "{{ security_group }}"
          description: The ansible webservers security group
          region: "{{ region }}"
          aws_access_key: "{{ ec2_access_key }}"
          aws_secret_key: "{{ ec2_secret_key }}"
          rules:
            - proto: tcp
              from_port: 22
              to_port: 22
              cidr_ip: 0.0.0.0/0
            - proto: tcp
              from_port: 80
              to_port: 80
              cidr_ip: 0.0.0.0/0
            - proto: tcp
              from_port: 443
              to_port: 443
              cidr_ip: 0.0.0.0/0
          rules_egress:
            - proto: all
              cidr_ip: 0.0.0.0/0
          vpc_id: vpc-8ed221e9
```
Notes: vpc_id is the VPC of an existing environment already created or it could be default vpc

### Creating & Launching the AWS EC2 instance

```
      - name: Launch the new EC2 Instance
        ec2:
          aws_access_key: "{{ ec2_access_key }}"
          aws_secret_key: "{{ ec2_secret_key }}"
          group: "{{ security_group }}"
          instance_type: "{{ instance_type }}"
          image: "{{ image }}"
          wait: yes
          wait_timeout: 500
          region: "{{ region }}"
          keypair: "{{ keypair }}"
          count: "{{ count }}"
          vpc_subnet_id: subnet-cf2372b9
        register: ec2
```
