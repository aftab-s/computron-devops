# Terraform

## 🔹 What is Terraform?
- **Terraform** is an open-source tool developed by **HashiCorp**.  
- It is used for **Infrastructure as Code (IaC)** — provisioning and managing cloud infrastructure through code.  
- It helps automate the process of creating, modifying, and destroying infrastructure resources.

---

## 🔹 Key Concepts

### 1. Infrastructure Lifecycle
- Traditionally, infrastructure was **long-lived and mutable**.  
- In modern DevOps, infrastructure is **short-lived and immutable**.  
- We **don’t modify** existing servers; instead, we **recreate** them when necessary.  
- Terraform helps automate this by defining resources in code.

---

## 🔹 Provisioning Methods
Infrastructure can be provisioned in three main ways:

| Type | Description |
|------|--------------|
| **GUI (Graphical User Interface)** | Manual setup via dashboards (e.g., AWS Console). |
| **API/CLI** | Command-line or API-based provisioning. |
| **IaC (Infrastructure as Code)** | Automated, code-based provisioning — Terraform is the key tool here. |

---

## 🔹 Types of IaC Tools

| Category | Example Tools | Purpose |
|-----------|----------------|----------|
| **Configuration Management** | Ansible | Manages configuration of existing systems. |
| **Server Templating Tools** | Packer | Builds machine images. |
| **Infrastructure Provisioning Tools** | Terraform | Provisions and manages cloud infrastructure. |

---

## 🔹 Terraform Architecture

### 1. Providers
- Plugins that interact with cloud platforms (AWS, Azure, GCP, etc.).  
- Each provider offers resource types (e.g., `aws_instance`, `azurerm_storage_account`).  

### 2. Backend Configuration
- Stores Terraform **state files** (e.g., locally or in S3).  
- Ensures consistency and supports team collaboration.

---

## 🔹 Core Terraform Commands

| Command | Description |
|----------|-------------|
| `terraform init` | Initializes the working directory and downloads provider plugins. |
| `terraform plan` | Shows what changes will be made to reach the desired state. |
| `terraform apply` | Applies the plan to create or modify resources. |
| `terraform destroy` | Destroys the managed infrastructure. |

---

## 🔹 Terraform Plan Output Symbols

| Symbol | Meaning |
|---------|----------|
| `+` | Resource **will be created** |
| `-` | Resource **will be destroyed** |
