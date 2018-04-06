---
title: Démarrage rapide de PowerShell dans Azure Cloud Shell (préversion) | Microsoft Docs
description: Démarrage rapide de PowerShell dans Cloud Shell
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: efee0842a2fca2afac28f179bba07c3b6682ee57
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Démarrage rapide de PowerShell dans Azure Cloud Shell (préversion)

Ce document explique comment utiliser PowerShell dans Cloud Shell dans le [portail Azure](https://aka.ms/PSCloudPreview).

> [!NOTE]
> Un démarrage rapide de [Bash dans Azure Cloud Shell](quickstart.md) est également disponible.

## <a name="start-cloud-shell"></a>Démarrer Cloud Shell

1. Cliquez sur le bouton **Cloud Shell** dans la barre de navigation supérieure du portail Azure

  ![](media/quickstart-powershell/shell-icon.png)

2. Sélectionnez l’environnement PowerShell à partir de la liste déroulante pour basculer vers le lecteur Azure`(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Exécuter des commandes PowerShell

Exécuter des commandes PowerShell normales dans Cloud Shell, telles que :

```PowerShell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Parcourir les ressources Azure

 1. Répertorier vos abonnements

    ``` PowerShell
    PS Azure:\> dir
    ```

 2. `cd` sur votre abonnement préféré

    ``` PowerShell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Afficher toutes vos ressources Azure sous l’abonnement actuel
 
    Saisir `dir` pour répertorier plusieurs vues de vos ressources Azure.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>Vue AllResources 
Saisissez `dir` sous le répertoire `AllResources` pour afficher vos ressources Azure.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Explorer les groupes de ressources

 Vous pouvez accéder au répertoire `ResourceGroups` et à l’intérieur d’un groupe de ressources spécifique rechercher des machines virtuelles.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> Vous pouvez remarquer que la deuxième fois que vous tapez `dir`, Cloud Shell est en mesure d’afficher les éléments beaucoup plus rapidement.
> Cela est rendu possible par les éléments enfants mis en mémoire cache pour améliorer l’expérience utilisateur.
Toutefois, vous pouvez toujours utiliser `dir -Force` pour obtenir des données actualisées.

### <a name="navigate-storage-resources"></a>Parcourir les ressources de stockage
    
En entrant dans le répertoire `StorageAccounts`, vous pouvez facilement parcourir vos ressources de stockage
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

La chaîne de connexion vous permet d’utiliser la commande suivante pour monter le partage Azure Files.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

Pour en savoir plus, consultez [Montage d’un partage Azure Files et accès au partage dans Windows][azmount].

Vous pouvez également parcourir les répertoires sous le partage Azure Files comme suit :

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Interagir avec des machines virtuelles

Vous pouvez trouver toutes vos machines virtuelles sous l’abonnement actuel via le répertoire `VirtualMachines`.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Appeler un script PowerShell sur les machines virtuelles à distance

 > [!WARNING]
 > Reportez-vous à [Résolution des problèmes de gestion à distance des machines virtuelles Azure](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  En supposant que vous avez une machine virtuelle, MyVM1, utilisons `Invoke-AzureRmVMCommand` pour appeler un bloc de script PowerShell sur la machine distante.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  Vous pouvez également commencer par accéder au répertoire virtualMachines, puis exécuter `Invoke-AzureRmVMCommand` comme suit.

  ``` PowerShell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  Vous voyez une sortie similaire à ce qui suit :

  ``` PowerShell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Se connecter de manière interactive à une machine virtuelle à distance

Vous pouvez utiliser `Enter-AzureRmVM` pour la connexion interactive à une machine virtuelle s’exécutant dans Azure.

  ``` PowerShell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

Vous pouvez également commencer par naviguer vers le répertoire `VirtualMachines` et exécuter `Enter-AzureRmVM` comme suit.

  ``` PowerShell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Découvrir les applications web

En entrant dans le répertoire `WebApps`, vous pouvez facilement parcourir vos ressources d’applications Web

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="ssh"></a>SSH

[Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH) est disponible dans le Cloud Shell PowerShell.
Pour vous authentifier auprès des serveurs ou machines virtuelles à l’aide de SSH, générez la paire de clés publique/privée dans le Cloud Shell et publiez la clé publique dans `authorized_keys` sur l’ordinateur distant, tel que `/home/user/.ssh/authorized_keys`.

> [!NOTE]
> Vous pouvez créer des clés publique/privée SSH à l’aide de `ssh-keygen` et les publier sur `$env:USERPROFILE\.ssh` dans le Cloud Shell.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>Utilisation d’un profil personnalisé pour conserver les paramètres SSH et GIT

Les sessions n’étant pas conservées à la déconnexion, enregistrez votre répertoire `$env:USERPROFILE\.ssh` sur `CloudDrive` ou créez un lien symbolique au lancement du Cloud Shell.
Ajoutez l’extrait de code suivant dans votre fichier profile.ps1 pour créer un lien symbolique vers CloudDrive.

``` PowerShell
# Check if the .ssh directory exists
if( -not (Test-Path $home\CloudDrive\.ssh)){
    mkdir $home\CloudDrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>Utilisation de SSH

Suivez les instructions fournies [ici](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) pour créer une configuration de machine virtuelle à l’aide d’applets de commande AzureRM.
Avant d’appeler `New-AzureRmVM` pour lancer le déploiement, ajoutez la clé publique SSH à la configuration de machine virtuelle.
La machine virtuelle créée contient la clé publique à l’emplacement `~\.ssh\authorized_keys` ; vous pouvez ainsi ouvrir une session SSH sur la machine virtuelle sans recourir à des informations d’identification.

``` PowerShell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>Répertorier les commandes disponibles

Sous le lecteur `Azure`, tapez `Get-AzureRmCommand` pour obtenir les commandes Azure spécifiques au contexte.

Vous pouvez également continuer d’utiliser `Get-Command *azurerm* -Module AzureRM.*` pour rechercher les commandes Azure disponibles.

## <a name="install-custom-modules"></a>Installer des modules personnalisés

Vous pouvez exécuter `Install-Module` pour installer les modules à partir de [PowerShell Gallery][gallery].

## <a name="get-help"></a>Get-Help

Tapez `Get-Help` pour obtenir des informations sur PowerShell dans Azure Cloud Shell.

``` PowerShell
PS Azure:\> Get-Help
```

Pour une commande spécifique, vous pouvez toujours exécuter `Get-Help` suivi d’une applet de commande.

``` PowerShell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Utiliser Azure Files pour stocker vos données

Vous pouvez créer un script, par exemple `helloworld.ps1`, et l’enregistrer dans votre `CloudDrive` pour l’utiliser dans les sessions d’interpréteur de commandes.

``` PowerShell
cd C:\users\ContainerAdministrator\CloudDrive
PS C:\users\ContainerAdministrator\CloudDrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\CloudDrive> .\helloworld.ps1
Hello World!
```

La prochaine fois que vous utiliserez PowerShell dans Cloud Shell, le fichier `helloworld.ps1` existera sous le répertoire `CloudDrive` qui monte le partage de fichiers Azure.

## <a name="use-custom-profile"></a>Utiliser le profil personnalisé

Vous pouvez personnaliser votre environnement PowerShell en créant des profils PowerShell `profile.ps1` ou `Microsoft.PowerShell_profile.ps1`. Enregistrez-le sous `CloudDrive` afin qu’il puisse être chargé dans chaque session PowerShell lorsque vous lancez Cloud Shell.

Pour savoir comment créer un profil, consultez [À propos des profils][profile].

## <a name="use-git"></a>Utiliser Git

Pour cloner un dépôt Git dans le Cloud Shell, vous devez créer un [jeton d’accès personnel][githubtoken] et l’utiliser comme nom d’utilisateur. Dès que vous disposez de votre jeton, clonez le dépôt comme suit :

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Les sessions de Cloud Shell n’étant pas rémanentes après une déconnexion ou une expiration de session, le fichier de configuration Git n’existe plus lors de la connexion suivante. Pour conserver votre configuration Git, vous devez enregistrer .gitconfig sur votre `CloudDrive` et le copier ou créer un lien symbolique lors du lancement de Cloud Shell. Utilisez l’extrait de code suivant dans votre Profile.ps1,pour créer un lien symbolique vers `CloudDrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Quittez l’interpréteur de commandes.

Entrez `exit` pour mettre fin à la session.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
