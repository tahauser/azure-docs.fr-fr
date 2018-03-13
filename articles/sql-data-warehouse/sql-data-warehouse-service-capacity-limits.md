---
title: "Limites de capacité SQL Data Warehouse | Microsoft Docs"
description: "Valeurs maximales pour les connexions, les bases de données, les tables et les requêtes pour SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: e1eac122-baee-4200-a2ed-f38bfa0f67ce
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: reference
ms.date: 12/14/2017
ms.author: kevin;barbkess
ms.openlocfilehash: 3a8edb3806f981ebb6f8c1ca6c994ae198df2ec2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2017
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limites de la capacité de SQL Data Warehouse
Les tableaux suivants présentent les valeurs maximales autorisées pour les différents composants d’Azure SQL Data Warehouse.

## <a name="workload-management"></a>Gestion des charges de travail
| Catégorie | Description | Maximale |
|:--- |:--- |:--- |
| [Data Warehouse Units (DWU)][Data Warehouse Units (DWU)] |DWU max pour un SQL Data Warehouse unique | [Niveau de performance](performance-tiers.md) Optimisé pour l’élasticité : DW6000<br></br>[Niveau de performance](performance-tiers.md) Optimisé pour le calcul : DW30000c |
| [Data Warehouse Units (DWU)][Data Warehouse Units (DWU)] |La valeur par défaut de DTU par serveur |54 000<br></br>Par défaut, le Quota de DTU de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 54 000, ce qui permet jusqu’à 6 000 DW. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en [créant un ticket de support][creating a support ticket] et en sélectionnant *Quota* en tant que type de demande.  Pour calculer vos besoins en matière de DTU, multipliez le nombre total de DWU nécessaire par 7,5, ou multipliez le nombre total de cDWU nécessaire par 9,0. Par exemple : <br></br>6 000 DW x 7,5 = 45 000 DTU<br></br>DW600c x 9,0 = 54 000 DTU.<br></br>Vous pouvez consulter votre consommation de DTU actuelle dans l’option SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. |
| Connexion de base de données |Sessions simultanées ouvertes |1 024<br/><br/>Chacune des 1024 sessions actives peut envoyer des requêtes à une base de données SQL Data Warehouse en même temps. Notez qu’il existe des limites sur le nombre de requêtes pouvant s’exécuter simultanément. En cas de dépassement d’une limite de concurrence, la demande est placée dans une file d’attente interne où elle attend d’être traitée. |
| Connexion de base de données |Mémoire maximale pour les instructions préparées |20 Mo |
| [Gestion des charges de travail][Workload management] |Nombre maximal de requêtes concurrentes |32<br/><br/> Par défaut, SQL Data Warehouse peut exécuter un maximum de 32 requêtes et files d’attente simultanées.<br/><br/>Le nombre de requêtes simultanées peut diminuer lorsque les utilisateurs sont affectés à des classes de ressources plus élevées ou lorsque SQL Data Warehouse a un [niveau de service](performance-tiers.md#service-levels) inférieur. Certaines requêtes, comme les requêtes DMV, sont toujours autorisées à s’exécuter. |
| [tempdb][Tempdb] |Go maximum |399 Go par DW100. Par conséquent, pour DWU1000, la taille de tempdb est 3,99 To |

## <a name="database-objects"></a>Objets de base de données
| Catégorie | Description | Maximale |
|:--- |:--- |:--- |
| Base de données |Taille maximale |240 To compressés sur disque<br/><br/>Cet espace est indépendant de tempdb ou de l’espace de journalisation. Par conséquent, cet espace est dédié aux tables permanentes.  La compression du cluster columnstore est estimée à 5 X.  Cette compression permet à la base de données d’atteindre un volume d’environ 1 Po lorsque toutes les tables sont en cluster columnstore (le type de table par défaut). |
| Table |Taille maximale |60 To compressés sur disque |
| Table |Tables par base de données |2 milliards |
| Table |Colonnes par table |1 024 colonnes |
| Table |Octets par colonne |Dépend de la colonne [type de données][data type].  La limite est de 8 000 pour les types de données Char, de 4 000 pour nvarchar ou 2 Go pour les types de données MAX. |
| Table |Octets par ligne, taille définie |8060 octets<br/><br/>Le nombre d’octets par ligne est calculé de la même manière que pour SQL Server avec la compression de page. Comme SQL Server, SQL Data Warehouse prend en charge le stockage de dépassement de ligne qui permet d’envoyer les **colonnes de longueur variable** hors ligne. Lorsque des lignes de longueur variable sont envoyées hors ligne, seule une racine de 24 octets est stockée dans l’enregistrement principal. Pour plus d’informations, consultez [Données de dépassement de ligne de plus de 8 Ko][Row-Overflow Data Exceeding 8 KB]. |
| Table |Partitions par table |15 000<br/><br/>Pour des performances élevées, nous vous recommandons de réduire au minimum le nombre de partitions nécessaires tout en prenant quand même en charge les besoins de votre entreprise. À mesure que le nombre de partitions augmente, la charge pour les opérations Langage de définition de données (DDL) et Langage de manipulation de données (DML) augmente et ralentit les performances. |
| Table |Caractères par valeur limite de partition. |4000 |
| Index |Index non-cluster par table. |999<br/><br/>Applicable uniquement aux tables de stockage de lignes. |
| Index |Index cluster par table. |1<br><br/>Applicable à la fois aux tables de stockage de lignes et de stockage de colonnes. |
| Index |Taille de la clé d’index. |900 octets.<br/><br/>Applicable aux index de stockage de lignes uniquement.<br/><br/>Il est possible de créer des index sur des colonnes varchar d’une taille maximale de plus de 900 octets si les données existantes dans les colonnes n’excèdent pas 900 octets quand l’index est créé. Toutefois, les actions INSERT ou UPDATE ultérieures sur les colonnes, qui amènent la taille totale à dépasser 900 octets, échouent. |
| Index |Colonnes clés par index. |16<br/><br/>Applicable aux index de stockage de lignes uniquement. Les index de stockage de colonnes cluster incluent toutes les colonnes. |
| Statistiques |Taille des valeurs de colonnes combinées. |900 octets. |
| Statistiques |Colonnes par objet de statistiques. |32 |
| Statistiques |Statistiques créées sur les colonnes par table. |30 000 |
| Procédures stockées |Nombre maximal de niveaux d’imbrication. |8 |
| Affichage |Colonnes par vue |1 024 |

## <a name="loads"></a>Charges
| Catégorie | Description | Maximale |
|:--- |:--- |:--- |
| Charges Polybase |60 Mo par ligne |1<br/><br/>Polybase charge uniquement les lignes de moins de 1 Mo et ne peuvent pas charger vers VARCHAR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX).<br/><br/> |

