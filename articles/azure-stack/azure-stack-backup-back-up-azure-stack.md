---
title: "Sauvegarde d’Azure Stack | Microsoft Docs"
description: "Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>Sauvegarde d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Effectuez une sauvegarde à la demande sur Azure Stack avec la sauvegarde en place. Si vous devez activer le service de sauvegarde d’infrastructure, voir la section [Activer la sauvegarde d’Azure Stack à partir du portail d’administration](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Les outils Azure Stack contiennent l’applet de commande **Start-AzSBackup**. Pour obtenir des instructions sur l’installation des outils, consultez [Devenir opérationnel avec PowerShell sur Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

## <a name="start-azure-stack-backup"></a>Démarrer la sauvegarde d’Azure Stack

Ouvrez Windows PowerShell avec une invite dans l’environnement de gestion de l’opérateur et exécutez les commandes suivantes :

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirmer la sauvegarde dans le portail d’administration

1. Ouvrez le portail d’administration Azure Stack ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)).
2. Sélectionnez **Plus de services** > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.
3. Recherchez le **nom** et la **date d’exécution** de la sauvegarde dans la liste des **sauvegardes disponibles**.
4. Vérifiez que l’**état** indique une **réussite**.

Vous pouvez également confirmer la sauvegarde à partir du portail d’administration. Accédez à `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le flux de travail de récupération à partir d’un événement de perte de données. Voir [Récupérer des données suites à une perte catastrophique](azure-stack-backup-recover-data.md).
