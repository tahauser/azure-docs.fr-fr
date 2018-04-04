---
title: Audit et réception de notifications relatives à des actions importantes dans votre abonnement Azure | Microsoft Docs
description: Comprendre l’historique de la gestion des ressources, de l’intégrité du service et d’autres activités relatives à l’abonnement dans le journal d’activité, puis utiliser une alerte de journal d’activité pour recevoir une notification par e-mail lorsqu’une opération disposant de privilèges élevés est effectuée dans votre abonnement.
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: dd04cd9c8074d49df168b32840d12901da83c834
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="audit-and-receive-notifications-about-important-actions-in-your-azure-subscription"></a>Audit et réception de notifications relatives à des actions importantes dans votre abonnement Azure

Le **journal d’activité Azure** fournit un historique des événements au niveau de l’abonnement dans Azure. Il fournit des informations indiquant *qui* a créé, mis à jour ou supprimé *quelles* ressources et *quand*. Vous pouvez créer une **alerte de journal d’activité** pour recevoir des notifications par e-mail, SMS ou webhook lorsqu’une activité correspondant à vos conditions d’alerte se produit. Cette procédure de démarrage rapide explore la création d’un groupe de sécurité réseau simple, la navigation dans le journal d’activité pour comprendre l’événement qui s’est produit et la création d’une alerte de journal d’activité pour vous avertir en cas de création d’un groupe de sécurité réseau.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Mise en réseau**, puis **Groupe de sécurité réseau**.

3. Entrez « myNetworkSG » en tant que **nom** et créez un groupe de ressources nommé **myResourceGroup**. Cliquez sur le bouton **Créer** .

    ![Créer un groupe de sécurité réseau dans le portail](./media/monitor-quick-audit-notify-action-in-subscription/create-network-security-group.png)

## <a name="browse-the-activity-log-in-the-portal"></a>Parcourir le journal d’activité dans le portail

Un événement décrivant la création du groupe de sécurité réseau a été ajouté au journal d’activité. Utilisez les instructions suivantes pour identifier cet événement.

1. Cliquez sur le bouton **Analyse** situé dans la liste de navigation de gauche. Il permet d’ouvrir la section Journal d’activité. Cette section contient l’historique de toutes les actions exécutées par les utilisateurs sur les ressources dans votre abonnement. Vous pouvez filtrer cet historique à l’aide de plusieurs propriétés, telles que le **groupe de ressources**, **l’intervalle de temps** et la **catégorie**.

2. Dans la section **Journal d’activité**, cliquez sur la liste déroulante **Groupe de ressources** et sélectionnez **myResourceGroup**. Dans la liste déroulante **Timespan** (Intervalle de temps), sélectionnez **Last 1 hour** (Dernière heure). Cliquez sur **Appliquer**.

    ![Filtrer le journal d’activité](./media/monitor-quick-audit-notify-action-in-subscription/browse-activity-log.png)

3. Cliquez sur l’événement **Write NetworkSecurityGroups** (Écrire des groupes de sécurité réseau) dans le tableau d’événements affiché.

## <a name="browse-an-event-in-the-activity-log"></a>Parcourir un événement dans le journal d’activité

La section qui s’affiche contient des informations de base sur l’opération qui a été effectuée, notamment le nom, l’horodatage et l’utilisateur ou l’application l’ayant effectuée.

![Afficher le résumé de l’événement dans le journal d’activité](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-summary.png)

Cliquez sur l’onglet **JSON** pour afficher tous les détails sur l’événement. Cela inclut les détails sur la façon dont l’utilisateur ou l’application a été autorisé(e) à effectuer l’opération, la catégorie et le niveau de l’événement, ainsi que l’état de l’opération.

![Afficher les détails de l’événement dans le journal d’activité](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-json.png)

## <a name="create-an-activity-log-alert"></a>Créer une alerte de journal d’activité

1. Cliquez sur l’onglet **Résumé** pour revenir au résumé de l’événement.

2. Dans la section du résumé qui s’affiche, cliquez sur **Ajouter une alerte de journal d’activité**.

    ![Créer un groupe de sécurité réseau dans le portail](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-summary.png)

3. Dans la section qui s’affiche, attribuez un nom et une description à l’alerte de journal d’activité.

4. Sous **Criteria** (Critères), vérifiez que **Event category** (Catégorie d’événement) est défini sur **Administrative** (Administration), que **Resource Type** (Type de ressource) est défini sur **Network security groups** (Groupes de sécurité réseau), que **Operation name** (Nom de l’opération) est défini sur **Create or Update Network Security Group** (Créer ou mettre à jour un groupe de sécurité réseau), que **Status** (État) est défini sur **Succeeded** (Réussi) et que tous les autres champs de critères sont vides ou définis sur **All** (Tous). Les critères définissent les règles utilisées pour déterminer si l’alerte doit être activée lorsqu’un nouvel événement apparaît dans le journal d’activité.

    ![Créer un groupe de sécurité réseau dans le portail](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-alert-criteria.png)

5. Sous **Alert via** (Alerter via), sélectionnez le groupe d’actions **New** (Nouveau) et entrez un **nom** et un **nom court** pour le groupe d’actions. Le groupe d’actions définit l’ensemble des actions effectuées lorsque l’alerte est activée (lorsque les critères correspondent à un nouvel événement).

6. Sous **Actions**, ajoutez 1 ou plusieurs actions en entrant un **nom** pour l’action, le **type d’action** (par exemple, e-mail, SMS ou webhook) et les **détails** de ce type d’action spécifique (par exemple, une URL de webhook, une adresse e-mail ou un numéro SMS).

    ![Créer un groupe de sécurité réseau dans le portail](./media/monitor-quick-audit-notify-action-in-subscription/activity-log-alert-actions.png)

7. Cliquez sur **Ok** pour enregistrer l’alerte de journal d’activité.

## <a name="test-the-activity-log-alert"></a>Tester l’alerte de journal d’activité

> [!NOTE]
> Il faut environ 5 minutes pour qu’une alerte de journal d’activité soit entièrement activée. Les nouveaux événements qui se produisent avant que l’alerte de journal d’activité ne soit entièrement activée ne génèrent pas de notifications.
>
>

Pour tester l’alerte, répétez les étapes de la section précédente sur la **création d’un groupe de sécurité réseau**, mais attribuez un nom différent à ce groupe de sécurité réseau et réutilisez le groupe de ressources existant. En quelques minutes, vous recevrez une notification indiquant que le groupe de sécurité réseau a été créé.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources et le groupe de sécurité réseau. Pour ce faire, tapez le nom du groupe de ressources que vous avez créé dans la zone de recherche en haut du portail et cliquez sur le nom du groupe de ressources. Dans la section qui s’affiche, cliquez sur le bouton **Supprimer un groupe de ressources**, tapez le nom du groupe de ressources, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez effectué une opération pour générer un événement de journal d’activité et vous avez créé une alerte de journal d’activité pour être averti si cette opération se produit de nouveau à l’avenir. Vous avez ensuite testé l’alerte en réexécutant cette opération. Dans Azure, les événements de journal d’activité des 90 derniers jours sont disponibles. Si vous souhaitez conserver des événements pendant plus de 90 jours, essayez d’archiver les données de votre journal d’activité de la même façon que vos autres données de surveillance.

> [!div class="nextstepaction"]
> [Archiver les données de surveillance](./monitor-tutorial-archive-monitoring-data.md)
