---
title: "En quoi consiste Azure SQL Data Warehouse ? | Microsoft Docs"
description: "Base de données distribuée dédiée aux entreprises qui prend en charge le traitement de pétaoctets de données relationnelles et non relationnelles. Il s’agit du premier entrepôt de données cloud prenant en charge l’augmentation, la réduction et la pause en quelques secondes."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jenniehubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj
ms.openlocfilehash: 7fd17c29c413905084c6b3736018d111d3b28fea
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>En quoi consiste Azure SQL Data Warehouse ?

SQL Data Warehouse est un entrepôt de données d’entreprise basé sur le cloud qui utilise le traitement massivement parallèle pour exécuter rapidement des requêtes complexes parmi des pétaoctets de données. Utilisez SQL Data Warehouse comme composant clé d’une solution Big Data. Importez des données volumineuses dans SQL Data Warehouse avec des requêtes T-SQL PolyBase simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances. À mesure de vos intégrations et analyses, l’entrepôt de données deviendra la seule source pertinente sur laquelle votre activité peut compter pour obtenir des informations précises.  


## <a name="key-component-of-big-data-solution"></a>Composant clé de la solution Big Data
SQL Data Warehouse est un composant clé d’une solution Big Data complète dans le cloud.

![Solution d’entrepôt de données](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Dans une solution de données cloud, les données sont ingérées dans des magasins de données volumineuses à partir de diverses sources. Une fois dans un magasin de données volumineuses, des algorithmes d’apprentissage de données, Spark et Hadoop préparent et forment les données. Lorsque les données sont prêtes pour l’analyse complexe, SQL Data Warehouse utilise PolyBase pour interroger les magasins de données volumineuses. PolyBase utilise des requêtes T-SQL standard pour rassembler les données dans SQL Data Warehouse.
 
SQL Data Warehouse stocke les données dans des tables relationnelles avec stockage en colonnes. Ce format réduit considérablement le coût de stockage des données et améliore les performances de requête. Une fois que les données sont stockées dans SQL Data Warehouse, vous pouvez exécuter des analyses à très grande échelle. Par rapport aux systèmes de base de données classiques, les requêtes d’analyses se terminent en quelques secondes plutôt qu’en quelques minutes, ou en quelques heures plutôt qu’en quelques jours. 

Les résultats d’analyse peuvent s’étendre aux applications ou bases de données de rapports mondiales. Les analystes commerciaux peuvent alors obtenir des informations pour prendre des décisions éclairées pour l’activité.

## <a name="optimization-choices"></a>Choix d’optimisation

SQL Data Warehouse propose [des niveaux de performance](performance-tiers.md) conçus pour vous offrir de la flexibilité et répondre à vos besoins en termes de données, qu’elles soient volumineuses ou non. Vous pouvez choisir un entrepôt de données optimisé pour l’élasticité ou pour le calcul. 

- Le **niveau de performance Optimisé pour l’élasticité** sépare les couches de calcul et de stockage dans l’architecture. Cette option convient parfaitement à des charges de travail qui peuvent exploiter au maximum la séparation entre le calcul et le stockage en effectuant des mises à l’échelle régulières pour prendre en charge de brèves périodes de pic d’activité. Ce niveau de calcul est proposé à un prix très attractif, et peut être mis à l’échelle pour prendre en charge la majorité des charges de travail des clients.

- Le **niveau de performance Optimisé pour le calcul** utilise le matériel Azure le plus récent pour introduire un nouveau cache de disque SSD NVMe qui garde les données les plus fréquemment consultées à proximité des processeurs, précisément là où vous souhaitez qu’elles se trouvent. En hiérarchisant automatiquement le stockage, ce niveau de performance correspond parfaitement aux requêtes complexes, puisque toutes les E/S sont gardées en local au niveau de la couche de calcul. En outre, le columnstore a été amélioré pour stocker un nombre illimité de données dans votre SQL Data Warehouse. Le niveau de performance Optimisé pour le calcul fournit le plus haut niveau d’extensibilité pour vous permettre de mettre à l’échelle jusqu’à 30 000 cDWU. Choisissez ce niveau pour les charges de travail nécessitant des performances exceptionnelles en continu.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un SQL Data Warehouse][create a SQL Data Warehouse] et [charger des exemples de données][load sample data]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

* [Témoignages de clients]
* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe de conseil clientèle]
* [Création d’un ticket de support]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Création d’un ticket de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Témoignages de clients]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
