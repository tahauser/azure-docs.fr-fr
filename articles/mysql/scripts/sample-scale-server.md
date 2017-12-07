---
title: "Exemples Azure CLI pour mettre à l’échelle un serveur Azure Database pour MySQL | Microsoft Docs"
description: "Cet exemple de script CLI met à l’échelle un serveur Azure Database pour MySQL vers un nouveau niveau de performance après l’analyse des métriques."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/02/2017
ms.openlocfilehash: 517208b76757655990f82f50677267320eb1393c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL à l’aide de la CLI Azure
Cet exemple de script CLI met à l’échelle un serveur de base de données Azure unique pour MySQL vers un nouveau niveau de performance après l’analyse des métriques.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur. Remplacez l’ID d’abonnement utilisé dans les commandes az monitor par votre propre ID d’abonnement. [!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=15-16 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Après l’exécution de l’exemple de script, vous pouvez supprimer le groupe de ressources et toutes les ressources associées en exécutant la commande suivante :[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Affiche la valeur de métrique pour les ressources. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure/overview)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour MySQL](../sample-scripts-azure-cli.md)
- Pour plus d’informations sur la mise à l’échelle, consultez [Niveaux de service](../concepts-service-tiers.md) et [Unités de calcul et unités de stockage](../concepts-compute-unit-and-storage.md).
