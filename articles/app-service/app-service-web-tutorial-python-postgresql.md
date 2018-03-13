---
title: "Créer une application web Python et PostgreSQL dans Azure | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application Python dans Azure en établissant une connexion à une base de données PostgreSQL."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: de20dae10ae6b43adcbc5040a8a71ba5650bafec
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="build-a-python-and-postgresql-web-app-in-azure"></a>Créer une application web Python et PostgreSQL dans Azure

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer une application web Docker Python et PostgreSQL dans Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

[Azure App Service](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel vous montre comment créer une application web Python de base dans Azure. Vous connectez cette application à une base de données PostgreSQL. Ceci fait, vous disposez d’une application Python Flask s’exécutant dans App Service.

![Application Python Flask dans App Service sur Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Gérer l’application dans le portail Azure

Vous pouvez suivre les étapes de ce didacticiel sur macOS. Les instructions pour Linux et Windows sont identiques dans la plupart des cas, mais les différences ne sont pas détaillées dans ce didacticiel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

1. [Installez Git](https://git-scm.com/)
1. [Installez Python](https://www.python.org/downloads/)
1. [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test de l’installation PostgreSQL locale et création d’une base de données

Ouvrez la fenêtre de terminal et exécutez `psql` pour vous connecter à votre serveur PostgreSQL local.

```bash
sudo -u postgres psql
```

Si la connexion est établie, cela signifie que votre base de données PostgreSQL est en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données PostgresQL est démarrée en suivant les étapes décrites dans la page [Downloads - PostgreSQL Core Distribution (Téléchargements - Distribution principale de PostgreSQL)](https://www.postgresql.org/download/).

Créez une base de données appelée *eventregistration* et configurez un utilisateur de base de données nommé *manager* avec le mot de passe *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Saisissez `\q` pour quitter le client PostgreSQL. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Création d’une application Python Flask locale

Cette étape consiste à configurer le projet Python Flask local.

### <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Ouvrez la fenêtre de terminal et entrez `CD` pour accéder à un répertoire de travail.

Exécutez les commandes suivantes pour cloner l’exemple de référentiel, puis rétablir à la validation de l’application initiale (avant `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Cet exemple de référentiel contient une application [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Exécution de l'application

Installez les packages requis et démarrez l’application.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Dans un navigateur, accédez à `http://localhost:5000`. Cliquez sur **S’inscrire** et créez un utilisateur de test.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-python-postgresql/local-app.png)

L’exemple d’application Flask stocke les données utilisateur dans la base de données. Si vous parvenez à inscrire un utilisateur, votre application écrit les données dans la base de données PostgreSQL locale.

À tout moment, pour arrêter le serveur Flask, tapez Ctrl+C dans le terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Créer PostgreSQL dans Azure

Dans cette étape, vous allez créer une base de données PostgreSQL dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Créer un serveur PostgreSQL

Créez un serveur PostgreSQL avec la commande [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

Dans la commande suivante, remplacez un nom de serveur unique par l’espace réservé *\<postgresql_name>* et un nom d’utilisateur par l’espace réservé *\<admin_username>*. Le nom de serveur est utilisé dans votre point de terminaison PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`). C’est pourquoi, il doit être unique parmi l’ensemble des serveurs dans Azure. Le nom d’utilisateur correspond au compte d’utilisateur administrateur de la base de données initiale. Vous êtes invité à choisir un mot de passe pour cet utilisateur.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

Lorsque le serveur de base de données Azure pour PostgreSQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Exécutez la commande Azure CLI suivante pour autoriser l’accès à la base de données à partir de toutes les adresses IP.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

L’interface Azure CLI confirme la création de la règle de pare-feu avec une sortie similaire à celle-ci :

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

### <a name="create-a-production-database-and-user"></a>Création d’un utilisateur et d’une base de données de production

Créez un utilisateur de base de données avec accès à une seule base de données. Vous utilisez ces informations d’identification pour éviter que l’application ait un accès complet au serveur.

Connectez-vous à la base de données (vous êtes invité à entrer votre mot de passe d’administrateur).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Créez la base de données et l’utilisateur à partir de l’interface de ligne de commande de PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

### <a name="test-app-locally-with-azure-postgresql"></a>Tester l’application en local avec Azure PostgreSQL

Revenons maintenant au dossier *app* du référentiel Github cloné. Vous pouvez exécuter l’application Python Flask en mettant à jour les variables d’environnement de la base de données.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Accédez à http://localhost:5000 dans un navigateur. Cliquez sur **S’inscrire** et créez une inscription de test. Vous écrivez maintenant des données dans la base de données dans Azure.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application Python connectée à PostgreSQL dans Azure App Service.

Votre référentiel Git contient déjà les fichiers suivants, dont il a besoin pour exécuter l’application web Flask dans App Service :

- `.deployment` : spécifie le script de déploiement personnalisé à exécuter.
- `deploy.cmd` : script de déploiement. C’est ici que `pip install` est exécuté.
- `web.config` : spécifie le script de point d’entrée à exécuter dans un `httpPlatformHandler` dans IIS.
- `run_waitress_server.py` : script de point d’entrée. Il démarre l’application web Flask dans un serveur [`waitress`](https://docs.pylonsproject.org/projects/waitress).

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Installation de Python

Dans cette étape, vous allez installer Python 3.6.2 avec les [extensions de site](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) dans App Service. Utilisez les informations d’identification que vous avez configurées dans [Configurer un utilisateur de déploiement](#configure-a-deployment-user) pour vous authentifier au point de terminaison REST.

Dans Cloud Shell, exécutez la commande suivante pour obtenir les informations de package de Python 3.6.2. Remplacez *\<deployment_user>* par le nom d’utilisateur de déploiement que vous avez configuré, et *\<app_name>* par le nom de votre application. Lorsque vous y êtes invité, utilisez le mot de passe de déploiement que vous avez configuré.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

Dans Cloud Shell, exécutez la commande suivante pour installer le package Python. Remplacez *\<deployment_user>* par le nom d’utilisateur de déploiement que vous avez configuré, et *\<app_name>* par le nom de votre application. Lorsque vous y êtes invité, utilisez le mot de passe de déploiement que vous avez configuré.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

À la sortie de la commande, vous pouvez voir que Python a été installé au chemin d’accès `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Configuration des paramètres de la base de données

Plus haut dans ce didacticiel, vous avez défini des variables d’environnement pour vous connecter à votre base de données PostgreSQL.

Dans App Service, vous définissez les variables d’environnement comme des _paramètres d’application_ à l’aide de la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

L’exemple suivant spécifie les informations de connexion à la base de données comme des paramètres d’application. Remplacez *\<app_name>* par le nom de votre application et *\<postgresql_name>* par le nom de votre serveur PostgreSQL.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure 

Accédez à l’application web déployée à l’aide de votre navigateur web. 

```bash 
http://<app_name>.azurewebsites.net 
```

Vous verrez des invités déjà inscrits, qui ont été enregistrés dans la base de données de production Azure à l’étape précédente.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Félicitations !** Vous avez exécuté une application Python Flask dans Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous ajoutez le nombre de participants à chaque inscription d’événement en mettant à jour le modèle `Guest`.

Vérifiez les fichiers marqués par la validation `modelChange` :

```bash
git checkout modelChange -- *
```

Cette version a déjà apporté les modifications nécessaires aux vues, aux contrôleurs et au modèle. Elle inclut également une migration de base de données générée via *alembic* (`flask db migrate`). Vous pouvez consulter les changements de tous les fichiers dans la [Vue de validation GitHubt](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e).

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Exécutez les commandes suivantes pour tester vos modifications en local en exécutant le serveur Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Accédez à http://localhost:5000 dans votre navigateur pour afficher les modifications. Créez une inscription de test.

![Application Python Flask s’exécutant localement](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans la fenêtre du terminal local, validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité. Créez un autre enregistrement d’événements.

```bash 
http://<app_name>.azurewebsites.net 
```

![Application Python Flask dans Azure App Service](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application web. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>étapes suivantes

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
