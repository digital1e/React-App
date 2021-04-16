## What is Terraform?

Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions.

There are mainly 4 files in this project as of now
1. main.tf
2. variables.tf
3. terrsform.tfvars
4. outputs.tf
<br/>

#### main.tf
This file is the entry point for the terraform to run the script, which contains the definition of resources to deploy. Below are the resources section which will deploy once terraform executes the script.

This block is used by terraform internally to initialize the scripts and to recognize which cloud is used.

```
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=2.46.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```
<br/>

This block is used for the resource group creation in which the resources resides.
> Note - if you update the resource group name next time terraform will create new resource group and create the resources in it.

```
resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.resource_group_location
}
```
<br/>

This block is used to create the CDN Profile where name is the CDN Profile name which gets created.
azurerm_resource_group.rg.name - this is reffering the resource group name to be used.
> Note - if you update the CDN Profile name next time terraform will creates new resource.

```
resource "azurerm_cdn_profile" "cdnprofile" {
  name                = "cdn-profile-1"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "Standard_Verizon"
}
```
<br/>

This block will create CDN Endpoint for the above CDN Profile.
azurerm_cdn_profile.cdnprofile.name - this is reffering the CDN Profile to be used.
> Note - if you update the CDN Endpoint name next time terraform will creates new resource.

```
resource "azurerm_cdn_endpoint" "cdnendpoint" {
  name                = "cdn-endpoint-1"
  profile_name        = azurerm_cdn_profile.cdnprofile.name
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  origin {
    name      = "cdnendpoint1"
    host_name = "www.contoso.com"
  }
}
```
<br/>

#### variables.tf
This file is used to define the variables type and optionally set a default value which will be used any where in the script.

```
variable "resource_group_name" {
  type    = string
  description = "Resource Group name in Azure."
}
```
<br/>

#### terrsform.tfvars
This file is used to set the actual values of the variables dynamically. The variables.tfvars file is used to define variables and the *.tf file declare that the variable exists. So we use a *.tfvar file to load in defaults as they are automatically loaded without any additional command line option.

```
resource_group_name     = "rg1"
resource_group_location = "West Europe"
```
<br/>

#### outputs.tf
This file is used for output declarations which can appear anywhere in your Terraform configuration files. However, putting them into a separate file called outputs.tf to make it easier for users to understand your configuration and what outputs to expect from it.

```
output "resource_group_id" {
    value = azurerm_resource_group.rg.id
}
```
