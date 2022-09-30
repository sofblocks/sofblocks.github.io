---
layout: single
title: "Azure Powershell"
permalink: /azpowershell/
tags: [Azure, Powershell, Scripting]
toc: true
---

PowerShell is a command-based shell and scripting language used for task automation and as a configuration framework tool. PowerShell runs on Windows, Linux, and macOS operating systems. 
Powershell can also be used to script, automate, and manage workloads running on cloud platforms such as GCP and Azure because of its well laid out syntax which makes it easy to learn and use.

In this post we will be looking on how to get started with Azure Powershell but first we must understand some of its components. 

What is  **CMDLETS**: A cmdlet is a single lightweight command that performs an action.   
What is a **Module**: A module is a set of related functionalities and resources that are grouped together. 

## Exploring PowerShell’s cmdlets

### Get-Command
The Get-Command cmdlet gets all commands that are installed on the computer, including cmdlets, aliases, functions, filters, scripts, and applications. Get-Command gets the commands from PowerShell modules and commands that were imported from other sessions.
```powershell
Get-Command "name of the command"
```

### Get-Help    
The cmdlet displays information about PowerShell concepts and commands.
```powershell
Get-help "name of the command"
```
![upload-image]({{ "/assets/imgs/notes/get-help.png" | relative_url }})

The above command returns basic information however you can add the "full" flag to return every information about the cmdlet or the "example" flag to list only the examples of the cmdlet.
```powershell
    Get-Command "name of the command" -Full
    Get-Command "name of the command" -Examples
```

Listing parameters of a cmdlet.
```powershell
    (Get-help "name of the command").parameters
    (get-command get-childitem).parameters
```

#### Shows navigable menu of all options when hitting Tab
In powershell window you can use the tab key to get command completion. So if you enter "get-com" and then type a TAB, it changes what you typed into "Get-Command".
To list all available parameters for a cmdlet you use Ctrl + Space.
![upload-image]({{ "/assets/imgs/notes/ctrlspace.png" | relative_url }})

Alternatively you can configure the tab key to perform the above action of Ctrl + Space.
Type the following command to open powershell automatic variable with a notepad.
```powershell
notepad $PROFILE 
```
Paste the following command
```powershell
# Shows navigable menu of all options when hitting Tab

Set-PSReadlineKeyHandler -Key Tab -Function MenuComplete
```
Next restart the session. Now if you press the tab key it will be equivalent to pressing the Ctrl + Space keys combination.

## Getting started with Azure PowerShell
### Installing the powershell module.

Now that we know some basic commands and useful shortcut keys, let get our hands dirty. First we shall start by installing the Azure module. 
```powershell
> Install-Module -Name Az -Force 
```
Once the Azure module is installed, connect to your Azure account using the **Connect-AzAccount** cmdlet. When you see the Microsoft Azure pop-up, sign in to your Azure account. You’re now logged in to Azure.

```powershell
Connect-AzAccount
```
You can also connect to Azure account with a specified tenant and subscription
```powershell
Connect-AzAccount -Tenant 'xxxx-xxxx-xxxx-xxxx' -SubscriptionId 'yyyy-yyyy-yyyy-yyyy'
``` 
To get the current account details used to authenticate Azure Resource Manager requests we use the **Get-AzContext** cmdlet.
![upload-image]({{ "/assets/imgs/notes/azcontext.png" | relative_url }})

### How to create an Azure VM using Azure PowerShell
To create a VM, start by creating the Azure resource group using New-AzResourceGroup. You’ll want to make this a variable because you’ll need it to pass information forward when you create the Azure VM.
```powershell
> $RG = New-AzResourceGroup -Name "name_of_the_resource_group" -Location "Location"  
---
> $RG = New-AzResourceGroup -Name "production" -location "EastUs"
```
While selecting a location in the Azure portal you just have to select from the drop down menu. However, in PowerShell you have to know exactly what the name of the data center is and whether it supports the resource you are going to deploy.
To list all the available regions run the following command.
```powershell
Get-AzLocation | Format-Table
```
The Providers property in the output lists the resources available in the region.
To find regions that support a specific resource e.g Azure SQL run the following command.
```powershell
Get-AzLocation | ? {$_.Providers -like "*sql*"} | Format-Table
```
To list all the services in a particular region:
```powershell
Get-AzLocation | ? {$_.Location -eq "eastasia"} | select Providers
```

Next, you'll create a virtual network followed by a virtual network subnet.
```powershell
$Vnet = New-AzVirtualNetwork -ResourGroup $RG.Name -Location $RG.Location -Name "Vnet" -AddressPrefix '10.0.0.0/16' 
```
```powershell
$subnet = New-AzVirtualNetworkSubnetConfig -Name "subnet" -VirtualNetwork $Vnet.Name  -AddressPrefix '10.0.0.0/24' 
```
Next update the Virtual Network to pick the changes.
```powershell
$Vnet | Set-AzVirtualNetwork
```
Next, you’ll create another variable for the creation the Azure VM using New-AzVM cmdlet. We'll go ahead and pass the resource group name variable and location variables.
### Asigning Public Address to a Virtual Machine.
```powershell
$PIP = New-AzPublicIpAddress -Name "name_of_the_pulicIP" -ResourceGroupName $RG.Name -AllocationMethod Dynamic -Location $RG.Location
```
```powershell
$vm = New-AzVM -ResourceGroup $RG.Name -Location $RG.Location -VirtualNetwork $Vnet.Name -Subnet $subnet.Name  -PublicIpAddressName $PIP.Name -OpenPorts 80,3389 -Name "VmName" -AsJob 
```
The AsJob switch runs the command in the background this gives you space and time to run other commands
in powershell. Such as creation of other resources in Azure.
You can inspect the job and progress at any time using the Get-Job cmdlet.
```powershell
Get-Job $vm
```

Get details of the created Virtual Machine:
```powershell
$vm_details = Get-AzVM -Name "vm_name" -ResourceGroup "Resource_group_name"
```
Manage the VM's
Start the VM:
```powershell
Start-AzVM -ResourceGroupName $RG.Name -Name $vm.Name
```
Stop the VM:
```powershell
Stop-AzVM -ResourceGroupName $RG.Name -Name $vm.Name
```
Restart the VM:
```powershell
Restart-AzVM -ResourceGroupName $RG.Name -Name $vm.Name
```