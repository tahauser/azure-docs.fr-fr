---
title: Créer des alertes de journal d’activité et les gérer avec la nouvelle expérience Alertes (préversion) dans Azure Monitor | Microsoft Docs
description: Cet article donne des informations sur la création d’alertes de journal d’activité avec l’onglet Alertes (préversion) sous Azure Monitor. Il décrit en détail la nouvelle expérience utilisateur de cette fonctionnalité.
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: a7553e4155df0d4ee49b798f44ca636dc7ecdcd2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Créer des alertes de journal d’activité avec la nouvelle expérience Alertes (préversion)

Les alertes de journal d’activité s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte.

Elles concernent les ressources Azure. Pour les créer, il est possible d’utiliser un modèle Azure Resource Manager. Elles peuvent également être créées, mises à jour ou supprimées dans le portail Azure. Cet article présente les concepts qui sous-tendent les alertes de journal d’activité. Il explique ensuite comment utiliser le Portail Azure pour configurer une alerte sur des événements du journal d’activité dans [Alertes Azure (préversion)](monitoring-overview-unified-alerts.md).

En général, les alertes de journal d’activité permettent de recevoir une notification en cas de modification particulière des ressources de l’abonnement Azure, souvent à l’échelle d’un groupe de ressources ou d’une ressource en particulier. Par exemple, vous pouvez demander à être informé lorsqu’une machine virtuelle de **myProductionResourceGroup** (exemple de groupe de ressources) est supprimée, ou que de nouveaux rôles sont attribués à l’un des utilisateurs de votre abonnement.

Vous pouvez configurer une alerte de journal d’activité en fonction de n’importe quelle propriété de niveau supérieur de l’objet JSON d’un événement de journal d’activité. Cependant, le portail affiche les options les plus courantes, comme l’expliquent les sections suivantes :

>[!NOTE]

> Lorsque la catégorie est « administratif », vous devez spécifier au moins un des critères précédents dans votre alerte. L’alerte créée ne s’activera peut-être pas à chaque fois qu’un événement sera créé dans les journaux d’activité.
>

Lorsqu’une alerte du journal d’activité devient active, elle utilise un groupe d’actions pour générer des actions ou des notifications. Un groupe d’actions est un jeu réutilisable de destinataires de notifications, telles que des adresses de messagerie, des URL webhook ou des numéros de téléphone SMS. Les destinataires peuvent être référencés à partir de plusieurs alertes pour centraliser et regrouper vos canaux de notification. Lorsque vous définissez votre alerte de journal d’activité, vous avez deux options. Vous pouvez :

* utiliser un groupe d’actions existant dans votre alerte de journal d’activité.
* créer un nouveau groupe d’action.

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le portail Azure](monitoring-action-groups.md).

