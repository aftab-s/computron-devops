# Terraform

## What is Terraform?
- **Terraform** is an open-source tool developed by **HashiCorp**.  
- It is used for **Infrastructure as Code (IaC)** i.e., provisioning and managing infrastructure through code.  
- It helps automate the process of creating, modifying, and destroying infrastructure resources.
- Traditionally, infrastructure was **long-lived and mutable**.  
- In modern DevOps, infrastructure is **short-lived and immutable**.  
- We **don’t modify** existing servers; instead, we **recreate** them when necessary.  
- Terraform helps automate this by defining resources in code.
- **Providers:** Plugins that interact with other platforms. 

---

## Provisioning Methods
Infrastructure can be provisioned in three main ways:

- **GUI (Graphical User Interface)** : Manual setup via dashboards (e.g., AWS Console). 
- **API/CLI** : Command-line or API-based provisioning. 
- **IaC (Infrastructure as Code)** : Automated, code-based provisioning. Terraform is the key tool here. 

---

## Types of IaC Tools

| Category | Example Tools | Purpose |
|-----------|----------------|----------|
| **Configuration Management** | Ansible | Manages configuration of existing systems. |
| **Server Templating Tools** | Docker | Builds machine images. |
| **Infrastructure Provisioning Tools** | Terraform | Provisions and manages infrastructure. |

---

## Core Terraform Commands

- `terraform init` : Initializes the working directory and downloads provider plugins. 
- `terraform plan` : Shows what changes will be made to reach the desired state. 
- `terraform apply` : Applies the plan to create to modify resources. Generates **`terraform.tfstate`**(a record of what resources Terraform has created, modified, or destroyed) which should not be shared. 

---

## Terraform Plan Output Symbols

- `+` : Resource **will be created** 
- `-` : Resource **will be destroyed** 

---

# Terraform Resource Block Structure

## Terraform Example

```hcl
resource "aws_instance" "local_file" {
  filename = "test.txt"         # Where to create the file
  content  = "Azure is great"  # What content to put inside the file
}
```

## Format

The standard format is:

```hcl
resource "<RESOURCE_TYPE>" "<IDENTIFIER>" {
  # ... Configuration arguments
}
```

---

## Key Components

### Component: 
- **resource**: The mandatory keyword starting every resource block.

---

### `<RESOURCE_TYPE>`

- `aws_instance`
- Defines the type of infrastructure to be created, **provided** by the **provider** (e.g., `aws_instance`: **aws** is the provider and **instance** is the resource provided by the provider. )

---

### `<RESOURCE_LOCAL_NAME>`

- `local_file`  
- A unique, local name used to refer to this resource within the Terraform configuration.


## Terraform Variables

- In Terraform, **.tfvars file** are used to assign values to input variables defined in your Terraform configuration (.tf files).
- Terraform initializes and can read all `.tfvars` files.  
- There is no strict **naming convention**, but keeping them organized is best practice.
- Combine all variables into a single `variables.tf` file, and bundle resources neatly in one place to maintain clarity.

### Order of Priority (Highest to Lowest)

- Command Line  >  .tfvars File  >  Default Value (variables.tf)
- If we create a file named `terraform.tfvars` and define:

```hcl
file_name = "hello.txt"
```

- Then the variable `file_name` will take the value `"hello.txt"`.  
- Terraform `.tfvars` files can override values defined in `variables.tf`.
- **Command line** has the highest priority.
- Example:

```bash
terraform apply -var file_name="cloud.txt"
```

- Here, the value provided in the command line (`cloud.txt`) overrides values set in both `.tfvars` and `variables.tf`.
- If there are multiple `.tfvars` files, we can specify which one to use explicitly:

```bash
terraform apply -var-file="dev.tfvars"
```

- Here, `"dev.tfvars"` is the name of the `.tfvars` file we want Terraform to use.

---

### Variable Definition Example

**In `variables.tf`:**

```hcl
variable "file_name" {
  default = "cloud.txt"
}
```

**In `terraform.tfvars`:**

```hcl
file_name = "hello.txt"
```

- When both are present, the `.tfvars` value (`"hello.txt"`) overrides the default value in `variables.tf`.  
- However, if a command-line argument is passed, it takes the highest priority.

---

### Resource Example

**In `main.tf`:**

```hcl
resource "local_file" "cloud" {
  filename = var.file_name
  content  = "AWS is great"
}
```

Here, `var.file_name` refers to the variable defined in `variables.tf`.

So if `variables.tf` defines `file_name = "cloud.txt"`, and if there is a `terraform.tfvars` for instance which defines `file_name = "hello.txt"`, then the `file_name` value from `terrafrom.tfvars` will replace the `file_name` in `variables.tf`. 

---

### Variable Example

**In `variables.tf`:**

```hcl
variable "file_name" {
  default     = "cloud.txt"
  type        = string
  description = " Value for the file name"
}
```

