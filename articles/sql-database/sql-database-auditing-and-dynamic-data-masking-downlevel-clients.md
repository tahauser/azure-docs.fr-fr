---
title: Audit de table, redirection TDS et points de terminaison IP pour Azure SQL Database | Microsoft Docs
description: Découvrez l’audit, la redirection TDS et les modifications de point de terminaison IP pendant l’implémentation de l’audit de table dans Azure SQL Database.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/31/2017
ms.author: giladm
ms.openlocfilehash: 1053869ed26739463ab6035692052d7b6b34bff0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>SQL Database : prise en charge des clients de niveau inférieur et modification des points de terminaison IP à des fins d’audit de table

> [!IMPORTANT]
> Ce document s’applique uniquement à l’audit de table, qui est **maintenant déprécié**.<br>
> Utilisez la nouvelle méthode [d’audit d’objets blob](sql-database-auditing.md), qui **n’exige pas** de modifications de la chaîne de connexion pour les clients de niveau inférieur. Vous trouverez des informations supplémentaires sur l’audit d’objets blob dans [Prise en main de l’audit de base de données SQL](sql-database-auditing.md).

L’[audit de base de données](sql-database-auditing.md) fonctionne automatiquement avec les clients SQL qui prennent en charge la redirection TDS. Notez que la redirection ne s’applique pas lorsque vous utilisez la méthode de l’audit d’objets Blob.

## <a id="subheading-1"></a>Prise en charge des clients de niveau inférieur
Tout client qui implémente TDS 7.4 doit également prendre en charge la redirection. Cependant, cette règle comporte deux exceptions : JDBC 4.0, qui ne prend pas complètement en charge la fonctionnalité de redirection et Tedious pour Node.JS, où la redirection n’a pas été implémentée.

Pour les « clients de niveau inférieur », c’est-à-dire ceux qui prennent en charge la version 7.3 de TDS et les versions antérieures, le nom de domaine complet du serveur dans la chaîne de connexion doit être modifié :

Nom de domaine complet du serveur d’origine dans la chaîne de connexion : <*nom du serveur*>.database.windows.net

Nom de domaine complet du serveur modifié dans la chaîne de connexion : <*nom du serveur*>.database.**secure**.windows.net

Voici une liste non exhaustive de « clients de niveau inférieur » :

* .NET 4.0 et versions antérieures
* ODBC 10.0 et versions antérieures
* JDBC (bien que JDBC prenne en charge la version 7.4 de TDS, la fonctionnalité de redirection TDS n’est pas entièrement prise en charge)
* Tedious (pour Node.JS)

**Remarque :** la modification des noms de domaines complets de serveur précédents peut aussi être utile pour appliquer une stratégie d’audit au niveau de SQL Server sans avoir à configurer chaque base de données (atténuation temporaire).

## <a id="subheading-2"></a>Modification des points de terminaison IP lors de l’activation de l’audit
Notez que l’activation de l’audit de table a pour effet de modifier le point de terminaison IP de votre base de données. Si vous avez défini des paramètres de pare-feu stricts, mettez-les à jour en conséquence.

Le nouveau point de terminaison IP de la base de données dépend de la région de votre base de données :

| Région de base de données | Points de terminaison IP possibles |
| --- | --- |
| Chine du Nord |139.217.29.176, 139.217.28.254 |
| Chine orientale |42.159.245.65, 42.159.246.245 |
| Est de l’Australie |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sud-est de l’Australie |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sud du Brésil |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centre des États-Unis |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| EUAP Centre des États-Unis |52.180.178.16, 52.180.176.190 |
| Est de l'Asie |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Est des États-Unis 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Est des États-Unis |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| EUAP Est des États-Unis |52.225.190.86, 52.225.191.187 |
| Inde centrale |104.211.98.219, 104.211.103.71 |
| Inde du Sud |104.211.227.102, 104.211.225.157 |
| Inde occidentale |104.211.161.152, 104.211.162.21 |
| Est du Japon |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Ouest du Japon |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Centre-Nord des États-Unis |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europe du Nord |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| États-Unis - partie centrale méridionale |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Asie du Sud-Est |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europe de l'Ouest |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| États-Unis de l’Ouest |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| États-Unis de l’Ouest 2 |13.66.224.156, 13.66.227.8 |
| Centre-Ouest des États-Unis |52.161.29.186, 52.161.27.213 |
| Centre du Canada |13.88.248.106, 13.88.248.110 |
| Est du Canada |40.86.227.82, 40.86.225.194 |
| Royaume-Uni Nord |13.87.101.18, 13.87.100.232 |
| Royaume-Uni Sud 2 |13.87.32.202, 13.87.32.226 |
