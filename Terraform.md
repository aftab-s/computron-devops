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

### .terraform\providers\registry.terraform.io\hashicorp\local\25.3\windows-386
providers Registered in registry.teraform.io  company wise registration

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

### Terraform init 
Scanning and searching for cloud like aws,local,azure,gcp
### Terraform plan 
1 to add, 0 to change, 0 to destroy
### Terraform apply
aws instance creation(infrastucture as a code)
After init 3 files created 
.terraform-folder creation

### Variable Defining

variable "file_name"{            -------> we created
  default="aws_text
  type= string
  description="value for the filename"
}

### Strings
.tf files all run terraform
* Resources all comes to main.tf
* variable defines to variables.tf (another file)

resources have variable ,when calling variable its declared in the variable.tf
naming conversion terraform.int
All .tf files run 

### THE Variable Priority
1  Command line has most priority
2  Then .tf vars file
3 Then variables.tf

1 terraform apply -var file_name="hello.txt"
2 .tf vars file
  terraform.tfvars
  variable_filename="cloud.txt"
3 variable.tf = aws.txt

###  Advantage
main.tf file we can share in terraform
and tfvars file can be edited as recievers need 
we can give variable file as our needs

terraform apply -var-file="dev.tfvars"
Replacing -specific vars.tf into specific file

### variables
variable "example"{

}

IN RESOURCE (MAIN FILE RESOURCE BLOCK VARIABLE IS CALLING)
var.example

no datatype in var so string,int all accept in variables example

*if variable "example"{
  type="String"
  default="hello"
}

2. terraform .tfvars
3. command line
    terraform apply 
### Boolean
  terraform apply -var "example_bool =false"

### List indexing
variable "example" {
  type=list(number)
  default= [1,2]
}          0 , 1     like array we call as a index number
for boolean
variable "example" {
  type=bool
  default= true
} 

### Map
variable "map_exmple"{
  type = map(string)
  default={
      student 1 ="t2.micro"
      student 2 ="t3.micro"
  }
}
### backup
backup_retention_period = var.database_config.backup? 7:0
ex: a = var.b ? 7 : 0
  if b=true{
    a=7;
  }
  else{
    a=0;
  }
 
 ### list of objects
 list under objects 

 ### Dynamic Lookup
 no default value
 when terraform plan input 
 variable "current"{
  type=string
 }
 value ask when terraform plan command runnning