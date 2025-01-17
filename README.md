# Example: Using Terraform network mirror for providers

This repo will show you how to have a static web server hosting your terraform providers so you can download them from this webserver instead of from the internet itself

For more details about Terraform Network Mirror [see the official HashiCorp documentation](https://www.terraform.io/cli/config/config-file#cli-configuration-file-terraformrc-or-terraform-rc). 

This repo uses Vagrant to create 2 virtual machines.

Vagrant virtual machine: **nginx**
- ubuntu virtual machine starts
- Nginx webserver will be installed, configured and started
- Terraform will be installed
- Terraform will download the AWS provider from the internet and store it under ```/vagrant/terraform_file_mirror```

Vagrant virtual machine: **tf**
- ubuntu virtual machine starts
- Terraform will be installed
- A terraform CLI configuration file will be placed on the server which will use the Nginx Webserver to download terraform provider from

After this you will manually login to the Vagrant virtual machine **tf** and initialize terraform.  
You will see the download of the provider happening from the nginx virtual machine instead of the public internet

# Prerequisites

## Vagrant
Vagrant [See documentation](https://www.vagrantup.com/docs/installation)  
Virtualbox [See documentation](https://www.virtualbox.org/wiki/Downloads)

## SSL certificate
- You must configure a FQDN in your DNS that points to the internal ip address ```192.168.56.33```. This address is configured in Vagrant and you will use it for the webserver. 
- Valid SSL certificates for the FQDN. 

If you don't have valid SSL certificates you can create one for free using Let's Encrypt. This repository includes an instruction on how to do this. [See documentation](nginx_create_certificate/README.md) This documentation will use Vagrant to create a server that can be used to create the SSL certificates. If you have SSL certificates you can skip this manual.    

Your SSL certificates should be placed in the ```config_files``` folder
as:
```
fullchain.pem 
privkey.pem; 
```

# How to

- Clone the repository to your local machine
```
git clone https://github.com/munnep/provider_network_mirror.git
```
- Go to the directory
```
cd provider_network_mirror
```
- copy your SSL certificates to the directory ```config_files```. Note the filenames you should use. 
```
fullchain.pem
privkey.pem
```
- edit the file ```config_files/terraformrc_example``` to match your FQDN in the url
- start the Vagrant virtual machine
```
vagrant up
```
- login to the virtual machine **tf**
```
vagrant ssh tf
```
- optional   
Set terraform LOG to DEBUG mode. You can then see clearly with the terraform initialization where the downloaded provider is coming from
```
export TF_LOG=DEBUG
```
- go to the ```/vagrant``` directory where the provider file is found
```
cd /vagrant
```
- Terraform initialize
```
terraform init
```
- sample output with DEBUG level. See the GET with your FQDN
```
2022-01-06T18:41:12.899Z [DEBUG] GET https://patrick.bg.hashicorp-success.com/registry.terraform.io/hashicorp/aws/3.70.0.json
- Installing hashicorp/aws v3.70.0...
- Installed hashicorp/aws v3.70.0 (verified checksum)
```
- exit out of the vagrant machine
```
exit
```
- destroy the vagrant machine
```
vagrant destroy
```


# done
- [x] create vagrant machine for nginx webserver to host the aws provider 
    - [x] install nginx
    - [x] install terraform
    - [x] download the provider with terraform from the internet
    - [x] configure certificates
    - [x] install terraform
    - [x] download the provider with terraform from the nginx webserver

# to do


