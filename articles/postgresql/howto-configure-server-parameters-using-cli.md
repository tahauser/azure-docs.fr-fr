---
title: "Configuration des paramètres de service dans Azure Database pour PostgreSQL | Microsoft Docs"
description: "Cet article décrit comment configurer les paramètres du service dans Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande (CLI) Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: a2a3395fad35599591e093385305f28bbb161014
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personnalisation des paramètres de configuration du serveur à l’aide de l’interface de ligne de commande Azure
Il est possible de lister, d’afficher et de mettre à jour les paramètres de configuration d’un serveur Azure PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Un sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié. 

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Créez un serveur et une base de données Azure Database pour PostgreSQL en suivant les instructions de la page [Créer une base de données Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Installez l’interface de ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) sur votre ordinateur ou utilisez [Azure Cloud Shell](../cloud-shell/overview.md) sur le Portail Azure avec votre navigateur.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Répertorier les paramètres de configuration de Base de données Azure pour le serveur PostgreSQL
Pour répertorier tous les paramètres modifiables dans un serveur, ainsi que leurs valeurs, exécutez la commande [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list).

Par exemple, vous pouvez répertorier les paramètres de configuration du serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration particulier pour un serveur, exécutez la commande [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show).

Cet exemple affiche les détails du paramètre de configuration de serveur **log\_min\_messages** pour le serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Modification de la valeur de paramètre de configuration du serveur
Vous pouvez également modifier la valeur d’un paramètre de configuration du serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur PostgreSQL. Pour mettre à jour la configuration, exécutez la commande [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set). 

Pour mettre à jour le paramètre de configuration de serveur **log\_min\_messages** pour le serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, il vous suffit de ne pas définir le paramètre `--value` facultatif. Le service applique alors la valeur par défaut. Dans l’exemple ci-dessus, elle peut se présenter comme suit :
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Cette commande réinitialise la configuration **log\_min\_messages** à la valeur par défaut **WARNING**. Pour plus d’informations sur la configuration du serveur et les valeurs autorisées, consultez la documentation PostgreSQL à la rubrique [Configuration du serveur](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Étapes suivantes
- Pour configurer et accéder aux journaux du serveur, consultez la rubrique [Journaux de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md)
