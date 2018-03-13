---
title: "Recevoir une notification quand une valeur de métrique remplit une condition | Microsoft Docs"
description: "Guide de démarrage rapide destiné à aider les utilisateurs à créer une métrique pour une application logique"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: quickstart
ms.date: 02/08/2018
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 3c1a6271d0cb2157cb23f1dd4193eadba2c1679b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="receive-a-notification-when-a-metric-value-meets-a-condition"></a>Recevoir une notification quand une valeur de métrique remplit une condition

Azure Monitor propose des métriques pour un grand nombre de ressources Azure. Ces métriques traduisent les performances et l’intégrité de ces ressources. Dans bon nombre de cas, les valeurs des métriques peuvent indiquer un problème au niveau d’une ressource. Vous pouvez créer des alertes de métriques pour détecter un comportement anormal et être notifié, le cas échéant. Ce guide de démarrage rapide vous explique de façon détaillée comment créer une application logique, comment créer un travail et comment visualiser les métriques de l’application logique. Il décrit ensuite les étapes permettant de créer une alerte et de recevoir une notification pour une métrique de la ressource d’application logique.

Pour plus d’informations sur les métriques et les alertes de métriques, consultez [Vue d’ensemble des métriques Azure Monitor](./monitoring-overview-metrics.md) et [Vue d’ensemble des alertes Azure Monitor](./monitoring-overview-alerts.md). 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-logic-app"></a>Créer une application logique

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Recherchez et sélectionnez **Application logique**. Cliquez sur le bouton **Créer** .

3. Entrez les informations d’application logique indiquées dans l’image suivante.  Utilisez l’emplacement par défaut. Sélectionnez l’option **Épingler au tableau de bord**.  Lorsque vous avez terminé, cliquez sur **Créer**. 

    ![Saisie d’informations de base sur votre application logique dans le portail](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-portal.png)  


4. L’application logique doit être épinglée à votre tableau de bord. Accédez à l’application logique en cliquant dessus.

5. Dans le panneau Applications logiques, sélectionnez **Concepteur d’application logique**.

     ![Création d’un déclencheur de périodicité dans le Concepteur d’application logique du panneau du portail](./media/monitoring-quick-resource-metric-alert-portal/logic-app-designer.png)  

6. Définissez vos valeurs comme indiqué dans l’illustration suivante.

    ![Configuration du déclencheur d’application logique dans le panneau du portail](./media/monitoring-quick-resource-metric-alert-portal/create-logic-app-triggers.png). 

7. Dans le concepteur, sélectionnez le déclencheur **Périodicité**.

8. Définissez un intervalle de 20 et une fréquence en seconde de sorte que votre application logique se déclenche toutes les 20 secondes.

9. Cliquez sur le bouton **Nouvelle étape**, puis sélectionnez **Ajouter une action**.

10. Choisissez l’option **HTTP**, puis sélectionnez **HTTP-HTTP**.

11. Sous **Méthode**, choisissez POST et indiquez l’adresse web de votre choix dans **Uri**.

12. Cliquez sur **Enregistrer**.

13. Les actions d’exécution de l’application logique peuvent prendre jusqu’à 5 minutes.  

## <a name="view-metrics-for-your-logic-app"></a>Afficher les métriques de votre application logique

1. Cliquez sur l’option **Moniteur** dans le volet de navigation gauche.

2. Sélectionnez l’onglet **Métriques**, puis renseignez **Abonnement**, **Groupe de ressources**, **Type de ressource** et **Ressource** pour votre application logique.

3. Dans la liste des mesures, choisissez **Exécutions démarrées**.

4. Modifiez la valeur **Intervalle de temps** du graphique pour afficher les données de la dernière heure.

5. Un graphique indiquant le nombre total d’exécutions que votre application logique a démarrées au cours de la dernière heure doit maintenant s’afficher. Si rien ne s’affiche, assurez-vous d’avoir patienté au moins 5 minutes après l’étape ci-dessus. Ensuite, actualisez votre navigateur. 

    ![Affichage d’un graphique de métriques pour la ressource d’application logique](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metric-chart.png)

## <a name="create-a-metric-alert-for-your-logic-app"></a>Créer une alerte de métrique pour votre application logique

1.  En haut à droite du panneau de métriques, cliquez sur le bouton **Ajouter une alerte Métrique**.

2. Nommez votre alerte de métrique « myLogicAppAlert » et décrivez-la brièvement.

3. Définissez la **Condition** de l’alerte de métrique sur « Supérieur à », fixez le **Seuil** à « 10 », puis définissez la **Période** sur « Au cours des 5 dernières minutes ».

4. Enfin, sous **Adresse(s) e-mail administrateur supplémentaire(s)**, entrez votre adresse e-mail. Avec cette alerte, vous êtes assuré de recevoir un e-mail dès lors que votre application logique enregistre plus 10 échecs d’exécution en l’espace de 5 minutes.

    ![Configuration d’une alerte d’application logique dans le panneau du portail](./media/monitoring-quick-resource-metric-alert-portal/logic-app-metrics-alert-portal.png)

## <a name="receive-metric-alert-notifications-for-your-logic-app"></a>Recevoir des notifications d’alerte de métrique pour votre application logique
1. Peu de temps après, vous devez recevoir un e-mail intitulé « Alertes Microsoft Azure » vous informant que l’alerte a été « activée ».

2. Revenez à votre application logique et modifiez le déclencheur de périodicité en sélectionnant un intervalle de 1 et la fréquence Heure.

3. Peu de temps après, vous devez recevoir un e-mail intitulé « Alertes Microsoft Azure » vous informant que l’alerte a été « résolue ».

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres guides de démarrage rapide de cette collection sont la prolongation du présent guide de démarrage rapide. Si vous envisagez d’utiliser ces autres guides de démarrage rapide ou les didacticiels, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.

1. Dans le menu gauche du portail Azure, cliquez sur **Moniteur**.

2. Sélectionnez l’onglet **Alertes**, recherchez l’alerte que vous avez créée dans ce guide de démarrage rapide, puis cliquez dessus.

3. Dans le volet Alerte Métrique, cliquez sur **Supprimer**.

4. Dans le menu gauche du portail Azure, recherchez **Application logique**, puis cliquez sur **Applications logiques**.

5. Dans le volet, cliquez sur l’application logique que vous avez créée dans ce guide de démarrage rapide dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer une alerte de métrique pour vos ressources. Pour plus d’informations sur les alertes de métriques, consultez notre présentation sur les alertes.

> [!div class="nextstepaction"]
> [Alertes sur les actions dans l’abonnement Azure Monitor](./monitor-quick-audit-notify-action-in-subscription.md )
