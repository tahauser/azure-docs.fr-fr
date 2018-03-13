---
title: "Créer des alertes de journal d’activité | Documents Microsoft"
description: "Recevez des notifications par SMS, webhook et e-mail lors de la survenue de certains événements dans le journal d’activité."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: c8a2ce3ca90895262e77c3895867d29c9d3530a2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts"></a>Créer des alertes de journal d’activité

## <a name="overview"></a>Vue d'ensemble
Les alertes de journal d’activité s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte. Il s’agit de ressources Azure, et peuvent donc être créées à l’aide d’un modèle Azure Resource Manager. Elles peuvent également être créées, mises à jour ou supprimées dans le portail Azure. Cet article présente les concepts qui sous-tendent les alertes de journal d’activité. Cet article décrit comment utiliser le portail Azure pour configurer une alerte sur les événements de journal d’activité.

> [!NOTE]

>  Les [Alertes (préversion)](monitoring-overview-unified-alerts.md) offrent actuellement une expérience améliorée de création et de gestion des journaux d’activité.  [Plus d’informations](monitoring-activity-log-alerts-new-experience.md)

En général, vous créez des alertes du journal d’activité pour recevoir des notifications lorsque :

* des modifications spécifiques se produisent dans votre abonnement Azure, souvent étendues à des groupes de ressources ou à des ressources. Par exemple, vous pouvez désirer être averti lorsqu’une machine virtuelle dans myProductionResourceGroup est supprimée. Vous pouvez également vouloir être averti si de nouveaux rôles sont attribués à un utilisateur dans votre abonnement.
* Un événement d’intégrité du service se produit. Les événements d’intégrité du service incluent la notification des incidents et des événements de maintenance qui s’appliquent aux ressources de votre abonnement.

Dans les deux cas, l’alerte du journal d’activité surveille uniquement les événements de l’abonnement dans lequel elle a été créée.

Vous pouvez configurer une alerte de journal d’activité en fonction de n’importe quelle propriété de niveau supérieur de l’objet JSON d’un événement de journal d’activité. Toutefois, le portail affiche uniquement les options les plus courantes :

