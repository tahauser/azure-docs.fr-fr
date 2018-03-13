---
title: "Concevoir votre première base de données Azure Database pour MySQL (Azure CLI)"
description: "Ce didacticiel explique comment créer et gérer un serveur et une base de données Azure Database pour MySQL avec Azure CLI 2.0 en ligne de commande."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: a609bbdf70599d0cceaf988a9a0bef51bc28716d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Concevoir votre première base de données Azure pour MySQL

Azure Database pour MySQL est un service de base de données relationnelle dans le cloud de Microsoft basé sur le moteur de base de données MySQL Community Edition. Dans ce didacticiel, vous allez utiliser l’interface Azure CLI (interface de ligne de commande) et d’autres utilitaires pour apprendre à :

> [!div class="checklist"]
> * Créer une base de données Azure pour MySQL
> * Configurer le pare-feu du serveur
> * Utiliser [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

Vous pouvez utiliser Azure Cloud Shell dans le navigateur, ou [installer Azure CLI 2.0]( /cli/azure/install-azure-cli) sur votre ordinateur pour exécuter les blocs de code de ce didacticiel.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Si vous possédez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource existe ou est facturée. Sélectionnez un ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Ajouter l’extension
Ajoutez l’extension de gestion Azure Database pour MySQL mise à jour à l’aide de la commande suivante :
```azurecli-interactive
az extension add --name rdbms
``` 

Vérifiez que vous avez installé la version d’extension appropriée. 
```azurecli-interactive
az extension list
```

Le retour JSON doit inclure les éléments suivants : 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.3"
}
```

Si la version 0.0.3 n’est pas retournée, exécutez la commande suivante pour mettre à jour l’extension : 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Créez un serveur Azure Database pour MySQL avec la commande az sql server create. Un serveur peut gérer plusieurs bases de données. En règle générale, une base de données distincte est utilisée pour chaque projet ou pour chaque utilisateur.

L’exemple suivant crée un serveur Azure Database pour MySQL situé dans `westus` dans le groupe de ressources `myresourcegroup` avec le nom `mydemoserver`. Le serveur dispose d’une connexion administrateur avec le nom `myadmin`. Il s’agit d’un serveur à usage général, de 4e génération avec 2 vCores. Remplacez `<server_admin_password>` par votre propre valeur.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.


## <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu
Créez une règle de pare-feu au niveau du serveur Azure Database pour MySQL avec la commande az mysql server firewall-rule create. Une règle de pare-feu au niveau du serveur permet à une application externe, comme l’outil de ligne de commande **mysql** ou MySQL Workbench de se connecter à votre serveur via le pare-feu du service Azure MySQL. 

L’exemple suivant crée une règle de pare-feu pour une plage d’adresses prédéfinie. Cet exemple montre l’intégralité de la plage possible d’adresses IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Obtenir des informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Se connecter au serveur à l’aide de mysql
Utilisez [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour établir une connexion à votre serveur Azure Database pour MySQL. Dans cet exemple, la commande est :
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Créer une base de données vide
Une fois que vous êtes connecté au serveur, créez une base de données vide.
```sql
mysql> CREATE DATABASE mysampledb;
```

À l’invite, exécutez la commande suivante pour basculer la connexion sur la base de données nouvellement créée :
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Créer des tables dans la base de données
Maintenant que vous savez vous connecter à la base de données Azure Database pour MySQL, effectuez certaines tâches de base.

Tout d’abord, créez une table et chargez-y des données. Nous allons créer une table qui stocke des données d’inventaire.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables
Maintenant que vous disposez d’une table, insérez-y des données. Dans la fenêtre d’invite de commandes ouverte, exécutez la requête suivante pour insérer des lignes de données.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Vous avez maintenant chargé deux lignes de données dans la table que vous avez créée précédemment.

## <a name="query-and-update-the-data-in-the-tables"></a>Interroger et mettre à jour les données des tables
Exécutez la requête suivante pour récupérer des informations à partir de la table de base de données.
```sql
SELECT * FROM inventory;
```

Vous pouvez également mettre à jour les données des tables.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La ligne est mise à jour en conséquence lorsque vous récupérez les données.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurer une version antérieure d’une base de données
Imaginez que vous avez supprimé cette table par erreur. Il s’agit de quelque chose que vous ne pouvez pas récupérer facilement. La base de données Azure pour MySQL vous permet de revenir à n’importe quel moment dans le temps au cours de ces 35 derniers jours et de procéder à une restauration à ce point dans le temps vers un nouveau serveur. Vous pouvez alors utiliser ce nouveau serveur pour récupérer les données supprimées. Les étapes suivantes restaurent le serveur à l’état dans lequel il était avant l’ajout de la table.

Pour effectuer la restauration, vous avez besoin des informations suivantes :

- Point de restauration : sélectionnez un point dans le temps avant la modification du serveur. Doit être supérieure ou égale à la plus ancienne valeur de sauvegarde de la base de données source.
- Serveur cible : spécifiez un nouveau nom de serveur sur lequel vous souhaitez effectuer la restauration
- Serveur source : indiquez le nom du serveur à partir duquel vous voulez effectuer la restauration
- Emplacement : vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

La commande `az mysql server restore` a besoin des paramètres suivants :
| Paramètre | Valeur suggérée | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Groupe de ressources dans lequel se trouve le serveur source.  |
| Nom | mydemoserver-restored | Nom du serveur créé par la commande de restauration. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Choisissez la date et l’heure à utiliser pour la restauration. Elles doivent être comprises dans la période de rétention de la sauvegarde du serveur source. Utilisez le format de date et d’heure ISO8601. Par exemple, vous pouvez utiliser votre propre fuseau horaire local, comme `2017-04-13T05:59:00-08:00`, ou le format UTC `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Nom ou identifiant du serveur source à partir duquel la restauration s’effectuera. |

La restauration d’un serveur à un point antérieur dans le temps entraîne la création d’un nouveau serveur, qui est la copie du serveur d’origine tel qu’il était à l’instant spécifié. Les valeurs d’emplacement et de niveau tarifaire du serveur restauré sont les mêmes que celles du serveur source.

La commande est synchrone ; elle est renvoyée après la restauration du serveur. Une fois la restauration terminée, recherchez le serveur créé. Vérifiez que les données ont été restaurées comme prévu.

## <a name="next-steps"></a>étapes suivantes
Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Création d’un serveur Azure Database pour MySQL
> * Configurer le pare-feu du serveur
> * Utiliser [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI samples](./sample-scripts-azure-cli.md) (Base de données Azure pour MySQL - Exemples avec Azure CLI)
