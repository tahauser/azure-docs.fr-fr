---
title: "Concepts de serveur dans une base de données Azure pour MySQL"
description: "Cette rubrique propose des considérations et des instructions relatives à l’utilisation des serveurs de base de données Azure pour MySQL."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0cf35efa7b8b4c6f78a8821c6d10e606813b7848
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Concepts de serveur dans une base de données Azure pour MySQL
Cet article présente des considérations et des instructions relatives à l’utilisation des serveurs Azure Database pour MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Qu’est-ce qu’un serveur de base de données Azure pour MySQL ?

Un serveur de base de données Azure pour MySQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur MySQL que vous connaissez peut-être en local. Plus précisément, le service Azure Database pour MySQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur de base de données Azure pour MySQL :

- est créé dans un abonnement Azure ;
- représente la ressource parente des bases de données ;
- fournit un espace de noms aux bases de données ;
- constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données qu’il contient) ;
- colocalise les ressources d’une région ;
- fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données ;
- fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc. ;
- est disponible dans plusieurs versions (pour plus d’informations, consultez la page [Versions prises en charge des bases de données Azure pour MySQL](./concepts-supported-versions.md)) ;

Dans un serveur Azure Database pour MySQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Les tarifs sont structurés par serveur, en fonction de la configuration du niveau tarifaire, des vCores et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur de base de données Azure pour MySQL ?

Les éléments suivants permettent de garantir un accès sécurisé à votre base de données.
|||
| :-- | :-- |
| **Authentification et autorisation** | Le serveur de base de données Azure pour MySQL prend en charge l’authentification MySQL native. Vous pouvez vous connecter et vous authentifier auprès d’un serveur avec les informations de connexion d’administrateur du serveur. |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par MySQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour aider à protéger vos données, une règle de pare-feu empêche tout accès à votre serveur de base de données tant que vous ne spécifiez pas les ordinateurs autorisés. Consultez la page [Règles de pare-feu d’un serveur de base de données Azure pour MySQL](./concepts-firewall-rules.md). |
| **SSL** | Le service prend en charge l’application des connexions SSL entre vos applications et votre serveur de base de données.  Consultez la page [Configurer la connectivité SSL dans une application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?
Vous pouvez gérer des serveurs de bases de données Azure pour MySQL à l’aide du Portail Azure ou d’Azure CLI.

## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour MySQL](./overview.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-service-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez la page [Bibliothèques de connexions de la base de données Azure pour MySQL](./concepts-connection-libraries.md).