- **Catégorie** : administration, intégrité du service, mise à l’échelle automatique et recommandation. Pour plus d’informations, reportez-vous à la rubrique [Présentation du journal d’activité Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Pour en savoir plus sur les événements d’intégrité du service, consultez [Recevoir des alertes de journal d’activité sur les notifications de service](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Groupe de ressources**
- **Ressource**
- **Type de ressource**
- **Nom de l’opération** : le nom de l’opération de Contrôle d’accès basé sur le rôle de Gestionnaire des ressources.
- **Niveau** : le niveau de gravité de l’événement (détaillé, informationnel, avertissement, erreur ou critique).
- **État** : l’état de l’événement, généralement « Démarré », « Échoué » ou « Réussi ».
- **Événement lancé par** : également appelé l’« appelant ». L’adresse e-mail ou l’identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération.

> [!NOTE]
> Lorsque la catégorie est « administration », vous devez spécifier au moins l’un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.

Lorsqu’une alerte du journal d’activité devient active, elle utilise un groupe d’actions pour générer des actions ou des notifications. Un groupe d’actions est un jeu réutilisable de destinataires de notifications, telles que des adresses de messagerie, des URL webhook ou des numéros de téléphone SMS. Les destinataires peuvent être référencés à partir de plusieurs alertes pour centraliser et regrouper vos canaux de notification. Lorsque vous définissez votre alerte de journal d’activité, vous avez deux options. Vous pouvez :

* utiliser un groupe d’actions existant dans votre alerte de journal d’activité.
* créer un nouveau groupe d’action.

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le portail Azure](monitoring-action-groups.md).

Pour en savoir plus sur les notifications d’intégrité du service, consultez [Recevoir des alertes de journal d’activité sur les notifications d’intégrité du service](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Créer une alerte pour un événement du journal d’activité avec un nouveau groupe d’actions dans le portail Azure
1. Dans le [portail](https://portal.azure.com), sélectionnez **Moniteur**.

    ![Le service « Moniteur »](./media/monitoring-activity-log-alerts/home-monitor.png)
2. Dans la section **Journal d’activité**, sélectionnez **Alertes**.

    ![L’onglet « Alertes »](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Sélectionnez **Ajouter une alerte de journal d’activité**, puis renseignez les champs.

4. Entrez un nom dans la boîte **Nom de l’alerte activité journal** et sélectionnez une **Description**.

    ![La commande « Ajouter une alerte activité journal »](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. La boîte **Abonnement** est automatiquement renseignée avec votre abonnement actuel. Cet abonnement est celui dans lequel le groupe d’actions sera enregistré. La ressource d’alerte est déployée pour cet abonnement et surveille les événements du journal d’activité.

    ![La boîte de dialogue « Ajouter une alerte activité journal »](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Sélectionnez le **Groupe de ressources** dans lequel la ressource d’alerte est créée. Le groupe de ressources n’est pas surveillé par l’alerte. Au lieu de cela, il s’agit du groupe de ressources où se trouve la ressource d’alerte.

7. Si vous le souhaitez, vous pouvez sélectionner une **Catégorie d’événement**, ce qui va modifier les filtres supplémentaires affichés. Pour les événements d’administration, les filtres incluent **Groupe de ressources**, **Ressource**, **Type de ressource**, **Nom de l’opération**, **Niveau**, **État**, et **Événement initié par**. Ces valeurs identifient les événements que cette alerte doit surveiller.

    >[!NOTE]
    >Vous devez spécifier au moins l’un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.
    >
    >

8. Entrez un nom dans la boîte **Nom du groupe d’actions** et entrez un nom dans la boîte **Nom court**. Le nom court est utilisé à la place d’un nom de groupe d’actions complet lorsque les notifications sont envoyées à l’aide de ce groupe.

9.  Définissez une liste d’actions en indiquant les éléments suivants :

    a. **Nom** : entrez le nom, alias ou identificateur de l’action.

    b. **Type d’action** : sélectionnez SMS, e-mail ou webhook.

    c. **Détails** : selon le type d’actions, entrez un numéro de téléphone, une adresse e-mail ou une URI Webhook.

10. Sélectionnez **OK** pour créer l’alerte.

L’alerte prend quelques minutes pour se propager entièrement et devenir active. Elle se déclenche lorsque de nouveaux événements correspondent aux critères de l’alerte.

Pour plus d’informations, consultez [Comprendre le schéma webhook utilisé dans les alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Le groupe d’actions défini dans cette procédure est réutilisable en tant que groupe d’actions existant, pour la définition de toutes les futures alertes.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Créer une alerte concernant un événement du journal d’activité pour un groupe d’actions existant dans le portail Azure
1. Suivez les étapes 1 à 7 dans la section précédente pour créer votre alerte de journal d’activité.

2. Sous **Notifier via**, sélectionnez le bouton du groupe d’actions **Existant**. Sélectionnez un groupe d’actions existant dans la liste.

3. Sélectionnez **OK** pour créer l’alerte.

L’alerte prend quelques minutes pour se propager entièrement et devenir active. Elle se déclenche lorsque de nouveaux événements correspondent aux critères de l’alerte.

## <a name="manage-your-alerts"></a>Gérez vos alertes

Une fois l’alerte créée, elle apparaît dans la section Alertes du panneau Surveillance. Sélectionnez l’alerte que vous souhaitez gérer pour :

* la modifier
* la supprimer
* la désactiver ou l’activer si vous voulez arrêter temporairement ou reprendre la réception de notifications pour l’alerte.

## <a name="next-steps"></a>Étapes suivantes
- Obtenir une [vue d’ensemble des alertes](monitoring-overview-alerts.md).
- En savoir plus sur la [limitation du débit des notifications](monitoring-alerts-rate-limiting.md).
- Consultez le [schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).
- En savoir plus sur les [groupes d’actions](monitoring-action-groups.md).  
- En savoir plus sur les [notifications sur l’intégrité du service](monitoring-service-notifications.md).
- Créer une [alerte de journal d’activité pour surveiller toutes les opérations du moteur de mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Créer une [alerte de journal d’activité pour surveiller tous les échecs d’opérations de mise à l’échelle automatique dans votre abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
