---
title: Azure SQL Data Sync (Préversion) | Microsoft Docs
description: Cette vue d’ensemble présente Azure SQL Data Sync (Préversion).
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 5abe3e5a56dfca263f109b86f473ac490da3eac7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>Synchroniser des données sur plusieurs bases de données cloud et locales avec SQL Data Sync (préversion)

SQL Data Sync est un service conçu sur la base de données SQL Azure qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données SQL et instances SQL Server.

Data Sync est basé sur le concept d’un groupe de synchronisation. Un groupe de synchronisation est un groupe de bases de données que vous souhaitez synchroniser.

Un groupe de synchronisation dispose des propriétés suivantes :

-   Le **schéma de synchronisation** décrit quelles sont les données en cours de synchronisation.

-   Le **sens de synchronisation** peut être bidirectionnel ou peut circuler dans une seule direction. Autrement dit, le sens de synchronisation peut être *Hub vers membre*, *Membre vers hub*, ou les deux.

-   L’**intervalle de synchronisation** correspond à la fréquence à laquelle la synchronisation se produit.

-   La **stratégie de résolution de conflit** est une stratégie au niveau groupe, qui peut être *Priorité au hub* ou *Priorité au membre*.

Data Sync utilise une topologie hub and spoke pour synchroniser les données. Vous définissez l’une des bases de données du groupe en tant que base de données Hub. Le reste des bases de données sont des bases de données membres. La synchronisation se produit uniquement entre le hub et des membres individuels.
-   Le **base de données Hub** doit être une base de données Azure SQL Database.
-   Les **bases de données membres** peuvent être des bases de données SQL, des bases de données locales SQL Server, ou des instances SQL Server sur des machines virtuelles Azure.
-   La **base de données de synchronisation** contient les métadonnées et le journal de Data Sync. La base de données de synchronisation doit être une base de données Azure SQL Database située dans la même région que la base de données Hub. La base de données de synchronisation est créée par le client et lui appartient.

