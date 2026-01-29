# S-DOEA - Workshop 5 - Infrastructure as Code - Ansible


## Ansible - Part 1


The objective of this workshop is to automate the installation of Code-Server on a server

### Workshop
Provision a Ubuntu server for this exercise. You can use Terraform or manually
provision an instance on DigitalOcean's console.
Once you have provisioned, note the IP address, root user and SSH keys used.
Use these information to create an inventory file, inventory.yaml.
Write a playbook that will use the inventory.yaml file to configure the
server. The playbook should perform the following tasks

• Update the /lib/systemd/system/code-server.service file
with the code server password; change the following line


```
Environment=PASSWORD=__PLACEHOLDER__
```
with the password, assuming that the password is mypassword

```
Environment=PASSWORD="mypassword"
```

• Update the /etc/nginx/sites-available/code-server.conf
file with the domain code-<ipv4_address>.nip.io; change the line
with server_name to

```
server_name code-<ipv4_address>.nip.io;

```
• Use systemd module to restart nginx and code-server services. You
must also perform a daemon reload viz. set daemon_reload to yes.


### Test
Test your deployment by browsing to http://```<ip-address>```

### Submission
When you have completed this workshop, commit your work to the repository.
The instructor will clone your repository at the end

### Setup

1. Access your Digital Ocean account.


2. Create a Ubuntu Droplet

<br>
<img style="float: center;" src="./screens/ansible11.png">
<br>

 * Select Singapore as region
 * Select Ubuntu as the server Image v20.04 x64

<br>
<img style="float: center;" src="./screens/ansible12.png">
<br>

* Select cost saving server type (6 USD)

<br>
<img style="float: center;" src="./screens/ansible13.png">
<br>

* Choose the SSH authentication method and generate a fresh SSH key pair. Click the "New SSH Key" button, then follow the instructions provided on the right-hand side. Paste the contents of the "cat" command into the Digital Ocean text area.

<br>
<img style="float: center;" src="./screens/ansible14.png">
<br>

* Finalize the droplet

<br>
<img style="float: center;" src="./screens/ansible15.png">
<br>

3. Access the newly created ubuntu server

```
ssh root@<public ip address>
```

<br>
<img style="float: center;" src="./screens/ansible16.png">
<br>

4. Generate the PKI key pair on the logon server

```
ssh-keygen
```

<br>
<img style="float: center;" src="./screens/ansible17.png">
<br>

5. Add the public key content to the Digital Ocean account security section, name it as www-1

<br>
<img style="float: center;" src="./screens/ansible18.png">
<br>

<br>
<img style="float: center;" src="./screens/ansible19.png">
<br>
<br>
<img style="float: center;" src="./screens/Screenshot from 2024-04-18 09-12-57.png">
<br>

6. Install terraform IAC tool on the ubuntu server

```
sudo apt update

```

```
sudo apt install snap
```

```
sudo snap install terraform --classic
```


7. Check the terraform version

```
terraform --version
```
<br>
<img style="float: center;" src="./screens/ansible22.png">
<br>

8. Install Ansible on the Ubuntu instance

```
sudo apt-add-repository ppa:ansible/ansible

sudo apt update

sudo apt install ansible-core
```

<br>
<img style="float: center;" src="./screens/Screenshot from 2023-12-11 14-18-17.png">
<br>

9. Check the ansible version

```
ansible --version
```

### Implementation
a. Create a directory called workshop02 in your course repository.

b. Read Step 1 and Step 2 of the following blog ( do not need to run any command in this page, FYI)
<https://www.digitalocean.com/community/tutorials/how-to-set-up-the-code-server-cloud-ide-platform-on-ubuntu-20-04.html>

c. Change directory into the workshop02 folder

d. Create an ansible template file for server configuration. code-server.conf.j2

```
server {
    listen 80;
    listen [::]:80;

    server_name {{codeserver_domain}} {{ansible_host}};

    location / {
        proxy_pass http://127.0.0.1:8080/;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection upgrade;
        proxy_set_header Accept-Encoding gzip;
    }
}
```
e. Create an ansible template file code-server.service.j2

```
[Unit]
Description=code-server
After=nginx.service

[Service]
Type=simple
Environment=PASSWORD={{codeserver_password}}
ExecStart=/usr/bin/code-server --bind-addr 127.0.0.1:8080 --user-data-dir /var/lib/code-server --auth password
Restart=always

[Install]
WantedBy=multi-user.target
```
f. Create a terraform template file inventory.yaml.tftpl

```
all:
    vars:
        ansible_connection: ssh
        ansible_user: root
        ansible_ssh_private_key: ${ssh_private_key}
    hosts:
        codeserver:
            ansible_host: ${codeserver_ip}
            codeserver_domain: ${codeserver_domain}
            codeserver_password : ${codeserver_password}
```

g. Create a provider terraform script provider.tf

```
terraform {
  required_providers {
    digitalocean = {
        source = "digitalocean/digitalocean"
        version = "2.26.0"
    }
    local = {
        source = "hashicorp/local"
        version = "2.4.0"
    }
  }
}

provider digitalocean {
    token = var.do_token
}

```

h. Create a variables terraform script variables.tf

```
variable do_token {
    type = string
    sensitive = true
}

variable do_region {
    type = string
    default = "sgp1"
}

variable do_image {
    type = string
    default = "ubuntu-22-04-x64"
}

variable do_size {
    type = string
    default = "s-1vcpu-1gb"
}

variable do_ssh_key {
    type = string
    default = "www-1"
}

variable ssh_private_key {
    type = string
}

variable codeserver_password {
    type = string
}

```

i. Create a resources terraform script resources.tf

```
#ssh key
data "digitalocean_ssh_key" "www-1" {
    name = var.do_ssh_key
}

resource "digitalocean_droplet" "codeserver" {
    name = "codeserver"
    image = var.do_image
    region = var.do_region
    size = var.do_size

    ssh_keys = [ data.digitalocean_ssh_key.www-1.id ]
}

resource "local_file" "root_at_codeserver" {
    filename = "root@${digitalocean_droplet.codeserver.ipv4_address}"
    content = ""
    file_permission = "0444"
}

resource "local_file" "inventory" {
    filename = "inventory.yaml"
    content = templatefile("inventory.yaml.tftpl",{
        codeserver_ip = digitalocean_droplet.codeserver.ipv4_address
        ssh_private_key = var.ssh_private_key
        codeserver_domain = "code-server-${digitalocean_droplet.codeserver.ipv4_address}.nip.io"
        codeserver_password = var.codeserver_password
    })
    file_permission = "0444"
}

output codeserver_ip {
    value =  digitalocean_droplet.codeserver.ipv4_address
}

```

Remeber to generate the Digital Ocean Personal Access Token and export to the environment variable

```
export DO_PAT=<your DO PAT>
```

```
terraform init
```

```
terraform plan -var "do_token=${DO_PAT}" -var "ssh_private_key=/root/.ssh/id_rsa" -var "codeserver_password=password123456"
```

```
terraform apply -auto-approve -var "do_token=${DO_PAT}" -var "ssh_private_key=/root/.ssh/id_rsa" -var "codeserver_password=password123456"
```

Get the playbook.yml from the solution github repository

```
ansible-playbook playbook.yaml -i inventory.yaml
```

## Solution Repository URL

https://github.com/kenken64/aipc-jun2023/tree/main/workshop02
