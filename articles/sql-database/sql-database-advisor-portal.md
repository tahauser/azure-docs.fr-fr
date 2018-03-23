---
title: Appliquer les recommandations relatives aux performances - Azure SQL Database | Microsoft Docs
description: Utilisez le portail Azure pour trouver des recommandations vous permettant d’optimiser les performances de votre Azure SQL Database.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 32071522bbe069e68bdaed181f64b814eb70b84c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="find-and-apply-performance-recommendations"></a>Rechercher et appliquer les recommandations en matière de performances

Vous pouvez utiliser le portail Azure pour trouver des recommandations vous permettant d’optimiser les performances de votre Azure SQL Database ou pour résoudre un problème identifié dans votre charge de travail. La page **Recommandations en matière de performances** du portail Azure vous permet de trouver les meilleures recommandations en fonction de leur impact potentiel. 

## <a name="viewing-recommendations"></a>Affichage des recommandations

Pour afficher et appliquer des recommandations en matière de performances, vous devez disposer des autorisations de [contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-what-is.md) adéquates dans Azure. Les autorisations **Lecteur** et **Collaborateur de base de données SQL** sont obligatoires pour afficher les recommandations, et les autorisations **Propriétaire** et **Collaborateur de base de données SQL** sont obligatoires pour exécuter toutes les actions, créer ou supprimer des index et annuler la création d’index.

Pour rechercher des recommandations en matière de performances sur le portail Azure, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Accédez à **Tous les services** > **Bases de données SQL**, puis sélectionnez votre base de données.
3. Accédez à **Recommandation sur les performances** pour afficher les recommandations disponibles pour la base de données choisie.

Les recommandations en matière de performances s’affichent dans un tableau similaire à celui de la figure suivante :

![Recommandations](./media/sql-database-advisor-portal/recommendations.png)

Les recommandations sont triées en fonction de leur impact potentiel sur les performances dans les catégories suivantes :

| Impact | Description |
|:--- |:--- |
| Élevé |Les recommandations ayant un impact élevé fournissent l’impact le plus important sur les performances. |
| Moyenne |Les recommandations ayant un impact moyen améliorent les performances, mais pas de manière substantielle. |
| Faible |Les recommandations ayant un faible impact fournissent de meilleures performances, mais les améliorations ne sont toutefois pas significatives. |


> [!NOTE]
> Azure SQL Database a besoin surveiller les activités au moins pendant une journée afin d’identifier certaines recommandations. Azure SQL Database peut plus facilement optimiser des modèles de requête cohérents que des pics d’activité aléatoires. Si les recommandations ne sont pas disponibles actuellement, la page **Recommandation sur les performances** fournit un message explicatif.
> 

Vous pouvez également afficher l’état des opérations historiques. Sélectionnez une recommandation ou un état pour afficher plus d’informations.

Voici un exemple de recommandation « Créer un index » dans le portail Azure.

