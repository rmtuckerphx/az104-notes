# AZ 104 - Microsoft Certified: Azure Administrator Associate


## Cloud Shell

Access:
* Azure Portal
* shell.azure.com
* Code snippets on Microsoft Learn

Supports:
* Bash
* PowerShell

Can save scripts on CloudDrive across sessions.
Can you `code` editor

Some common add-ons:
* bash
* sh
* Azure CLI (az)
* Azure Functions CLI
* code
* vim
* nano
* git
* Kubectl
* iPython Client
* Terraform


You can use Azure Cloud Shell to:

* Open a secure command-line session from any browser-based device.
* Interact with Azure resources without the need to install plug-ins or add-ons to your device.
* Persist files between sessions for later use.
* Use either Bash or PowerShell, whichever you prefer, to manage Azure resources.
* Edit files (such as scripts) via the Cloud Shell editor.

You shouldn't use Azure Cloud Shell if:

* You intend to leave a session open for more than 20 minutes for long running scripts or activities. In these cases, your session is disconnected without warning, and the current state is lost.
* You need admin permissions, such as sudo access, from within the Azure CLI or PowerShell environment.
* You need to install tools that aren't supported in the limited Cloud Shell environment, but instead require an environment such as a custom virtual machine or container.
* You need storage from different regions. You might need to back up and synchronize this content since only one region can have the storage allocated to Azure Cloud Shell.
* You need to open multiple sessions at the same time. Azure Cloud Shell allows only one instance at time and isn't suitable for concurrent work across multiple subscriptions or tenants.


## PowerShell

Command-llne shell and scripting language.
Inputs and outputs are objects not text.

Types of commands include:
* Cmdlet (command let)
* Function
* Alias
* Script



`$PSVersionTable`

`$PSVersionTable.PSVersion`


`Get-Help`


`Get-Command`

`Get-Command -Noun file*`

`Get-Command -Verb get -Noun file*`


## Azure Resource Manager (ARM) templates

Declarative way to deploy Infrastructure As Code (IaC) using JSON. Bicep is recommended over ARM for those new to IaC.

Advantages to IaC:
* Consistent configurations
* Improved scalability
* Faster deployments
* Better traceability

**ARM Template**

```
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "defaultValue": "mystorageacct",
      "metadata": {
        "description": "Name of the Storage Account"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for the Storage Account"
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "metadata": {
        "description": "Storage Account SKU"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2023-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('skuName')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
   ]
```

Deploy
```
New-AzResourceGroupDeployment -ResourceGroupName MyRG -TemplateFile .\storage.json -skuName Standard_LRS -storageAccountName mtuckeraz104

OR

az deployment group create --resource-group MyRG --template-file .\storage.json --parameters skuName=Standard_LRS storageAccountName=mtuckeraz104
```


**Bicep file**

```
@description('Name of the Storage Account')
param storageAccountName string = 'mystorageacct'

@description('Location for the Storage Account')
param location string = resourceGroup().location

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
])
@description('Storage Account SKU')
param skuName string = 'Standard_LRS'

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  sku: {
       name: skuName
  }
  kind: 'StorageV2'
  properties: {}

```

Deploy
```
New-AzResourceGroupDeployment -ResourceGroupName MyRG -TemplateFile .\storage.bicep -skuName Standard_LRS -storageAccountName mtuckeraz104

OR

az deployment group create --resource-group MyRG --template-file .\storage.bicep --parameters skuName=Standard_LRS storageAccountName=mtuckeraz104
```

