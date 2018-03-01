---
title: "Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL"
description: "Cet article explique comment effectuer une migration avec temps d’arrêt minimal en extrayant une base de données PostgreSQL dans un fichier de sauvegarde, en restaurant la base de données PostgreSQL à partir d’un fichier d’archive créé par pg_dump dans Azure Database pour PostgreSQL et en configurant la charge initiale et la synchronisation des données en continu depuis la base de données source vers la base de données cible à l’aide d’Attunity Replicate pour Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Migration avec temps d’arrêt minimal vers Azure Database pour PostgreSQL
Pour migrer votre base de données PostgreSQL existante vers Azure Database pour PostgreSQL, utilisez Attunity Replicate pour Microsoft Migrations. Attunity Replicate est une offre commune d’Attunity et de Microsoft. Fournie avec Azure Database Migration Service, cette solution est offerte sans frais supplémentaires aux clients Microsoft. 

Attunity Replicate permet de réduire les temps d’arrêt durant les migrations de base de données. De fait, la base de données source demeure opérationnelle tout au long du processus.

Attunity Replicate est un outil de réplication des données qui prend en charge la synchronisation des données entre diverses sources et cibles. Il propage le script de création de schéma et les données associés à chaque table de base de données. Attunity Replicate ne propage pas les autres artefacts (tels que les procédures stockées, les déclencheurs, les fonctions, etc.), ni ne convertit, par exemple, le code PL/SQL hébergé dans ces artefacts en code T-SQL.

> [!NOTE]
> Bien qu’Attunity Replicate prenne en charge un large éventail de scénarios de migration, cette solution se concentre sur la prise en charge d’un sous-ensemble spécifique de paires source/cible.

Schématiquement, le processus de migration avec temps d’arrêt minimal inclut les phases suivantes :

* **Migration du schéma source PostgreSQL** vers une base de données Azure Database pour PostgreSQL en utilisant la commande [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) avec le paramètre -n, puis en utilisant la commande [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html).

* **Configuration de la charge initiale et de la synchronisation des données en continu depuis la base de données source vers la base de données cible** à l’aide d’Attunity Replicate pour Microsoft Migrations. Cette opération réduit le temps pendant lequel vous devez définir la base de données source en lecture seule en vue du basculement de vos applications vers la base de données PostgreSQL cible sur Azure.

Pour plus d’informations sur l’offre Attunity Replicate pour Microsoft Migrations, consultez les ressources suivantes :
 - Accédez à la page web d’[Attunity Replicate pour Microsoft Migrations](https://aka.ms/attunity-replicate).
 - Téléchargez [Attunity Replicate pour Microsoft Migrations](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Accédez à la [Communauté Attunity Replicate](https://aka.ms/attunity-community/), où vous trouverez un Guide de démarrage rapide, des didacticiels et un support.
 - Pour obtenir des instructions sur l’utilisation d’Attunity Replicate afin d’effectuer une migration depuis PostgreSQL vers Azure Database pour PostgreSQL, reportez-vous au [Guide de migration de base de données](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).