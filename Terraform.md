# TERRAFORM
Hashicorp configuration language
It’s a simple, human-readable language used to write infrastructure as code (IaC) Used mainly in Terraform
### There are 2 categories
##  long lived + mutable (changable)
  long time server, every time code changing 

## Short lived + immutable (not changable)
short lived deleted and created immutable nochange
### Terraform is an example ,only instance and pods are deleted and recreated 
    provisioning
###    1   GUI
###    2   API/CLI
###    3   Infrastructure as code

Terraform is Infrastructure as Code
Supports all clouds local,AWS,GCP,Azure
Saved as .tf file


## TERRAFORM has 3 steps   init,plan,apply
**`terraform init`**    
Scanning and searching for the cloud like aws,azure,gcp,local
after init 3 files created    .terraform- folder is created
providers registered in regitry.terraform as companywise
\hashicorp\   ->  aws
\local\
\2.5.3\ version
windows -> or linux
.terraform\providers\registry.terraform.io\ hashicorp\local\2.5.5\windows-386

**`terraform plan`**  
### One to add + and one to delete- 0 to change shows all the details that happens

**`terraform apply`**  
### AWS instance Creation (infrastructure as a code)

## tfstate
created when we terraform apply tfstate is the most important file dont share
When Terraform creates or manages infrastructure (like servers, networks etc)
it needs to remember what it built before That memory is stored in a file called terraform.tfstate


### Terraform Syntax
` resource "aws_instance" "sample" {
  filename  =
  content   =
} `

 aws   -- provider
 _instance -- resourcetype
 sample -- identifier
   { --   identifier indenxing }