Pour en savoir plus sur les notifications d’intégrité du service, consultez [Recevoir des alertes de journal d’activité sur les notifications d’intégrité du service](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>Nouveautés de la préversion Alertes pour les journaux d’activité

[Alertes Azure (aperçu)](monitoring-overview-unified-alerts.md) offre désormais une meilleure expérience utilisateur pour les alertes de journal d’activité. Avec cette [expérience optimisée](monitoring-overview-unified-alerts.md), vous pouvez à présent :

- [Créer](#create-an-alert-rule-for-an-activity-log) et [gérer](#view-and-manage-activity-log-alert-rules) les règles d’alerte de journal d’activité sur le panneau **Monitor** > **Alertes (préversion)**. En savoir plus sur les [Journaux d’activité](monitoring-overview-activity-logs.md).

- **Nouvelles options pour la cible des alertes** : à la création d’une règle d’alerte de journal d’activité, il est maintenant possible de sélectionner une ressource cible, un groupe de ressources ou un abonnement.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Créer une règle d’alerte pour un journal d’activité

> [!NOTE]

>  Lorsque vous créez des règles d’alerte, vérifiez les points suivants :

> - L’abonnement présent dans l’étendue n’est pas différent de celui dans lequel l’alerte est créée.
- Les critères doivent être : niveau / état / appelant / groupe de ressources / ID de ressource / type de ressource / catégorie d’événement, sur lesquels l’alerte est configurée.
- Il n’existe aucune condition « anyOf » ni aucune condition imbriquée dans le JSON de configuration des alertes (en fait, une seule condition allOf est autorisée, sans aucune autre allOf/anyOf).


Procédez comme suit :

1. Sur le Portail Azure, sélectionnez **Monitor** > **Alertes (préversion)**.
2. Cliquez sur **Nouvelle règle d’alerte** en haut de la fenêtre **Alertes (préversion)**.

     ![nouvelle règle d'alerte](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     La fenêtre **Créer une règle** s’affiche.

      ![nouvelles options d'alerte](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. Sous **Définir une condition d’alerte,** indiquez les informations suivantes, puis cliquez sur **Fait**.

    - **Cible de l’alerte :** pour afficher et sélectionner la cible de la nouvelle alerte, utilisez **Filtrer par abonnement** / **Filtrer par type de ressource** et sélectionnez la ressource ou le groupe de ressources dans la liste qui s’affiche.

    > [!NOTE]

    > Vous pouvez sélectionner une ressource, un groupe de ressources ou tout un abonnement.

    **Vue Exemple de cible de l’alerte**

     ![Sélectionner la cible](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - Sous **Critères de la cible**, cliquez sur **Ajouter des critères** et sélectionnez le type de signal **Journal d’activité**.

    - Sélectionnez le signal dans la liste qui s’affiche.

    Vous pouvez sélectionner la chronologie de l’historique du journal et la logique d’alerte correspondante pour ce signal cible :

    **Écran Ajouter des critères**

    ![ajouter des critères](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Période de l’historique** : au cours des dernières 6/12/24 heures, au cours de la semaine dernière.

    **Logique d'alerte** :

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. Sous **Définir les détails des règles d’alerte**, indiquez les informations suivantes :

    - **Nom de la règle d’alerte** : nom de la nouvelle règle d’alerte.
    - **Description** : description de la nouvelle règle d’alerte.
    - **Enregistrer l’alerte dans le groupe de ressources** : sélectionnez le groupe de ressources dans lequel vous souhaitez enregistrer cette nouvelle règle.

5. Dans le menu déroulant sous **Groupe d’actions**, spécifiez le groupe d’actions que vous souhaitez affecter à cette nouvelle règle d’alerte. Vous pouvez également [créer un nouveau groupe d’actions](monitoring-action-groups.md) et l’affecter à la nouvelle règle. Pour créer un groupe, cliquez sur **+ Nouveau groupe**.

6. Pour activer les règles après l’avoir créé, cliquez sur **Oui** dans l’option **Activer la règle lors de la création**.
7. Cliquez sur **Créer une règle d'alerte**.

    La nouvelle règle d’alerte de journal d’activité est créée et un message de confirmation s’affiche en haut à droite de la fenêtre.

    ![ règle d’alerte créée](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Vous pouvez activer, désactiver, modifier ou supprimer une règle. [En savoir plus](#view-and-manage-activity-log-alert-rules) sur la gestion des règles de journal d’activité.

## <a name="view-and-manage-activity-log-alert-rules"></a>Afficher et gérer les règles d’alerte de journal d’activité

1. Sur le Portail Azure, cliquez sur **Monitor** > **Alertes (préversion)**, puis sur **Gérer les règles** en haut à gauche de la fenêtre.

    ![ gérer les règles d’alerte](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    La liste des règles disponibles s’affiche.

2. Recherchez la règle de journal d’activité à modifier.

    ![ rechercher des règles d’alerte de journal d'activité](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Vous pouvez utiliser les filtres disponibles (**Abonnement**, **Groupe de ressources** ou **Ressource**) pour trouver la règle d’activité que vous souhaitez modifier.

    > [!NOTE]

    > Les seuls champs que vous pouvez éditer sont : **Description** , **Critères cibles** et **Groupes d’actions**.

3.  Sélectionnez la règle, puis double-cliquez pour modifier ses options. Apportez les modifications nécessaires, puis cliquez sur **Enregistrer**.

    ![ gérer les règles d’alerte](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Vous pouvez désactiver, activer ou supprimer un profil. Sélectionnez l’option souhaitée en haut de la fenêtre, après avoir sélectionné la règle en suivant les instructions de l’étape 2.


## <a name="next-steps"></a>Étapes suivantes

- [Archiver des alertes de journal d’activité](monitoring-archive-activity-log.md)
- [Transmettre des journaux d’activité à Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Migrer vers des journaux d’activité](monitoring-migrate-management-alerts.md)
