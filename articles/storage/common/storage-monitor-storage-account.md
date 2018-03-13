---
title: "Surveillance d’un compte de stockage Azure | Microsoft Docs"
description: "Découvrez comment surveiller un compte de stockage dans Azure en utilisant le portail Azure."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: 20cb425b64bfba06d64b3c6c41f9ef496395ad8e
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Surveillance d'un compte de stockage dans le portail Azure

[Azure Storage Analytics](../storage-analytics.md) fournit des métriques pour tous les services de stockage et des journaux pour les objets Blob, les files d’attente et les tables. Vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour configurer les métriques et les journaux enregistrés pour votre compte, et configurer des graphiques qui fournissent des représentations visuelles de vos données de métrique.

> [!NOTE]
> L’analyse des données de surveillance dans le portail Azure occasionne des frais. Pour plus d'informations, consultez la page [Storage Analytics et facturation](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Azure Files prend actuellement en charge les métriques de Storage Analytics, mais pas encore la journalisation.
>
> Les comptes de stockage avec un type de réplication Stockage redondant dans une zone (ZRS) prennent en charge les mesures et la journalisation. Les comptes ZRS précédents, qui ont été renommés ZRS Classic, ne prennent pas en charge les mesures et la journalisation. Pour plus d’informations sur ZRS, consultez [Stockage redondant dans une zone](storage-redundancy.md#zone-redundant-storage). 
> 
> Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configuration de la surveillance d'un compte de stockage

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, puis le nom du compte de stockage pour ouvrir le tableau de bord du compte.
1. Sélectionnez **Diagnostics** dans la section **SURVEILLANCE** du panneau du menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Sélectionnez le **type** de données de métrique pour chaque **service** à surveiller et la **stratégie de rétention** pour les données. Vous pouvez également désactiver la surveillance en définissant **l’état** sur **Off**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Il existe deux types de métriques que vous pouvez activer pour chaque service. Les deux sont activés par défaut pour les nouveaux comptes de stockage :

   * **Agréger** : collecte des métriques telles que l'entrée/la sortie, la disponibilité, la latence et les pourcentages de réussite. Ces métriques sont agrégées pour les services Blob, File d’attente, Table et Fichier.
   * **Par API** : outre les métriques agrégées, collecte le même ensemble de métriques pour chaque opération de stockage de l’API du service Stockage Azure.

   Pour définir la stratégie de rétention de données, déplacez le curseur **Rétention (en jours)** ou entrez le nombre de jours durant lesquels les données sont conservées (de 1 à 365 jours). La valeur par défaut pour les nouveaux comptes de stockage est de sept jours. Si vous ne souhaitez pas définir de stratégie de rétention, entrez 0. Dans ce cas, il vous appartient de supprimer ou non les données de surveillance.

   > [!WARNING]
   > Vous êtes facturé lorsque vous supprimez manuellement les données de métrique. Les données analytiques obsolètes (antérieures à votre stratégie de rétention des données) sont supprimées par le système sans frais. Nous vous recommandons de définir une stratégie de rétention en fonction de la durée de conservation que vous souhaitez appliquer aux données d’analyse de votre compte. Consultez [Quels sont les frais encourus quand vous activez les métriques de stockage ?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) pour plus d’informations.
   >

1. Une fois la configuration de la surveillance terminée, sélectionnez **Enregistrer**.

Un ensemble de métriques par défaut s’affiche dans des graphiques sur le panneau du compte de stockage, ainsi que les panneaux des services individuels (blob, file d’attente, table et fichier). Une fois que vous avez activé les métriques d’un service, l’affichage des données dans les graphiques peut prendre jusqu’à une heure. Vous pouvez sélectionner **Modifier** sur n’importe quel graphique de métrique pour [configurer les métriques](#how-to-customize-metrics-charts) qui sont affichées dans le graphique.

Vous pouvez désactiver la collecte de métriques et la journalisation en définissant **l’état** sur **Off**.

> [!NOTE]
> Le stockage Azure utilise un [stockage Table](../common/storage-introduction.md#table-storage) pour stocker les métriques pour votre compte de stockage, et stocke les métriques dans des tables de votre compte. Pour plus d’informations, consultez. [Stockage des métriques](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personnaliser les graphiques de métrique

La procédure suivante permet de choisir les métriques de stockage à afficher dans un graphique de métrique. 

1. Commencez par afficher un graphique de métrique de stockage dans le portail Azure. Vous trouverez les graphiques dans le **panneau du compte de stockage** et dans le panneau **Métriques** d’un service individuel (blob, file d’attente, table, fichier).

   Dans cet exemple, nous utilisons le graphique suivant qui apparaît dans le **panneau du compte de stockage** :

   ![Sélection de graphique dans le portail Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Ensuite, cliquez n’importe où dans le graphique pour ouvrir le panneau **Métrique**. Sélectionnez **Modifier le graphique** pour ouvrir le panneau **Modifier le graphique**.

   ![Bouton Modifier le graphique sur le panneau du graphique](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. Dans le panneau **Modifier le graphique**, sélectionnez la **période** des métriques à afficher dans le graphique et le **service** (blob, file d’attente, table, fichier) dont vous souhaitez afficher les métriques. Ici, nous avons choisi d’afficher les métriques de la semaine précédente pour le service blob :

   ![Sélection de la période et du service dans le panneau Modifier le graphique](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Sélectionnez la **métrique** individuelle à afficher dans le graphique, puis cliquez sur **OK**. Par exemple, ici, nous avons choisi d’afficher les métriques *ContainerCount* et *ObjectCount* :

   ![Sélection de métrique individuelle dans le panneau Modifier le graphique](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Les paramètres de votre graphique n’ont aucune incidence sur la collecte, l’agrégation ou le stockage de données de surveillance dans le compte de stockage, uniquement sur l’affichage des données de métrique.

### <a name="metrics-availability-in-charts"></a>Disponibilité des métriques dans les graphiques

La liste des métriques disponibles change selon le service que vous avez choisi dans la liste déroulante et le type d’unité du graphique que vous modifiez. Par exemple, vous pouvez sélectionner les métriques de pourcentage comme *PercentNetworkError* et *PercentThrottlingError* uniquement si vous modifiez un graphique qui affiche les unités en pourcentage :

![Graphique de pourcentage d’erreur de demande dans le portail Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Résolution des métriques

Les métriques que vous avez sélectionnées dans Diagnostics déterminent la résolution des métriques qui sont disponibles pour votre compte :

* La surveillance **Agréger** fournit des métriques telles que l’entrée/la sortie, la disponibilité, la latence et les pourcentages de réussite. Ces métriques sont agrégées à partir des services Blob, File d’attente, Table et Fichier.
* **Par API** offre une résolution plus pointue, avec les métriques disponibles pour les opérations de stockage individuelles, en plus des agrégats au niveau du service.

## <a name="configure-metrics-alerts"></a>Configurer des alertes de métriques

Vous pouvez créer des alertes pour vous avertir lorsque les seuils sont atteints pour les métriques des ressources de stockage.

1. Pour ouvrir le **panneau Règles d’alerte**, faites défiler jusqu'à la section **SURVEILLANCE** du **panneau de menu** et sélectionnez **Règles d’alerte**.
1. Sélectionnez **Ajouter une alerte** pour ouvrir le panneau **Ajouter une règle d’alerte**
1. Sélectionnez une **ressource** (blob, fichier, file d’attente, table) dans la liste déroulante, puis entrez un **nom** et une **Description** pour votre nouvelle règle d’alerte.
1. Sélectionnez la **métrique** pour laquelle vous souhaitez ajouter une alerte, une **condition** d’alerte et un **seuil**. Le type d’unité de seuil change en fonction de la métrique choisie. Par exemple, « count » est le type d’unité pour *ContainerCount*, tandis que l’unité pour la métrique *PercentNetworkError* est un pourcentage.
1. Sélectionnez la **Période**. Les métriques qui atteignent ou dépassent le seuil durant la période déclenchent une alerte.
1. (Facultatif) Configurez des notifications **E-mail** et **Webhook**. Pour plus d’informations sur webhooks, consultez [Configurer un webhook sur une alerte de métrique Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Si vous ne configurez pas de notifications e-mail ou webhook, les alertes s’affichent uniquement dans le portail Azure.

![Panneau « Ajouter une règle d’alerte » dans le portail Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Ajouter des graphiques de métriques au tableau de bord du portail

Vous pouvez ajouter des graphiques de métriques Stockage Azure pour l’un de vos comptes de stockage au tableau de bord du portail.

1. Cliquez sur **Modifier le tableau de bord** lorsque le tableau de bord est affiché dans le [portail Azure](https://portal.azure.com).
1. Dans la **Galerie de vignettes**, sélectionnez **Recherche des vignettes par** > **Type**.
1. Sélectionnez **Type** > **Comptes de stockage**.
1. Dans **Ressources**, sélectionnez le compte de stockage dont vous souhaitez ajouter les métriques au tableau de bord.
1. Sélectionnez **Catégories** > **Surveillance**.
1. Glissez-déplacez la vignette du graphique sur votre tableau de bord pour la métrique à afficher. Répétez cette opération pour toutes les métriques à afficher sur le tableau de bord. Dans l’image suivante, le graphique « Objets Blob - Nombre total de requêtes » est mis en surbrillance comme exemple, mais tous les graphiques peuvent être placés sur votre tableau de bord.

   ![Galerie de vignettes dans le portail Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Sélectionnez **Personnalisation terminée** en haut du tableau de bord lorsque vous avez terminé d’ajouter des graphiques.

Une fois que vous avez ajouté des graphiques à votre tableau de bord, vous pouvez les personnaliser davantage comme décrit dans [Personnaliser les graphiques de métrique](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Configuration de la journalisation

Vous pouvez demander au Stockage Azure d’enregistrer les journaux de diagnostic pour les requêtes de lecture, d’écriture et de suppression pour les services blob, table et file d’attente. La stratégie de rétention des données que vous définissez s’applique également à ces journaux.

> [!NOTE]
> Azure Files prend actuellement en charge les métriques de Storage Analytics, mais pas encore la journalisation.
>

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Comptes de stockage**, puis le nom du compte de stockage pour ouvrir le panneau du compte de stockage.
1. Sélectionnez **Diagnostics** dans la section **SURVEILLANCE** du panneau du menu.

    ![Élément de menu Diagnostics sous SURVEILLANCE dans le portail Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Vérifiez que **l’état** est défini sur **On**, puis sélectionnez les **services** pour lesquels vous souhaitez activer la journalisation.

    ![Configurez la journalisation dans le portail Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Cliquez sur **Enregistrer**.

Les journaux de diagnostic sont enregistrés dans un conteneur d’objets blob nommé $logs dans votre compte de stockage. Vous pouvez afficher les données du journal à l’aide d’un explorateur de stockage, comme [Microsoft Storage Explorer](http://storageexplorer.com) ou par programme à l’aide de la bibliothèque cliente de stockage ou de PowerShell.

Pour plus d’informations sur l’accès au conteneur $logs, consultez [Activation de la journalisation du stockage et accès aux données des journaux](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [les métriques, la journalisation et la facturation](../storage-analytics.md) pour Storage Analytics.
* [Activer les métriques Stockage Azure et afficher les données des métriques](../storage-enable-and-view-metrics.md) à l’aide de PowerShell et par programme avec C#.
