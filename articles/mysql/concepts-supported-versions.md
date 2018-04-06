---
title: Versions prises en charge dans Azure Database pour MySQL
description: Décrit les versions prises en charge des bases de données Azure pour MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: cfebdbe7485f0ffaa15828803d72c2a3f97c118d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versions prises en charge du serveur de base de données Azure pour MySQL
Azure Database for MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur InnoDB.  Azure Database pour MySQL prend actuellement en charge les versions suivantes :

## <a name="mysql-version-5638"></a>MySQL Version 5.6.38
Consultez la [documentation](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) MySQL pour en savoir plus sur les améliorations et les correctifs dans MySQL 5.6.38.

## <a name="mysql-version-5720"></a>MySQL Version 5.7.20
Consultez la [documentation](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.htmll) MySQL pour en savoir plus sur les améliorations et les correctifs dans MySQL 5.7.20.

> [!NOTE]
> Dans le service, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL. 

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Le service gère automatiquement les correctifs pour les mises à jour de version mineures. Les mises à niveau de version principales ne sont pas prises en charge (par ex. la mise à niveau à partir de MySQL 5.6 pour MySQL 5.7).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
