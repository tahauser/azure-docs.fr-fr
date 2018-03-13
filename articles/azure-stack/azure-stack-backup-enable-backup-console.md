---
title: "Activer la sauvegarde d’Azure Stack à partir du portail d’administration | Microsoft Docs"
description: "Activez le service de sauvegarde d’infrastructure via le portail d’administration pour permettre la restauration d’Azure Stack en cas de panne."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 456a0db9771f5963c8d4375d54a22257f6ca1c56
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Activer la sauvegarde d’Azure Stack à partir du portail d’administration

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Activez le service de sauvegarde d’infrastructure via le portail d’administration afin qu’Azure Stack puisse générer des sauvegardes. Vous pouvez utiliser ces sauvegardes pour restaurer votre environnement en cas de panne.

> [!Note]  
> Avant d’activer la sauvegarde via la console, vous devez configurer le service de sauvegarde. Vous pouvez configurer le service de sauvegarde à l’aide de PowerShell. Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Activer la sauvegarde

1. Ouvrez le portail d’administration Azure Stack ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)).
2. Sélectionnez **Plus de services** > **Sauvegarde d’infrastructure**. Choisissez **Configuration** dans le panneau **Sauvegarde d’infrastructure**.

    ![Azure Stack - Paramètres du contrôleur de sauvegarde](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Saisissez le chemin d’accès à l’**emplacement de stockage de sauvegarde**. Utilisez une chaîne UNC (Universal Naming Convention) pour le chemin d’un partage de fichiers hébergé sur un appareil distinct. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés. Pour le service, vous pouvez utiliser une adresse IP. Pour garantir la disponibilité des données de sauvegarde après un sinistre, l’appareil doit se trouver dans un emplacement distinct.
    > [!Note]  
    > Si votre environnement prend en charge la résolution de noms à partir du réseau d’infrastructure Azure Stack vers votre environnement d’entreprise, vous pouvez utiliser un nom de domaine qualifié complet plutôt que l’adresse IP.
4. Saisissez le **nom d’utilisateur** à l’aide du domaine et du nom d’utilisateur. Par exemple : `Contoso\administrator`.
5. Saisissez le **mot de passe** de l’utilisateur.
5. Saisissez une nouvelle fois le mot de passe pour le **confirmer**.
6. Indiquez une clé prépartagée dans la zone **Clé de chiffrement**. Les fichiers de sauvegarde sont chiffrés avec cette clé. Pensez à stocker cette clé à un emplacement sécurisé. Une fois que vous avez défini cette clé pour la première fois ou que vous procédez ultérieurement à une rotation de la clé, vous ne pouvez pas voir cette clé à partir de cette interface. Pour obtenir plus d’instructions en vue de générer une clé prépartagée, suivez les scripts de la rubrique [Activer la sauvegarde d’Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Sélectionnez **OK** pour enregistrer vos paramètres de contrôleur de sauvegarde.

Pour exécuter une sauvegarde, vous devez télécharger les outils Azure Stack, puis exécuter la cmdlet PowerShell **Start-AzSBackup** sur votre nœud administration Azure Stack. Pour plus d’informations, voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Étapes suivantes

 - Apprenez à exécuter une sauvegarde. Voir [Sauvegarde d’Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Apprenez à vérifier que votre sauvegarde a été exécutée. Voir [Confirmer la sauvegarde dans le portail d’administration](azure-stack-backup-back-up-azure-stack.md ).
