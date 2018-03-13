---
title: "Sauvegarde et récupération de données pour Azure Stack avec le service Infrastructure Backup | Microsoft Docs"
description: "Vous pouvez sauvegarder et restaurer la configuration et les données du service en utilisant le service Infrastructure Backup."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 84ce0d72ff826ecb3f5deff165db00a1e50ae89d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Sauvegarde et récupération de données pour Azure Stack avec le service Infrastructure Backup

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez sauvegarder et restaurer la configuration et les données du service en utilisant le service Infrastructure Backup. Chaque installation Azure Stack contient une instance du service. Vous pouvez utiliser des sauvegardes créées par le service pour le redéploiement du cloud Azure Stack visant à restaurer les données d’identité, de sécurité et d’Azure Resource Manager.

Vous pouvez activer la sauvegarde quand vous êtes prêt à passer votre cloud en production. N’activez pas la sauvegarde si vous prévoyez d’effectuer les tests et la validation pendant longtemps.

Avant d’activer votre service de sauvegarde, vérifiez que [la configuration requise est en place](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Le service Infrastructure Backup n’inclut pas les données utilisateur ni les applications. Consultez les articles suivants pour obtenir des instructions sur la sauvegarde et la restauration des fournisseurs de ressources [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql) et [MySQL](https://aka.ms/azure-stack-mysql), et des données utilisateur associées.

## <a name="the-infrastructure-backup-service"></a>Le service Infrastructure Backup

Le service contient les fonctionnalités suivantes.

| Fonctionnalité                                            | Description                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Services d’infrastructure de sauvegarde                     | Coordonnent la sauvegarde sur une partie des services d’infrastructure d’Azure Stack. Si un sinistre se produit, les données peuvent être restaurées lors d’un redéploiement. |
| Compression et chiffrement des données de sauvegarde exportées | Les données de sauvegarde sont compressées et chiffrées par le système avant d’être exportées vers l’emplacement de stockage externe fourni par l’administrateur.                |
| Surveillance des tâches de sauvegarde                              | Le système envoie une notification quand les tâches de sauvegarde échouent et indique les étapes de correction.                                                                                                |
| Expérience de gestion des sauvegardes                       | Backup RP prend en charge l’activation de la sauvegarde.                                                                                                                         |
| Récupération cloud                                     | Si une perte catastrophique de données se produit, les sauvegardes peuvent être utilisées pour restaurer les informations Azure Stack principales lors d’un déploiement.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Vérifier la configuration requise pour le service Infrastructure Backup

- **Emplacement de stockage**  
  Vous avez besoin d’un partage de fichiers accessible depuis Azure Stack et pouvant contenir sept sauvegardes. Chaque sauvegarde est d’environ 10 Go. Votre partage doit pouvoir stocker 140 Go de sauvegardes. Pour plus d’informations sur la sélection d’un emplacement de stockage pour le service Azure Stack Infrastructure Backup, consultez [Configuration requise pour le contrôleur de sauvegarde](azure-stack-backup-reference.md#backup-controller-requirements).
- **Informations d'identification**  
  Vous avez besoin d’un compte d’utilisateur de domaine et d’informations d’identification : par exemple, vous pouvez utiliser les informations d’identification de l’administrateur d’Azure Stack.
- **Clé de chiffrement**  
  Les fichiers de sauvegarde sont chiffrés avec cette clé. Pensez à stocker cette clé à un emplacement sécurisé. Une fois que vous avez défini cette clé pour la première fois ou que vous procédez ultérieurement à une rotation de la clé, vous ne pouvez pas voir cette clé à partir de cette interface. Pour obtenir plus d’instructions pour générer une clé prépartagée, suivez les scripts dans [Activer la sauvegarde d’Azure Stack avec PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [activer la sauvegarde d’Azure Stack à partir du portail d’administration](azure-stack-backup-enable-backup-console.md).
- Découvrez comment [activer la sauvegarde d’Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Découvrez comment [sauvegarder Azure Stack](azure-stack-backup-back-up-azure-stack.md )
- Découvrez comment [récupérer des données suite à une perte catastrophique](azure-stack-backup-recover-data.md)
