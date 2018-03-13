---
title: "Mise à l’échelle du nombre d’instances manuellement ou avec la mise à l’échelle automatique avec le portail Azure | Microsoft Docs"
description: "Découvrez comment effectuer une mise à l’échelle de vos services Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: 9ff52bd29644c7c585d2a85fcbe49e8800f6a035
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="scale-instance-count-manually-or-automatically"></a>Mise à l'échelle manuelle ou automatique du nombre d’instances
Dans le [portail Azure](https://portal.azure.com/), vous pouvez définir manuellement le nombre d'instances de votre service, ou définir les paramètres pour qu'il soit automatiquement mis à l'échelle en fonction de la demande. On parle alors généralement *d’extension* ou de *réduction des instances*.

Avant d’effectuer cette mise à l'échelle en fonction du nombre d'instances, vous devez prendre en considération que la mise à l’échelle est non seulement affectée par le nombre d’instances, mais également par le **niveau de tarification** . Les différentes options de tarification qui vous sont proposées peuvent disposer d’une mémoire et d’un nombre de cœurs plus ou moins importants, et fournir de cette manière de meilleures performances pour le même nombre d'instances (c'est-à-dire *l’extension* ou la *réduction des instances*). Cet article aborde plus en détail la *réduction* ou *l’extension des instances*.

Vous pouvez effectuer une mise à l’échelle dans le portail et utiliser également [l’API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) ou le [Kit de développement logiciel (SDK) .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) pour modifier manuellement ou automatiquement la mise à l’échelle .

