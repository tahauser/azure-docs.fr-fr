---
title: "Configurer les paramètres de service dans Azure Database pour MySQL | Microsoft Docs"
description: "Cet article décrit comment configurer les paramètres de service dans Azure Database pour MySQL à l’aide de l’utilitaire en ligne de commande Azure CLI."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: 6a0d218a9b9cb41a87264cfd5f653bb631b0bce9
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Personnaliser les paramètres de configuration du serveur à l’aide d’Azure CLI
Vous pouvez répertorier, afficher et mettre à jour les paramètres de configuration d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI, l’utilitaire en ligne de commande Azure. Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié. 

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- L’utilitaire en ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Répertorier les paramètres de configuration de serveur pour Azure Database pour MySQL
Pour répertorier tous les paramètres modifiables sur un serveur, ainsi que leurs valeurs, exécutez la commande [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list).

Vous pouvez répertorier les paramètres de configuration du serveur **myserver4demo.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server myserver4demo
```
Pour obtenir la définition de chacun des paramètres répertoriés, consultez la section de référence MySQL dans [Server System Variables (Variables système du serveur)](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration particulier pour un serveur, exécutez la commande [az mysql server configuration show](/cli/azure/mysql/server/configuration#show).

Cet exemple affiche les détails du paramètre de configuration de serveur **slow\_query\_log** pour le serveur **myserver4demo.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
## <a name="modify-a-server-configuration-parameter-value"></a>Modifier une valeur de paramètre de configuration de serveur
Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur MySQL. Pour mettre à jour la configuration, exécutez la commande [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set). 

Pour mettre à jour le paramètre de configuration de serveur **slow\_query\_log** pour le serveur **myserver4demo.mysql.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo --value ON
```
Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, omettez le paramètre `--value` facultatif. Le service applique alors la valeur par défaut. Pour l’exemple ci-dessus, on aurait ce qui suit :
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server myserver4demo
```
Ce code réinitialise la configuration **slow\_query\_log** à la valeur par défaut **OFF**. 

## <a name="next-steps"></a>Étapes suivantes
- Guide pratique pour configurer des [paramètres de serveur dans le portail Azure](howto-server-parameters.md)
