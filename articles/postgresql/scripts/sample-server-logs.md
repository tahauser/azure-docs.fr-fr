---
title: "Script Azure CLI : Télécharger des journaux de serveurs dans Azure Database pour PostgreSQL"
description: "Cet exemple de script Azure CLI montre comment activer et télécharger les journaux d’un serveur Azure Database pour PostgreSQL."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 195a9d1162798e916a9fc8fc6efce58a0af9f2eb
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Activer et télécharger les journaux de requêtes lentes d’un serveur Azure Database pour PostgreSQL à l’aide d’Azure CLI
Cet exemple de script CLI montre comment activer et télécharger les journaux de requêtes lentes d’un seul serveur Azure Database pour PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez l’article [Installer Azure CLI 2.0]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI.

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres. Remplacez <log_file_name> dans les commandes `az monitor` par votre propre nom de fichier journal de serveur.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=18-19 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | Crée un serveur PostgreSQL qui héberge les bases de données. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Répertoriez les valeurs de configuration pour un serveur. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Mettez à jour la configuration d’un serveur. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | Répertoriez les fichiers journaux pour un serveur. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | Téléchargez les fichiers journaux. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure/overview)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour PostgreSQL](../sample-scripts-azure-cli.md)
- [Configurer et consulter les journaux du serveur sur le Portail Azure](../howto-configure-server-logs-in-portal.md)
