---
title: "Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL | Microsoft Docs"
description: "Cet article explique comment effectuer une migration avec temps d’arrêt minimal en extrayant une base de données PostgreSQL dans un fichier de sauvegarde, en restaurant la base de données PostgreSQL à partir d’un fichier d’archive créé par pg_dump dans Azure Database pour PostgreSQL et en configurant la charge initiale et la synchronisation des données en continu depuis la base de données source vers la base de données cible à l’aide d’Attunity Replicate pour Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
Vous pouvez migrer votre base de données MySQL existante vers Azure Database pour PostgreSQL à l’aide d’Attunity Replicate pour Microsoft Migrations, offre commune d’Attunity et de Microsoft qui est fournie avec Azure Database Migration Service gratuitement aux clients de Microsoft. En outre, Attunity Replicate pour Microsoft Migrations permet de migrer la base de données en limitant les temps d’arrêt, opération durant laquelle la base de données source continue d’être opérationnelle.

Attunity Replicate est un outil de réplication de données qui permet la synchronisation des données entre une variété de sources et de cibles, en propageant le script de création du schéma et les données associées à chaque table de base de données. Attunity Replicate ne propage pas les autres artefacts (tels que les procédures stockées, les déclencheurs ou les fonctions), ni ne convertit, par exemple, le code PL/SQL hébergé dans ces artefacts en code T-SQL.

> [!NOTE]
> Bien qu’Attunity Replicate prenne en charge un large éventail de scénarios de migration, Attunity Replicate pour Microsoft Migrations se concentre sur la prise en charge d’un sous-ensemble spécifique de paires source/cible.

Schématiquement, le processus de migration avec temps d’arrêt minimal inclut les phases suivantes :

1. **Migration du schéma source PostgreSQL** vers une base de données Azure Database pour PostgreSQL en utilisant la commande [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) avec le paramètre -n, puis en utilisant la commande [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

2. **Configuration de la charge initiale et de la synchronisation des données en continu depuis la base de données source vers la base de données cible** à l’aide d’Attunity Replicate pour Microsoft Migrations. Cette opération réduit le temps pendant lequel vous devez définir la base de données source en lecture seule en vue du basculement de vos applications vers la base de données PostgreSQL cible sur Azure.

Pour plus d’informations sur l’offre Attunity Replicate pour Microsoft Migrations, consultez les ressources suivantes :
 - La [page web](https://aka.ms/attunity-replicate) d’Attunity Replicate pour Microsoft Migrations
 - La page de [téléchargement](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) d’Attunity Replicate pour Microsoft Migrations
 - La [Communauté](https://microsoft.attunity.com/) Attunity Replicate, ou vous trouverez un Guide de démarrage rapide, des didacticiels et un support.
 - Pour obtenir des instructions sur l’utilisation d’Attunity afin d’effectuer une migration depuis PostgreSQL vers Azure Database pour PostgreSQL, reportez-vous au [Guide de migration de base de données](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).