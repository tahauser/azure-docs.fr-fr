---
title: "Résoudre les problèmes liés à System Center Data Protection Manager avec Sauvegarde Azure | Microsoft Docs"
description: "Résoudre les problèmes dans System Center Data Protection Manager."
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Résoudre les problèmes liés à System Center Data Protection Manager

Vous trouverez les dernières notes de publication pour SC DPM [ici](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016).
La matrice de prise en charge de la protection est disponible [ici](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="replica-is-inconsistent"></a>Le réplica est incohérent.

Cette erreur peut se produire pour diverses raisons, telles que l’échec du travail de création du réplica, des problèmes liés au journal des modifications, des erreurs de filtrage du niveau du volume des bitmaps, l’arrêt inattendu de la machine source, le débordement du journal de synchronisation ou l’incohérence du réplica. Suivez ces étapes pour résoudre ce problème :
- Pour supprimer l’état incohérent, exécutez la vérification de cohérence manuellement ou planifiez une vérification de cohérence quotidienne.
- Vérifiez que vous disposez de la dernière version de MAB Server ou System Center DPM.
- Assurez-vous que la vérification de cohérence automatique est activée.
- Essayez de redémarrer les services à partir de l’invite de commandes (« net stop dpmra », puis « net start dpmra »).
- Vérifiez que les exigences de connectivité et de bande passante réseau sont respectées.
- Vérifiez si la machine source a été arrêtée de manière inattendue.
- Vérifiez que le disque est intègre et que l’espace est suffisant pour le réplica.
- Vérifiez qu’aucun travail de sauvegarde n’est en cours d’exécution en même temps qu’un autre.

## <a name="online-recovery-point-creation-failed"></a>Échec lors de la création de points de récupération

Suivez ces étapes pour résoudre ce problème :
- Vérifiez que vous disposez de la dernière version de l’agent Sauvegarde Azure.
- Essayez de créer manuellement un point de récupération dans la zone de tâches Protection.
- Assurez-vous d’exécuter la vérification de cohérence sur la source de données.
- Vérifiez que les exigences de connectivité et de bande passante réseau sont respectées.
- L’état des données du réplica n’est pas cohérent. Créez un point de récupération de disque de cette source de données.
- Vérifiez que le réplica est présent et non manquant.
- Le réplica doit disposer de suffisamment d’espace pour créer le journal USN.

## <a name="unable-to-configure-protection"></a>Configuration de la protection impossible.

Cette erreur apparaît quand le serveur DPM ne peut pas contacter le serveur protégé. Suivez ces étapes pour résoudre ce problème :
- Vérifiez que vous disposez de la dernière version de l’agent Sauvegarde Azure.
- Vérifiez que votre serveur DPM et le serveur protégé sont connectés (réseau/pare-feu/proxy).
- Si vous protégez SQL Server, vérifiez que sysadmin est activé pour NT AUTHORITY\SYSTEM dans les propriétés de la connexion.

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>Ce serveur n’est pas inscrit au coffre spécifié par les informations d’identification du coffre

Cette erreur apparaît quand le fichier des informations d’identification du coffre sélectionné n’appartient pas au coffre Recovery Services associé au serveur de sauvegarde Azure/System Center DPM sur lequel la récupération est tentée. Suivez ces étapes pour résoudre ce problème :
- Téléchargez le fichier des informations d’identification du coffre Recovery Services pour lequel le serveur de sauvegarde Azure/System Center DPM est inscrit.
- Essayez d’inscrire le serveur auprès du coffre en utilisant le dernier fichier d’informations d’identification du coffre téléchargé.

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>Les données récupérables ne sont pas disponibles ou bien le serveur sélectionné n’est pas un serveur DPM
Cette erreur apparaît quand aucun autre serveur de sauvegarde Azure/System Center DPM n’est inscrit auprès du coffre Recovery Services, que les serveurs n’ont pas encore chargé les métadonnées ou que le serveur sélectionné n’est pas un serveur de sauvegarde Azure/System Center DPM.
- S’il existe d’autres serveurs de sauvegarde Azure/System Center DPM inscrits auprès du coffre Recovery Services, assurez-vous que le dernier agent Sauvegarde Azure est installé.
- S’il existe d’autres serveurs Azure Backup/System Center DPM inscrits auprès du coffre Recovery Services, patientez un jour après l’installation pour lancer le processus de récupération. Le travail nocturne charge les métadonnées de toutes les sauvegardes protégées sur le cloud. Les données sont disponibles pour la récupération.

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>La phrase secrète saisie ne correspond pas à la phrase secrète associée au serveur suivant

> [!NOTE]
>Si vous avez oublié/perdu la phrase secrète de chiffrement, il est impossible de récupérer les données. La seule option consiste à regénérer la phrase secrète et à l’utiliser pour chiffrer les données de sauvegarde futures.
>
>

Cette erreur apparaît quand la phrase secrète de chiffrement utilisée dans le processus de chiffrement des données à partir des données du serveur de sauvegarde Azure/System Center DPM en cours de récupération ne correspond pas à la phrase secrète de chiffrement fournie. L'agent ne peut pas déchiffrer les données. Par conséquent, la récupération échoue. Suivez ces étapes pour résoudre ce problème :
- Fournissez la phrase secrète de chiffrement associée au serveur de sauvegarde Azure/System Center DPM pour lequel les données sont en cours de récupération. 
