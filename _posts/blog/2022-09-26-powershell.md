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
What is  CMDLETS: A cmdlet is a single lightweight command that performs an action   
What is A Module: A module is a set of related functionalities and resources that are grouped together. 

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