---
title: "Gestion de bases de données dans Azure SQL Data Warehouse | Microsoft Docs"
description: "Vue d’ensemble de la gestion des bases de données SQL Data Warehouse. Inclut des outils de gestion, des unités DWU et une montée en puissance des performances, une résolution des problèmes de performances des requêtes, la mise en œuvre de stratégies de sécurité adaptées et la restauration d’une base de données en cas d’altération des données ou de panne au niveau régional."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: acf521bdc15dfab4c7e43081159bc1385768838e
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gestion de bases de données dans Azure SQL Data Warehouse
SQL Data Warehouse automatise de nombreux aspects de la gestion de vos bases de données. Par exemple, pour mettre les performances à l’échelle, vous n’avez qu’à ajuster le niveau de ressources de calcul en fonction de vos besoins et ne payer que pour ces ressources. SQL Data Warehouse effectue ensuite tout le travail de montée en charge et de réduction.

Vous devez sans aucun doute surveiller votre charge de travail pour identifier vos besoins en matière de performances, ainsi que pour résoudre des requêtes à long terme. Vous devez également effectuer quelques tâches de sécurité pour gérer les autorisations pour les utilisateurs et les connexions.

Cette présentation couvre ces aspects de la gestion de SQL Data Warehouse.

* Outils de gestion
* Mise à l’échelle des ressources de calcul
* Suspension et reprise
* Meilleures pratiques pour les performances
* Surveillance des requêtes
* Sécurité
* Sauvegarder et restaurer des données

## <a name="management-tools"></a>Outils de gestion
Vous pouvez utiliser divers outils pour gérer des bases de données dans SQL Data Warehouse. Au cours de vos travaux de gestion de bases de données, vous développerez des préférences quant aux outils pour chaque type de tâche que vous devez effectuer.

### <a name="azure-portal"></a>Portail Azure
Le [portail Azure][Azure portal] est un portail basé sur le Web dans lequel vous pouvez créer, mettre à jour et supprimer des bases de données et surveiller des ressources de base de données. Cet outil est très utile si vous n’êtes pas familiarisé avec Microsoft Azure, si vous gérez un petit nombre d’entrepôts de données, ou si vous devez effectuer rapidement une action.

Pour la prise en main du portail Azure, consultez la rubrique [Créer un entrepôt de données SQL (portail Azure)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools dans Visual Studio
[SQL Server Data Tools][SQL Server Data Tools] (SSDT) dans Visual Studio vous permet de vous connecter à vos bases de données, de les gérer et de les développer. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), essayez la fonction SSDT de Visual Studio.

SSDT comprend l’Explorateur d’objets SQL Server qui vous permet de visualiser, de connecter et d’exécuter des scripts dans des bases de données SQL Data Warehouse. Pour vous connecter rapidement à SQL Data Warehouse, il vous suffit de cliquer sur le bouton **Ouvrir dans Visual Studio** de la barre de commandes lorsque vous affichez les détails de la base de données dans le portail Azure.  

