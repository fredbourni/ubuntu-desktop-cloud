# Ubuntu Desktop Remote VM Automation

## Description

[Ansible](https://www.ansible.com/) playbooks for creating virtual machines running
[Ubuntu Desktop](https://ubuntu.com/download/desktop) 20.04 with remote desktop (NoMachine) on public clouds.
The included [remote](roles/remote/tasks/main.yml) role is quite opinionated with my favorite tools pre-installed,
a dark theme and everything I enjoy with Ubuntu for having fun with the Cloud.

The remote desktop software in use is: [NoMachine](https://www.nomachine.com/). NoMachine is an amazing
software which is free for personal use.
If you use this in a business environment, please pay for licenses and/or leverage their enterprise solutions. 

## Supported Cloud Providers

- Amazon Web Services
- Microsoft Azure
- Google Cloud Platform
- DigitalOcean

## How To

1. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
2. Setup you favorite Cloud credentials.
3. Run the ansible commands for your favorite Cloud provider (see examples below).
4. [Install NoMachine](https://www.nomachine.com/download) and use its client to connect to your VM.
    1. Use the external IP assigned to your VM for the host.
    2. Use the user/pass given in your ansible commands (_main_user_ and _main_pass_).
5. NoMachine will ask you to create a display, say yes.
6. Have fun.

## What if I do not like your specific set of packages?

To get a default Ubuntu setup without any extra software installed, simply skip the _package_ tag:
```
ansible-playbook --skip-tags package [...]
```

You may also skip any other tags in the [main tasks YAML](roles/remote/tasks/main.yml) file. 

## Google Cloud Platform (GCP)

### Requirements

- [Python Google Authentication Library](https://pypi.org/project/google-auth/)
- [Python HTTP requests library](https://pypi.org/project/requests/)
- [GCP project](https://cloud.google.com/resource-manager/docs/creating-managing-projects)
- [Service account](https://cloud.google.com/iam/docs/creating-managing-service-accounts) with proper permissions and JSON key 

### Example
```
ansible-playbook gcp.yml -v --extra-vars "region=northamerica-northeast1 zone=a project=remote sa_key=service-account-key.json main_user=yourusername main_pass='p4ssw0rd'"
```

### Variables

#### Mandatory

- **main_user** (main username)
- **main_pass** (main user password)
- **project** (GCP project)
- **region** (GCP region) 
- **sa_key** (service account JSON key path)
- **zone** (GCP zone)

#### Optional

- **address** (ip address object name)
- **disk** (disk object name)
- **gcp_cred_kind** (authentication for creating objects)
- **firewall** (firewall object name)
- **reboot** (skip reboot if set to false)  
- **timezone** (timezone name, ex: America/Toronto)  
- **vm** (virtual machine object name)
- **vm_size** (hard disk size in GB)
- **vm_type** (instance type)

## Amazon Web Services (AWS)

### Requirements

- [EC2 key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#prepare-key-pair)
- [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) (default one is OK)

### Example
```
export AWS_ACCESS_KEY_ID='AAAA000AAAAAA0AAAAA0'
export AWS_SECRET_ACCESS_KEY='aa0aaaa0aa00aaa00aa00a0aaa0aaaaaaaaa0aa0'

ansible-playbook aws.yml -v -u ubuntu --private-key ~/.ssh/private.pem --extra-vars "region=ca-central-1 keypair=remote vpc_id=vpc-aa0000a0 main_user=yourusername main_pass='p4ssw0rd'"
```

### Variables

#### Mandatory

- **keypair** (SSH key pair name)
- **main_user** (main username)
- **main_pass** (main user password)
- **region** (AWS region)
- **vpc_id** (vpc object id)


#### Optional

- **image** (AWS AMI id)
- **instance_type** (instance type)
- **reboot** (skip reboot if set to false)
- **security_group_name** (security group object name)
- **timezone** (timezone name, ex: America/Toronto)

## Microsoft Azure

### Requirements

- [Resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups)
- [Azure AD application and a service principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal)
- SSH public/private keys

### Example
```
export AZURE_CLIENT_ID='0000a000-a000-0000-00a0-aa000000aa00'
export AZURE_SECRET='-secret-'
export AZURE_SUBSCRIPTION_ID='000000aa-0aa0-0000-aa0a-a00a000000aa'
export AZURE_TENANT='000a0aa0-0000-0a0a-aa0a-000a0aaaa00a'

ansible-playbook azure.yml -v --user=azadmin --private-key ~/.ssh/key --extra-vars "resource_group=remoterg storage_account=remotesa pub_key=~/.ssh/key.pub main_user=yourusername main_pass='p4ssw0rd'"
```

### Variables

#### Mandatory

- **main_user** (main username)
- **main_pass** (main user password)
- **pub_key** (SSH public key file path)
- **region** (Azure region)
- **resource_group** (resource group object name)
- **storage_account** (storage account object name, needs to be unique across azure so pick wisely)

#### Optional

- **admin_user** (admin username)
- **disk_size** (disk size in GB)
- **image_offer** (os image offer name)
- **image_publisher** (os image publisher name)
- **image_sku** (os image SKU)
- **image_version** (os image version)
- **network_interface** (network interface object name)
- **public_ip** (public IP object name)
- **reboot** (skip reboot if set to false)  
- **security_group** (security group object name)
- **storage_type** (storage type name)
- **subnet** (subnet object name)
- **subnet_cidr** (private subnet CIDR)
- **timezone** (timezone name, ex: America/Toronto)
- **virtual_network** (virtual network object name)
- **vm** (vm object name)
- **vm_size** (instance type)


## Digital Ocean

### Requirements

- [Access token](https://www.digitalocean.com/docs/apis-clis/api/create-personal-access-token/)
- [Account SSH keys](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/to-account/)


### Example
```
export DO_OAUTH_TOKEN='aaa0000000000a0a0a000a00aaa000aaaa0a0000a0a00aaa0a00a0000a000aaa'

ansible-playbook do.yml -v --user=root --private-key ~/.ssh/key --extra-vars "region=tor1 ssh_key_id=000000000 main_user=yourusername main_pass='p4ssw0rd'"
```

### Variables

#### Mandatory

- **main_user** (main username)
- **main_pass** (main user password)
- **pub_key** (SSH public key file path)
- **region** (DigitalOcean region)
- **ssh_key_id** (global SSH key ID)

#### Optional

- **image** (Digital Ocean OS image name)
- **reboot** (skip reboot if set to false)  
- **timezone** (timezone name, ex: America/Toronto)
- **vm** (vm object name)
- **vm_size** (vm size id)
