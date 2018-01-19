---
title: "Exemple de script Azure PowerShell - Migrer des objets blob entre des comptes de stockage à l’aide d’AzCopy sur Windows | Microsoft Docs"
description: "Utilisez AzCopy pour copier des objets blob d’un compte de stockage Azure à un autre."
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 1/3/2018
ms.author: v-rogara
ms.openlocfilehash: 0902792b2367aa56285e7e074e184ffa35fca466
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Migrer des objets blob entre des comptes de stockage à l’aide d’AzCopy sur Windows

Cet exemple copie tous les objets blob d’un compte de stockage source fourni par l’utilisateur dans un compte de stockage cible fourni par l’utilisateur. 

Cette opération est effectuée avec la commande `Get-AzureStorageContainer`, qui répertorie tous les conteneurs d’un compte de stockage. L’exemple émet ensuite les commandes AzCopy qui copient chaque conteneur du compte de stockage source dans le compte de stockage de destination. En cas d’échec, l’exemple effectue une ou plusieurs nouvelles tentatives en fonction de la valeur $retryTimes définie (la valeur par défaut est trois, mais vous pouvez la changer à l’aide du paramètre `-RetryTimes`). Si chaque nouvelle tentative échoue, l’utilisateur peut réexécuter le script en utilisant l’exemple avec le dernier conteneur copié avec succès à l’aide du paramètre `-LastSuccessContainerName`. L’exemple poursuit alors la copie des conteneurs à partir de ce point.

Cet exemple nécessite le module Stockage Azure PowerShell version **4.0.2** ou ultérieure. Vous pouvez utiliser `Get-Module -ListAvailable Azure.storage` pour vérifier quelle version est installée. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Cet exemple nécessite également la dernière version [d’AzCopy sur Windows](http://aka.ms/downloadazcopy). Le répertoire d’installation par défaut est `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`

Cet exemple spécifie le nom et la clé d’un compte de stockage source, le nom et la clé d’un compte de stockage cible, et le chemin complet d’AzCopy.exe (s’il n’est pas installé dans le répertoire par défaut).

Voici quelques extraits de code correspondant à cet exemple :

Si AzCopy est installé dans le répertoire par défaut :
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Si AzCopy n’est pas installé dans le répertoire par défaut :

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Si l’exemple échoue et qu’il doit être réexécuté à partir d’un conteneur spécifique : 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Exemple de script

```Powershell
# Run the script in a new open Powershell window, which has not run other cmdlets, or AzCopy performance could suffer .
# Install Azure PowerShell before runing the script: https://github.com/Azure/azure-powershell/releases
# Install AzCopy before runing the script: https://docs.microsoft.com/azure/storage/common/storage-use-azcopy
# Do not modify the Source or Destination accounts while the script is running

 param (
    [Parameter(Mandatory = $true, 
    HelpMessage= "Source Storage account name.")]
    [ValidatePattern("^[a-z0-9`]{3,24}$")]
    [String]$srcStorageAccountName,

    [Parameter(Mandatory = $true, 
    HelpMessage= "Source Storage account key.")]
    [String]$srcStorageAccountKey,
    
    [Parameter(Mandatory = $true, 
    HelpMessage= "Destination Storage account name.")]
    [ValidatePattern("^[a-z0-9`]{3,24}$")]
    [String]$DestStorageAccountName,

    [Parameter(Mandatory = $true, 
    HelpMessage= "Destination Storage account key.")]
    [String]$DestStorageAccountKey,

    [Parameter(Mandatory = $false, 
    HelpMessage= "Input the full filePath of the AzCopy.exe, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe")]
    [String]$AzCopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe",

    [Parameter(Mandatory = $false, 
    HelpMessage='Sets the number of retries in an event of failure. Set to 0 for no retry, set -1 for infinite retry.')]
    [Int32]$RetryTimes = 3,

    [Parameter(Mandatory = $false, 
    ValueFromPipeline = $true, 
    HelpMessage='Used for resume operation. When provided, the script will copy containers that are alphabetically after $LastSuccessContainerName')]
    [String]$LastSuccessContainerName = $nullz
 )

 if( (Get-Item $AzCopyPath).Exists)
 {

    $FileItemVersion = (Get-Item $AzCopyPath).VersionInfo
    $FilePath = ("{0}.{1}.{2}.{3}" -f  $FileItemVersion.FileMajorPart,  $FileItemVersion.FileMinorPart,  $FileItemVersion.FileBuildPart,  $FileItemVersion.FilePrivatePart)

    if([version] $FilePath -lt "7.0.0.2")
    {
        $AzCopyPath = Read-Host "Version of AzCopy found at default install directory is of a lower, unsupported version. Please input the full filePath of the AzCopy.exe that is version 7.0.0.2 or higher, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
    }
 }
 elseIf( (Get-Item $AzCopyPath).Exists -eq $false)
 {
    $AzCopyPath = Read-Host "Input the full filePath of the AzCopy.exe, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
 }

# Create and check Storage context
$Error.Clear()
$srcCtx = New-AzureStorageContext -StorageAccountName $srcStorageAccountName -StorageAccountKey $srcStorageAccountKey
if ($srcCtx -eq $null)
{
    Write-Error "Script could not create source Storage Context, possibly due to invalid StorageAccountName or StorageAccount Key terminating: $Error[0]";
    return;
}
$destCtx = New-AzureStorageContext -StorageAccountName $destStorageAccountName -StorageAccountKey $destStorageAccountKey
if ($destCtx -eq $null)
{
    Write-Error "Script could not create destination storage context, possibly due to invalid StorageAccountName or StorageAccount Key terminating: $Error[0]";
    return;
}

