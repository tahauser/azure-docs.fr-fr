---
title: Créer, afficher et gérer des alertes à l’aide d’Azure Monitor - Alerts (préversion) | Microsoft Docs
description: Utilisez la nouvelle expérience unifiée d’alertes Azure pour créer, afficher et gérer des règles d’alerte de métriques et de journaux à partir d’un seul emplacement.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 36729da3-e002-4a64-86b2-2513ca2cbb58
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: b6b6bfee5b9e9036a6d7ff17ff1a8d4de542bbd3
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="create-view-and-manage-alerts-using-azure-monitor---alerts-preview"></a>Créer, afficher et gérer des alertes à l’aide d’Azure Monitor - Alerts (préversion)

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment configurer des alertes à l’aide de la nouvelle interface Alerts (préversion) dans le portail Azure. La définition d’une règle d’alerte se fait en trois parties :
- Cible : ressource Azure spécifique à surveiller
- Critères : condition spécifique ou logique qui, une fois détectée (signal), doit déclencher une action
- Action : appel spécifique est envoyé au récepteur d’une notification (courrier électronique, SMS, webhook, etc.)

Alerts (préversion) utilise le terme **alertes de journal** pour décrire les alertes où le signal est une requête personnalisée basée sur [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou [Azure Application Insights](../application-insights/app-insights-analytics.md). La fonctionnalité d’alerte de métrique appelée [alertes Métrique en temps quasi réel](monitoring-near-real-time-metric-alerts.md) dans l’expérience d’alerte existante est dénommée **Alertes Métrique** dans Alerts (préversion). Dans *Alertes Métrique*, certains types de ressources fournissent des [métriques multidimensionnelles](monitoring-metric-charts.md) pour une ressource Azure spécifique. Par conséquent, les alertes pour ces ressources peuvent être rendues plus spécifiques à l’aide de filtres supplémentaires appliqué à des dimensions. Ces alertes sont appelées **Alertes Métrique multidimensionnelles**.
Azure Alerts (préversion) fournit également une vue unifiée pour toutes vos règles d’alerte et la possibilité de les gérer un emplacement unique, y compris l’affichage de toutes les alertes non résolues. En savoir plus sur les fonctionnalités d’[Azure Alerts (préversion) - Vue d’ensemble](monitoring-overview-unified-alerts.md).

> [!NOTE]
> Bien qu’Azure Alerts (préversion) offre une nouvelle expérience améliorée pour la création d’alertes dans Azure, l’expérience [Azure Alerts](monitoring-overview-alerts.md) existante reste utilisable.
>

Vous trouverez ci-dessous un guide détaillé pour l’utilisation d’Azure Alerts (préversion).

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Créer une règle d’alerte avec le portail Azure
1. Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alerts (préversion)** sous la section SURVEILLER.  
    ![Surveillance](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Cliquez sur le bouton **Nouvelle règle d’alerte** pour créer une alerte dans Azure.
    ![Ajouter une alerte](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. La section Créer une alerte s’affiche avec ses trois parties : *définition de la condition d’alerte*, *définition des détails de l’alerte* et *définition du groupe d’actions*.

    ![Créer une règle](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Définissez la condition d’alerte en utilisant le lien **Sélectionner une ressource**, puis en spécifiant la cible via la sélection d’une ressource. Filtrez correctement en choisissant l’*abonnement*, le *type de ressource* et la *ressource* nécessaires.

    >[!NOTE]

    > Avant de continuer, vérifiez les signaux disponibles pour la ressource sélectionnée.

    ![Sélectionner une ressource](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Dans la mesure où Azure Alerts (préversion) permet de créer différents types d’alertes à partir d’une seule interface, selon le type d’alerte souhaité, choisissez une des étapes suivantes :

    - Pour les **alertes de métrique** : suivez les étapes 5 à 7, puis passez directement à l’étape 11.
    - Pour **Alertes de journal**, passez à l’étape 8.

    > [!NOTE]

    > L’option Alertes unifiées (préversion) prend également en charge les alertes de journal d’activité. [Plus d’informations](monitoring-activity-log-alerts-new-experience.md)

5. *Alertes de métrique* : assurez-vous que le **type de ressource** est sélectionné avec le type de signal **Métrique**. Ensuite, une fois la **ressource** appropriée choisie, cliquez sur le bouton *Terminé* pour revenir à la création de l’alerte. Utilisez ensuite le bouton **Ajouter des critères** pour choisir le signal spécifique dans la liste des options de signal, le service de surveillance et le type répertorié, qui sont disponibles pour la ressource sélectionnée précédemment.

    ![Sélectionner une ressource](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Toutes les ressources capables [d’alertes en quasi temps réel](monitoring-near-real-time-metric-alerts.md) sont répertoriées avec le service de surveillance **Plateforme** et le type de signal indiqué **Métrique**

6. *Alertes de métrique* : une fois le signal sélectionné, vous pouvez indiquer la logique de génération d’alertes. Pour référence, les données historiques du signal s’affichent avec une option permettant d’ajuster la fenêtre de temps à l’aide la fonction **Afficher l’historique**, avec un intervalle allant des six dernières heures à la semaine précédente. Avec la visualisation en place, la **logique d’alerte** peut être sélectionnée parmi les options proposées de condition, d’agrégation et de seuil. En tant qu’aperçu de la logique fournie, la condition figure dans la visualisation, ainsi que l’historique de signal, pour indiquer quand l’alerte aurait été déclenchée. Enfin, indiquez pendant combien de temps l’alerte doit rechercher la condition spécifiée en sélectionnant l’option de **période**, ainsi que la fréquence à laquelle l’alerte doit être exécutée en sélectionnant **Fréquence**.

    ![Configurer une logique du signal pour une métrique](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Alertes de métrique* : si le signal est une métrique, une fenêtre d’alerte peut être filtrée à l’aide de plusieurs points de données ou dimensions pour cette ressource Azure. Comme pour les alertes de métriques, la visualisation de l’historique de signal peut être choisie en indiquant une période de la liste déroulante **Afficher l’historique**. En outre, il est possible de sélectionner des dimensions de la métrique choisie pour filtrer les séries chronologiques requises. Le choix des dimensions (5 au maximum) est facultatif. La **logique d’alerte** peut être sélectionnée parmi les options proposées de condition, d’agrégation et de seuil. En tant qu’aperçu de la logique fournie, la condition figure dans la visualisation, ainsi que l’historique de signal, pour indiquer quand l’alerte aurait été déclenchée par le passé. Enfin, indiquez pendant combien de temps l’alerte doit rechercher la condition spécifiée en sélectionnant l’option de **période**, ainsi que la fréquence à laquelle l’alerte doit être exécutée en sélectionnant **Fréquence**.

    ![Configurer une logique de signal pour une métrique multidimensionnelle](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Alertes de journal* : assurez-vous que le **Type de ressource** est une source analytique telle que *Log Analytics* ou *Application Insights* et le type de signal **Journal**. Ensuite, une fois la **ressource** appropriée choisie, cliquez sur le bouton *Terminé*. Utilisez ensuite le bouton **Ajouter des critères** pour afficher la liste des options de signal disponibles pour la ressource et à partir de l’option **Recherche de journal personnalisée** de la liste de signaux pour le service de surveillance des journaux choisi, tel que *Log Analytics* ou *Application Insights*.

   ![Sélectionner une ressource : recherche de journal personnalisée](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Les listes Alerts (préversion) peuvent importer une requête analytique en tant que type de signal - **Journal (requête enregistrée)**, comme indiqué dans l’illustration ci-dessus. Les utilisateurs peuvent donc perfectionner votre requête dans Analytics et l’enregistrer pour l’utiliser ultérieurement dans Alerts. Pour en savoir plus sur l’enregistrement de requêtes, consultez la section relative à [l’utilisation des recherches de journaux dans Log Analytics](../log-analytics/log-analytics-log-searches.md) ou aux [requêtes partagées dans Application Insights Analytics](../log-analytics/log-analytics-overview.md). 

9.  *Alertes de journal* : une fois sélectionnée, la requête de génération d’alerte peut être indiquée dans le champ **Requête de recherche**. Si la syntaxe de la requête est incorrecte, le champ affiche le message d’erreur en rouge. Si la syntaxe de la requête est correcte, les données de la requête indiquée sont indiquées à titre de référence sous forme de graphique avec la possibilité d’ajuster la fenêtre de temps entre les six dernières heures et la semaine précédente.

 ![Configurer une règle d’alerte](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > La visualisation des données historiques n’est possible que si les résultats de la requête comportent des informations temporelles. Si votre requête produit des données synthétisées ou des valeurs de colonnes spécifiques, les mêmes informations sont indiquées sous forme de tracé unique.

    >  Pour le type de mesure de métrique d’alertes de journal utilisant Application Insights, vous pouvez spécifier la variable à utiliser pour regrouper les données à l’aide de l’option **Agréger sur**, comme illustré ci-dessous :

    ![Option Agréger sur](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Alertes de journal* : avec la visualisation en place, la **logique d’alerte** peut être sélectionnée parmi les options proposées de condition, d’agrégation et de seuil. Enfin, dans la logique, spécifiez le moment auquel évaluer la condition indiquée à l’aide de l’option **Période**. Précisez également la fréquence d’exécution de l’alerte en sélectionnant **Fréquence**.
Les **alertes de journal** peuvent reposer sur les éléments suivants :
   - *Nombre d’enregistrements* : une alerte est créée si le nombre d’enregistrements renvoyés par la requête est supérieur ou inférieur à la valeur indiquée.
   - *Mesure de métrique* : une alerte est créée si chaque *valeur d’agrégation* dans les résultats dépasse la valeur de seuil indiquée et s’il existe un *regroupement* selon la valeur choisie. Le nombre de violations d’une alerte est le nombre de fois où que le seuil est dépassé pendant la période choisie. Vous pouvez spécifier le nombre total de violations pour obtenir toutes les combinaisons de violations dans les résultats ou les violations consécutives pour exiger que les violations aient lieu dans des échantillons consécutifs. Apprenez-en davantage sur les [alertes de journal et leurs types](monitor-alerts-unified-log.md).

    > [!TIP]
    > Actuellement dans Alerts (préversion), les alertes de recherche de journal acceptent des valeurs personnalisées de *période* et de *fréquence* en minutes. Ces valeurs peuvent varier entre 5 et 1 440 minutes (c'est-à-dire 24 heures). Par conséquent, pour une période d’alerte de trois heures, convertissez cette durée en minutes (180).

11. Ensuite, définissez un nom pour votre alerte dans le champ **Nom de la règle d’alerte** avec une **description** détaillant les spécificités de l’alerte et une valeur de **gravité** choisie parmi les options fournies. Ces détails sont réutilisés dans l’ensemble des courriers électroniques d’alerte, notifications ou messages Push d’Azure Monitor. En outre, l’utilisateur peut activer immédiatement la règle d’alerte lors de la création via l’option **Activer la règle lors de sa création**.

    Pour les **alertes de journal** uniquement, certaines fonctionnalités supplémentaires sont disponibles dans les détails d’alerte :

    - **Supprimer des alertes** : quand vous activez la suppression de la règle d’alerte, les actions correspondant à la règle sont désactivées pour une durée définie après la création d’une alerte. La règle est toujours en cours d’exécution et crée des enregistrements d’alerte si les critères sont satisfaits. Cela vous donne le temps de résoudre le problème sans exécuter des actions en double.

        ![Supprimer des alertes de journal](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

12. Enfin, le cas échéant, spécifiez le **groupe d’actions** à déclencher pour la règle d’alerte lorsque la condition d’alerte est remplie. Vous pouvez choisir n’importe quel groupe d’actions existant avec l’alerte ou créer un autre groupe d’actions. En fonction du groupe d’actions sélectionné, lors du déclenchement de l’alerte, Azure envoie des courriers électroniques, envoie des SMS, appelle des webhooks, corrige le problème à l’aide de runbooks Azure, envoie un message Push à votre outil ITSM, etc. En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).

    Pour les **alertes de journal**, certaines fonctionnalités supplémentaires sont disponibles afin de passer outre les actions par défaut :

    - **Notification par courrier électronique** : substitue *L’objet du message électronique* dans le message, envoyé via le groupe d’actions, si une ou plusieurs actions de messagerie existent dans ledit groupe d’actions. Vous ne pouvez pas modifier le corps du message et ce champ n’est **pas** destiné à l’adresse de messagerie.
    - **Inclure la charge utile Json personnalisée** : remplace le JSON webhook utilisé par les groupes d’actions, si une ou plusieurs actions de webhook existent dans ledit groupe d’actions. L’utilisateur peut spécifier le format JSON à utiliser pour tous les webhooks configurés dans le groupe d’actions associé ; Pour plus d’informations sur les formats de webhook, voir [Action webhook pour les alertes de journal](monitor-alerts-unified-log-webhook.md). L’option Tester webhook est fournie pour vérifier le format et le traitement par la destination à l’aide de l’exemple de JSON, et cette option, comme son nom l’indique, est utilisée uniquement à des fins de **test**.

        ![Remplacements d’actions pour les alertes de journal](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

13. Si tous les champs sont valides et qu’une coche verte est visible, le bouton **Créer une règle d’alerte** peut être activé. L’alerte est alors créée dans Azure Monitor - Alerts (préversion). Toutes les alertes peuvent être consultées dans le tableau de bord Alerts (préversion).

    ![Création de règles](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="view-your-alerts-in-azure-portal"></a>Visualiser les alertes dans le portail Azure

1. Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alerts (préversion)** sous la section SURVEILLER.  

2. Le **tableau de bord Alerts (préversion)** s’affiche. Il contient toutes les alertes Azure unifiées et rassemblées dans un ![tableau de bord d’alertes](./media/monitoring-overview-unified/alerts-preview-overview.png) unique.
3. De gauche à droite, le tableau de bord affiche les éléments suivants (cliquez pour afficher la liste détaillée) :
    - *Alertes déclenchées* : nombre d’alertes correspondant à la logique et déclenchées
    - *Nombre total de règles d’alerte* : nombre de règles d’alerte créées et, implicitement, nombre d’alertes actuellement activées
4. La liste de toutes les alertes déclenchées est affichée : l’utilisateur peut cliquer pour voir les détails.
5. Pour rechercher des alertes spécifiques, vous pouvez utiliser les options de liste déroulante (en haut) pour filtrer les *abonnements, groupes de ressources et/ou ressources*. Pour les alertes non résolues, utilisez l’option de *filtrage d’alerte* pour rechercher un mot clé en fonction *du nom, des critères d’alerte, du groupe de ressources et de la ressource cible*.

## <a name="managing-your-alerts-in-azure-portal"></a>Gestion des alertes dans le portail Azure
1. Dans le [portail](https://portal.azure.com/), sélectionnez **Surveiller** et choisissez **Alerts (préversion)** sous la section SURVEILLER.  
2. Cliquez sur le bouton **Gérer les règles** dans la barre supérieure pour accéder à la section de gestion des règles, où toutes les règles d’alerte créées sont répertoriées, y compris les alertes désactivées.
3. Pour rechercher des règles d’alerte spécifiques, vous pouvez utiliser les filtres de la liste déroulante située en haut, qui permettent de filtrer les règles d’alerte selon l’*abonnement, les groupes de ressources et/ou les ressources*. Le volet de recherche au-dessus de la liste des règles d’alerte marquée *Filtrer les alertes* permet également d’indiquer mot clé, qui est comparé à *nom de l’alerte, à la condition et à la ressource cible* afin d’afficher uniquement les règles correspondantes.
   ![Gérer les règles d’alerte](./media/monitoring-overview-unified/alerts-preview-rules.png)
4. Pour afficher ou modifier une règle d’alerte spécifique, cliquez sur son nom affiché en tant que lien.
5. L’alerte définie est affichée dans la structure à trois niveaux : 1) Condition d’alerte, 2) Détails de l’alerte, 3) Groupe d’actions. Vous pouvez cliquer sur les **critères de la cible** pour modifier la logique d’alerte, ou vous pouvez ajouter un nouveau critère après avoir supprimé la logique antérieure à l’aide de l’icône Corbeille. De même, dans la section des détails de l’alerte, il est possible de modifier la **description** et la **gravité**. Vous pouvez modifier le groupe d’actions ou en créer un autre pour établir un lien avec l’alerte à l’aide du bouton **Nouveau groupe d’actions**.

   ![Modifier une règle d’alerte](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Le panneau supérieur reflète également les modifications apportées à l’alerte : **Enregistrer** pour valider toutes les modifications apportées à l’alerte, **Ignorer** pour revenir en arrière sans valider les modifications apportées à l’alerte, **Désactiver** pour désactiver l’alerte, après quoi elle n’est plus exécutée ou ne déclenche plus d’action. Enfin, **Supprimer** pour supprimer définitivement la règle d’alerte entière d’Azure.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail les nouvelles [alertes de métriques en quasi temps réel (préversion)](monitoring-near-real-time-metric-alerts.md).
- Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
- En savoir plus sur les [alertes de journal dans Azure Alerts (préversion)](monitor-alerts-unified-log.md)
- [En savoir plus sur les Alertes de journal d’activité dans Alertes (préversion)](monitoring-activity-log-alerts-new-experience.md)