## <a name="scaling-manually"></a>Mise à l'échelle manuelle
1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir**, puis accédez à la ressource que vous souhaitez mettre à l'échelle, telle qu'un **plan App Service**.
2. Cliquez sur **Paramètres > Monter en charge (plan App Service).**
3. Un historique des mises à l’échelle automatiques du service est affiché en haut du panneau **Mise à l’échelle**, sous l’onglet **Historique des exécutions**.
   
    ![Volet Scale](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Seules les actions effectuées par mise à l'échelle automatique seront affichées dans ce graphique. Si vous modifiez manuellement le nombre d'instances, cette modification n'apparaîtra pas dans ce graphique.
   > 
   > 
4. Vous pouvez ajuster manuellement le nombre des **Instances** sous l’onglet **Configurer**.
5. Cliquez sur la commande **Enregistrer** pour augmenter ou diminuer presque immédiatement le nombre d'instances.

## <a name="scaling-based-on-a-pre-set-metric"></a>Mise à l'échelle en fonction d’une mesure prédéfinie
Si vous souhaitez modifier automatiquement le nombre d'instances en fonction d’une mesure, sélectionnez la mesure de votre choix dans la liste déroulante **Mettre à l’échelle par**. Pour un **plan App Service**, vous pouvez, par exemple, effectuer une mise à l’échelle via **Pourcentage UC**.

1. Lorsque vous sélectionnez une mesure, un curseur et/ou des zones de texte apparaîtront pour vous permettre d’entrer le nombre d'instances que vous souhaitez mettre à l'échelle entre :
   
    ![Volet Scale avec pourcentage UC](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    La mise à l'échelle automatique n'utilisera jamais votre service au-dessus ou au-dessous des limites que vous définissez, et ce, quelle que soit votre charge.
2. Vous choisissez ensuite la plage cible de la mesure. Si vous choisissez, par exemple, l’option **Pourcentage UC**, vous pouvez définir un objectif pour la moyenne du processeur sur toutes les instances de votre service. L’extension des instances se produit lorsque la moyenne du processeur dépasse le nombre maximum défini. De la même manière, la réduction des instances se produit chaque fois que la moyenne du processeur chute en-deçà du nombre minimum.
3. Cliquez sur la commande **Enregistrer** . La mise à l'échelle automatique réalisera une vérification toutes les minutes pour vous assurer que vous êtes bien dans la plage de l'instance ainsi que dans la cible de votre mesure. Lorsque votre service reçoit du trafic supplémentaire, plusieurs instances vous seront attribuées automatiquement.

## <a name="scale-based-on-other-metrics"></a>Mise à l’échelle en fonction d’autres mesures
Vous pouvez effectuer une mise à l’échelle en fonction de mesures autres que les présélections qui s'affichent dans la liste déroulante **Mettre à l’échelle par** et même disposer d’un ensemble complexe de règles d’extension ou de réduction des instances.

### <a name="adding-or-changing-a-rule"></a>Ajout ou modification d'une règle
1. Sélectionnez les **règles de performances et de planification** dans la liste déroulante **Mettre à l’échelle par** : ![Règles de performance](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. Si vous avez déjà effectué une mise à l'échelle automatique, vous découvrirez les règles précises dont vous disposiez.
3. Pour effectuer une mise à l’échelle sur une autre mesure, cliquez sur la ligne **Ajouter une règle** . Vous pouvez également cliquer sur une des lignes existantes pour passer d’une mesure dont vous disposiez auparavant à une mesure via laquelle vous souhaitez effectuer une mise à l'échelle.
   ![Add rule](./media/insights-how-to-scale/Insights_AddRule.png)
4. Vous devez désormais sélectionner les mesures que vous souhaitez mettre à l'échelle. Lorsque vous choisissez une mesure, plusieurs éléments doivent être prendre en compte :
   
   * La *ressource* d’où provient la mesure. Elle sera généralement celle que vous avez mise à l’échelle. Toutefois, si vous souhaitez effectuer une mise à l'échelle via la longueur d'une file d'attente de stockage, la ressource représentera la file d'attente via laquelle vous souhaitez effectuer une mise à l’échelle.
   * Le *nom de la mesure* .
   * L’ *agrégation de temps* de la mesure. Voici comment les données sont combinées sur la *durée*.
5. Après avoir choisi votre mesure, vous choisissez le seuil de la mesure ainsi que l’opérateur. Vous pouvez, par exemple, dire **Supérieur à** **80 %**.
6. Choisissez ensuite l'action que vous souhaitez effectuer. Il existe deux types d'actions bien distincts :
   
   * Augmenter ou diminuer de : ceci ajoute ou supprime le nombre de **valeurs** d'instances que vous définissez
   * Augmenter ou diminuer le pourcentage : ceci modifiera le nombre d'instances en termes de pourcentage. Vous pouvez, par exemple, écrire 25 dans le champ **Valeur** , et si vous disposez actuellement de 8 instances, 2 d’entre elles seront ajoutées.
   * Augmenter ou diminuer jusqu’à : ceci définit le nombre d'instances établies jusqu’à la **valeur** que vous définissez.
7. Enfin, vous pouvez choisir « Cool down », c’est-à-dire le temps d'attente de cette règle entre la dernière action de mise à l'échelle et la prochaine.
8. Après avoir configuré votre règle, cliquez sur **OK**.
9. Une fois que vous avez configuré toutes les règles que vous souhaitez, n’oubliez pas de cliquer sur la commande **Enregistrer** .

### <a name="scaling-with-multiple-steps"></a>Mise à l'échelle en plusieurs étapes
Les exemples ci-dessus sont assez simples. Toutefois, si vous souhaitez définir des règles d’extension ou de réduction plus agressives, vous pouvez même en ajouter plusieurs pour la même mesure. Vous pouvez, par exemple, définir deux règles de mise à l'échelle sur Pourcentage UC :

1. Extension d'une instance si le pourcentage UC est supérieur à 60 %
2. Extension de trois instances si le pourcentage UC est supérieur à 85 %

![Règles de mise à l'échelle multiples](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Avec cette règle supplémentaire, si la charge dépasse 85 % avant une action de mise à l'échelle, vous obtenez deux instances supplémentaires au lieu d'une.

## <a name="scale-based-on-a-schedule"></a>Mise à l'échelle en fonction d’une planification
Par défaut, lorsque vous créez une règle de mise à l'échelle, celle-ci s’appliquera en permanence. Vous pouvez le constater lorsque vous cliquez sur l'en-tête du profil :

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Toutefois, vous souhaiterez peut-être définir des règles d’extension ou de réduction plus agressives durant la journée ou bien la semaine, plutôt que le week-end. Vous pouvez même arrêter intégralement votre service hors des heures de travail.

1. Pour ce faire, sur le profil que vous avez, sélectionnez **Périodicité** au lieu de **Toujours,** et choisissez les heures auxquelles vous souhaitez que le profil s’applique.
2. Pour qu’un profil s’applique durant la semaine, dans la liste déroulante **Jours**, désactivez **Samedi** et **Dimanche**.
3. Pour qu'un profil s’applique le jour, attribuez comme **heure de début** l'heure de la journée à laquelle vous souhaitez commencer.
   
    ![Périodicité par défaut](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Cliquez sur **OK**.
5. Ensuite, vous devez ajouter le profil que vous souhaitez appliquer à d'autres moments. Cliquez sur la ligne **Ajouter un profil** .
    ![Off Work](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Vous pouvez, par exemple, intituler votre second nouveau profil **Absent(e) du bureau**.
7. Sélectionnez ensuite **Périodicité** et choisissez la plage du nombre d’instances souhaitée durant cette période.
8. Comme avec le profil par défaut, choisissez les **jours** durant lesquels vous souhaitez appliquer ce profil et l’ **heure de début** de la journée.
   
   > [!NOTE]
   > La mise à l'échelle automatique utilisera les règles de passage à l’heure d’été pour n’importe quel **fuseau horaire** que vous sélectionnez. Cependant, lors du passage à l’heure d’été, le décalage UTC indiquera le décalage de base, et non le décalage UTC du passage à l’heure d’été.
   > 
   > 
9. Cliquez sur **OK**.
10. Vous devez ajouter les règles que vous souhaitez appliquer à votre second profil. Cliquez sur **Ajouter une règle**et établissez la même règle qui est appliquée à votre profil par défaut.
    
    ![Ajouter une règle pour s’absenter du bureau](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Assurez-vous de créer une règle relative à l’extension et à la réduction des instances, sinon le nombre d’instances ne fera qu’augmenter (ou diminuer) sur ce profil.
12. Puis, cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service.
* [Réceptions de notifications d'alerte](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* [Surveillance des performances d'une application](../application-insights/app-insights-azure-web-apps.md) si vous voulez comprendre exactement comment votre code s'exécute dans le cloud.
* [Affichage des événements et du journal d’activité](insights-debugging-with-events.md) pour découvrir tout ce qui s'est produit dans votre service.
* [Surveillance de la disponibilité et de la réactivité des pages Web](../application-insights/app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.

