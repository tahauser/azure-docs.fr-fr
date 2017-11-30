---
title: "Configuration et accès aux journaux du serveur pour PostgreSQL à l’aide de la ligne de commande Azure | Microsoft Docs"
description: "Cet article décrit comment configurer les journaux du serveur dans Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure, et comment y accéder."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: d18ec44ecede44829b488ac9864bbfae2c62883a
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuration et accès aux journaux du serveur à l’aide de la ligne de commande Azure
Vous pouvez télécharger les journaux d’erreurs du serveur PostgreSQL à l’aide de l’interface de ligne de commande Azure. Toutefois, l’accès aux journaux des transactions n’est pas pris en charge. 

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installer l’utilitaire de ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell dans le navigateur.

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuration de la journalisation pour Azure Database pour PostgreSQL
Vous pouvez configurer le serveur afin d’accéder aux journaux de requêtes et aux journaux d’erreurs. Les journaux d’erreurs peuvent contenir des informations sur le nettoyage automatique, la connexion et les points de contrôle.
1. Activation de la journalisation
2. Mise à jour de l\_’instruction de journalisation et de l’instruction de\_ \_durée minimale\_de journalisation pour activer la journalisation des requêtes
3. Mise à jour de la période de rétention

Pour plus d’informations, consultez [Personnalisation des paramètres de configuration du serveur](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Répertorier les journaux pour le serveur Azure Database pour PostgreSQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mypgserver-20170401.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup** et les diriger vers un fichier texte appelé **liste\_fichiers\_journaux.txt.**
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Téléchargement des journaux localement à partir du serveur
La commande [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) vous permet de télécharger des fichiers journaux individuels pour votre serveur. 

Cet exemple télécharge le fichier journal spécifique pour le serveur **mypgserver-20170401.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les journaux de serveur, consultez la rubrique [Journaux de serveur dans la base de données Azure pour PostgreSQL](concepts-server-logs.md)
- Pour plus d’informations sur les paramètres du serveur, consultez la rubrique [Personnalisation des paramètres de configuration de serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md)
