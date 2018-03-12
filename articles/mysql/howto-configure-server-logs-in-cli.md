---
title: "Accéder aux journaux du serveur dans Azure Database pour MySQL à l’aide d’Azure CLI"
description: "Cet article décrit comment accéder aux journaux du serveur dans Azure Database pour MySQL à l’aide de l’utilitaire en ligne de commande Azure CLI."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8cd83722569eef503030b7e7438a73209cb812d6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configurer et accéder aux journaux du serveur à l’aide d’Azure CLI
Vous pouvez télécharger les journaux des serveurs Azure Database pour MySQL à l’aide de l’interface CLI Azure (utilitaire de ligne de commande Azure).

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.

## <a name="configure-logging-for-azure-database-for-mysql"></a>Configuration de la journalisation pour Azure Database pour MySQL
Vous pouvez configurer le serveur afin d’accéder au journal des requêtes lentes MySQL.
1. Activez la journalisation en définissant le paramètre de **journalisation\_des\_requêtes lentes** sur ON.
2. Réglez les autres paramètres tels que les instructions de **durée\_d’une requête\_longue** et de **journalisation\_de l\_’instruction\_slow admin**.

Voir [Comment configurer les paramètres du serveur](howto-configure-server-parameters-using-cli.md) pour savoir comment définir la valeur de ces paramètres via l’interface CLI Azure.

Par exemple, la commande CLI suivante active la journalisation des requêtes lentes, définit la durée de requête longue sur 10 secondes et désactive la journalisation de l’instruction slow admin. Enfin, elle répertorie les options de configuration à vérifier.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Répertorier les journaux pour le serveur Azure Database pour MySQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list).

Vous pouvez répertorier les fichiers journaux pour le serveur **mydemoserver.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup** et les diriger vers un fichier texte appelé **liste\_fichiers\_journaux.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Télécharger des journaux à partir du serveur
La commande [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) vous permet de télécharger des fichiers journaux individuels pour votre serveur. 

Cet exemple télécharge le fichier journal spécifique pour le serveur **mydemoserver.mysql.database.azure.com** du groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [journaux de serveurs dans Azure Database pour MySQL](concepts-server-logs.md)
