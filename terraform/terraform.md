---
layout: default
title: Terraform
has_children: true
---

# Terraform

## Install on Ubuntu

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
## Write Ansible inventory with TF template plugin




## Write Ansible inventory with TF inventory plugin

It would be nice if this worked but it is barely usable. If you want to ry then install the [terraform-inventory](https://github.com/adammck/terraform-inventory) plugin. It is binary that should be somewhere in your path.

```json
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}

resource "null_resource" "ansible_inventory" {
  provisioner "local-exec" {
    command = "terraform-inventory --list ./ > inventory.json"
  }
}
```

