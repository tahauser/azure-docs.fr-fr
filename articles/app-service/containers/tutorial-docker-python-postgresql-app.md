---
title: Créer une application web Docker Python et PostgreSQL dans Azure | Microsoft Docs
description: Découvrez comment faire fonctionner une application Docker Python dans Azure en établissant une connexion à une base de données PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 70cdbaa10d5e4ba39d4f378e05ae606a577ade99
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Créer une application web Docker Python et PostgreSQL dans Azure

Web App pour conteneurs offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel vous montre comment créer une application web Docker Python de base dans Azure. Vous connectez cette application à une base de données PostgreSQL. Ceci fait, vous disposez d’une application Python Flask s’exécutant dans un conteneur Docker sur [App Service sur Linux](app-service-linux-intro.md).

![Application Docker Python Flask dans App Service sur Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Gérer l’application dans le portail Azure

Vous pouvez suivre les étapes de cet article sur MacOS. Les instructions pour Linux et Windows sont identiques dans la plupart des cas, mais les différences ne sont pas détaillées dans ce didacticiel.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

1. [Installez Git](https://git-scm.com/)
1. [Installez Python](https://www.python.org/downloads/)
1. [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)
1. [Installez Docker Community Edition](https://www.docker.com/community-edition)

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

Exécutez les commandes suivantes pour cloner l’exemple de référentiel, puis accédez à la version *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Cet exemple de référentiel contient une application [Flask](http://flask.pocoo.org/). 

### <a name="run-the-application"></a>Exécution de l'application

> [!NOTE] 
> Plus tard, vous simplifierez ce processus en créant un conteneur Docker à utiliser avec la base de données de production.

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

![Application Python Flask s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app.png)

L’exemple d’application Flask stocke les données utilisateur dans la base de données. Si vous parvenez à inscrire un utilisateur, votre application écrit les données dans la base de données PostgreSQL locale.

À tout moment, pour arrêter le serveur Flask, tapez Ctrl+C dans le terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Création d’une base de données de production PostgreSQL

Dans cette étape, vous allez créer une base de données PostgreSQL dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Création d’une règle de pare-feu pour le serveur de base de données Azure pour PostgreSQL

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

## <a name="connect-your-python-flask-application-to-the-database"></a>Connexion de votre application Python Flask à la base de données

Dans cette étape, vous connectez votre exemple d’application Python Flask au serveur de base de données Azure pour PostgreSQL que vous avez créé.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Création d’une base de données vide et configuration d’un nouvel utilisateur de l’application de base de données

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

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Test de l’application en local sur la base de données Azure PostgreSQL

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

![Application Python Flask s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Exécution de l’application à partir d’un conteneur Docker

Créez l’image conteneur Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker affiche une confirmation de création du conteneur.

```bash
Successfully built 7548f983a36b
```

Dans la racine du référentiel, ajoutez un fichier de variable d’environnement nommé _db.env_, puis ajoutez-y les variables d’environnement de base de données suivantes. L’application se connecte à la base de données de production Azure Database pour PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Exécutez l’application à partir du conteneur Docker. La commande suivante spécifie le fichier de variables d’environnement et mappe le port Flask par défaut 5000 au port local 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

La sortie est similaire à ce que vous avez vu plus haut. Toutefois, la migration de la base de données initiale n’a plus besoin d’être effectuée et elle est donc ignorée.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

La base de données contient déjà l’inscription que vous avez créée précédemment.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Charger le conteneur Docker vers un registre de conteneurs

Dans cette étape, vous chargez le conteneur Docker dans un registre de conteneurs. Utilisez Azure Container Registry, mais vous pourriez aussi utiliser d’autres registres très répandus, comme Docker Hub.

### <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Dans la commande suivante, pour créer un registre de conteneurs, remplacez *\<registry_name>* par un nom unique de registre de conteneurs Azure de votre choix.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Sortie

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Récupération des informations d’identification du Registre pour l’extraction et la transmission d’images Docker

Pour afficher les informations d’identification du registre, activez d’abord le mode administration.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Vous voyez deux mots de passe. Notez le nom d’utilisateur et le premier mot de passe.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Chargement de votre conteneur Docker vers Azure Container Registry

Connectez-vous à votre registre. Lorsque vous y êtes invité, renseignez le mot de passe que vous venez de récupérer.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Transférez votre image Docker vers le registre.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Déploiement de l’application Docker Python Flask sur Azure

Dans cette étape, vous déployez votre application Python Flask basée sur le conteneur Docker dans Azure App Service.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

Créez une application web dans le plan App Service *myAppServicePlan* avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

L’application web vous offre un espace d’hébergement pour déployer votre code, et fournit une URL pour vous permettre d’afficher l’application déployée. Utilisez  pour créer l’application web.

Dans la commande suivante, remplacez l’espace réservé *\<app_name>* par un nom d’application unique. Ce nom est utilisé dans l’URL par défaut de l’application web. C’est pourquoi il doit être unique parmi l’ensemble des applications dans Azure App Service.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Configuration des variables d’environnement de base de données

Plus haut dans ce didacticiel, vous avez défini des variables d’environnement pour vous connecter à votre base de données PostgreSQL.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

L’exemple suivant spécifie les informations de connexion à la base de données comme des paramètres d’application. Il utilise également la variable *PORT* qui mappe le PORT 5000 de votre conteneur Docker pour qu’il reçoive le trafic HTTP sur le PORT 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Configuation du déploiement du conteneur Docker

AppService peut automatiquement télécharger et exécuter un conteneur Docker.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Chaque fois que vous mettez à jour le conteneur Docker ou modifiez les paramètres, redémarrez l’application. Le redémarrage garantit que tous les paramètres sont appliqués et que le dernier conteneur est extrait du registre.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure 

Accédez à l’application web déployée à l’aide de votre navigateur web. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> L’application web prend plus de temps à se charger, car le conteneur doit être téléchargé et démarré lorsque sa configuration est modifiée.

Vous verrez des invités déjà inscrits, qui ont été enregistrés dans la base de données de production Azure à l’étape précédente.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Félicitations !** Vous exécutez une application Python Flask basée sur un conteneur Docker dans Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous ajoutez le nombre de participants à chaque inscription d’événement en mettant à jour le modèle Invité.

Vérifiez la version *0.2-migration* avec la commande git suivante :

```bash
git checkout tags/0.2-migration
```

Cette version a déjà apporté les modifications nécessaires aux vues, aux contrôleurs et au modèle. Elle inclut également une migration de base de données générée via *alembic* (`flask db migrate`). Vous pouvez voir toutes les modifications apportées, via la commande git suivante :

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Exécutez les commandes suivantes pour tester vos modifications en local en exécutant le serveur Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Accédez à http://localhost:5000 dans votre navigateur pour afficher les modifications. Créez une inscription de test.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Générez la nouvelle image Docker, placez-la dans le registre de conteneurs et redémarrez l’application.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité. Créez un autre enregistrement d’événements.

```bash 
http://<app_name>.azurewebsites.net 
```

![Application Docker Python Flask dans Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application web. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Étapes suivantes

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](../app-service-web-tutorial-custom-domain.md)
