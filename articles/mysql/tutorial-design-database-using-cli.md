---
title: "Concevoir votre première base de données Azure pour MySQL - Azure CLI | Microsoft Docs"
description: "Ce didacticiel explique comment créer et gérer un serveur et une base de données Azure Database pour MySQL avec Azure CLI 2.0 en ligne de commande."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/28/2017
ms.custom: mvc
ms.openlocfilehash: f17f2cab39b42341886ed86e1c08569ca8f5eff0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
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
Créez un [groupe de ressources Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources nommé `mycliresource` à l’emplacement `westus`.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL
Créez un serveur Azure Database pour MySQL avec la commande az sql server create. Un serveur peut gérer plusieurs bases de données. En règle générale, une base de données distincte est utilisée pour chaque projet ou pour chaque utilisateur.

L’exemple suivant crée un serveur Azure Database pour MySQL situé dans `westus` dans le groupe de ressources `mycliresource` avec le nom `mycliserver`. Le serveur dispose d’une connexion administrateur avec le nom `myadmin` et le mot de passe `Password01!`. Le serveur est créé avec le niveau de performance **De base** et **50** unités de calcul partagées entre toutes les bases de données dans le serveur. Vous pouvez faire augmenter ou diminuer le calcul et le stockage selon les besoins de l’application.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu
Créez une règle de pare-feu au niveau du serveur Azure Database pour MySQL avec la commande az mysql server firewall-rule create. Une règle de pare-feu au niveau du serveur permet à une application externe, comme l’outil de ligne de commande **mysql** ou MySQL Workbench de se connecter à votre serveur via le pare-feu du service Azure MySQL. 

L’exemple suivant crée une règle de pare-feu pour une plage d’adresses prédéfinie. Cet exemple montre l’intégralité de la plage possible d’adresses IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Obtenir des informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.
```azurecli-interactive
az mysql server show --resource-group mycliresource --name mycliserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-mysql"></a>Se connecter au serveur à l’aide de mysql
Utilisez [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour établir une connexion à votre serveur Azure Database pour MySQL. Dans cet exemple, la commande est :
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
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
Maintenant que vous savez comment vous connecter à la base de données Azure Database pour MySQL, effectuez certaines tâches de base :

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
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

Pour restaurer le serveur [à un point dans le temps](./howto-restore-server-portal.md) avant la suppression de la table. La restauration d’un serveur à un autre point dans le temps crée un serveur en double par rapport au serveur d’origine, à condition qu’il se trouve au sein de la période de rétention de votre [niveau de service](./concepts-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une base de données Azure pour MySQL
> * Configurer le pare-feu du serveur
> * Utiliser [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI samples](./sample-scripts-azure-cli.md) (Base de données Azure pour MySQL - Exemples avec Azure CLI)
