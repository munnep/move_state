# Move a resource in the state file
This repository will show you an example on how to use the ```terraform state mv``` command to move a resource within a Terraform state file


This specific example does the following: 
- create 2 resources
- change the terraform code of 1 resource to a module
- alter the state file by using ```terraform state mv```


# Prerequisites

## Install terraform  
See the following documentation [How to install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)

# How to
- Clone the repository to your local machine
```
git clone https://github.com/munnep/move_state.git
```
- Change your directory
```
cd move_state
```
- Terraform initialize
```
terraform init
```
- Terraform apply
```
terraform apply
```
```
Terraform will perform the following actions:

  # null_resource.hello will be created
  + resource "null_resource" "hello" {
      + id = (known after apply)
    }

  # random_pet.name will be created
  + resource "random_pet" "name" {
      + id        = (known after apply)
      + length    = 4
      + separator = "-"
    }

Plan: 2 to add, 0 to change, 0 to destroy.
```
- create a directory for the module called module_random
```
mkdir module_random
```
- create a file ```module_random/main.tf``` with the following content
```
resource "random_pet" "name" {
  length    = "4"
  separator = "-"
}

output "name" {
  value = random_pet.name.id
}
```
- change the ```main.tf``` in the root module to the following 
```
module "random" {
  source = "./module_random"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${module.random.name}"
  }
}
```
- update module information
```
terraform get
```
- if you run ```terraform plan``` it will show the following
```
terraform plan
```
```
Terraform will perform the following actions:

  # random_pet.name will be destroyed
  # (because random_pet.name is not in configuration)
  - resource "random_pet" "name" {
      - id        = "severely-firmly-vital-octopus" -> null
      - length    = 4 -> null
      - separator = "-" -> null
    }

  # module.random.random_pet.name will be created
  + resource "random_pet" "name" {
      + id        = (known after apply)
      + length    = 4
      + separator = "-"
    }

Plan: 1 to add, 0 to change, 1 to destroy.
```
- Terraform wants to recreate the random_pet resource. We have to move the resource inside the state file to the module notation. Use the ```terraform state mv``` command to make this change
```
terraform state mv random_pet.name module.random.random_pet.name
```
- run ```terraform apply``` and this shouldn't make any changes
```
terraform apply
```
```
No changes. Your infrastructure matches the configuration.
```
- Destroy the resources
```
terraform destroy
```

