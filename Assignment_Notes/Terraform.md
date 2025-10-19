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

| Type | Description |
|------|--------------|
| **GUI (Graphical User Interface)** | Manual setup via dashboards (e.g., AWS Console). |
| **API/CLI** | Command-line or API-based provisioning. |
| **IaC (Infrastructure as Code)** | Automated, code-based provisioning. Terraform is the key tool here. |

---

## Types of IaC Tools

| Category | Example Tools | Purpose |
|-----------|----------------|----------|
| **Configuration Management** | Ansible | Manages configuration of existing systems. |
| **Server Templating Tools** | Docker | Builds machine images. |
| **Infrastructure Provisioning Tools** | Terraform | Provisions and manages infrastructure. |

---

## Core Terraform Commands

| Command | Description |
|----------|-------------|
| `terraform init` | Initializes the working directory and downloads provider plugins. |
| `terraform plan` | Shows what changes will be made to reach the desired state. |
| `terraform apply` | Applies the plan to create to modify resources. Generates **`terraform.tfstate`**(a record of what resources Terraform has created, modified, or destroyed) which should not be shared. |

---

## Terraform Plan Output Symbols

| Symbol | Meaning |
|---------|----------|
| `+` | Resource **will be created** |
| `-` | Resource **will be destroyed** |

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


