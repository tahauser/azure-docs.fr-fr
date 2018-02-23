---
title: "Présentation de la configuration d’état souhaité pour Azure | Microsoft Docs"
description: "Présentation associée à l’utilisation du gestionnaire d’extensions Microsoft Azure pour la configuration d’état souhaité Powershell. Cela inclut les conditions préalables, l’architecture, les applets de commande, etc."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

L’agent de machine virtuelle Microsoft Azure et les extensions associées font partie des services d’infrastructure Microsoft Azure.
Les extensions de machine virtuelle sont des composants logiciels qui étendent les fonctionnalités d’une machine virtuelle et simplifient les diverses opérations de gestion de celle-ci.

Le principal cas d’utilisation de l’extension de configuration d’état souhaité est pour démarrer une machine virtuelle sur le [service Azure Automation DSC](../../automation/automation-dsc-overview.md) qui offre certains [avantages](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service), parmi lesquels la gestion en continu de la configuration et de l’intégration de machine virtuelle avec d’autres outils opérationnels tels que la Surveillance Azure.

Il est également possible d’utiliser l’extension DSC indépendamment du service Azure Automation DSC. Toutefois, il s’agit d’une action unique qui se produit en cours de déploiement et il n’y a pas de création de rapports ou de gestion de la configuration en continu autres que localement à l’intérieur de la machine virtuelle.

Cet article contient des informations pour les deux scénarios, l’extension DSC pour l’intégration d’Azure Automation et l’utilisation de l’extension DSC en tant qu’outil pour attribuer des configurations à des machines virtuelles à l’aide du Kit de développement logiciel (SDK) Azure.

## <a name="prerequisites"></a>configuration requise

