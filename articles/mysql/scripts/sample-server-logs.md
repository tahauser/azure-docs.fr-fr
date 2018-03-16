---
title: "Script Azure CLI : Télécharger des journaux de serveurs dans Azure Database pour MySQL"
description: "Cet exemple de script Azure CLI montre comment activer et télécharger les journaux de serveur pour un seul serveur Azure Database pour MySQL."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 311bade14e00346e18167684bd022ff0e9630701
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Activer et télécharger les journaux de requête serveur lents d’un serveur Azure Database pour MySQL à l’aide d’Azure CLI
Cet exemple de script CLI montre comment activer et télécharger les journaux de serveur lents d’un seul serveur Azure Database pour MySQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’exécuter l’interface CLI localement, Azure CLI 2.0 ou ultérieur est indispensable pour poursuivre la procédure décrite dans cet article. Pour vérifier la version, exécutez `az --version`. Consultez l’article [Installer Azure CLI 2.0]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau votre version d’Azure CLI. 

## <a name="sample-script"></a>Exemple de script
Dans cet exemple de script, modifiez les lignes en surbrillance pour mettre à jour le nom d’utilisateur et le mot de passe d’administrateur et utiliser les vôtres. Remplacez <log_file_name> dans les commandes `az monitor` par votre propre nom de fichier journal de serveur.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=18-19 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées après l’exécution du script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes décrites dans le tableau suivant :

| **Commande** | **Remarques** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Crée un serveur MySQL qui héberge les bases de données. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Répertoriez les valeurs de configuration pour un serveur. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Mettez à jour la configuration d’un serveur. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | Répertoriez les fichiers journaux pour un serveur. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Téléchargez les fichiers journaux. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur Azure CLI : [Documentation d’Azure CLI](/cli/azure)
- Essayer des scripts supplémentaires : [Exemples Azure CLI pour Base de données Azure pour MySQL](../sample-scripts-azure-cli.md)