![Création d’index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Application des recommandations
Grâce à Azure SQL Database, vous pouvez contrôler totalement la façon dont les recommandations sont activées à l’aide de l’une des trois options suivantes : 

* Appliquer les recommandations individuelles une à la fois.
* Activer le réglage automatique pour appliquer automatiquement les recommandations.
* Pour implémenter une recommandation manuellement, exécutez le script T-SQL recommandé sur votre base de données.

Sélectionnez une recommandation pour afficher ses détails, puis cliquez sur **Afficher le script** pour savoir exactement comment la recommandation doit être créée.

La base de données reste en ligne tant que la recommandation est appliquée. L’utilisation de la recommandation en matière de performances ou du réglage automatique ne place jamais une base de données hors connexion.

### <a name="apply-an-individual-recommendation"></a>Appliquer une recommandation individuelle
Vous pouvez consulter et accepter les recommandations une à la fois.

1. Dans la page **Recommandations**, cliquez sur une recommandation.
2. Dans la page **Détails**, cliquez sur le bouton **Appliquer**.
   
    ![Appliquer une recommandation](./media/sql-database-advisor-portal/apply.png)

La recommandation sélectionnée est appliquée à la base de données.

### <a name="removing-recommendations-from-the-list"></a>Suppression de recommandations de la liste
Si votre liste de recommandations contient des éléments que vous souhaitez supprimer de la liste, vous pouvez ignorer la recommandation :

1. Sélectionnez une recommandation dans la liste des **Recommandations** pour afficher les informations.
2. Dans la page **Détails**, cliquez sur **Ignorer**.

Si vous le souhaitez, vous pouvez rajouter des éléments rejetés à la liste **Recommandations** :

1. Dans la page **Recommandations**, cliquez sur **Affichage ignoré**.
2. Sélectionnez un élément rejeté dans la liste pour afficher les détails le concernant.
3. Si vous le souhaitez, vous pouvez cliquer sur **Annuler le rejet** pour ajouter l’index à la liste principale de **Recommandations**.

> [!NOTE]
> Notez que, si le [paramétrage automatique](sql-database-automatic-tuning.md) de SQL Database est activé, et que vous avez manuellement ignoré l’une des recommandations de la liste, celle-ci ne s’appliquera jamais automatiquement. Il est pratique d’ignorer une recommandation pour activer le paramétrage automatique dans les cas où une recommandation en particulier ne doit pas s’appliquer.
> Vous pouvez rétablir l’ancien comportement en sélectionnant l’option Annuler l’action Ignorer pour ajouter les recommandations ignorées à la liste Recommandations.
> 

### <a name="enable-automatic-tuning"></a>Activer le réglage automatique
Vous pouvez configurer Azure SQL Database de manière à implémenter automatiquement des recommandations. Dès qu’une recommandation est disponible, elle est automatiquement appliquée. Comme pour toutes les recommandations gérées par le service, si l’impact sur les performances est négatif, la recommandation est annulée.

1. Dans la page **Recommandations**, cliquez sur **Automatiser**:
   
    ![Paramètres du conseiller](./media/sql-database-advisor-portal/settings.png)
2. Sélectionnez les actions à automatiser :
   
    ![Index recommandés](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Exécuter manuellement le script T-SQL recommandé
Sélectionnez une recommandation, puis cliquez sur **Afficher le script**. Exécutez ce script sur votre base de données pour appliquer la recommandation manuellement.

*Les index qui sont exécutés manuellement ne sont pas surveillés ni validés en ce qui concerne l’impact du service sur les performances*. Nous vous suggérons donc de surveiller ces index après leur création pour vérifier s’ils améliorent les performances et de les ajuster ou de les supprimer, si nécessaire. Pour plus d’informations sur la création d’index, consultez [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Annulation de recommandations
Les recommandations ayant l’état **En attente**, **En cours de vérification** ou **Réussite** peuvent être annulées. Les recommandations avec l'état **En cours d'exécution** ne peuvent pas être annulées.

1. Sélectionnez une recommandation dans la zone **Historique des réglages** pour ouvrir la page **Détails des recommandations**.
2. Cliquez sur **Annuler** pour abandonner le processus d'application de la recommandation.

## <a name="monitoring-operations"></a>Surveillance des opérations
L’application d’une recommandation ne se produit pas toujours instantanément. Le portail fournit des informations concernant l’état de la recommandation. Voici les états possibles des index :

| Statut | Description |
|:--- |:--- |
| Pending |La commande Appliquer la recommandation a été reçue et son exécution est planifiée. |
| En cours d'exécution |La recommandation est en cours d’application. |
| Vérification |La recommandation a été correctement appliquée et le service évalue les avantages. |
| Succès |La recommandation a été correctement appliquée et les avantages ont été évalués. |
| Error |Une erreur s’est produite au cours du processus d'application de la recommandation. Il peut s’agir d’un problème temporaire ou éventuellement d’un changement de schéma dans la table auquel cas le script n’est plus valide. |
| Annulation |La recommandation a été appliquée, mais a été jugée non performante et est automatiquement annulée. |
| Annulée |La recommandation a été annulée. |

Cliquez sur une recommandation in-process dans la liste pour afficher plus d’informations :

![Index recommandés](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Annulation d'une recommandation
Si vous avez utilisé les recommandations en matière de performances pour appliquer la recommandation (ce qui signifie que vous n’avez pas exécuté manuellement le script T-SQL), celle-ci annule automatiquement la modification s’il apparaît que son impact sur les performances est négatif. Si vous souhaitez simplement annuler une recommandation, vous pouvez procéder comme suit :

1. Sélectionnez une recommandation appliquée avec succès dans la zone **Historique de paramétrage** .
2. Dans la page **Détails de la recommandation**, cliquez sur **Annuler**.

![Index recommandés](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Analyse de l’impact des recommandations d’index sur les performances
Une fois les recommandations correctement implémentées (actuellement, seulement les opérations d’index et les recommandations de paramétrage des requêtes), vous pouvez cliquer sur **Informations sur la requête** dans la page Détails de la recommandation pour ouvrir [Query Performance Insight](sql-database-query-performance.md) et voir l’impact de vos principales requêtes sur les performances.

![Surveiller l’impact sur les performances](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Résumé
Azure SQL Database fournit des recommandations pour améliorer les performances des bases de données SQL. Les scripts T-SQL vous aident à optimiser votre base de données, avec à la clé une amélioration des performances des requêtes.

## <a name="next-steps"></a>Étapes suivantes
Surveillez vos recommandations et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. Azure SQL Database continue à surveiller et à fournir des recommandations pouvant potentiellement améliorer les performances de votre base de données. 

* Consultez [Réglage automatique](sql-database-automatic-tuning.md) pour en savoir plus sur le réglage automatique dans Azure SQL Database.
* Consultez [Recommandations en matière de performances](sql-database-advisor.md) pour obtenir une vue d’ensemble des recommandations relatives aux performances Azure SQL Database.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md)

