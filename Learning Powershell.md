# Learning PowerShell

## 1. PowerShell Overview

### 1.1 PowerShell Basics

- Use PowerShell for consistent, repeatable tasks
- Work with built-in providers
- Talk to Active Directory, registry, and WMI, natively
- Bridge the gap between previous languages with aliases



### 1.2 How to read the language

#### Windows PowerShell Syntax

![PS_Syntax](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/01_01_PowerShell_Syntax.PNG?raw=true)





![PS_Syntax2](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/01_02_PowerShell_Syntax.PNG?raw=true)



#### Power of the Pipe

**The pip operator "|"**

- Output from one command becomes input for the next
- It strings together multiple commands
- Parameter binding is the key
- "|" routes information to the correct parameters-with very little effort
- `get-service | out-file c:\services.txt`
- Output from **get-service** is piped to Out-File, which will create a list of services in a text file called services.txt



### 1.3 Getting help

`tab`: auto complete

```powershell
Get-Help 
Get-Help Get-Service
Get-Help Get-Service -examples
Get-Help Get-Service -detailed
Get-Help Get-Service -full
Get-Help Get-Service -online
```



### 1.4 Discovering cmdlets and aliases

```powershell
Get-Command
dir
ls
gci
get-childitem
```

`dir`,  `ls`, `gci`  are aliases for a cmdlet `get-childitem`

You can create your own aliases if you wanna shorten down the name of a long cmdlet, or you can leverage the ones that are built in to help leverage some of that knowledge you might had from past command prompt type of environment.

Use `Get-Help` to find out what aliases are available for any given cmdlet

```powershell
Get-Help Get-ChildItem -full
```



### 1.5 Get-Service and Get-Member



```powershell
Get-Service

Status   Name               DisplayName
------   ----               -----------
Stopped  AarSvc_7035f       Agent Activation Runtime_7035f
Running  AdobeARMservice    Adobe Acrobat Update Service
Stopped  AJRouter           AllJoyn Router Service
Stopped  ALG                Application Layer Gateway Service
Stopped  AppIDSvc           Application Identity
Running  Appinfo            Application Information
Stopped  AppMgmt            Application Management
```

We have 3 parameters, `Status`, `Name`, and `DisplayName`. One of the great things that we can do is that we can actually query those services in and find out information. Let's say we wanna find out all the services that are currently stopped via PowerShell:

```powershell
Get-Service | Where-Object {$_.status -eq "stopped"}

Status   Name               DisplayName
------   ----               -----------
Stopped  AarSvc_7035f       Agent Activation Runtime_7035f
Stopped  AJRouter           AllJoyn Router Service
Stopped  ALG                Application Layer Gateway Service
Stopped  AppIDSvc           Application Identity
```

There're more properties than just  `Status`, `Name`, and `DisplayName`. To find those out, we can run another cmdlet `Get-Member`

```powershell
Get-Service |Get-Member 


   TypeName: System.ServiceProcess.ServiceController

Name                      MemberType    Definition
----                      ----------    ----------
Name                      AliasProperty Name = ServiceName
RequiredServices          AliasProperty RequiredServices = ServicesDependedOn
Disposed                  Event         System.EventHandler Disposed(System.Object, System.EventArgs)
Close                     Method        void Close()
Continue                  Method        void Continue()
CreateObjRef              Method        System.Runtime.Remoting.ObjRef
CanPauseAndContinue       Property      bool CanPauseAndContinue {get;}
CanShutdown               Property      bool CanShutdown {get;}
CanStop                   Property      bool CanStop {get;}
```

This shows all the parameters that are underneath the `Get-Service`



### Quiz

1. You asked your development team why they want to use PowerShell. Each one came back with one reason. Select the correct reason.

   A. PowerShell is Object Oriented

   B. PowerShell supports bulk operations

   C. all of these answers

   D. PowerShell automates tasks

2. Using PowerShell, you can create your own aliases to some specific commands.

   A. TRUE

   B. FALSE

3. Which PowerShell command lists the services?

   A. `Get-Service -Name '*net*'   `

   B. `Get-Service`

   C. `ls-service`

   D. `Get-Service -Name 'all'                 `

4. `dir` and `ls` are aliases for `gci`.

   A. TRUE

   B. FALSE

5. You have aliases in a file and you need to bring them into PowerShell. Which command should you use?

   A. `Import-Alias`

   B. `Get-Alias`

   C. `Set-Alias`

   D. `Export-Alias`



Answers:

1. all of these answers
2. TRUE
3. `get-service`: this cmdlet will return all the available services.
4. FALSE
5. `Import-Alias`: this cmdlet imports an alias file into Windows PowerShell



## 2. Using PowerShell

