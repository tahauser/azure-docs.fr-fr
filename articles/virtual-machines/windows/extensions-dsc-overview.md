---
title: "Présentation de la configuration d’état souhaité pour Azure | Microsoft Docs"
description: "Présentation associée à l’utilisation du gestionnaire d’extensions Microsoft Azure pour la configuration d’état souhaité Powershell. Cela inclut les conditions préalables, l’architecture, les applets de commande, etc."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
ms.openlocfilehash: deb360e36b68f7ddb13b00946c700d0c83890ca6
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

L’agent de machine virtuelle Microsoft Azure et les extensions associées font partie des services d’infrastructure Microsoft Azure. Les extensions de machine virtuelle sont des composants logiciels qui étendent les fonctionnalités d’une machine virtuelle et simplifient les diverses opérations de gestion de celle-ci. Par exemple, l’extension VMAccess permet de réinitialiser le mot de passe d’un administrateur, ou l’extension de script personnalisé d’exécuter un script sur la machine virtuelle.

Cet article présente l’extension de configuration d’état souhaité Microsoft PowerShell (PowerShell DSC, Desired State Configuration) pour les machines virtuelles Azure, dans le cadre du Kit de développement logiciel (SDK) Azure PowerShell. Vous pouvez utiliser les nouvelles applets de commande pour charger et appliquer une configuration PowerShell DSC sur une machine virtuelle Azure activée avec l’extension PowerShell DSC. L’extension DSC PowerShell fait appel à DSC PowerShell pour mettre en œuvre la configuration DSC reçue sur la machine virtuelle. Cette fonctionnalité est également disponible dans le portail Azure.

## <a name="prerequisites"></a>configuration requise
**Ordinateur local** Pour pouvoir interagir avec l’extension de machine virtuelle Azure, utilisez le portail Azure ou le Kit de développement logiciel (SDK) Azure PowerShell. 

