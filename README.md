# move_state
move statefile

# Goal:
Start with 1 folder with multiple state, end with 2 folder, separate state and module.
Description:
We will create 1 folder, and then we will separate into 2 different projects.
Instructions:
Create 1 folder with random_pet and null provider

- create a ```main.tf``` with the following content
```
resource "random_pet" "name" {
 length    = "4"
 separator = "-"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${random_pet.name.id}"
  }
}
```
- run terraform init
```
terraform init
```
- run terraform apply
```
terraform apply
```
- you should now have a state file
- create a directory for the module called module_random
```
mkdir module_random
cd module
```
- create a file ```maint.tf``` under the module_random folder with the following content
```
resource "random_pet" "name" {
 length    = "4"
 separator = "-"
}
```
- change the ```main.tf``` in the root module to the following content
```
module "random" {
  source = "./module_random"
}

resource "null_resource" "hello" {
  provisioner "local-exec" {
    command = "echo Hello ${random_pet.name.id}"
  }
}

```



Run terraform apply, you should end with a new repo created, and state locally.

The goal of this lab is move the random_pet into a separate project.

Hints:
move state
modules



Create 1 folder for sample code, run terraform init / apply
Create a folder for random_pet
Make code for a module
Update code to use module
Terraform init
Use terraform state mv to rename the state
Terraform apply should say the nothing to be created, state should persists.
Terraform destroy should work and delete the existing state