PowerShell is an Object-based language and that each object can pass into the other one. And it does one at a time.

### 2.1 Functions

Functions provide some useful capabilities inside of PowerShell. We see them just as cmdlets, and we actually access them just like we would any other cmdlets. In reality, underneath the cover, functions actually have a wide variety of commands that they actually do and run for us. You don't really have to worry so much about the built in functions, cause you'll work with them just like any other cmdlet. However, you can also make your own functions that perform a variety of tasks.

`funtion add`

Example. Function to add up a number

```powershell
function add
{
$add =[int](2+2)
Write-Output "$add"
}

add
```



### 2.2 Relieving anxiety with -WhatIf and -Confirm

Problem: PowerShell can cause anxiety

Solution: Risk Mitigation Parameters

1. `-whatif`: what if I run this cmdlets and get an idea of what's gonna happen

```powershell
Get-Service |Stop-Service - whatif
```

2. `confirm`: take each object one at a time and passes it over, and ask "are you sure you wanna do this"

```powershell
Get-Service |Stop-Service - confirm
```



### 2.3 ISE and you

ISE (Integrated Scripting Environment): GUI environment

IntelliSense: code completion/assist/hinting, especially when running across a new cmdlet

F5: run script

F8: run selection

Command add-on: help you learn PS 

New Remote PowerShell Tab



### 2.4 Working with output

- Default format is table
- Sort first, then output the table

```powershell
##Pipe Formatting
Get-Service |Format-List displayname, status, requiredservices
#Get-Service |Format-Table displayname, status, requiredservices
#Get-Service |Format-List *
Get-Service |Format-Table displayname, status, requiredservices |Sort-Object -Property status # raise an error
Get-Service |Sort-Object -Property status |Format-Table displayname, status, requiredservices

Get-Service |Out-File C:\Users\JY\Desktop\Learning_PowerShell\Output\services.txt
Get-Service |Out-File C:\Users\JY\Desktop\Learning_PowerShell\Output\services.csv
```



### 2.5 Grid View

Output data to a GUI environment

```powershell
Get-Service |Out-GridView
Get-Service |Format-Table displayname, status, requiredservices |Out-GridView # raise an error
Get-Service |Select-Object displayname, status, requiredservices |Out-GridView
Get-Service |Select-Object * |Out-GridView
```

- sort
- filter

![Grid_View1](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/02_05_GridView.PNG?raw=true)

- `Select Object`: control what parameters go into GridView

![Grid_View2](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/02_05_GridView2.PNG?raw=true)

![Grid_View3](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/02_05_GridView3.PNG?raw=true)



### 2.6 Running PowerShell Remotely

One way:

```powershell
Get-Service
Get-Service -ComputerName webserver
Get-Service -ComputerName dcdsc, webserver |Format-Table machinename, name, status
Get-Service -ComputerName dcdsc, webserver |Sort-Object -Property machinename |Format-Table machinename, name, status
Get-Service -ComputerName webserver |Select-Object * |Out-GridView
Get-Service -ComputerName webserver, dcdsc |Select-Object * |Out-GridView
```

The other way:

`File -> New Remote PowerShell Tab`

![PS_Remote](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/02_06_Running_Remotely.PNG?raw=true)



### Quiz

1. Select the cmdlet that will generate an error when run.

   A. `Get-Service | Select-Object displayName, status | Out-GridView`

   B. `Get-Service | Out-GridView`

   C. `Get-Service | Select-Object * | Out-GridView`

   D. `Get-Service | Format-Table displayName, status | Out-GridView`

2. You need to display all the services' properties in a key-value pair format separated by a colon. Which command would you use?

   A. `Get-Service | format-list * | Sort-Object -Property status`

   B. `Get-Service | format-table *`

   C. `Get-Service | format-list *`

   D. `Get-Service | format-list displayname, status`

3. What type is NOT allowed when exporting from PowerShell to a file is `_____`.

   A. csv

   B. html

   C. txt

   D. none of these answers



Answers:

1. `Get-Service | Format-Table displayName, status | Out-GridView`

This option will generate an error because you are asking PowerShell to display the results in two different formats.

2. `get-service | format-list *`

You need to use the `format-list` command to display the properties as indicated. The `*` will display all the properties.

3. none of these answers

All of these formats can be exported from Powershell.





## 3. Taking PowerShell On Premises and Online



### 3.1 Finding and installing modules

Module: Collections of cmdlets for a particular function or application

1. Module autoloading

2. Load Module manually

```powershell
Get-Module -ListAvailable

Import-Module -Name applocker
Get-Command -Module applocker
```

![PS_Modules](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/03_01_Modules.PNG?raw=true)