> [!NOTE]
> Si vous utilisez une base de données locale, vous devez [configurer un agent local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchroniser des données entre des bases de données](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Quand utiliser Data Sync

Data Sync est utile dans les cas où les données doivent être mises à jour entre plusieurs bases de données Azure SQL Database ou bases de données SQL Server. Voici les principaux cas d’usage pour Data Sync :

-   **Synchronisation de données hybride :** avec Data Sync, vous pouvez maintenir la synchronisation de données entre vos bases de données locales et les bases de données SQL Azure pour activer des applications hybrides. Cette fonctionnalité peut intéresser les clients qui envisagent de passer au cloud et souhaiteraient placer une partie de leur application dans Azure.

-   **Applications distribuées :** dans de nombreux cas, il est recommandé de séparer les différentes charges de travail entre plusieurs bases de données. Par exemple, si vous possédez une base de données de production de grande taille, mais que vous devez également exécuter un rapport ou une charge de travail analytique de ces données, il est utile de disposer d’une seconde base de données pour cette charge de travail supplémentaire. Cette approche réduit l’impact sur les performances de votre charge de travail de production. Vous pouvez utiliser Data Sync afin de maintenir la synchronisation de ces deux bases de données.

-   **Applications distribuées globalement :** De nombreuses entreprises sont présentes dans plusieurs régions et même dans plusieurs pays. Afin de réduire la latence du réseau, il est préférable de conserver vos données dans une région proche. Avec Data Sync, vous pouvez facilement synchroniser des bases de données dans différentes régions partout dans le monde.

Data Sync ne convient pas aux scénarios suivants :

-   Récupération d’urgence

-   Mise à l’échelle en lecture

-   ETL (OLTP vers OLAP)

-   Migration du serveur local SQL vers la base de données Azure SQL Database

## <a name="how-does-data-sync-work"></a>Comment fonctionne Data Sync ? 

-   **Suivi des modifications de données :** Data Sync effectue le suivi des modifications en utilisant des déclencheurs d’insertion, de mise à jour et de suppression. Les modifications sont enregistrées dans une table latérale dans la base de données utilisateur.

-   **Synchronisation des données :** Data Sync est conçu dans un modèle de Hub and Spoke. Le hub se synchronise avec chaque membre individuellement. Les modifications depuis le hub sont téléchargées vers le membre, puis les modifications à partir du membre sont téléchargées vers le hub.

-   **Résolution des conflits :** Data Sync fournit deux options pour la résolution de conflit, *Priorité au hub* ou *Priorité au membre*.
    -   Si vous sélectionnez *Priorité au hub*, les modifications dans le hub remplacent toujours les modifications dans le membre.
    -   Si vous sélectionnez *Priorité au membre*, les modifications dans le membre remplacent toujours les modifications dans le hub. S’il existe plusieurs membres, la valeur finale dépend du membre qui se synchronise en premier.

## <a name="sync-req-lim"></a> Spécifications et limitations

### <a name="general-considerations"></a>Considérations d’ordre général

#### <a name="eventual-consistency"></a>Cohérence éventuelle
Étant donné que Data Sync est basé sur le déclencheur, la cohérence transactionnelle n’est pas garantie. Microsoft garantit que toutes les modifications sont effectuées par la suite et que Data Sync n’entraîne pas de perte de données.

#### <a name="performance-impact"></a>Impact sur les performances
Data Sync utilise des déclencheurs d’insertion, de mise à jour et de suppression pour effectuer le suivi des modifications. Cela crée des tables latérales dans la base de données utilisateur pour le suivi des modifications. Ces activités de suivi des modifications ont un impact sur votre charge de travail de base de données. Évaluez votre niveau de service et effectuez une mise à niveau si nécessaire.

### <a name="general-requirements"></a>Conditions générales

-   Chaque table doit avoir une clé primaire. Ne modifiez pas la valeur de la clé primaire dans une ligne. Si vous avez à le faire, supprimez la ligne et recréez-la avec la nouvelle valeur de clé primaire. 

-   L’isolement de capture instantanée doit être activé. Pour plus d’informations, consultez [Isolement de capture instantanée dans SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitations générales

-   Une table ne peut pas avoir une colonne d’identité qui n’est pas la clé primaire.

-   Les noms des objets (bases de données, tables et colonnes) ne peuvent pas contenir les caractères imprimables suivants : point (.), crochet gauche ou crochet droit (]).

-   L’authentification Azure Active Directory n’est pas prise en charge.

#### <a name="unsupported-data-types"></a>Types de données non pris en charge

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (prise en charge de XML)

-   Curseur, horodateur, hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitations des dimensions de la base de données et du service

| **Dimensions**                                                      | **Limite**              | **Solution de contournement**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Nombre maximal de groupes de synchronisation auquel peut appartenir une base de données.       | 5.                      |                             |
| Nombre maximal de points de terminaison dans un seul groupe de synchronisation              | 30                     | Créer plusieurs groupes de synchronisation |
| Nombre maximal de points de terminaison locaux dans un seul groupe de synchronisation. | 5.                      | Créer plusieurs groupes de synchronisation |
| Noms de la base de données, de la table, du schéma et des colonnes                       | 50 caractères par nom |                             |
| Tables dans un groupe de synchronisation                                          | 500                    | Créer plusieurs groupes de synchronisation |
| Colonnes d’une table dans un groupe de synchronisation                              | 1 000                   |                             |
| Taille de ligne de données sur une table                                        | 24 Mo                  |                             |
| Intervalle de synchronisation minimale                                           | 5 minutes              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>FAQ sur SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>Combien coûte le service de synchronisation des données SQL Data Sync (préversion) ?

L’utilisation de la préversion de SQL Data Sync n’engendre aucun coût lié au service proprement dit.  Toutefois, les frais de transfert de données pour le déplacement des données vers et depuis votre instance SQL Database vous seront facturés. Pour en savoir plus, voir [Tarification de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Quelles régions prennent en charge la synchronisation des données ?

SQL Data Sync (préversion) est disponible dans toutes les régions de cloud public.

### <a name="is-a-sql-database-account-required"></a>Un compte SQL Database est-il requis ? 

Oui. Vous devez avoir un compte SQL Database pour héberger la base de données Hub.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Puis-je utiliser Data Sync pour effectuer une synchronisation entre les bases de données SQL Server locales uniquement ? 
Pas directement. Vous pouvez effectuer une synchronisation entre les bases de données SQL Server locales de façon indirecte, en créant une base de données Hub dans Azure, puis en ajoutant les bases de données locales au groupe de synchronisation.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>Puis-je utiliser Data Sync pour envoyer des données de ma base de données de production vers une base de données vide, et garder mes données synchronisées ? 
Oui. Créez manuellement le schéma dans la nouvelle base de données en créant le script à partir de la base de données d’origine. Après avoir créé le schéma, ajoutez les tables à un groupe de synchronisation pour copier les données et les garder synchronisées.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Dois-je utiliser SQL Data Sync pour sauvegarder et restaurer mes bases de données ?

Il est déconseillé d’utiliser SQL Data Sync (préversion) pour créer une sauvegarde de vos données. Vous ne pouvez pas sauvegarder et restaurer à un point précis dans le temps, car les synchronisations de SQL Data Sync (version préliminaire) ne sont pas affectées à des versions. Par ailleurs, SQL Data Sync (préversion) ne sauvegarde pas d’autres objets SQL, notamment les procédures stockées, et n’effectue pas rapidement l’équivalent d’une opération de restauration.

Consultez [Copie d'une base de données SQL Azure](sql-database-copy.md) pour prendre connaissance d’une technique de sauvegarde recommandée.

### <a name="is-collation-supported-in-sql-data-sync"></a>Le classement est-il pris en charge dans SQL Data Sync ?

Oui. SQL Data Sync prend en charge le classement dans les scénarios suivants :

-   Si les tables du schéma de synchronisation sélectionné ne sont pas encore dans votre Hub ou dans les bases de données membres, lorsque vous déployez le groupe de synchronisation, le service crée automatiquement les tables et colonnes correspondantes avec les paramètres de classement sélectionnés dans les bases de données de destination vides.

-   Si les tables à synchroniser existent déjà dans votre Hub ou vos bases de données membres, SQL Data Sync requiert que les colonnes clés primaires aient le même classement dans le Hub que dans les bases de données membres pour déployer avec succès le groupe de synchronisation. Il n’existe aucune restriction de classement sur les colonnes autres que les colonnes clés primaires.

### <a name="is-federation-supported-in-sql-data-sync"></a>La fédération est-elle prise en charge dans SQL Data Sync ?

La base de données racine de fédération peut être utilisée sans limitation dans SQL Data Sync (préversion). Vous ne pouvez pas ajouter de point de terminaison de base de données fédérée à la version actuelle de SQL Data Sync (préversion).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur SQL Data Sync, consultez :

-   [Configurer Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Bonnes pratiques pour Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Surveiller Azure SQL Data Sync avec OMS Log Analytics](sql-database-sync-monitor-oms.md)
-   [Résoudre les problèmes liés à Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Exemples PowerShell complets qui montrent comment configurer SQL Data Sync :
    -   [Utilisez PowerShell pour la synchronisation entre plusieurs bases de données SQL Azure](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Utiliser PowerShell pour la synchronisation entre une base de données SQL Azure et une base de données locale SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Télécharger la documentation de l’API REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Pour plus d’informations sur SQL Database, consultez :

-   [Vue d’ensemble des bases de données SQL](sql-database-technical-overview.md)
-   [Gestion du cycle de vie des bases de données](https://msdn.microsoft.com/library/jj907294.aspx)
