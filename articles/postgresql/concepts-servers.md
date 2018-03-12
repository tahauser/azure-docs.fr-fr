---
title: Concepts de serveur dans Azure Database pour PostgreSQL
description: "Cet article indique les éléments à prendre en considération et fournit des instructions pour configurer et gérer des serveurs Azure Database pour PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2db18b014606799bdf5707c4c19f363bbc323e5c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-postgresql-servers"></a>Serveurs Azure Database pour PostgreSQL
Cet article présente des considérations et des instructions relatives à l’utilisation des serveurs Azure Database pour PostgreSQL.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Qu’est-ce qu’un serveur Azure Database pour PostgreSQL ?
Un serveur Azure Database pour PostgreSQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur PostgreSQL que vous connaissez peut-être en local. Plus précisément, le service PostgreSQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur Azure Database pour PostgreSQL :

- Est créé dans un abonnement Azure.
- Représente la ressource parente des bases de données.
- Fournit un espace de noms aux bases de données.
- Constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données qu’il contient).
- Colocalise les ressources d’une région.
- Fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données (.postgresql.database.azure.com).
- Fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc.
- Est disponible dans plusieurs versions. Pour plus d’informations, consultez [Versions de bases de données PostgreSQL prises en charge](concepts-supported-versions.md).
- Peut être étendu par les utilisateurs. Pour plus d’informations, consultez la page [Extensions de PostgreSQL](concepts-extensions.md).

Dans un serveur Azure Database pour PostgreSQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Les tarifs sont structurés par serveur, en fonction de la configuration du niveau tarifaire, des vCores et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur Azure Database pour PostgreSQL ?
Les éléments suivants permettent de garantir un accès sécurisé à votre base de données :

|||
|:--|:--|
| **Authentification et autorisation** | Le serveur Azure Database pour PostgreSQL prend en charge l’authentification PostgreSQL native. Vous pouvez vous connecter et vous authentifier auprès du serveur avec les informations de connexion d’administrateur du serveur. |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par PostgreSQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour renforcer la protection de vos données, une règle de pare-feu empêche tout accès à votre serveur et à ses bases de données tant que vous ne précisez pas quels ordinateurs sont autorisés. Consultez [Règles de pare-feu d’un serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md). |

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?
Vous pouvez gérer des serveurs Azure Database pour PostgreSQL à l’aide du [Portail Azure](https://portal.azure.com) ou [d’Azure CLI](/cli/azure/postgres).

## <a name="server-parameters"></a>Paramètres de serveur
Les paramètres de serveur PostgreSQL déterminent la configuration du serveur. Dans Azure Database pour PostgreSQL, la liste des paramètres peut être affichée et modifiée à l’aide du portail Azure ou d’Azure CLI. 

Azure Database pour PostgreSQL étant un service géré pour Postgres, ses paramètres configurables sont un sous-ensemble des paramètres d’une instance locale de Postgres (pour plus d’informations sur les paramètres de Postgres, consultez la [documentation de PostgreSQL](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Votre serveur Azure Database pour PostgreSQL est activé avec des valeurs par défaut pour chaque paramètre lors de la création. Les paramètres qui nécessitent un redémarrage du serveur ou un accès de super utilisateur pour que les modifications entrent en vigueur ne peuvent pas être configurés par l’utilisateur.


## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service dans [Vue d’ensemble d’Azure Database pour PostgreSQL](overview.md).
- Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](concepts-pricing-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez [Bibliothèques de connexions Azure Database pour PostgreSQL](concepts-connection-libraries.md).
- Affichez et modifiez les paramètres de serveur par le biais du [portail Azure](howto-configure-server-parameters-using-portal.md) ou [d’Azure CLI](howto-configure-server-parameters-using-cli.md).
