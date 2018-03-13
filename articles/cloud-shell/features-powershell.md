---
title: "Fonctionnalités PowerShell dans Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Vue d’ensemble des fonctionnalités de PowerShell dans Azure Cloud Shell"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: 9f089b5c5d573bf469d6920f9144b7cc5c97c03a
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Fonctionnalités et outils pour PowerShell dans Azure Cloud Shell (préversion)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Fonctionnalités et outils pour [Bash](features.md) est également disponible.

PowerShell dans Cloud Shell (préversion) s’exécute sur `Windows Server 2016`.

## <a name="features"></a>Caractéristiques

### <a name="secure-automatic-authentication"></a>Authentification automatique sécurisée

PowerShell dans Cloud Shell (préversion) authentifie automatiquement et en toute sécurité l’accès au compte pour Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Persistance des fichiers entre les sessions

Pour conserver les fichiers entre les sessions, Cloud Shell vous guide à travers le processus d’association d’un partage de fichiers Microsoft Azure au premier lancement.
Par la suite, Cloud Shell associera automatiquement votre espace de stockage (monté sous forme de `$home\clouddrive`) pour toutes les sessions à venir.
Chaque requête pour Cloud Shell allouant une machine temporaire, les fichiers qui ne se trouvent pas dans `$home\clouddrive` et l’état de l’ordinateur ne sont pas persistants d’une session à l’autre.

[En savoir plus sur l’association de partages de fichiers Azure à Cloud Shell.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Lecteur Azure (Azure :)

PowerShell dans Cloud Shell (préversion) vous démarre dans le lecteur Azure (`Azure:`).
Le lecteur Azure facilite la détection et la navigation de ressources Azure telles que les ressources de calcul, réseau, de stockage etc. similaires à la navigation de système de fichiers.
Vous pouvez continuer à utiliser les [applets de commande Azure PowerShell](https://docs.microsoft.com/powershell/azure) que vous connaissez pour gérer ces ressources.
Toutes les modifications apportées aux ressources Azure, effectuées directement dans le portail Azure ou via les applets de commande Azure PowerShell, sont immédiatement répercutées dans le lecteur Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Prise en compte du contexte
- **Étendue du groupe de ressources** : quand dans le contexte d’un chemin d’accès à un groupe de ressources dans le lecteur Azure (`Azure:`), le nom du groupe de ressources est automatiquement transmis aux applets de commande Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand** : cette applet de commande renvoie la liste des commandes applicables dans le contexte de l’emplacement sous le lecteur Azure (`Azure:`). Par exemple, il affiche uniquement les commandes liées au stockage lorsque l’utilisateur est sous `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Modification de script PowerShell enrichi

Lorsque vous utilisez VIM pour modifier des fichiers PowerShell (`.ps1,.psm1,.psd1`), vous obtenez automatiquement la mise en surbrillance de la syntaxe et la prise en charge IntelliSense.
Prise en charge IntelliSense est implémentée via un vim-plug-in qui interagit avec une instance locale de [PowerShell Editor Services](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Utilisez `TAB` pour obtenir la saisie semi-automatique (IntelliSense) sur les noms de commande, les noms de paramètres et les valeurs de paramètres (le cas échéant).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Modèle extensible

[PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget) simplifie l’installation (et la mise à jour) des modules personnalisés et des scripts à partir de [PowerShell Gallery](https://www.powershellgallery.com).
Après l’installation, vos modules sont automatiquement rendus persistants entre les sessions Cloud Shell.

> [!TIP]
> Les modules installés par les utilisateurs sont enregistrés dans le dossier `$Home\CloudDrive\.pscloudshell\WindowsPowerShell`. Un lien symbolique pour ce dossier est créé dans le dossier documents de l’utilisateur (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Gestion des machines virtuelles invitées

À l’aide des deux commandes intégrées - `Enter-AzureRmVM` et `Invoke-AzureRmVMCommand`, vous pouvez gérer à distance vos machines virtuelles Azure.
Ces commandes s’appuient sur la communication à distance PowerShell et nécessitent une connectivité PowerShell aux machines virtuelles Azure.

## <a name="tools"></a>Outils

|**Catégorie**    |**Name**                                 |
|----------------|-----------------------------------------|
|Outils Azure     |[Azure PowerShell (5.2.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI (2.0.26)](https://docs.microsoft.com/cli/azure/overview)|
|Éditeurs de texte    |vim<br> nano                             |
|Gestionnaire de package |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Contrôle de code source  |git                                      |
|Bases de données       |[Module SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [Utilitaire sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Outils de test      |Pester                                   |

## <a name="language-support"></a>Support multilingue

|**Langage**|**Version**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 et [6.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Étapes suivantes

[Démarrage rapide avec PowerShell dans Cloud Shell (préversion)](quickstart-powershell.md)

[En savoir plus sur Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