3. [PowerShell gallery](https://www.powershellgallery.com/)

   Lots of scripts, don't have to reinvent the wheel, do reverse engineering instead.

   However, you might run into some security issues - execution policy:

   ![Execution_Policy_Issue](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/03_02_Execution_Policy_Issue.PNG?raw=true)

   When you work with and download scripts, PowerShell doesn't trust them by default. It only trusts scripts and things that are created on the local machine (i.e. local scripts will run fine, but for any other remote script it has to be digitally signed to be able to verify that it'll work properly.)

   There're 4 levels of security that you can set this execution policy for:

   - restricted: nothing runs on the system
   - all signed: all the scripts we have to have them digitally signed and encrypted
   - remote signed
   - unrestricted: it's great for testing, but this is not something you wanna do in a production environment! 



### 3.2 Server Cmdlets

1. Install roles and features

```powershell
Get-WindowsFeature

Get-WindowsFeature -Name Web-Server

Get-WindowsFeature -Name Web-Server | Install-WindowsFeature
```



2. Set up a backup

   One of the great things that we can do in PowerShell scripts is set variables. When do do backup with PowerShell, just like wo do it with GUI, we have to create a policy, tell it what to backup, when to back it up, and then perform a backup

```powershell
Get-WindowsFeature -Name Windows-Server-Backup | Install-WindowsFeature

$policy = New-WBPolicy

$fileSpec = New-WBFileSpec -FileSpec C:\important

Add-WBFileSpec -Policy $policy -FileSpec $fileSpec
```

Here's where a great thing of variables come in. If we wanna change that location or add locations, all we have to do is to change this `fileSpec` line, then we don't have to worry about changing it anywhere lese in the script.

```powershell
$policy = New-WBPolicy

$fileSpec = New-WBFileSpec -FileSpec C:\important

Add-WBFileSpec -Policy $policy -FileSpec $fileSpec

$backupLocation = New-WBBackupTarget -VolumePath E:

Add-WBBackupTarget -Policy $policy -Target $backupLocation

Set-WBSchedule -Policy $policy 09:00
```

Go ahead, configure, and set that policy:

```powershell
Set-WBPolicy -Policy $policy

$BUpolicy = Get-WBPolicy

Start-WBBackup -Policy $BUpolicy
```



### 3.3 Office 365 and PowerShell

- Two ways
- Azure Active Directory PowerShell for Graph module
  - Install-Module -Name AzureAD
- Microsoft Azure Active Directory Module for Windows PowerShell
  - Install the 64-bit version of the Microsoft Online Services Sign-in Assistant (provides basic authentication for Office 365 environment)
  - Install-Module MSOnline



[Connect to Microsoft 365 with PowerShell](https://docs.microsoft.com/en-us/microsoft-365/enterprise/connect-to-microsoft-365-powershell?view=o365-worldwide)



### 3.4 Azure PowerShell

Everything we do is done with the Azure Resource Manager

![Azure_PowerShell](https://github.com/Jingy1Ma/Learning-PowerShell/blob/main/Images/03_04_AzurePowerShell.PNG?raw=true)

Azure PowerShell

- Client-based shell based on Windows (.NET), Linux, or macOS(.NET Core)

Azure Cloud Shell

- Browser-based environment for PowerShell and Bashh
- portal.azure.com

Azure command-line interface (CLI)

- CLI for Windows, Linux, and macOS
- Bash on Windows Subsystem for Linux



1. Azure PowerShell

```powershell
install-module az -allowclobber -scope currentuser

get-module

get-command -module "az.*"

get-command -module "az.websites"
```

`allowclobber`: replace existing command



2. Azure Cloud Shell

   A built-in editor inside the Cloud Shell, so we can actually create and save our scripts, and they're gonna be store inside the subscription. You can call these scripts from inside the Cloud Shell, or you can install these scripts from local PowerShell as well. So we have the ability to kind of have a center repository inside our Azure subscription for automation and for scripting. 

   

3. Azure command-line interface (CLI)

   It's cross-shell compatible.

```powershell
az
az group --help
az group create --help # learn about create command
```

In you're inside the Bash, but don't know Bash, just bring up the CLI using `az`

```bash
az
az group create --help
```

It returns the same exact result.



### Quiz

With PowerShell 3.0, running a command from a module that has not been loaded yet will automatically load the module.

A. TRUE

B. FALSE



Answer:

TRUE





## Next Steps

- Create a test environment
- Start simple
- Get-Help and Get-Command
- [Leverage the Script Center](https://docs.microsoft.com/en-us/samples/browse/?redirectedfrom=TechNet-Gallery)


## Reference
[1] https://www.linkedin.com/learning/learning-powershell/
