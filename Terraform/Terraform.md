# What is Terraform?

Terraform is an open-source tool developed by HashiCorp.  
It is used for Infrastructure as Code (IaC)  
we can define, manage, and automate cloud infrastructure using simple configuration files 

---
# Infrastructure Lifecycle

Traditionally, infrastructure was **long-lived and mutable**, meaning servers and resources were created once and then manually updated or changed over time.  

now, infrastructure is **short-lived and immutable**.  
Instead of modifying existing servers, we **destroy and recreate** them with the updated configuration whenever needed.  
This ensures consistency, reduces errors, and makes deployments faster and more reliable.


## Terraform Step

**terraform init** - Sets up Terraform in your project and downloads provider plugins.

**terraform plan** - Shows what changes will be made before applying.  
                     here,  
                     "+" shows changes that is added  
                     "-" shows changes that is removed

**terraform apply** - Builds or modifies infrastructure as defined in code.

---

## The standard format of terraform code is

<<<<<<< HEAD:Terraform/Terraform
=======
---


### Core Commands (Lifecycle)
* **`terraform init`**: **First step.** Initializes the working directory and downloads necessary **Provider** plugins (and sets up the backend configuration).
* **`terraform plan`**: Shows the execution plan—**what changes** will be made. It's a "dry run." A `+` means a resource **will be created**, and a `-` means it **will be destroyed**.
* **`terraform apply`**: Executes the plan to **create or modify** the infrastructure to match the desired state defined in your code.
* **`terraform destroy`**: Destroys all infrastructure resources managed by the configuration.

---

## Resource Block Structure (The Code)

A **resource block** is an object that Terraform manages. It starts with the mandatory keyword **`resource`**.

The format is: `resource "<RESOURCE_TYPE>" "<RESOURCE_LOCAL_NAME>" { ... arguments }`

* **`<RESOURCE_TYPE>`:** means the type of infrastructure to create (e.g., `local_file`). This is provided by the active Provider.
* **`<RESOURCE_LOCAL_NAME>`:** A unique name (e.g., `"test_file"`) used only for referencing the resource within the Terraform code.
* **Arguments:** These are provider-specific settings (e.g., `filename = "test.txt"`, `content = "Azure is great"`) needed to configure the resource.

**Example:** :
>>>>>>> 1fd7ade38033e368bb5d6d7f83dfba5598af8083:Terraform/Terraform.md
```hcl
resource "<RESOURCE_TYPE>" "<RESOURCE_LOCAL_NAME>" {
  # ...
}

## Terraform Example:

'''hcl
resource "local_file" "test_file" {
<<<<<<< HEAD:Terraform/Terraform
  filename = "test.txt"         
  content  = "Azure is great"  

Resource Type: local_file (Manages files on the local filesystem)
Local Name: test_file (A name you choose)

# Terraform tfstate

The **Terraform state file**  is a file that keeps track of all the resources Terraform manages.  

When we run Terraform commands like `plan` or `apply`, Terraform will  Reads the `tfstate` file to know what resources already exist, and Compares it with  configuration files and decides what needs to be created, updated, or destroyed.
The `terraform.tfstate` file is created automatically after running `terraform apply`

=======
  filename = "test.txt"
  content  = "Azure is great"
}
>>>>>>> 1fd7ade38033e368bb5d6d7f83dfba5598af8083:Terraform/Terraform.md
