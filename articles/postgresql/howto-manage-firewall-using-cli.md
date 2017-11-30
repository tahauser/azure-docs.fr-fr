---
title: "Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Décrit comment créer et gérer des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande d’Azure."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: c3cb598825477bd588a6680d5c6ddb07b72eca79
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs de gérer l’accès à un serveur Azure Database pour PostgreSQL à partir d’une adresse IP spécifique ou d’une plage d’adresses IP. À l’aide de commandes d’interface de ligne de commande Azure pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles de pare-feu pour gérer votre serveur. Pour une vue d’ensemble des règles de pare-feu Azure Database pour PostgreSQL, consultez la rubrique [Règles de pare-feu d’un serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md).
- Installer l’utilitaire de ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell dans le navigateur.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configuration des règles de pare-feu pour Azure Database pour PostgreSQL
Les commandes [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) permettent de configurer des règles de pare-feu.

## <a name="list-firewall-rules"></a>Répertorier les règles de pare-feu 
Pour répertorier les règles de pare-feu de serveur existant, exécutez la commande [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
La sortie répertorie les règles de pare-feu éventuelles, par défaut au format JSON. Vous pouvez utiliser le commutateur `--output table` pour obtenir une sortie dans un format de tableau plus lisible.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Créer une règle de pare-feu
Pour créer une règle de pare-feu sur le serveur, exécutez la commande [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create). 

En spécifiant 0.0.0.0 en tant que plage `--start-ip-address` et 255.255.255.255 en tant que plage `--end-ip-address`, l’exemple suivant permet à toutes les adresses IP d’accéder au serveur **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Pour autoriser l’accès à une seule adresse IP, fournissez la même adresse en `--start-ip-address` et en `--end-ip-address`, comme dans cet exemple.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez créée, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.

## <a name="update-firewall-rule"></a>Mettre à jour une règle de pare-feu 
Mettez à jour une règle de pare-feu existante sur le serveur à l’aide de la commande [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update). Indiquez le nom de la règle de pare-feu existante comme entrée puis les adresses IP de début et de fin à mettre à jour.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez mise à jour, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.
> [!NOTE]
> Si la règle de pare-feu n’existe pas, elle est créée par la commande de mise à jour.

## <a name="show-firewall-rule-details"></a>Afficher les détails d’une règle de pare-feu
Vous pouvez également afficher les détails d’une règle de pare-feu existante au niveau du serveur en exécutant la commande [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez spécifiée, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.

## <a name="delete-firewall-rule"></a>Supprimer une règle de pare-feu
Pour révoquer l’accès pour une plage d’adresses IP vers le serveur, supprimez une règle de pare-feu existante en exécutant la commande [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete). Indiquez le nom de la règle de pare-feu existante.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
En cas de réussite, aucun résultat ne s’affiche. En cas d’échec, le texte du message d’erreur est retourné.

## <a name="next-steps"></a>Étapes suivantes
- De même, vous pouvez utiliser un navigateur web pour [créer et gérer les règles de pare-feu Azure Database pour PostgreSQL feu à l’aide du portail Azure](howto-manage-firewall-using-portal.md).
- En savoir plus sur les [règles de pare-feu du serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md).
- Pour vous aider à vous connecter à un serveur Azure Database pour PostgreSQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md).
