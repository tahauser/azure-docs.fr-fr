---
title: Résoudre les problèmes d’événements d’entrée mal formée dans Azure Stream Analytics | Microsoft Docs
description: Déterminer l’événement qui pose problème dans une tâche Azure Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problèmes courants dans Stream Analytics et étapes de dépannage

## <a name="troubleshoot-for-malformed-input-events"></a>Résoudre les problèmes d’événements d’entrée mal formée

Si le flux d’entrée de votre tâche Stream Analytics contient des messages mal formés, des problèmes de sérialisation peuvent se produire. Ces messages mal formés peuvent être liés à une sérialisation incorrecte, comme dans le cas d’une parenthèse manquante dans un objet JSON ou d’un format d’horodatage incorrect. Lorsqu’une tâche Stream Analytics reçoit un message mal formé, elle le supprime et affiche un avertissement pour l’utilisateur. Un symbole d’avertissement s’affiche dans la vignette **Entrées** de votre tâche Stream Analytics. (Ce signe d’avertissement existe tant que la tâche est en cours d’exécution) :

![Vignette Entrées](media/stream-analytics-malformed-events/inputs_tile.png)

Vous pouvez activer les journaux de diagnostics pour afficher les détails de l’avertissement. Pour les événements d’entrée mal formés, les journaux d’exécution contiennent une entrée avec un message semblable à ceci : « Message : Impossible de désérialiser les événements d’entrée à partir de la ressource <blob URI> au format json ». 

### <a name="troubleshooting-steps"></a>Étapes de dépannage

1. Accédez à la vignette Entrées, puis cliquez dessus pour afficher les avertissements.
2. La vignette Détails de l’entrée affiche un ensemble d’avertissements avec des informations détaillées sur le problème. Voici un exemple de message d’avertissement qui indique la partition, le décalage et les numéros de séquence où se trouvent des données JSON mal formées. 

   ![Message d’avertissement avec décalage](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Pour obtenir les données JSON qui ont un format incorrect, exécutez le code CheckMalformedEvents.cs, disponible dans le [dépôt d’exemples GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ce code lit l’ID de la partition et le décalage, puis imprime les données situées dans ce décalage. 

4. Une fois que vous avez lu les données, vous pouvez analyser et corriger le format de sérialisation. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Gestion des enregistrements en double lors de l’utilisation d’Azure SQL Database comme sortie d’une tâche Stream Analytics

Quand vous configurez Azure SQL Database comme sortie d’une tâche Stream Analytics, les enregistrements sont insérés en bloc dans la table de destination. En règle générale, Azure Stream Analytics garantit [au moins une remise]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) dans le récepteur de sortie, mais vous pouvez toujours [obtenir exactement une remise]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) dans la sortie SQL quand une contrainte unique est définie pour la table SQL. 

Une fois que les contraintes de clé unique sont configurées pour la table SQL et qu’il existe des enregistrements en double qui sont insérés dans la table SQL, Azure Stream Analytics supprime l’enregistrement en double en fractionnant les données en lots et en insérant de manière récursive les lots jusqu’à ce qu’un seul enregistrement en double soit trouvé. S’il existe un nombre considérable de lignes en double dans votre pipeline, le fractionnement et l’insertion de manière récursive des données en ignorant les doublons un par un est un processus long. Si vous voyez plusieurs messages d’avertissement de violation de clé dans votre journal d’activité au cours de l’heure passée, il est probable que votre sortie SQL ralentit l’intégralité de la tâche. Pour résoudre ce problème, vous devez [configurer l’index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) qui provoque la violation de clé en activant l’option IGNORE_DUP_KEY. L’activation de cette option permet à SQL d’ignorer les valeurs en double au cours des insertions en bloc et SQL Azure génère simplement un message d’avertissement au lieu d’une erreur. Azure Stream Analytics ne génère plus d’erreurs de violation de clé primaire.

Notez les observations suivantes lors de la configuration d’IGNORE_DUP_KEY pour plusieurs types d’index :

* Vous ne pouvez pas définir IGNORE_DUP_KEY sur une clé primaire ni une contrainte unique qui utilise ALTER INDEX ; vous devez supprimer et recréer l’index.  
* Vous pouvez définir l’option IGNORE_DUP_KEY avec ALTER INDEX pour un index unique, qui est différent de la contrainte PRIMARY KEY/UNIQUE et créé à l’aide de la définition CREATE INDEX ou INDEX.  
* IGNORE_DUP_KEY ne s’applique pas aux index de stockage de colonnes, car vous ne pouvez pas imposer l’unicité sur ces derniers.  

## <a name="next-steps"></a>Étapes suivantes

* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