Pour prendre SSDT en main dans Visual Studio, consultez [Interroger Azure SQL Data Warehouse avec Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Outils de ligne de commande
Les outils en ligne de commande sont la solution idéale pour l’automatisation de vos charges de travail.  PowerShell et sqlcmd sont également deux manières intéressantes d’automatiser vos processus.  Nous vous recommandons d’utiliser ces outils pour gérer un grand nombre de serveurs logiques et pour déployer des modifications de ressources au sein d’un environnement de production, dans la mesure où les tâches requises peuvent alors être incluses dans un script, puis automatisées.

### <a name="dynamic-management-views"></a>Vues de gestion dynamique (DMV)
Les vues de gestion dynamique sont indispensables pour la gestion du SQL Data Warehouse. Presque toutes les informations figurant dans le portail sont basées sur les vues de gestion dynamique. Pour afficher une liste des vues de gestion dynamique SQL Data Warehouse, consultez les [Vues système SQL Data Warehouse][SQL Data Warehouse system views].

Pour commencer, consultez les rubriques [Se connecter et lancer des requêtes avec SQLCMD][Connect and query with sqlcmd] et [Créer une base de données (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul
Dans SQL Data Warehouse, vous pouvez rapidement mettre les performances à l’échelle en augmentant ou diminuant les ressources de calcul du processeur, de la mémoire et de la bande passante d’E/S. Pour mettre les performances à l’échelle, vous devez simplement ajuster le nombre de data warehouse units (DWU) que SQL Data Warehouse alloue à votre base de données. SQL Data Warehouse apporte les modifications rapidement et traite toutes les modifications matérielles ou logicielles sous-jacentes.

Pour plus d’informations sur la mise à l’échelle des unités DWU, consultez [Mise à l’échelle des performances].

## <a name="pause-and-resume"></a>Suspension et reprise
Pour réduire les coûts, vous pouvez interrompre et reprendre des ressources de calcul à la demande. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée. Les unités DWU ne vous seront pas facturées pendant que la base de données est suspendue.

Pour plus d’informations, consultez [Suspension du calcul][Pause compute], et [Reprise du calcul][Resume compute].

## <a name="performance-best-practices"></a>Meilleures pratiques pour les performances
Lorsque vous devez prendre en main une nouvelle technologie, rechercher les conseils et astuces adaptés à vos besoins dès le départ peut vous faire gagner beaucoup de temps.  Vous trouverez des meilleures pratiques dans plusieurs de nos rubriques.

Pour voir un résumé des considérations les plus importantes dont vous devez tenir compte lors du développement de votre charge de travail, consultez la rubrique [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse Best Practices] (en anglais).

## <a name="query-monitoring"></a>Surveillance des requêtes
Parfois, l’exécution d’une requête est trop longue, mais vous n’êtes pas sûr d’en identifier la cause. SQL Data Warehouse comprend des vues de gestion dynamique (DMV) que vous pouvez utiliser pour déterminer la requête dont l’exécution est trop longue.

Pour rechercher des requêtes à long terme, consultez la rubrique [Surveiller votre charge de travail à l'aide de vues de gestion dynamique][Monitor your workload using DMVs].

## <a name="security"></a>Sécurité
Pour maintenir la sécurisation du système, vous devez être vigilent et vous protéger contre tout type d’accès non autorisé. Un système de sécurité doit s’assurer que des règles de pare-feu sont en place par pour que seules des adresses IP autorisées puissent se connecter. Il nécessite une authentification correcte des informations d’identification de l’utilisateur. Une fois un utilisateur connecté à la base de données, il doit uniquement disposer des autorisations pour effectuer un nombre minimal d’actions. Pour sécuriser les données, vous pouvez utiliser le chiffrement. Il est également important de disposer de processus d’audit et de suivi pour vous permettre de retracer les événements en cas d’activité suspecte.

Pour en savoir plus sur la gestion de la sécurité, consultez la [vue d’ensemble de la sécurité][Security overview].

## <a name="back-up-and-restore"></a>Sauvegarder et restaurer des données
Le stockage de sauvegardes fiables de vos données est un élément essentiel de toute base de données de production. SQL Data Warehouse permet de sécuriser vos données en sauvegardant automatiquement vos bases de données actives à intervalles réguliers. Ces sauvegardes vous permettent d’effectuer une récupération à partir des scénarios où vous avez endommagé vos données ou accidentellement supprimé vos données ou votre base de données.  Pour en savoir plus sur la planification de la sauvegarde de données, la stratégie de rétention et la restauration d’une base de données, consultez [Restore from snapshot (Restaurer à partir d’un instantané)][Restore from snapshot].

## <a name="next-steps"></a>étapes suivantes
La mise en œuvre de principes de conception de base de données adaptés facilite la gestion de vos bases de données dans SQL Data Warehouse. Pour en savoir plus, consultez la [Vue d’ensemble du développement][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: pause-and-resume-compute-portal.md#pause-compute
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: pause-and-resume-compute-portal.md#resume-compute
[Mise à l’échelle des performances]: quickstart-scale-compute-portal.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
