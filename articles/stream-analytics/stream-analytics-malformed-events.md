---
title: "Résoudre les problèmes d’événements d’entrée mal formée dans Azure Stream Analytics | Microsoft Docs"
description: "Déterminer l’événement qui pose problème dans une tâche Azure Stream Analytics"
keywords: 
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-for-malformed-input-events"></a>Résoudre les problèmes d’événements d’entrée mal formée

Si le flux d’entrée de votre tâche Stream Analytics contient des messages mal formés, des problèmes de sérialisation peuvent se produire. Ces messages mal formés peuvent être liés à une sérialisation incorrecte, comme dans le cas d’une parenthèse manquante dans un objet JSON ou d’un format d’horodatage incorrect. Lorsqu’une tâche Stream Analytics reçoit un message mal formé, elle le supprime et affiche un avertissement pour l’utilisateur. Un symbole d’avertissement s’affiche dans la vignette **Entrées** de votre tâche Stream Analytics :

![Vignette Entrées](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Étapes de dépannage

1. Accédez à la vignette Entrées, puis cliquez dessus pour afficher les avertissements.
2. La vignette Détails de l’entrée affiche un ensemble d’avertissements avec des informations détaillées sur le problème. Voici un exemple de message d’avertissement qui indique la partition, le décalage et les numéros de séquence où se trouvent des données JSON mal formées. 

   ![Message d’avertissement avec décalage](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Pour connaître les données JSON dont le format est incorrect, exécutez l’extrait de code suivant. Ce bloc de code lit l’ID de la partition et le décalage, puis imprime les données. L’exemple complet est disponible dans le [dépôt d’exemples GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Une fois que vous avez lu les données, vous pouvez analyser et corriger le format de sérialisation.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>Étapes suivantes

* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
