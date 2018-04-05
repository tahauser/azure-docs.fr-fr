---
title: Guide pratique pour configurer les sorties de données pour les travaux Stream Analytics | Microsoft Docs
description: Configurer les sorties pour les tâches Stream Analytics | segment du parcours d’apprentissage.
keywords: sortie de données, déplacement des données
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: jeanb
ms.openlocfilehash: 9e1a8640421c4232cf4abc09794ca584995906f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Comment configurer les sorties de données pour les tâches Stream Analytics

Les tâches Azure Stream Analytics peuvent être connectées à une ou plusieurs sorties de données qui définissent une connexion à un récepteur de données existant. Quand votre travail Stream Analytics traite et transforme les données entrantes, un flux d’événements de sortie de données est écrit dans la sortie de votre travail.

Les sorties de données Stream Analytics peuvent servir de source aux tableaux de bord ou aux alertes en temps réel, déclencher des workflows de déplacement ou simplement archiver les données pour le traitement par lot ultérieurement. Stream Analytics bénéficie d'une intégration de première classe avec plusieurs services Azure, qui sont expliqués en détail ici.

Pour ajouter une sortie à votre tâche Stream Analytics :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre tâche et cliquez successivement sur **Sorties**, puis sur **Ajouter** dans le panneau Sorties qui s’affiche.
   
    ![Ajout de sorties](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Attribuez un nom convivial à cette sortie dans la zone **Alias de sortie** . Ce nom pourra être utilisé dans la requête de votre tâche plus tard pour faire référence à la sortie.  
   
    Renseignez le reste des propriétés de connexion requises pour vous connecter à votre sortie.  Ces champs varient selon le type de sortie et sont définis en détail ici.  
   
    ![Choisir le type de déplacement des données](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. Selon le type de sortie, vous devrez peut-être spécifier comment les données sont sérialisées ou mises en forme. Les paramètres de sérialisation spécifiques pour chaque type de sortie sont décrits ici.
   
    Renseignez le reste des propriétés de connexion requises pour vous connecter à votre source de données. Ces champs varient selon le type d’entrée et le type de source et sont définis en détail dans l’[article Créer une tâche](stream-analytics-create-a-job.md).  

> [!Note]
>
> Tout élément de sortie ajouté à la tâche doit exister avant que la tâche soit démarrée et que les événements commencent à transiter. Par exemple, si vous utilisez le stockage d'objets Blob en tant que sortie, la tâche ne créera pas de compte de stockage automatiquement. Celui-ci doit être créé par l'utilisateur avant que la tâche ASA soit démarrée.
> 
 

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

