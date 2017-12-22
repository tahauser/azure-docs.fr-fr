---
title: "Migrer : utilitaire de migration de l’entrepôt de données | Microsoft Docs"
description: Migrer vers SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="data-warehouse-migration-utility-preview"></a>Utilitaire de migration de l’entrepôt de données (version préliminaire)
> [!div class="op_single_selector"]
> * [Télécharger l’utilitaire de migration][Download Migration Utility]
> 
> 

L’utilitaire de migration de l’entrepôt de données est un outil conçu pour migrer le schéma et les données du SQL Server et de la base de données SQL Azure vers Azure SQL Data Warehouse. Lors de la migration du schéma, l’outil mappe automatiquement le schéma correspondant à partir de la source vers la destination. Une fois le schéma migré, les outils offrent la possibilité de déplacer les données avec des scripts générés automatiquement.

Outre la migration du schéma et des données, cet outil vous permet de générer des rapports de compatibilité qui résument les incompatibilités entre les instances cible et source, qui empêcheraient la migration simplifiée.

## <a name="get-started"></a>Prise en main
Comme condition préalable à l’installation, vous aurez besoin de l’utilitaire de ligne de commande BCP, pour exécuter des scripts de migration, et d’Office, pour afficher le rapport de compatibilité. Après le lancement de l’exécutable téléchargé, vous êtes invité à accepter un CLUF standard avant l’installation de l’outil.

En outre, pour exécuter l’utilitaire de migration, vous avez besoin des autorisations suivantes pour les bases de données que vous souhaitez migrer : CREATE DATABASE, ALTER ANY DATABASE ou VIEW ANY DEFINITION.

### <a name="launching-the-tool-and-connecting"></a>Lancement de l’outil et connexion
Lancez l’outil en cliquant sur l’icône du Bureau qui apparaît après l’installation. Lors de l’ouverture de l’outil, une page de connexion initiale vous permet de choisir votre source et votre destination pour l’outil de migration. À ce stade, nous prenons en charge SQL Server et la base de données SQL Azure en tant que sources et SQL Data Warehouse en tant que destination. Après la sélection de ces options, vous devez vous connecter à votre serveur source en renseignant le nom du serveur et en vous authentifiant, puis en cliquant sur Se connecter.

Une fois l’authentification effectuée, l’outil affiche une liste des bases de données présentes sur le serveur auquel vous êtes connecté. Vous pouvez commencer la migration en sélectionnant une base de données que vous souhaitez migrer, puis en cliquant sur Migrer les éléments sélectionnés.

## <a name="migration-report"></a>Rapport de migration
Lorsque vous sélectionnez l’option « Vérifier la compatibilité des bases de données » dans l’outil, vous obtenez un rapport qui résume toutes les incompatibilités d’objets dans la base de données que vous avez souhaité migrer. Vous trouverez une liste élargie de certaines des fonctionnalités de SQL Server qui ne sont pas présentes dans SQL Data Warehouse, dans notre [documentation relative à la migration][migration documentation]. Une fois le rapport généré, vous pouvez l’enregistrer et l’ouvrir dans Excel.

Notez que lors de la génération du schéma de migration, la plupart des problèmes identifiés comme Object sont ajustés afin de permettre la migration immédiate de ces données. Veuillez consulter les modifications afin de vous assurer que vous ne souhaitez pas apporter d’ajustements supplémentaires avant d’appliquer le schéma.

## <a name="migrate-schema"></a>Migration du schéma
Une fois la connexion effectuée, la sélection de l’option Migrer le schéma génère un script de migration de schéma pour les tables sélectionnées. Ce script porte la structure de la table, mappe les types de données incompatibles vers des formulaires plus compatibles et crée des informations d’identification de sécurité et un schéma si cela a été indiqué par l’utilisateur dans les paramètres de migration. Ce code peut être exécuté sur l’instance SQL Data Warehouse ciblée, enregistré dans un fichier, copié dans votre Presse-papiers, voire modifié en ligne avant d’entreprendre une action supplémentaire.  

Comme mentionné ci-dessus, le schéma de migration passe en revue les modifications de migration effectuées par l’outil afin de vérifier que vous les comprenez bien.  

## <a name="migrate-data"></a>Migration des données
En cliquant sur l’option Migrer les données, vous pouvez générer des scripts BCP qui déplaceront vos données vers les fichiers plats de votre serveur dans un premier temps, puis directement dans votre SQL Data Warehouse. Nous vous recommandons de suivre ce processus pour déplacer de petites quantités de données, dans la mesure où les nouvelles tentatives ne sont pas intégrées et où des erreurs peuvent se produire en cas de perte de la connexion réseau. Pour exécuter ce processus, l’utilitaire de ligne de commande BCP doit être installé et le schéma pour les données doit avoir déjà été créé.

Après avoir rempli les paramètres ci-dessus, il vous suffit de cliquer sur Exécuter la migration ; un ensemble de deux packages est généré à l’emplacement spécifié. Exécutez le fichier d’exportation pour exporter les données depuis votre source de migration vers des fichiers plats, et exécutez le fichier d’importation pour importer vos données dans SQL Data Warehouse.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez migré certaines données, découvrez comment [développer][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
