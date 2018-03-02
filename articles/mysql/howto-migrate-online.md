---
title: "Migration avec temps d’arrêt minimal vers Azure Database pour MySQL"
description: "Cet article explique comment effectuer une migration avec temps d’arrêt minimal d’une base de données MySQL vers Azure Database pour MySQL et comment configurer la charge initiale et la synchronisation des données en continu depuis la base de données source vers la base de données cible à l’aide d’Attunity Replicate pour Microsoft Migrations."
services: mysql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e1be72d97570643cc8a7c6eb05d3d363e96357b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour MySQL
Pour migrer votre base de données MySQL existante vers Azure Database pour MySQL, utilisez Attunity Replicate pour Microsoft Migrations. Attunity Replicate est une offre commune d’Attunity et de Microsoft. Fournie avec Azure Database Migration Service, cette solution est offerte sans frais supplémentaires aux clients Microsoft. 

Attunity Replicate permet de réduire les temps d’arrêt durant les migrations de base de données. De fait, la base de données source demeure opérationnelle tout au long du processus.

Attunity Replicate est un outil de réplication des données qui prend en charge la synchronisation des données entre diverses sources et cibles. Il propage le script de création de schéma et les données associés à chaque table de base de données. Attunity Replicate ne propage pas les autres artefacts (tels que les procédures stockées, les déclencheurs, les fonctions, etc.), ni ne convertit, par exemple, le code PL/SQL hébergé dans ces artefacts en code T-SQL.

> [!NOTE]
> Bien qu’Attunity Replicate prenne en charge un large éventail de scénarios de migration, cette solution se concentre sur la prise en charge d’un sous-ensemble spécifique de paires source/cible.

Schématiquement, le processus de migration avec temps d’arrêt minimal inclut les phases suivantes :

* **Migration du schéma source MySQL** vers un service de base de données géré Azure Database pour MySQL à l’aide de [MySQL Workbench](https://www.mysql.com/products/workbench/).

* **Configuration de la charge initiale et de la synchronisation des données en continu depuis la base de données source vers la base de données cible** à l’aide d’Attunity Replicate pour Microsoft Migrations. Cette opération réduit le temps pendant lequel vous devez définir la base de données source en lecture seule en vue du basculement de vos applications vers la base de données MySQL cible sur Azure.

Pour plus d’informations sur l’offre Attunity Replicate pour Microsoft Migrations, consultez les ressources suivantes :
 - Accédez à la page web d’[Attunity Replicate pour Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Téléchargez [Attunity Replicate pour Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Accédez à la [Communauté Attunity Replicate](https://aka.ms/attunity-community), où vous trouverez un Guide de démarrage rapide, des didacticiels et un support.
 - Pour obtenir des instructions sur l’utilisation d’Attunity Replicate afin d’effectuer une migration depuis MySQL vers Azure Database pour MySQL, reportez-vous au [Guide de migration de base de données](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).