- **Ordinateur local**: pour pouvoir interagir avec l’extension de machine virtuelle Azure, utilisez le portail Azure ou le Kit de développement logiciel (SDK) Azure PowerShell.
- **Agent invité** : la machine virtuelle Azure définie par la configuration DSC doit inclure un système d’exploitation prenant en charge Windows Management Framework (WMF) 4.0 ou 5.0. Pour la liste complète des versions de système d’exploitation prises en charge, voir la page de l’[historique des versions de l’extension DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termes et concepts

Ce guide part du principe que vous connaissez les concepts suivants :

- **Configuration** : document de configuration DSC.
- **Nœud** : cible d’une configuration DSC. Dans ce document, le terme « nœud » fait toujours référence à une machine virtuelle Azure.
- **Données de configuration** : fichier .psd1 contenant les données d’environnement pour une configuration.

## <a name="architectural-overview"></a>Présentation de l’architecture

L’extension DSC d’Azure utilise l’infrastructure de l’agent Azure VM pour fournir, mettre en œuvre et créer des rapports sur les configurations DSC sur des machines virtuelles Azure.
L’extension DSC accepte un document de configuration et un ensemble de paramètres.
Si aucun fichier n’est fourni, un [script de configuration par défaut](###default-configuration-script) est incorporé avec l’extension utilisée uniquement pour la définition des métadonnées dans le [Gestionnaire de configuration local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Lorsque l’extension est appelée pour la première fois, elle installe une version de Windows Management Framework (WMF) en utilisant la logique suivante :

1. Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est effectuée. En effet, la dernière version de PowerShell est installée sur Windows Server 2016.
2. Si la propriété `wmfVersion` est spécifiée, cette version de WMF est installée, sauf si elle est incompatible avec le système d’exploitation de la machine virtuelle.
3. Si aucune propriété `wmfVersion` n’est spécifiée, la dernière version applicable de WMF est installée.

L’installation de WMF nécessite un redémarrage.
Après redémarrage, l’extension télécharge le fichier .zip spécifié dans la propriété `modulesUrl`, s’il est fourni.
Si cet emplacement figure dans le stockage d’objets blob Azure, un jeton SAP peut être spécifié dans la propriété `sasToken` pour l’accès au fichier.
Une fois le fichier ZIP téléchargé et décompressé, la fonction de configuration définie dans `configurationFunction` est exécutée pour générer un fichier MOF.
Ensuite, l’extension exécute la commande `Start-DscConfiguration -Force` en utilisant le fichier MOF généré.
L’extension capture la sortie et l’écrit dans le canal d’état Azure.

### <a name="default-configuration-script"></a>Script de configuration par défaut

L’extension DSC Azure inclut un script de configuration par défaut destiné à être utilisé lorsque l’intégration une machine virtuelle au service Azure Automation DSC.
Les paramètres de script sont alignés sur les propriétés configurables du [Gestionnaire de configuration local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
Les paramètres du script sont [documentés](extensions-dsc-template.md##default-configuration-script) et le script complet est disponible dans [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Extension DSC dans les modèles ARM

Les modèles de déploiement Azure Resource Manager (ARM) sont la méthode prévue pour travailler avec l’extension DSC dans la plupart des scénarios.
Pour plus d’informations et des exemples d’inclusion de l’extension DSC dans les modèles de déploiement ARM, voir la page de documentation dédié [Extension de configuration d’état souhaité avec des modèles Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Applets de commande PowerShell de l’extension DSC

Les applets de commande PowerShell pour la gestion de l’extension DSC sont idéalement utilisés dans les scénarios interactifs de résolution de problèmes et de collecte d’informations.
Les applets de commande PowerShell peuvent être utilisées pour empaqueter, publier et surveiller des déploiements de l’extension DSC.
Veuillez noter que les applets de commande pour l’extension DSC n’ont pas encore été mises à jour pour fonctionner avec le [Script de configuration par défaut](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration` récupère un fichier de configuration, l’analyse à la recherche de ressources DSC dépendantes, puis crée un fichier .zip contenant la configuration et les ressources DSC nécessaires pour mettre en œuvre la configuration.
Elle peut aussi créer le package en local, à l’aide du paramètre `-ConfigurationArchivePath` .
Dans le cas contraire, elle publie le fichier .zip dans le stockage d’objets blob Azure, et le sécurise avec un jeton SAP.

Le fichier .zip créé par cette applet de commande inclut le script de configuration .ps1, à la racine du dossier d’archivage.
Pour les ressources, le dossier du module est placé dans le dossier d’archivage.

`Set-AzureRMVMDscExtension` injecte les paramètres requis par l’extension DSC PowerShell dans un objet de configuration de machine virtuelle.

`Get-AzureRMVMDscExtension` récupère l’état de l’extension DSC d’une machine virtuelle spécifique.

`Get-AzureRMVMDscExtensionStatus` récupère l’état de la configuration DSC mise en œuvre par le gestionnaire d’extensions DSC.
Cette action peut être effectuée sur une seule machine virtuelle ou sur un groupe de machines virtuelles.

`Remove-AzureRMVMDscExtension` supprime le gestionnaire d’extensions d’une machine virtuelle donnée.
Cette applet de commande ne supprime **pas** la configuration, ne désinstalle pas WMF, et ne modifie pas les paramètres appliqués à la machine virtuelle.
Elle ne fait que supprimer le gestionnaire d’extensions. 

Informations importantes concernant les applets de commande de l’extension DSC AzureRM :

- Les applets de commande Azure Resource Manager sont synchrones.
- Les paramètres ResourceGroupName, VMName, ArchiveStorageAccountName, Version et Location sont tous requis.
- Le paramètre ArchiveResourceGroupName est facultatif. Vous pouvez spécifier ce paramètre lorsque votre compte de stockage appartient à un groupe de ressources différent de celui dans lequel la machine virtuelle est créée.
- Le commutateur AutoUpdate active la mise à jour automatique du gestionnaire d’extensions vers la dernière version dès que celle-ci est disponible. Ce paramètre peut entraîner des redémarrages sur la machine virtuelle lors de la publication d’une nouvelle version de la machine virtuelle.

### <a name="getting-started-with-cmdlets"></a>Prise en main des applets de commande

L’extension DSC Azure est capable d’utiliser des documents de configuration DSC directement pour configurer des machines virtuelles Azure pendant le déploiement, même si cela n’a pas pour effet d’inscrire le nœud sur Azure Automation, de sorte que le nœud n’est **PAS* géré de manière centralisée.

Voici un exemple simple de configuration.
Enregistrez-le en local, sous le nom « IisInstall.ps1 » :

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

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Fonctionnalités du portail Azure

Accédez à une machine virtuelle. Sous Paramètres -> Général, cliquez sur Extensions.
Un volet est créé.
Cliquez sur « Ajouter » et sélectionnez PowerShell DSC.

Le portail requiert une entrée.
**Script ou modules de configuration** : ce champ est obligatoire (le formulaire n’a pas été mis à jour pour le [Script de configuration par défaut](###default-configuration-script)).
Cette fonctionnalité nécessite un fichier .ps1 contenant un script de configuration, ou un fichier .zip comprenant un script de configuration .ps1 à la racine, ainsi que toutes les ressources dépendantes dans les dossiers des modules.
Cet élément peut être créé avec l’applet de commande `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluse dans le Kit de développement logiciel (SDK) Azure PowerShell.
Le fichier ZIP sera chargé dans votre stockage d’objets blob d’utilisateur sécurisé par un jeton SAP.

**Fichier PSD1 de données de configuration**: ce champ est facultatif.
Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour le sélectionner et le charger dans votre stockage d’objets blob d’utilisateur, où il sera sécurisé par un jeton SAP.

**Nom de configuration qualifié du module**: les fichiers .ps1 peuvent avoir plusieurs fonctions de configuration.
Entrez le nom du script PS1 de configuration suivi de '\' et du nom de la fonction de configuration.
Par exemple, si votre script PS1 s’appelle « configuration.ps1 » et que la configuration s’appelle « IisInstall », entrez : `configuration.ps1\IisInstall`

**Arguments de configuration** : si la fonction de configuration prend des arguments, entrez-les ici au format `argumentName1=value1,argumentName2=value2`.
Il s’agit d’un format d’argument de configuration différent de celui qui est accepté via les applets de commande PowerShell ou les modèles Resource Manager.

## <a name="logging"></a>Journalisation
Les journaux sont placés dans le répertoire suivant :

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>étapes suivantes
Pour plus informations sur DSC PowerShell, [voir le centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examinez le [modèle Azure Resource Manager pour l’extension DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Pour accéder aux fonctionnalités supplémentaires que vous pouvez gérer avec DSC PowerShell, [parcourez PowerShell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) pour voir des ressources DSC supplémentaires.

Pour en savoir plus sur l’intégration de paramètres sensibles dans des configurations, voir [Gérer les informations d’identification en toute sécurité avec le gestionnaire d’extensions DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
