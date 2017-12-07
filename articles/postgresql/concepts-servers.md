---
title: "Concepts de serveur dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Cette rubrique propose des considérations et des instructions relatives à la configuration et à la gestion des serveurs Azure Database pour PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: a1008936c053316630360403be688e4eedc8b2c0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="azure-database-for-postgresql-servers"></a>Serveurs de base de données Azure pour PostgreSQL
Cet article présente des considérations et des instructions relatives à l’utilisation des serveurs Azure Database pour PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Qu’est-ce qu’un serveur de base de données Azure pour PostgreSQL ?
Un serveur de base de données Azure pour PostgreSQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur PostgreSQL que vous connaissez peut-être en local. Plus précisément, le service PostgreSQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur de base de données Azure pour PostgreSQL :

- est créé dans un abonnement Azure ;
- représente la ressource parente des bases de données ;
- fournit un espace de noms aux bases de données ;
- constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données qu’il contient) ;
- colocalise les ressources d’une région ;
- fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données (.postgresql.database.azure.com) ;
- fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc. ;
- est disponible dans plusieurs versions (pour plus d’informations, consultez [Versions de bases de données PostgreSQL prises en charge](concepts-supported-versions.md)).
- peut être étendu par les utilisateurs (pour plus d’informations, consultez la page [Extensions de PostgreSQL](concepts-extensions.md)).

Dans une base de données Azure Database pour serveur PostgreSQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. La tarification est structurée par serveur, en fonction de la configuration du niveau tarifaire, des unités de calcul et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur de base de données Azure pour PostgreSQL ?
Les éléments suivants permettent de garantir un accès sécurisé à votre base de données.

| :-- | :-- | | **Authentification et autorisation** | Le serveur Azure Database pour PostgreSQL prend en charge l’authentification PostgreSQL native. Vous pouvez vous connecter et vous authentifier auprès du serveur avec les informations de connexion d’administrateur du serveur. | | **Protocole** | Le service prend en charge un protocole par messages utilisé par PostgreSQL. | | **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. | | **Pare-feu** | Pour renforcer la protection de vos données, une règle de pare-feu empêche tout accès à votre serveur et à ses bases de données tant que vous ne spécifiez pas les ordinateurs autorisés. Consultez la page [Règles de pare-feu d’un serveur de base de données Azure pour PostgreSQL](concepts-firewall-rules.md). |

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?
Vous pouvez gérer des serveurs Azure Database pour PostgreSQL à l’aide du [Portail Azure](https://portal.azure.com) ou [d’Azure CLI](/cli/azure/postgres).

## <a name="server-parameters"></a>Paramètres de serveur
Les paramètres de serveur PostgreSQL déterminent la configuration du serveur. Dans Azure Database pour PostgreSQL, la liste des paramètres peut être affichée et modifiée par le biais du portail Azure ou d’Azure CLI. 

Azure Database pour PostgreSQL étant un service géré pour Postgres, ses paramètres configurables sont un sous-ensemble des paramètres d’une instance locale de Postgres (pour plus d’informations sur les paramètres de Postgres, consultez la [documentation de PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Votre serveur Azure Database pour PostgreSQL est activé avec des valeurs par défaut pour chaque paramètre lors de la création. Les paramètres qui nécessitent un redémarrage du serveur ou un accès de super utilisateur pour que les modifications entrent en vigueur ne peuvent pas être configurés par l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour PostgreSQL](overview.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](concepts-service-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez la page [Bibliothèques de connexions de la base de données Azure pour PostgreSQL](concepts-connection-libraries.md).
- Affichez et modifiez les paramètres de serveur par le biais du [portail Azure](howto-configure-server-parameters-using-portal.md) ou [d’Azure CLI](howto-configure-server-parameters-using-cli.md).
