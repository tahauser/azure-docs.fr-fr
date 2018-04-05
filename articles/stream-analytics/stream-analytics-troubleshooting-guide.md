---
title: Guide de dépannage pour Azure Stream Analytics | Microsoft Docs
description: Comment résoudre les problèmes liés à votre travail Stream Analytics
keywords: Guide de dépannage
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 87e8b99cb2774966ce22c76e1afed6a754749e9d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Guide de dépannage pour Azure Stream Analytics

Résoudre les problèmes liés à Azure Stream Analytics peut à première vue sembler relativement complexe. Suite à une longue collaboration avec les utilisateurs, nous avons créé ce guide pour vous aider à rationaliser le processus de dépannage et à y voir plus clair concernant vos entrées, vos sorties, vos requêtes et vos fonctions.

## <a name="troubleshoot-your-stream-analytics-job"></a>Résoudre les problèmes liés à votre travail Stream Analytics

Pour résoudre plus efficacement les problèmes liés à votre travail Stream Analytics, procédez comme suit :

1.  Testez votre connectivité :
    - Vérifiez la connectivité en entrée et en sortie à l’aide du bouton **Tester la connexion** pour chacune des entrées et des sorties.

2.  Examinez vos données d’entrée :
    - Utilisez [l’Explorateur Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) pour établir la connexion à Azure Event Hub (si l’entrée Event Hub est utilisée) afin de vérifier que les données d’entrée sont communiquées à Event Hub.  
    - Utilisez le bouton [**Exemple de données**](stream-analytics-sample-data-input.md) pour chacune des entrées, puis téléchargez l’exemple de données d’entrée.
    - Examinez l’exemple de données afin d’appréhender la forme des données : le schéma et les [types de données](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testez votre requête :
    - Dans l’onglet **Requête**, utilisez le bouton **Test** et les exemples de données téléchargés afin [d’évaluer la requête](stream-analytics-test-query.md). Examinez les éventuelles erreurs et tentez de les corriger.
    - Si vous utilisez l’objet [**Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), assurez-vous que les événements présentent des horodatages postérieurs à [l’heure de début du travail](stream-analytics-out-of-order-and-late-events.md).

4.  Déboguez votre requête :
    - Régénérez progressivement la requête en utilisant des étapes incluant des instructions SELECT simples et des agrégats plus complexes. Utilisez les clauses [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) pour générer la logique de requête, étape par étape.
    - Utilisez [SELECT INTO](stream-analytics-select-into.md) pour déboguer les étapes de la requête.

5.  Éliminez les pièges les plus courants, comme par exemple :
    - Une clause [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx) dans la requête a filtré l’ensemble des événements, ce qui empêche la génération des sorties.
    - Lorsque vous utilisez des fonctions de fenêtre, attendez la durée totale de la fenêtre pour obtenir une sortie.
    - L’horodatage des événements est antérieur à l’heure de début du travail, provoquant l’abandon des événements.

6.  Utilisez l’ordre des événements :
    - Si toutes les étapes précédentes se sont déroulées correctement, accédez au panneau **Paramètres** et choisissez [**Ordre des événements**](stream-analytics-out-of-order-and-late-events.md). Vérifiez que cette stratégie est configurée pour s’intégrer logiquement à votre scénario. La stratégie n’est *pas* appliquée lorsque vous utilisez le bouton **Test** pour tester la requête. C’est une des différences entre le test en navigateur et l’exécution réelle du travail.

7.  Déboguez la requête à l’aide de métriques :
    - Si vous n’obtenez aucune sortie une fois l’intervalle prévu écoulé (en fonction de la requête), procédez comme suit :
        - Consultez les [**métriques de surveillance**](stream-analytics-monitoring.md) dans l’onglet **Surveiller**. Comme les valeurs sont agrégées, les métriques sont différées de quelques minutes.
            - Si le nombre d’événements d’entrée n’est pas nul, le travail peut lire les données. Si le nombre d’événements d’entrée est nul, alors :
                - Vérifiez si la source de données comporte des données valides à l’aide de [l’Explorateur Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Cette vérification s’applique si le travail utilise le concentrateur d’événements comme entrée.
                - Vérifiez si les formats de sérialisation et d’encodage des données sont respectés.
                - Si le travail utilise un concentrateur d’événements, vérifiez si le corps du message est *Null*.
            - Si le nombre d’erreurs de conversion de données n’est pas nul et augmente, plusieurs causes sont possibles :
                - Le travail n’est peut-être pas en mesure de désérialiser les événements.
                - Le schéma d’événements ne correspond peut-être pas au schéma défini ou attendu des événements de la requête.
                - Les types de données de certains champs de l’événement ne correspondent peut-être pas aux types attendus.
            - Si le nombre d’erreurs d’exécution n’est pas nul, cela signifie que le travail peut recevoir les données, mais que des erreurs se produisent durant le traitement de la requête.
                - Pour trouver les erreurs, accédez aux [journaux d’audit](../azure-resource-manager/resource-group-audit.md) et filtrez sur l’état *Échec*.
            - Si le nombre d’événements d’entrée n’est pas nul et que le nombre d’événements de sortie est nul, cela peut s’expliquer par l’une des raisons suivantes :
                - Le traitement de la requête a abouti à un nombre nul d’événements de sortie.
                - Les événements ou leurs champs peuvent être formés de manière inappropriée et n’entraîner aucune sortie après le traitement des requêtes.
                - Le travail n’a pas pu transmettre les données au [récepteur de sortie](stream-analytics-select-into.md) pour des raisons d’authentification ou de connectivité.
        - Dans tous ces cas d’erreur, les messages des journaux d’opérations donnent des détails supplémentaires (notamment sur le déroulé des événements), sauf lorsque la logique de requête a filtré l’ensemble des événements. Si le traitement de plusieurs événements génère des erreurs, Stream Analytics consigne les 3 premiers messages d’erreur du même type dans un intervalle de 10 minutes dans les journaux des opérations. Il supprime ensuite toutes les autres erreurs identiques avec un message indiquant que les erreurs survenant trop rapidement sont supprimées.

8. Débogage à l’aide des journaux de diagnostic et d’audit :
    - Appliquez des filtres aux [journaux d’audit](../azure-resource-manager/resource-group-audit.md) pour identifier et déboguer les erreurs.
    - Utilisez les [journaux de diagnostic du travail](stream-analytics-job-diagnostic-logs.md) pour identifier et déboguer les erreurs.

9. Analysez les sorties :
    - Lorsque l’état du travail est défini sur *En cours d’exécution*, la sortie peut apparaître dans la source de données du récepteur, en fonction de la durée stipulée dans la requête.
    - Lorsque les sorties dirigées vers un type de sortie spécifique ne s’affichent pas, réacheminez-les vers un type de sortie moins complexe, comme les objets blob Azure. Vérifiez ensuite que la sortie s’affiche à l’aide de l’explorateur de stockage. En outre, vérifiez si des limitations à la sortie empêchent la réception des données.

10. Utilisez l’analyse de flux de données avec les métriques du diagramme de travail :
    - Pour analyser le flux de données et identifier les problèmes, utilisez le [diagramme de travail comprenant les métriques](stream-analytics-job-diagram-with-metrics.md).

11. Créez une demande de support :
    - Enfin, si le problème persiste, créez une demande de support Microsoft en utilisant l’ID d’abonnement qui contient votre travail.

## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