## <a name="queries"></a>Requêtes
| Catégorie | Description | Maximale |
|:--- |:--- |:--- |
| Requête |Requêtes mises en file d’attente sur les tables utilisateur. |1 000 |
| Requête |Requêtes simultanées sur les vues système. |100 |
| Requête |Requêtes mises en file d’attente sur les vues système |1 000 |
| Requête |Nombre maximal de paramètres |2 098 |
| Batch |Taille maximale |65 536*4 096 |
| Résultats SELECT |Colonnes par ligne |4096<br/><br/>Une ligne ne peut pas contenir plus de 4 096 colonnes dans le résultat SELECT. Le nombre de 4 096 colonnes n’est pas toujours garanti. Si le plan de requête exige une table temporaire, le maximum de 1 024 colonnes par table peut s’appliquer. |
| SELECT |Sous-requêtes imbriquées |32<br/><br/>Un instruction SELECT ne peut pas contenir plus de 32 sous-requêtes imbriquées. Le nombre de 32 sous-requêtes n’est pas toujours garanti. Par exemple, une instruction JOIN peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut aussi être limité par la mémoire disponible. |
| SELECT |Colonnes par JOIN |1 024 colonnes<br/><br/>L’instruction JOIN ne peut pas contenir plus de 1 024 colonnes. Le nombre de 1024 colonnes n’est pas toujours garanti. Si le plan JOIN exige une table temporaire avec davantage de colonnes que le résultat JOIN, la limite de 1 024 s’applique à la table temporaire. |
| SELECT |Octets par colonnes GROUP BY. |8 060<br/><br/>Les colonnes incluses dans la clause GROUP BY peuvent comporter un maximum de 8 060 octets. |
| SELECT |Octets par colonnes ORDER BY |8 060 octets.<br/><br/>Les colonnes incluses dans la clause ORDER BY peuvent comporter un maximum de 8 060 octets. |
| Identificateurs et constantes par instruction |Nombre d’identificateurs et constantes référencés. |65 535<br/><br/>SQL Data Warehouse limite le nombre d’identificateurs et de constantes pouvant être contenus dans une seule expression d’une requête. Cette limite s’élève à 65 535. Le dépassement de ce nombre génère l’erreur SQL Server 8632. Pour plus d’informations, consultez [Erreur interne : une limite des services d’expression est dépassée][Internal error: An expression services limit has been reached]. |

## <a name="metadata"></a>Métadonnées
| Vue système | Nombre maximal de lignes |
|:--- |:--- |
| Sys.dm_pdw_component_health_alerts |10 000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |Nombre total de rôles de travail DMS pour les 1 000 dernières demandes SQL. |
| sys.dm_pdw_errors |10 000 |
| sys.dm_pdw_exec_requests |10 000 |
| sys.dm_pdw_exec_sessions |10 000 |
| sys.dm_pdw_request_steps |Nombre total d’étapes pour les 1 000 dernières demandes SQL stockées dans sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10 000 |
| sys.dm_pdw_sql_requests |Les 1 000 dernières demandes SQL stockées dans sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>étapes suivantes
Pour plus d’informations, consultez la rubrique [Vue d’ensemble de référence de SQL Data Warehouse][SQL Data Warehouse reference overview].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md
[SQL Data Warehouse reference overview]: ./sql-data-warehouse-overview-reference.md
[Workload management]: ./sql-data-warehouse-develop-concurrency.md
[Tempdb]: ./sql-data-warehouse-tables-temporary.md
[data type]: ./sql-data-warehouse-tables-data-types.md
[creating a support ticket]: /sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->
[Row-Overflow Data Exceeding 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[Internal error: An expression services limit has been reached]: https://support.microsoft.com/kb/913050
