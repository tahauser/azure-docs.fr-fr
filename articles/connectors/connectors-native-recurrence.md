---
title: "Planifier l’exécution régulière de tâches et flux de travail - Azure Logic Apps | Microsoft Docs"
description: "Créer et planifier l’exécution régulière de tâches, actions, flux de travail, processus et charges de travail avec des applications logiques"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0dead955f9eb723dfa232d3ce751498a09ce1b29
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="create-and-schedule-regularly-running-tasks-with-azure-logic-apps"></a>Créer et planifier l’exécution régulière de tâches avec Azure Logic Apps

Pour planifier des tâches, actions, charges de travail ou processus à exécuter régulièrement, vous pouvez créer un flux de travail d’application logique qui démarre avec le **déclencheur** [Planification - Périodicité](../logic-apps/logic-apps-overview.md#logic-app-concepts). Ce déclencheur permet de définir une date et une heure de démarrage de la périodicité et une planification périodique de tâches comme les suivantes, entre autres :

* Obtenir des données internes : [exécuter une procédure stockée SQL](../connectors/connectors-create-api-sqlazure.md) tous les jours.
* Obtenir des données externes : extraire des rapports météorologiques de NOAA toutes les 15 minutes.
* Générer des rapports de données : envoyer par e-mail un résumé de toutes les commandes supérieures à une quantité spécifique de la semaine antérieure.
* Traiter des données : compresser les images téléchargées le jour même tous les jours aux heures creuses.
* Nettoyez des données : supprimer tous les tweets de plus de trois mois.
* Archiver des données : envoyer les factures vers un service de sauvegarde tous les mois.

Ce déclencheur prend en charge de nombreux modèles, par exemple :

* Exécuter immédiatement et répéter toutes les *n* secondes, minutes, heures, jours, semaines ou mois.
* Démarrer à une heure spécifique, exécuter immédiatement et répéter toutes les *n* secondes, minutes, heures, jours, semaines ou mois.
* Exécuter et répéter une ou plusieurs fois par jour, par exemple à 8h00 et 17h00.
* Exécuter et répéter chaque semaine, mais uniquement certains jours, par exemple le samedi et le dimanche.
* Exécuter et répéter chaque semaine, mais uniquement certains jours et à des heures précises, par exemple du lundi au vendredi à 8h00 et 17h00.

Chaque fois que le déclencheur de périodicité est activé, Logic Apps crée et exécute une nouvelle instance de votre flux de travail d’application logique.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Ajouter un déclencheur de périodicité à votre application logique

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Créez une application logique vide ou découvrez [comment créer une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Une fois affiché le concepteur Logic Apps, dans la zone de recherche, entrez « périodicité » comme filtre. Sélectionnez le déclencheur **Planification - Périodicité**. 

   ![Déclencheur Planification - Périodicité](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Ce déclencheur est désormais la première étape de votre application logique.

3. Définissez l’intervalle et la fréquence de la périodicité. Dans cet exemple, définissez ces propriétés de sorte que votre flux de travail soit exécuté chaque semaine. 

   ![Définir l’intervalle et la fréquence](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Pour accéder à d’autres d’options de planification, sélectionnez **Afficher les options avancées**. 

   ![Autres options](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Vous pouvez à présent définir ces options : 

   * Définissez une date et une heure de début pour activer le déclencheur. 
   Si vous spécifiez une date et une heure de début, vous pouvez également appliquer un fuseau horaire. 

   * Si vous sélectionnez « Jour » ou « Semaine » comme fréquence, vous pouvez sélectionner des heures spécifiques pour la périodicité. 

   * Si vous choisissez « Semaine », vous pouvez également sélectionner des jours spécifiques de la semaine.
   
   ![Options avancées de planification](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Par exemple, supposons que nous sommes le lundi 4 septembre 2017 aujourd’hui. 
   Le déclencheur de périodicité suivant ne s’activera *pas avant* la date et l’heure de début définies, à savoir le lundi 18 septembre 2017 à 8h00 (PST). 
   Notez toutefois que la périodicité planifiée est définie sur 10h30, 12h30 et 14h30 uniquement le lundi. Par conséquent, le déclencheur s’activera et créera une instance de flux de travail d’application logique pour la première fois à 10h30. 
   Pour plus d’informations sur le fonctionnement des heures de début, consultez ces [exemples d’heure de début](#start-time).
   Les exécutions suivantes auront lieu le même jour à 12h30 et 14h30. 
   Chaque périodicité crée sa propre instance de flux de travail. L’ensemble de la planification se répète ensuite régulièrement chaque lundi. 
   [*Existe-t-il d’autres exemples de périodicité ?*](#example-recurrences)

   ![Exemple de planification avancée](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > Le déclencheur affiche un aperçu de la périodicité que vous avez spécifiée uniquement quand vous sélectionnez « Jour » ou « Semaine » comme fréquence.
   
6. Créez à présent votre flux de travail restant avec des actions ou instructions de contrôle de flux. Pour connaître les autres d’actions que vous pouvez ajouter, consultez [Connecteurs](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Détails du déclencheur

Vous pouvez configurer ces propriétés pour le déclencheur de périodicité.

| NOM | Obligatoire | Nom de la propriété | type | Description | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Fréquence** | OUI | frequency | Chaîne | L’unité de temps de la périodicité est : **Seconde**, **Minute**, **Heure**, **Jour**, **Semaine** ou **Mois**. | 
| **Intervalle** | OUI | interval | Entier  | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence. <p>L’intervalle par défaut est de 1 minute. Les intervalles minimaux et maximaux sont les suivants : <p>- Mois : 1-16 mois </br>- Jour : 1-500 jours </br>- Heure : 1-12 000 heures </br>- Minute : 1-72 000 minutes </br>- Seconde : 1-9 999 999 secondes<p>Par exemple, si l’intervalle est de 6 et que la fréquence soit définie sur « Mois », la périodicité est alors tous les 6 mois. | 
| **Fuseau horaire** | Non  | timeZone | Chaîne | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Sélectionnez le fuseau horaire à appliquer. | 
| **Heure de début** | Non  | startTime | Chaîne | Fournissez une heure de début au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous sélectionnez un fuseau horaire <p>-ou- <p>AAAA-MM-JJThh:mm:ssZ si vous ne sélectionnez pas de fuseau horaire <p>Par exemple, si vous choisissez le 18 septembre 2017 à 14h00, spécifiez « 2017-09-18T14:00:00 » et sélectionnez un fuseau horaire tel que « Pacific Time » (Heure du Pacifique). Vous pouvez également spécifier « 2017-09-18T14:00:00Z » sans fuseau horaire. <p>**Remarque :** cette heure de début doit être conforme à la [spécification date/heure ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) au [format date/heure UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mais sans [décalage UTC](https://en.wikipedia.org/wiki/UTC_offset). Si vous ne sélectionnez pas de fuseau horaire, vous devez ajouter la lettre « Z » à la fin sans espace. Ce « Z » fait référence à l’équivalent en [temps nautique](https://en.wikipedia.org/wiki/Nautical_time). <p>Pour les planifications simples, l’heure de début est la première occurrence, tandis que pour les planifications complexes, le déclencheur ne s’active pas avant l’heure de début. [*Comment puis-je utiliser la date et l’heure de début ?*](#start-time) | 
| **Aux jours indiqués** | Non  | weekDays | Chaîne ou tableau de chaînes | Si vous sélectionnez « Semaine », vous pouvez sélectionner un ou plusieurs jours pour l’exécution du flux de travail : **Lundi**, **Mardi**, **Mercredi**, **Jeudi**, **Vendredi**, **Samedi** et **Dimanche** | 
| **Aux heures indiquées** | Non  | hours | Entier ou tableau d’entiers | Si vous sélectionnez « Jour » ou « Semaine », vous pouvez sélectionner un ou plusieurs entiers compris entre 0 et 23 pour les heures de la journée durant lesquelles exécuter le flux de travail. <p>Par exemple, si vous spécifiez « 10 », « 12 » et « 14 », vous obtenez 10h00, 12h00 et 14h00 comme marques horaires. | 
| **Aux minutes indiquées** | Non  | minutes | Entier ou tableau d’entiers | Si vous sélectionnez « Jour » ou « Semaine », vous pouvez sélectionner un ou plusieurs entiers compris entre 0 et 59 pour les minutes de l’heure durant lesquelles exécuter le flux de travail. <p>Par exemple, vous pouvez spécifier « 30 » pour les minutes et à l’aide de l’exemple précédent des heures de la journée, vous obtenez 10h30, 12h30 et 14h30. | 
||||| 

## <a name="json-example"></a>Exemple JSON

Voici un exemple de [définition de déclencheur de périodicité](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) :

``` json
{
    "triggers": {
        "Recurrence": {
            "type": "Recurrence",
            "recurrence": {
                "frequency": "Week",
                "interval": 1,
                "schedule": {
                    "hours": [
                        10,
                        12,
                        14
                    ],
                    "minutes": [
                        30
                    ],
                    "weekDays": [
                        "Monday"
                    ]
                },
               "startTime": "2017-09-07T14:00:00",
               "timeZone": "Pacific Standard Time"
            }
        }
    }
}
```

## <a name="faq"></a>Forum Aux Questions

<a name="example-recurrences"></a>

**Q :** Existe-t-il d’autres exemples de planifications de périodicité ? </br>
**R :** Oui, voici des exemples supplémentaires :

| Périodicité | Intervalle | Fréquence | Heure de début | Aux jours indiqués | Aux heures indiquées | Aux minutes indiquées | Remarque |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Exécution toutes les 15 minutes (sans date ni heure de début) | 15 | Minute | {aucune} | {non disponible} | {aucune} | {aucune} | Cette planification démarre immédiatement, puis calcule les périodicités suivantes en fonction de la dernière heure d’exécution. | 
| Exécution toutes les 15 minutes (avec date et heure de début) | 15 | Minute | *startDate*T*startTime*Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes sont ensuite calculées en fonction de la dernière heure d’exécution. | 
| Exécution toutes les heures, à l’heure spécifiée (avec date et heure de début) | 1 | Hour | *startDate*Thh:00:00Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les exécutions périodiques suivantes ont lieu toutes les heures à la minute « 00 ». <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. | 
| Exécution toutes les heures, tous les jours (sans date ni heure de début) | 1 | Hour | {aucune} | {non disponible} | {aucune} | {aucune} | Cette planification démarre immédiatement et calcule les périodicités suivantes en fonction de la dernière heure d’exécution. <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. | 
| Exécution toutes les heures, tous les jours (avec date et heure de début) | 1 | Hour | *startDate*T*startTime*Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées. Les périodicités suivantes sont ensuite calculées en fonction de la dernière heure d’exécution. <p>Si la fréquence est « Semaine » ou « Mois », cette planification s’exécute respectivement un seul jour par semaine ou un seul jour par mois. | 
| Exécution 15 minutes après l’heure, toutes les heures (avec date et heure de début) | 1 | Hour | *startDate*T00:15:00Z | {non disponible} | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées, à savoir 00h15, 1h15, 2h15, etc. | 
| Exécution 15 minutes après l’heure, toutes les heures (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Cette planification s’exécute à 00h15, 1h15, 2h15, etc. Cette planification est en outre l’équivalent d’une fréquence de type « Heure » et d’une heure de début avec « 15 » minutes. | 
| Exécution toutes les 15 minutes à la marque de 15 minutes (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Cette planification ne démarre pas avant la marque de 15 minutes spécifiée suivante. | 
| Exécution à 8h00 tous les jours (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 8 | {aucune} | Cette planification s’exécute tous les jours à 8h00, selon la planification spécifiée. | 
| Exécution à 8h00 tous les jours (avec date et heure de début) | 1 | jour | *startDate*T08:00:00Z | {non disponible} | {aucune} | {aucune} | Cette planification s’exécute à 8h00 tous les jours, selon l’heure de début spécifiée. | 
| Exécution à 8h30 tous les jours (sans date ni heure de début) | 1 | jour | {aucune} | {non disponible} | 8 | 30 | Cette planification s’exécute tous les jours à 8h30, selon la planification spécifiée. | 
| Exécution à 8h30 tous les jours (avec date et heure de début) | 1 | jour | *startDate*T08:30:00Z | {non disponible} | {aucune} | {aucune} | Cette planification démarre à la date de début spécifiée, à 8h30. | 
| Exécution à 8h30 et 16h30 tous les jours | 1 | jour | {aucune} | {non disponible} | 8, 16 | 30 | | 
| Exécution à 8h30, 8h45, 16h30 et 16h45 tous les jours | 1 | jour | {aucune} | {non disponible} | 8, 16 | 30, 45 | | 
| Exécution le samedi à 17h00 (sans date ni heure de début) | 1 | Semaine | {aucune} | Samedi | 17 | 00 | Cette planification s’exécute tous les samedis à 17h00. | 
| Exécution le samedi à 17h00 (avec date et heure de début) | 1 | Semaine | *startDate*T17:00:00Z | Samedi | {aucune} | {aucune} | Cette planification ne démarre *pas avant* la date et l’heure de début spécifiées, à savoir le 9 septembre 2017 à 17h00 dans cet exemple. Les exécutions périodiques suivantes ont lieu tous les samedis à 17h00. | 
| Exécution les mardi et jeudi à 17h00 | 1 | Semaine | {aucune} | Mardi, Jeudi | 17 | {aucune} | Cette planification s’exécute tous les mardis et jeudis à 17h00. | 
| Exécution toutes les heures pendant les heures de travail | 1 | Semaine | {aucune} | Sélectionnez tous les jours, sauf le samedi et le dimanche. | Sélectionnez les heures de la journée souhaitées. | Sélectionnez les minutes de l’heure souhaitées. | Par exemple, si vos heures de travail sont de 8h00 à 17h00, sélectionnez « 8, 9, 10, 11, 12, 13, 14, 15, 16, 17 » comme heures de la journée. <p>Si vos heures de travail sont de 8h30 à 17h30, sélectionnez les heures précédentes de la journée, plus « 30 » minutes de l’heure. | 
| Exécution une fois par jour le week-end | 1 | Semaine | {aucune} | Samedi, Dimanche | Sélectionnez les heures de la journée souhaitées. | Sélectionnez les minutes de l’heure souhaitées. | Cette planification s’exécute tous les samedis et dimanches aux heures spécifiées. | 
| Exécution toutes les 15 minutes toutes les deux semaines le lundi uniquement | 2 | Semaine | {aucune} | Lundi | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Cette planification s’exécute un lundi sur deux à chaque marque de 15 minutes. | 
| Exécution toutes les heures un jour par mois | 1 | Mois | {voir remarque} | {non disponible} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {voir remarque} | Si vous ne spécifiez pas de date et heure de début, cette planification utilise la date et heure de création. Pour contrôler les minutes pour la planification de la périodicité, spécifiez les minutes de l’heure, une heure de début ou utilisez l’heure de création. Par exemple, si l’heure de début ou l’heure de création est 8h25, cette planification s’exécute à 8h25, 9h25, 10h25, etc. | 
||||||||| 

<a name="start-time"></a>

**Q :** Comment puis-je utiliser la date et l’heure de début ? </br>
**R :** Voici quelques modèles qui montrent comment vous pouvez contrôler la périodicité à l’aide de la date et l’heure de début, et comment le moteur Logic Apps exécute ces périodicités :

| Heure de début | Périodicité sans planification | Périodicité avec planification | 
| ---------- | --------------------------- | ------------------------ | 
| {aucune} | Exécute la première charge de travail instantanément. <p>Exécute les charges de travail suivantes en fonction de la dernière heure d’exécution. | Exécute la première charge de travail instantanément. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. | 
| Heure de début dans le passé | Calcule le temps d’exécution en fonction de l’heure de début spécifiée et ignore les heures d’exécution passées. Exécute la première charge de travail à la prochaine heure d’exécution. <p>Exécute les charges de travail suivantes selon des calculs basés sur la dernière heure d’exécution. <p>Pour une explication plus détaillée, consultez l’exemple fourni après ce tableau. | Exécute la première charge de travail *exactement* à l’heure de début, en fonction de la planification calculée à partir de l’heure de début. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. <p>**Remarque :** si vous spécifiez une périodicité avec une planification, mais sans spécifier d’heures ni de minutes, les exécutions suivantes sont calculées à partir des heures ou minutes, respectivement, de la première heure d’exécution. | 
| Heure de début actuelle ou future | Exécute la première charge de travail à l’heure de début spécifiée. <p>Exécute les charges de travail suivantes selon des calculs basés sur la dernière heure d’exécution. | Exécute la première charge de travail *exactement* à l’heure de début, en fonction de la planification calculée à partir de l’heure de début. <p>Exécute les charges de travail suivantes en fonction de la planification spécifiée. <p>**Remarque :** si vous spécifiez une périodicité avec une planification, mais sans spécifier d’heures ni de minutes, les exécutions suivantes sont calculées à partir des heures ou minutes, respectivement, de la première heure d’exécution. | 
||||

**Exemple d’heure de début passée avec une périodicité, mais sans planification** 

| Heure de début | Heure actuelle | Périodicité | Planification |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Tous les 2 jours | {aucune} | 
||||| 

Dans ce scénario, le moteur Logic Apps calcule les heures d’exécution en fonction de l’heure de début, ignore les heures d’exécution passées et utilise l’heure de début suivante pour la première exécution. Après la première exécution, les exécutions suivantes sont basées sur la planification calculée à partir de l’heure de début. Voici à quoi ressemble cette périodicité :

| Heure de début | Première heure d'exécution | Heures d’exécution suivantes | 
| ---------- | ------------ | ---------- | 
| **07**/09/2017 à 14h00 | **09**/09/2017 à 14h00 | **11**/09/2017 à 14h00 </br>**13**/09/2017 à 14h00 </br>**15**/09/2017 à 14h00 </br>Etc.
||||| 

Par conséquent, pour ce scénario, peu importe si l’heure de début spécifiée remonte à loin ou pas (par exemple, **05**/09/2017 à 14h00 ou **01**/09/2017 à 14h00), votre première heure d’exécution est la même.

## <a name="next-steps"></a>étapes suivantes

* [Actions et déclencheurs de flux de travail](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Connecteurs](../connectors/apis-list.md)
