# ansible-aws-elasticsearch

### Requirements:

- Ansible
- boto
- AWS admin access

### AWS credentials:

Ansible uses python-boto library to call AWS API, and boto needs AWS credentials in order to perform all the functions. There are many ways to configure your AWS credentials. The easiest way is to crate a .boto file under your user home directory:
```shell
vim ~/.boto
```
Then add the following:
```shell
[Credentials]
aws_access_key_id = <your_access_key_here>
aws_secret_access_key = <your_secret_key_here>
```

### To use this Role:

Edit the `group_vars/all.yml` vars file:
```yaml
cluster: elasticsearch

# Tags
tags:
  Name: "{{ cluster }}"

# ec2_key
key_name: "devops-key"
ssh_key_location: ~/.ssh/id_rsa.pub 

# VPC
vpc_region: us-east-1
vpc_name: "{{ cluster }}-vpc"
vpc_cidr_block: 172.22.0.0/16
vpc_subnets:
  - cidr: 172.22.1.0/24
    az: "{{ vpc_region }}a"
    resource_tags: { "Name":"{{ cluster }}" }
  - cidr: 172.22.2.0/24
    az: "{{ vpc_region }}c"
    resource_tags: { "Name":"{{ cluster }}" }
sg_name: "{{ cluster }}-security-group"

# EC2
image: ami-ff427095 # ubuntu trusty 14.04
instances_count: 1 # count per one subnet
instances_type: t2.micro
instances_data_size: 10
instances_device_name: /dev/sdb

# LB
lb_name: "{{ cluster }}-load-balancer"

# ElasticSearch
es_mount_point: /data
es_cluster_name: "{{ cluster }}"
es_data_dir: "{{ es_mount_point }}/elasticsearch/data"
```

### Create aws environment

```shell
ansible-playbook -i ec2.py create_project.yml
```

If you want download existed project from git you should provide `git_url` variable
```shell
ansible-playbook -i ec2.py create_project.yml -e "git_url=<git url>"
