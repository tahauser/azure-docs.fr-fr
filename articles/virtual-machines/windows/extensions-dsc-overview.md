---
title: Présentation de la configuration d’état souhaité pour Azure | Microsoft Docs
description: Apprenez à utiliser le gestionnaire d’extensions Microsoft Azure pour la configuration d’état souhaité (DSC) PowerShell. Cet article décrit la configuration requise et l’architecture, et contient des applets de commande.
services: virtual-machines-windows
documentationcenter: ''
author: mgreenegit
manager: timlt
editor: ''
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
ms.openlocfilehash: 5b16261c9a9f046b7bc55a06dd71aa154a0cec27
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Présentation du gestionnaire d’extensions de configuration d’état souhaité Microsoft Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

L’agent de machine virtuelle Microsoft Azure et les extensions associées font partie des services d’infrastructure Microsoft Azure. Les extensions de machine virtuelle sont des composants logiciels qui étendent les fonctionnalités d’une machine virtuelle et simplifient ses diverses opérations de gestion.

L’extension de configuration d’état souhaité (DSC) Azure sert principalement à amorcer une machine virtuelle sur le [service Azure Automation DSC](../../automation/automation-dsc-overview.md). L’amorçage d’une machine virtuelle offre divers [avantages](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#pull-service), notamment la gestion en continu de la configuration de la machine virtuelle et l’intégration avec d’autres outils opérationnels, tels que Azure Monitoring.

Vous pouvez utiliser l’extension DSC indépendamment du service Automation DSC. Cela implique cependant une action bien spécifique qui intervient au cours du déploiement. Aucune fonction de création de rapports ou de gestion de la configuration n’est disponible si ce n’est en local, au niveau de la machine virtuelle.

Cet article fournit des informations sur deux scénarios : l’utilisation de l’extension DSC pour l’intégration d’Automation et l’utilisation de l’extension DSC en tant qu’outil permettant d’attribuer des configurations à des machines virtuelles à l’aide du Kit de développement logiciel (SDK) Azure.

## <a name="prerequisites"></a>Prérequis

- **Ordinateur local**: pour pouvoir interagir avec l’extension de machine virtuelle Azure, vous devez utiliser le portail Azure ou le Kit de développement logiciel (SDK) Azure PowerShell.
- **Agent invité** : la machine virtuelle Azure définie par la configuration DSC doit inclure un système d’exploitation prenant en charge Windows Management Framework (WMF) version 4.0 ou ultérieure. Pour la liste complète des versions de système d’exploitation prises en charge, voir [l’historique des versions de l’extension DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termes et concepts

Ce guide part du principe que vous connaissez les concepts suivants :

- **Configuration** : document de configuration DSC.
- **Nœud** : cible d’une configuration DSC. Dans ce document, le terme *nœud* fait toujours référence à une machine virtuelle Azure.
- **Données de configuration** : fichier .psd1 contenant les données d’environnement pour une configuration.

## <a name="architecture"></a>Architecture

L’extension DSC d’Azure utilise l’infrastructure de l’agent Azure VM pour fournir, mettre en œuvre et créer des rapports sur les configurations DSC sur des machines virtuelles Azure. L’extension DSC accepte un document de configuration et un ensemble de paramètres. Si aucun fichier n’est fourni, un [script de configuration par défaut](#default-configuration-script) est incorporé avec l’extension. Le script de configuration par défaut est utilisé uniquement pour définir les métadonnées dans le [Gestionnaire de configuration local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Lorsque l’extension est appelée pour la première fois, elle installe une version de WMF en utilisant la logique suivante :

* Si le système d’exploitation de la machine virtuelle Azure est Windows Server 2016, aucune action n’est effectuée. En effet, la dernière version de PowerShell est installée sur Windows Server 2016.
* Si la propriété **wmfVersion** est spécifiée, cette version de WMF est installée, sauf si elle est incompatible avec le système d’exploitation de la machine virtuelle.
* Si aucune propriété **wmfVersion** n’est spécifiée, la dernière version applicable de WMF est installée.

L’installation de WMF nécessite un redémarrage. Après le redémarrage, l’extension télécharge le fichier .zip spécifié dans la propriété **modulesUrl**, s’il est fourni. Si cet emplacement figure dans le stockage Blob Azure, vous pouvez spécifier un jeton SAP dans la propriété **sasToken** pour l’accès au fichier. Une fois le fichier ZIP téléchargé et décompressé, la fonction de configuration définie dans **configurationFunction** est exécutée pour générer un fichier .mof. L’extension exécute ensuite **Start-DscConfiguration-Force** en utilisant le fichier .mof généré. L’extension capture la sortie et l’écrit dans le canal d’état Azure.

### <a name="default-configuration-script"></a>Script de configuration par défaut

L’extension DSC Azure inclut un script de configuration par défaut destiné à être utilisé lorsque vous intégrez une machine virtuelle au service Azure Automation DSC. Les paramètres de script sont alignés sur les propriétés configurables du [Gestionnaire de configuration local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig). Pour les paramètres de script, consultez [Script de configuration par défaut](extensions-dsc-template.md#default-configuration-script) dans [Extension de configuration d’état souhaité avec des modèles Azure Resource Manager](extensions-dsc-template.md). Pour le script complet, consultez le [modèle de démarrage rapide Azure dans GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Extension DSC dans les modèles Resource Manager

Dans la plupart des scénarios, les modèles de déploiement Resource Manager sont la méthode prévue pour travailler avec l’extension DSC. Pour plus d’informations et pour obtenir des exemples d’inclusion de l’extension DSC dans les modèles de déploiement Resource Manager, voir l’article [Extension de configuration d’état souhaité avec des modèles Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Applets de commande PowerShell de l’extension DSC

Les applets de commande PowerShell utilisés pour la gestion de l’extension DSC sont idéalement utilisés dans les scénarios interactifs de résolution de problèmes et de collecte d’informations. Vous pouvez utiliser les applets de commande pour empaqueter, publier et surveiller des déploiements de l’extension DSC. Notez que les applets de commande pour l’extension DSC n’ont pas encore été mises à jour pour fonctionner avec le [Script de configuration par défaut](#default-configuration-script).

L’applet de commande **Publish-AzureRMVMDscConfiguration** récupère un fichier de configuration, l’analyse pour y trouver les ressources DSC dépendantes, puis crée un fichier .zip. Le fichier .zip contient la configuration et les ressources DSC nécessaires pour déployer la configuration. La cmdlet peut également créer le package en local en utilisant le paramètre *-OutputArchivePath*. Dans le cas contraire, elle publie le fichier .zip dans le stockage Blob Azure, et le sécurise avec un jeton SAP.

Le script de configuration .ps1 créé par cette applet de commande se trouve dans le fichier .zip placé à la racine du dossier d’archivage. Le dossier du module est placé dans le dossier d’archivage, sous les ressources.

L’applet de commande **Set-AzureRMVMDscExtension** injecte les paramètres requis par l’extension DSC PowerShell dans un objet de configuration de la machine virtuelle.

L’applet de commande **Get-AzureRMVMDscExtension** extrait l’état de l’extension DSC d’une machine virtuelle spécifique.

L’applet de commande **Get-AzureRMVMDscExtensionStatus** extrait l’état de la configuration DSC imposée par le gestionnaire d’extensions DSC. Cette action peut être effectuée sur une seule machine virtuelle ou sur un groupe de machines virtuelles.

L’applet de commande **Remove-AzureRMVMDscExtension** supprime le gestionnaire d’extensions d’une machine virtuelle spécifique. Cette applet de commande ne supprime *pas* la configuration, ne désinstalle pas WMF, et ne modifie pas les paramètres appliqués à la machine virtuelle. Elle ne fait que supprimer le gestionnaire d’extensions. 

Informations importantes sur les applets de commande de l’extension DSC de Resource Manager :

- Les applets de commande Azure Resource Manager sont synchrones.
- Les paramètres *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* et *Location* sont tous obligatoires.
- Le paramètre *ArchiveResourceGroupName* est facultatif. Vous pouvez spécifier ce paramètre lorsque votre compte de stockage appartient à un groupe de ressources différent de celui dans lequel la machine virtuelle est créée.
- Utilisez le commutateur **AutoUpdate** pour mettre automatiquement à jour le gestionnaire d’extensions vers la dernière version dès que celle-ci est disponible. Ce paramètre peut entraîner des redémarrages sur la machine virtuelle lors de la publication d’une nouvelle version de WMF.

### <a name="get-started-with-cmdlets"></a>Prise en main des applets de commande

L’extension DSC Azure peut utiliser des documents de configuration DSC pour configurer directement des machines virtuelles Azure pendant le déploiement. Cette étape n’enregistre pas le nœud auprès d’Automation. Le nœud n’est *pas* géré de manière centralisée.

L’exemple suivant illustre un exemple simple de configuration. Enregistrez la configuration en local en tant que IisInstall.ps1.

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

Les commandes suivantes placent le script IisInstall.ps1 sur la machine virtuelle spécifiée. Les commandes exécutent également la configuration, puis génèrent un rapport d’état.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Fonctionnalités du portail Azure

Pour configurer DSC dans le portail :

1. Accédez à une machine virtuelle. 
2. Sous **Paramètres** > **Général**, sélectionnez **Extensions**. 
3. Dans le nouveau volet qui s’affiche, sélectionnez **Ajouter**, puis sélectionnez **DSC PowerShell**.

Le portail a besoin des données suivantes :

* **Script ou modules de configuration** : ce champ est obligatoire (le formulaire n’a pas été mis à jour pour le [script de configuration par défaut](#default-configuration-script)). Les scripts et modules de configuration nécessitent un fichier .ps1 qui contient un script de configuration ou un fichier .zip avec un script de configuration .ps1 à la racine. Si vous utilisez un fichier .zip, toutes les ressources dépendantes doivent figurer dans les dossiers de module à l’intérieur du fichier .zip. Vous pouvez créer le fichier .zip à l’aide de la cmdlet **Publish-AzureVMDscConfiguration -OutputArchivePath** incluse dans le Kit de développement logiciel (SDK) Azure PowerShell. Le fichier .zip sera chargé dans votre stockage d’objets blob d’utilisateur et sécurisé par un jeton SAP.

* **Fichier PSD1 de données de configuration**: ce champ est facultatif. Si votre configuration nécessite un fichier de données de configuration dans .psd1, utilisez ce champ pour sélectionner le champ de données et le charger dans votre stockage d’objets blob d’utilisateur. Le fichier de données de configuration est sécurisé par un jeton SAP dans le stockage Blob.

* **Nom de configuration qualifié du module** : vous pouvez inclure plusieurs fonctions de configuration dans un fichier .ps1. Entrez le nom du script .ps1 de configuration suivi de \\ et du nom de la fonction de configuration. Par exemple, si votre script .ps1 s’appelle « configuration.ps1 » et que la configuration s’appelle **IisInstall**, entrez **configuration.ps1\IisInstall**.

* **Arguments de configuration** : si la fonction de configuration prend des arguments, entrez-les ici au format **argumentName1=value1,argumentName2=value2**. Il s’agit d’un format d’argument de configuration différent de celui qui est accepté via les applets de commande PowerShell ou les modèles Resource Manager.

## <a name="logs"></a>Journaux
Placez les journaux à cet emplacement :

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus informations sur DSC PowerShell, reportez-vous au [centre de documentation PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Examinez le [modèle Resource Manager pour l’extension DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Pour obtenir plus de fonctionnalités gérables avec DSC PowerShell ainsi que plus de ressources DSC, parcourez la [galerie PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Pour en savoir plus sur l’intégration de paramètres sensibles dans des configurations, voir [Gérer les informations d’identification en toute sécurité avec le gestionnaire d’extensions DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

