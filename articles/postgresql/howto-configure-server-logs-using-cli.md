---
title: Configurer et accéder aux journaux de serveur pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
description: Cet article décrit comment configurer les journaux du serveur dans Azure Database pour PostgreSQL à l’aide de la ligne de commande Azure CLI et comment y accéder.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 951dcca562c08698b4ce4528d005fc91152ea337
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configurer et accéder aux journaux du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux des erreurs du serveur PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Toutefois, l’accès aux journaux des transactions n’est pas pris en charge. 

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md)
- L’utilitaire de la ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuration de la journalisation pour Azure Database pour PostgreSQL
Vous pouvez configurer le serveur afin d’accéder aux journaux de requêtes et aux journaux d’erreurs. Les journaux des erreurs peuvent contenir des informations sur le nettoyage automatique, la connexion et les points de contrôle.
1. Activez la journalisation.
2. Pour activer la journalisation des requêtes, mettez à jour **l’instruction\_du journal** et **l’instruction\_durée\_minimale\_du journal**.
3. Mettez à jour la période de rétention.

Pour plus d’informations, consultez [Personnalisation des paramètres de configuration du serveur](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Répertorier les journaux pour le serveur Azure Database pour PostgreSQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup**. Puis, dirigez la liste des fichiers journaux vers un fichier texte appelé **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Téléchargement des journaux localement à partir du serveur
Avec la commande [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) vous pouvez télécharger des fichiers journaux individuels pour votre serveur. 

Utilisez l’exemple suivant pour télécharger le fichier journal spécifique pour le serveur **mydemoserver.postgres.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les journaux de serveur, consultez [Journaux de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md).
- Pour plus d’informations sur les paramètres du serveur, consultez [Personnalisation des paramètres de configuration de serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md).