**Agent invité** La machine virtuelle Azure qui sera définie par la configuration DSC doit inclure un système d’exploitation prenant en charge Windows Management Framework (WMF) 4.0 ou 5.0. Pour la liste complète des versions de système d’exploitation prises en charge, voir l’ [historique des versions de l’extension DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termes et concepts
Ce guide part du principe que vous connaissez les concepts suivants :

* **Configuration** : document de configuration DSC. 
* **Nœud** : cible d’une configuration DSC. Dans ce document, le terme « nœud » fait toujours référence à une machine virtuelle Azure.
* **Données de configuration** : fichier .psd1 contenant les données d’environnement d’une configuration.

## <a name="architectural-overview"></a>Présentation de l’architecture
L’extension DSC d’Azure utilise l’infrastructure de l’agent Azure VM pour fournir, mettre en œuvre et créer des rapports sur les configurations DSC sur des machines virtuelles Azure. L’extension DSC attend un fichier ZIP contenant au moins un document de configuration et un ensemble de paramètres, fournis par le Kit de développement logiciel (SDK) Azure PowerShell ou sur le portail Azure.

Lorsque l’extension est appelée pour la première fois, elle lance un processus d’installation. Ce processus installe une version du logiciel Windows Management Framework (WMF) en utilisant la logique suivante :

1. Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est effectuée. En effet, la dernière version de PowerShell est installée sur Windows Server 2016.
2. Si la propriété `wmfVersion` est spécifiée, cette version de WMF est installée, sauf si elle est incompatible avec le système d’exploitation de la machine virtuelle.
3. Si aucune propriété `wmfVersion` n’est spécifiée, la dernière version applicable de WMF est installée.

L’installation de WMF nécessite un redémarrage. Après redémarrage, l’extension télécharge le fichier .zip spécifié dans la propriété `modulesUrl` . Si cet emplacement figure dans le stockage d’objets blob Azure, un jeton SAP peut être spécifié dans la propriété `sasToken` pour l’accès au fichier. Une fois le fichier ZIP téléchargé et décompressé, la fonction de configuration définie dans `configurationFunction` est exécutée pour générer le fichier MOF. Ensuite, l’extension exécute la commande `Start-DscConfiguration -Force` sur le fichier MOF généré. L’extension capture la sortie et la réécrit dans le canal d’état Azure. À ce stade, le gestionnaire de configuration local DSC gère la surveillance et la correction de la manière habituelle. 

## <a name="powershell-cmdlets"></a>Applets de commande PowerShell
Des applets de commande PowerShell peuvent être utilisées avec Azure Resource Manager ou le modèle de déploiement classique pour empaqueter, publier et surveiller les déploiements d’extensions DSC. Les applets de commande suivantes correspondent aux modules de déploiement classique, mais vous pouvez remplacer « Azure » par « AzureRm » pour utiliser le modèle Azure Resource Manager. Par exemple, `Publish-AzureVMDscConfiguration` utilise le modèle de déploiement classique, tandis que `Publish-AzureRmVMDscConfiguration` utilise Azure Resource Manager. 

`Publish-AzureVMDscConfiguration` récupère un fichier de configuration, l’analyse à la recherche de ressources DSC dépendantes, puis crée un fichier .zip contenant la configuration et les ressources DSC nécessaires pour mettre en œuvre la configuration. Elle peut aussi créer le package en local, à l’aide du paramètre `-ConfigurationArchivePath` . Dans le cas contraire, elle publie le fichier .zip dans le stockage d’objets blob Azure, et le sécurise avec un jeton SAP.

Le fichier .zip créé par cette applet de commande inclut le script de configuration .ps1, à la racine du dossier d’archivage. Pour les ressources, le dossier du module est placé dans le dossier d’archivage. 

`Set-AzureVMDscExtension` injecte les paramètres requis par l’extension DSC PowerShell dans un objet de configuration de machine virtuelle. Dans le modèle de déploiement classique, les modifications de la machine virtuelle doivent être appliquées à une machine virtuelle Azure avec `Update-AzureVM`. 

`Get-AzureVMDscExtension` récupère l’état de l’extension DSC d’une machine virtuelle spécifique. 

`Get-AzureVMDscExtensionStatus` récupère l’état de la configuration DSC mise en œuvre par le gestionnaire d’extensions DSC. Cette action peut être effectuée sur une seule machine virtuelle ou sur un groupe de machines virtuelles.

`Remove-AzureVMDscExtension` supprime le gestionnaire d’extensions d’une machine virtuelle donnée. Cette applet de commande ne supprime **pas** la configuration, ne désinstalle pas WMF, et ne modifie pas les paramètres appliqués à la machine virtuelle. Elle ne fait que supprimer le gestionnaire d’extensions. 

**Principales différences dans les applets de commande ASM et Azure Resource Manager**

* Les applets de commande Azure Resource Manager sont synchrones. alors que les applets de commande ARM sont asynchrones.
* Les éléments ResourceGroupName, VMName, ArchiveStorageAccountName, Version et Location sont tous des paramètres requis dans Azure Resource Manager.
* L’élément ArchiveResourceGroupName est un nouveau paramètre facultatif pour Azure Resource Manager. Vous pouvez spécifier ce paramètre lorsque votre compte de stockage appartient à un groupe de ressources différent de celui dans lequel la machine virtuelle est créée.
* L’élément ConfigurationArchive est appelé ArchiveBlobName dans Azure Resource Manager.
* L’élément ContainerName est appelé ArchiveContainerName dans Azure Resource Manager.
* L’élément StorageEndpointSuffix est appelé ArchiveStorageEndpointSuffix dans Azure Resource Manager.
* Le commutateur AutoUpdate a été ajouté dans Azure Resource Manager pour activer la mise à jour automatique du gestionnaire d’extensions vers la dernière version, dès que celle-ci est disponible. Ce paramètre peut entraîner des redémarrages sur la machine virtuelle lors de la publication d’une nouvelle version de la machine virtuelle. 

## <a name="azure-portal-functionality"></a>Fonctionnalités du portail Azure
Accédez à une machine virtuelle. Sous Paramètres -> Général, cliquez sur Extensions. Un volet est créé. Cliquez sur « Ajouter » et sélectionnez PowerShell DSC.

Le portail requiert une entrée.
**Script ou modules de configuration**: ce champ est obligatoire. Cette fonctionnalité nécessite un fichier .ps1 contenant un script de configuration, ou un fichier .zip comprenant un script de configuration .ps1 à la racine, ainsi que toutes les ressources dépendantes dans les dossiers des modules. Cet élément peut être créé avec l’applet de commande `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluse dans le Kit de développement logiciel (SDK) Azure PowerShell. Le fichier ZIP sera chargé dans votre stockage d’objets blob d’utilisateur sécurisé par un jeton SAP. 

**Fichier PSD1 de données de configuration**: ce champ est facultatif. Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour le sélectionner et le charger dans votre stockage d’objets blob d’utilisateur, où il sera sécurisé par un jeton SAP. 

**Nom de configuration qualifié du module**: les fichiers .ps1 peuvent avoir plusieurs fonctions de configuration. Entrez le nom du script PS1 de configuration suivi de '\' et du nom de la fonction de configuration. Par exemple, si votre script PS1 s’appelle « configuration.ps1 » et que la configuration s’appelle « IisInstall », entrez : `configuration.ps1\IisInstall`

**Arguments de configuration** : si la fonction de configuration prend des arguments, entrez-les ici au format `argumentName1=value1,argumentName2=value2`. Il s’agit d’un format d’argument de configuration différent de celui qui est accepté via les applets de commande PowerShell ou les modèles Resource Manager. 

## <a name="getting-started"></a>Prise en main
L’extension Azure DSC récupère les documents de configuration DSC et les met en œuvre sur des machines virtuelles Azure. Voici un exemple simple de configuration. Enregistrez-le en local, sous le nom « IisInstall.ps1 » :

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Les étapes suivantes placent le script IisInstall.ps1 sur la machine virtuelle spécifiée, exécutent la configuration et génèrent un rapport sur l’état.
###<a name="classic-model"></a>Modèle classique
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Modèle Azure Resource Manager

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Journalisation
Les journaux sont placés dans le répertoire suivant :

```
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>étapes suivantes
Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examinez le [modèle Azure Resource Manager pour l’extension DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Pour accéder aux fonctionnalités supplémentaires que vous pouvez gérer avec DSC PowerShell, [parcourez PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour voir des ressources DSC supplémentaires.

Pour en savoir plus sur l’intégration de paramètres sensibles dans des configurations, voir [Gérer les informations d’identification en toute sécurité avec le gestionnaire d’extensions DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