#Check Source and Destination Storage account Connection
$Error.Clear()
$Containers = Get-AzureStorageContainer -MaxCount 1 -Context $srcCtx
if ($Error.Count -gt 0)
{
    Write-Error "Script failed to connect to source Storage account, terminating: $Error[0]";
    return;
}
$Containers = Get-AzureStorageContainer -MaxCount 1 -Context $destCtx
if ($Error.Count -gt 0)
{
    Write-Error "Script failed to connect to destination Storage account, terminating: $Error[0]";
    return;
}

#Check AzCopy Path
if((Test-Path $AzCopyPath) -eq $false)
{
    Write-Error "Script is terminating since the provided AzCopyPath does not exist: $AzCopyPath ";
    return;
}
elseif((Get-Item $AzCopyPath).BaseName -ne "AzCopy" )
{
    Write-Error "Script is terminating since the provided AzCopyPath does not refer to the AzCopy exe: $AzCopyPath ";
    return;
}
elseif((Get-Item $AzCopyPath).BaseName -eq "AzCopy")
{
    $FileItemVersion = (Get-Item $AzCopyPath).VersionInfo
    $FilePath = ("{0}.{1}.{2}.{3}" -f  $FileItemVersion.FileMajorPart,  $FileItemVersion.FileMinorPart,  $FileItemVersion.FileBuildPart,  $FileItemVersion.FilePrivatePart)

    if([version] $FilePath -lt "7.0.0.2")
    {
        $AzCopyPath = Read-Host "Version of AzCopy found at provided path is of a lower, unsupported version. Please input the full filePath of the AzCopy.exe that is version 7.0.0.2 or higher, e.g.: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe"
    }
}

$OutputLastSuccessContainer = $LastSuccessContainerName;
$HasReachedLastSuccessContainer = $false;

if ($LastSuccessContainerName -eq $null -or $LastSuccessContainerName -eq "")
{
    $HasReachedLastSuccessContainer = $true;
}

# For list container
$MaxReturn = 250
$Token = $Null

do{
    # List source containers
    $retry = 1
    $Error.Clear()
    $srcContainers = Get-AzureStorageContainer -MaxCount $MaxReturn -ContinuationToken $Token -Context $srcCtx

    # If list container fail, retry it
    while(($Error.Count -gt 0) -and ($RetryTimes -eq -1 -or $retry -le $retryTimes))
    {
        Write-Host "Retry List containers $retry"
        $Error.Clear()
        $srcContainers = Get-AzureStorageContainer -MaxCount $MaxReturn -ContinuationToken $Token -Context $srcCtx
        $retry++
    }

    # If list container fail after retry, break script
    if ($Error.Count -gt 0){
        Write-Error "Terminating the script since listing source containers failed: $Error[0]";
        $CopyContainerFail = $true
        break;
    }
    $Token = $srcContainers[$srcContainers.Count -1].ContinuationToken;

    # Transfer containers one by one
    $CopyContainerFail = $false
    foreach ($container in $srcContainers)
    {
        if (!$HasReachedLastSuccessContainer)
        {
            if ($container.Name -eq $LastSuccessContainerName)
            {
                $HasReachedLastSuccessContainer = $true;
            }
            Write-Host "Skipping container copy: $($container.Name)"
            Continue;
        }

        Write-Host "Start copying container: $($container.Name)"
        $retry = 1

        # Get AzCopy command for transfer one container
        $destContainer = $destCtx.StorageAccount.CreateCloudBlobClient().GetContainerReference($container.Name)
        $azCopyCmd = [string]::Format("""{0}"" /source:{1} /dest:{2} /sourcekey:""{3}"" /destkey:""{4}"" /snapshot /y /s",$AzCopyPath, $container.CloudBlobContainer.Uri.AbsoluteUri, $destContainer.Uri.AbsoluteUri, $srcStorageAccountKey, $DestStorageAccountKey)
    
        # Execute the AzCopy command first time
        Write-Host "$azCopyCmd"
        $result = cmd /c $azCopyCmd
        foreach($s in $result)
        {
            Write-Host $s 
        }

        # If transfer failed, retry it
        while(($LASTEXITCODE -ne 0) -and ($RetryTimes -eq -1 -or $retry -le $retryTimes))
        {
            Write-Host "Retry $retry : $azCopyCmd"
            $result = cmd /c $azCopyCmd
            foreach($s in $result)
            {
                Write-Host $s 
            }
            $retry++
        }

        # If tranfer failed after retry, print error
        if ($LASTEXITCODE -ne 0){
            Write-Error "Container copy failed: $($container.Name)";
            $CopyContainerFail = $true
            break;
        }
        else
        {
            Write-Host "Finished copying container: $($container.Name)"
            Write-Host ""
            $OutputLastSuccessContainer = $container.Name
        }
    }
    if($CopyContainerFail)
    {
        break;
    }
}
While ($Token -ne $Null)

# Summary the copy result
if ($CopyContainerFail)
{
    if(($OutputLastSuccessContainer -ne $null) -and ($OutputLastSuccessContainer -ne ""))
    {
        Write-Warning "To resume, rerun the script and append the parameter: ""-LastSuccessContainer $OutputLastSuccessContainer"""
        return $OutputLastSuccessContainer
    }
    else
    {
        Write-Warning "To resume, rerun the script."
        return $null
    }
}
else
{
    Write-Host "All Containers copied successfully."
    return ""
}
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour copier des données d’un compte de stockage à un autre. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Retourne les conteneurs associés à ce compte de stockage. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Crée un contexte de stockage Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
