# Intro to Azure - Azure resource group
Azure is a continually expanding set of cloud services that help your organization meet your current and future business challenges. 
##  Azure resource group
Resource groups (RG) in Azure is a new approach to group a collection of assets in logical groups for easy or even automatic provisioning, monitoring, and access control, and for more effective management of their costs.

A **resource group** is a container to manange and aggregate resources in a **single unit**.
- Recources can exist in **only one** resource group
- Resources can exist in **different** regoins
- Resources can be moved to differente resource groups
- Applications can utilize multiple resource groups
- A resource can **interact** with resources in **other resource groups**
- **Tags** for Resources are **not** **inherited** by default from their **Resource Group**
- By default, **permissions set** at the resource level are inherited by the resources in the resource group
- Deleting the resource group will remove the resource group as well as all the resources in that resource group. 

### Azure resource
- An Azure resource can have multiple Delete locks.

- An Azure resource inherits locks from its resource group.

- If an Azure resource has a Read-only lock, you can add a Delete lock to the resource.