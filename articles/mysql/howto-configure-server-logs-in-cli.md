---
title: "Accéder aux journaux des serveurs dans Azure Database pour MySQL à l’aide de l’interface CLI Azure | Microsoft Docs"
description: "Cet article décrit comment accéder aux journaux des serveurs dans Azure Database pour MySQL à l’aide de l’utilitaire en ligne de commande Azure CLI."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 908f28d8bd3d0dcbd03636e69cd47b5c47f3cfde
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuration et accès aux journaux du serveur à l’aide de la ligne de commande Azure
Vous pouvez télécharger les journaux des serveurs Azure Database pour MySQL à l’aide de l’interface CLI Azure (utilitaire de ligne de commande Azure).

## <a name="prerequisites"></a>Composants requis
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
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server myserver4demo --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server myserver4demo --value OFF
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Répertorier les journaux pour le serveur Azure Database pour MySQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list).

Vous pouvez répertorier les fichiers journaux pour le serveur **myserver4demo.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup** et les diriger vers un fichier texte appelé **liste\_fichiers\_journaux.txt.**
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server myserver4demo > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Télécharger des journaux à partir du serveur
La commande [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) vous permet de télécharger des fichiers journaux individuels pour votre serveur. 

Cet exemple télécharge le fichier journal spécifique pour le serveur **myserver4demo.mysql.database.azure.com** sous le groupe de ressources **myresourcegroup** dans votre environnement local.
```azurecli-interactive
az mysql server-logs download --name 20170414-myserver4demo-mysql.log --resource-group myresourcegroup --server myserver4demo
```

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [journaux de serveurs dans Azure Database pour MySQL](concepts-server-logs.md)
