---
title: Événement étendus dans SQL Database | Microsoft Docs
description: Décrit les événements étendus (XEvents) dans la base de données SQL Azure et les différences entre les sessions d’événements dans la base de données SQL Azure et dans Microsoft SQL Server.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: dc4660b33d8df28fd59929838e054f6703c48107
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="extended-events-in-sql-database"></a>Événement étendus dans la base de données SQL
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Cette rubrique explique les quelques différences entre l’implémentation d’événements étendus dans la base de données SQL Azure et dans Microsoft SQL Server.

- La base de données SQL V12 a intégré la fonctionnalité d’événements étendus au cours de la seconde moitié du calendrier 2015.
- Cette fonctionnalité est présente dans SQL Server depuis 2008.
- L’ensemble de fonctionnalités des événements étendus sur la base de données SQL est un sous-ensemble robuste des fonctionnalités de SQL Server.

*XEvents* est un surnom informel parfois utilisé pour désigne les « événements étendus » dans les blogs et autres emplacements informels.

Des informations complémentaires sur les événements étendus, pour Base de données SQL Azure et Microsoft SQL Server, sont disponibles dans :

- [Démarrage rapide : Événements étendus dans SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Événements étendus](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Prérequis


Cette rubrique part du principe que vous connaissez déjà les éléments suivants :

- [Service Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Événements étendus](http://msdn.microsoft.com/library/bb630282.aspx) dans Microsoft SQL Server.

- La majeure partie de notre documentation sur les événements étendus s’applique à SQL Server et au service Base de données SQL.

Il est utile d’avoir une connaissance préalable des éléments suivants lorsque vous choisissez le fichier d’événements comme [cible](#AzureXEventsTargets):

- [Service Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Utilisation d’Azure PowerShell avec Azure Storage](../storage/common/storage-powershell-guide-full.md) - Cette rubrique fournit des informations complètes sur PowerShell et le service Azure Storage.

## <a name="code-samples"></a>Exemples de code

Des rubriques connexes fournissent deux exemples de code :


- [Code cible de la mémoire tampon en anneau pour les événements étendus dans la base de données SQL](sql-database-xevent-code-ring-buffer.md)
    - Script Transact-SQL simple court.
    - Dans la rubrique concernant l’exemple de code, nous insistons sur le fait que, lorsque vous avez fini de traiter une cible de la mémoire tampon en anneau, vous devez en libérer les ressources en exécutant une instruction `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` alter-drop. Vous pouvez ensuite ajouter une autre instance de mémoire tampon en anneau avec l’instruction `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Code cible du fichier d’événements pour les événements étendus dans la base de données SQL](sql-database-xevent-code-event-file.md)
    - La Phase 1 est PowerShell pour créer un conteneur Azure Storage.
    - La Phase 2 est Transact-SQL qui utilise le conteneur Azure Storage.

## <a name="transact-sql-differences"></a>Différences Transact-SQL


- Lorsque vous exécutez la commande [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx) sur SQL Server, vous pouvez utiliser la clause **ON SERVER** . Mais sur la base de données SQL vous utilisez la clause **ON DATABASE** à la place.


- La clause **ON DATABASE** s’applique également aux commandes Transact-SQL [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) et [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx).


- Il est recommandé d’inclure l’option de session d’événement de **STARTUP_STATE = ON** dans vos instructions **CREATE EVENT SESSION** ou **ALTER EVENT SESSION**.
    - La valeur **= ON** prend en charge le redémarrage automatique après la reconfiguration de la base de données logique en raison d’un basculement.

## <a name="new-catalog-views"></a>Nouvelles vues catalogue

La fonctionnalité des événements étendus est prise en charge par plusieurs [vues catalogue](http://msdn.microsoft.com/library/ms174365.aspx). Les vues catalogue vous indiquent les *métadonnées ou définitions* des sessions d’événements créées par l’utilisateur dans la base de données actuelle. Les affichages ne renvoient pas d’informations sur les instances des sessions d’événements actives.

| Nom de<br/>la vue catalogue | Description |
|:--- |:--- |
| **sys.database_event_session_actions** |Renvoie une ligne pour chaque action sur chaque événement d’une session d’événements. |
| **sys.database_event_session_events** |Renvoie une ligne pour chaque événement d’une session d’événements. |
| **sys.database_event_session_fields** |Renvoie une ligne pour chaque colonne pouvant être personnalisée définie explicitement sur les événements et les cibles. |
| **sys.database_event_session_targets** |Renvoie une ligne pour chaque cible d’événement pour une session d’événements. |
| **sys.database_event_sessions** |Renvoie une ligne pour chaque session d’événements dans la base de données SQL. |

Dans Microsoft SQL Server, les noms des vues catalogue similaires contiennent *.server\_* au lieu de *.database\_*. Les noms suivent le modèle **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Nouvelles vues de gestion dynamique [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx)

La base de données SQL Azure comporte des [vues de gestion dynamique (DMV)](http://msdn.microsoft.com/library/bb677293.aspx) qui prennent en charge les événements étendus. Les vues de gestion dynamique vous renseignent sur les sessions d’événements *actives* .

| Nom de la vue de gestion dynamique | Description |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Renvoie des informations sur les actions d’une session d’événements. |
| **sys.dm_xe_database_session_events** |Renvoie des informations sur les sessions d’événements. |
| **sys.dm_xe_database_session_object_columns** |Montre les valeurs de configuration pour les objets liés à une session. |
| **sys.dm_xe_database_session_targets** |Renvoie des informations sur les cibles d’une session d’événements. |
| **sys.dm_xe_database_sessions** |Renvoie une ligne pour chaque session d’événements incluse dans la base de données actuelle. |

Dans Microsoft SQL Server, les noms des vues catalogue similaires ne contiennent pas la partie *\_database*, par exemple :

- **sys.dm_xe_sessions**, au lieu de name<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Vues de gestion dynamique communes aux deux produits
Pour les événements étendus, il existe des vues de gestion dynamique supplémentaires qui sont communes à la base de données SQL Azure et Microsoft SQL Server :

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Rechercher les actions, cibles et événements étendus disponibles

Vous pouvez exécuter une simple instruction SQL **SELECT** pour obtenir une liste des événements, actions et cibles disponibles.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Cibles de vos sessions d’événements de Base de données SQL

Voici les cibles capables de capturer des résultats à partir de vos sessions d’événements sur la base de données SQL :

- [Cible de mémoire tampon en anneau](http://msdn.microsoft.com/library/ff878182.aspx) -Maintient brièvement les données d’événements en mémoire.
- [Cible de compteur d’événements](http://msdn.microsoft.com/library/ff878025.aspx) - Compte tous les événements qui se produisent pendant une session d’événements étendus.
- [Cible du fichier d’événements](http://msdn.microsoft.com/library/ff878115.aspx) - Écrit les mémoires tampons complètes dans un conteneur Azure Storage.

L’API [Suivi d’événements pour Windows](http://msdn.microsoft.com/library/ms751538.aspx) n’est pas disponible pour les événements étendus sur la base de données SQL.

## <a name="restrictions"></a>Restrictions

Il existe certaines différences liées à la sécurité qui conviennent à l’environnement cloud de Base de données SQL :

- Les événements étendus sont fondés sur le modèle d’isolement à locataire unique. Une session d’événements dans une base de données ne peut pas accéder aux données ou événements d’une autre base de données.
- Vous ne pouvez pas émettre une instruction **CREATE EVENT SESSION** dans le contexte de la base de données **master**.

## <a name="permission-model"></a>Modèle d’autorisation

Vous devez disposer de l’autorisation **Contrôle** sur la base de données pour émettre une instruction **CREATE EVENT SESSION**. Le propriétaire de la base de données (dbo) possède l’autorisation **Contrôle** .

### <a name="storage-container-authorizations"></a>Autorisations de conteneur de stockage

Le jeton SAP que vous générez pour votre conteneur Azure Storage doit spécifier **rwl** pour les autorisations. La valeur **rwl** fournit les autorisations suivantes :

- Lire
- Écrire
- Liste

## <a name="performance-considerations"></a>Considérations relatives aux performances

Il existe des scénarios où l’utilisation intensive des événements étendus peut accumuler plus de mémoire active qu’il n’en faut pour l’intégrité de l’ensemble du système. Par conséquent, le système de la base de données SQL Azure définit et ajuste dynamiquement les limites de quantité de mémoire active pouvant être accumulée par une session d’événements. De nombreux facteurs entrent dans le calcul dynamique.

Si vous recevez un message d’erreur indiquant qu’une quantité maximale de mémoire a été appliquée, vous pouvez envisager certaines actions correctives, notamment :

- Exécuter moins de sessions d’événement simultanées.
- À l’aide des instructions **CREATE** et **ALTER** pour les sessions d’événements, réduisez la quantité de mémoire spécifiée dans la clause **MAX\_MEMORY**.

### <a name="network-latency"></a>Latence du réseau

La cible **Fichier d’événement** peut rencontrer une latence ou des problèmes de réseau lors de la persistance de données sur des objets blob Azure Storage. D’autres événements de la base de données SQL peuvent être retardés en attendant la fin de la communication réseau. Ce délai peut ralentir votre charge de travail.

- Pour atténuer ce risque de baisse des performances, évitez de définir l’option **EVENT_RETENTION_MODE** sur **NO_EVENT_LOSS** dans vos définitions de session d’événements.

## <a name="related-links"></a>Liens connexes

- [Utilisation d’Azure PowerShell avec Azure Storage](../storage/common/storage-powershell-guide-full.md)
- [Applets de commande Azure Storage](http://msdn.microsoft.com/library/dn806401.aspx)
- [Utilisation d’Azure PowerShell avec Azure Storage](../storage/common/storage-powershell-guide-full.md) - Cette rubrique fournit des informations complètes sur PowerShell et le service Azure Storage.
- [Utilisation du stockage d’objets blob à partir de .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Billets de blog de Jonathan Kehayias sur les événements étendus dans Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- La page web *Mises à jour de service* Azure, restreinte par paramètre à Azure SQL Database :
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Vous trouverez d’autres rubriques d’exemples de code pour les événements étendus en suivant le lien ci-dessous. Toutefois, vous devez vérifier régulièrement les exemples pour voir s’ils ciblent Microsoft SQL Server ou la base de données SQL Azure. Vous pouvez ensuite déterminer si vous devez apporter quelques modifications mineures avant d’exécuter un exemple.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
