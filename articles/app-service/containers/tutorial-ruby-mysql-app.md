---
title: "Créer une application web Ruby et MySQL dans Azure App Service sur Linux | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application Ruby dans Azure en établissant une connexion à une base de données MySQL dans Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 951e66e47cf8fbe9d2cdf1606a8d63054bcada13
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Créer une application web Ruby et MySQL dans Azure App Service sur Linux

[App Service sur Linux](app-service-linux-intro.md) fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux. Ce didacticiel vous explique comment créer une application web Ruby et comment la connecter à une base de données MySQL. Une fois terminé, vous disposerez d’une application [Ruby on Rails](http://rubyonrails.org/) s’exécutant dans App Service sur Linux.

![Application Ruby on Rails s’exécutant dans Azure App Service](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application Ruby on Rails à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

* [Installez Git](https://git-scm.com/)
* [Installez Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [Installez Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Installez et démarrez MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Préparation du MySQL local

Dans cette étape, vous allez créer une base de données dans votre serveur MySQL local qui vous sera utile dans ce didacticiel.

### <a name="connect-to-local-mysql-server"></a>Se connecter au serveur MySQL local

Dans une fenêtre de terminal, connectez-vous à votre serveur MySQL local. Vous pouvez utiliser cette fenêtre de terminal pour exécuter toutes les commandes de ce didacticiel.

```bash
mysql -u root -p
```

Si vous êtes invité à entrer un mot de passe, tapez le mot de passe du compte `root`. Si vous avez oublié votre mot de passe de compte racine, consultez [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL : réinitialisation du mot de passe racine).

Si la commande est exécutée correctement, votre serveur MySQL est en cours d’exécution. Dans le cas contraire, assurez-vous que votre serveur MySQL local est démarré en suivant ces [étapes consécutives à l’installation de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Créer une application Ruby on Rails localement
Dans cette étape, vous allez créer un exemple d’application Ruby on Rails, configurer sa connexion à la base de données et l’exécuter localement. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.

Exécutez la commande suivante pour cloner l’exemple de référentiel :

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd` vers votre répertoire cloné. Installez les packages requis.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>Configuration de la connexion MySQL

Dans le référentiel, ouvrez _config/database.yml_ et indiquez le nom d’utilisateur et le mot de passe racines de l’instance MySQL locale (ligne 13). Si vous avez installé MySQL à l’aide d’un outil tel que [Homebrew](https://brew.sh/), les informations d’identification indiquées dans le fichier sont déjà définies sur les valeurs par défaut (à savoir : `root` et un mot de passe vide).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>Télécharger l’exemple localement

Exécutez les [migrations de Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) pour créer les tables requises par l’application. Pour identifier les tables créées dans les migrations, consultez le répertoire _db/migrate_ dans le dépôt Git.

```bash
rake db:create
rake db:migrate
```

Exécutez l'application.

```bash
rails server
```

Dans un navigateur, accédez à `http://localhost:3000`. Ajoutez quelques tâches dans la page.

![Ruby on Rails se connecte correctement à MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Création de MySQL dans Azure

Dans cette étape, vous allez créer une base de données MySQL dans [Azure Database pour MySQL (Version préliminaire)](/azure/mysql). Vous configurerez ensuite l’application Ruby on Rails pour la connexion à cette base de données.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Création d’un serveur MySQL

Créez un serveur Azure Database pour MySQL (préversion) avec la commande [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé _&lt;mysql_server_name>_ (les caractères valides sont `a-z`, `0-9` et `-`). Ce nom fait partie du nom d’hôte du serveur MySQL (`<mysql_server_name>.mysql.database.azure.com`) et doit donc être globalement unique.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

Lorsque le serveur MySQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Pour le moment, Azure Database pour MySQL (version préliminaire) ne limite pas les connexions aux services Azure seulement. Étant donné que les adresses IP sont affectées dynamiquement dans Azure, il est préférable d’activer toutes les adresses IP. Le service est en version préliminaire. Les meilleures méthodes de sécurisation de votre base de données sont planifiées.
>

### <a name="connect-to-production-mysql-server-locally"></a>Se connecter au serveur de production MySQL localement

Dans la fenêtre de terminal, connectez-vous au serveur MySQL dans Azure. Utilisez la valeur spécifiée précédemment pour _&lt;mysql_server_name>_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Quand une invite de mot de passe apparaît, utilisez _My5up3r$tr0ngPa$w0rd!_, que vous avez spécifié au cours de la création du serveur de base de données.

### <a name="create-a-production-database"></a>Création d’une base de données de production

À l’invite `mysql`, créez une base de données.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données nommé _railsappuser_ et accordez-lui tous les privilèges dans la base de données `sampledb`.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Quittez la connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Connexion de l’application à Azure MySQL

Dans cette étape, vous allez connecter l’application Ruby on Rails à la base de données MySQL que vous avez créée dans Azure Database pour MySQL (préversion).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurer la connexion à la base de données

Dans le dépôt, ouvrez _config/database.yml_. En bas du fichier, remplacez les variables de production par le code suivant. Pour l’espace réservé _&lt;mysql_server_name>_, utilisez le nom du serveur MySQL que vous avez créé.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Enregistrez les modifications.

> [!NOTE]
> La propriété `sslca` est ajoutée et pointe vers un fichier _.pem_ existant dans l’exemple de dépôt. Par défaut, la base de données Azure pour MySQL applique les connexions SSL à partir des clients. Ce certificat `.pem` indique comment établir des connexions SSL avec Azure Database pour MySQL. Pour plus d’informations, consultez l’article [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](../../mysql/howto-configure-ssl.md).
>

### <a name="test-the-application-locally"></a>Tester localement l’application

Dans le terminal local, définissez les variables d’environnement suivantes :

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Exécutez les migrations de base de données Rails avec les valeurs de production que vous venez de configurer pour créer les tables dans votre base de données MySQL dans Azure Database pour MySQL (préversion). 

```bash
rake db:migrate RAILS_ENV=production
```

Durant l’exécution dans l’environnement de production, l’application Rails nécessite des ressources précompilées. Générez les ressources nécessaires à l’aide de la commande suivante :

```bash
rake assets:precompile
```

L’environnement de production Rails utilise également des secrets pour gérer la sécurité. Générez une clé secrète.

```bash
rails secret
```

Enregistrez la clé secrète dans les variables respectives utilisées par l’environnement de production Rails. Pour plus de commodité, utilisez la même clé pour les deux variables.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Activez l’environnement de production Rails pour traiter les fichiers CSS et JavaScript.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Exécutez l’exemple d'application dans l'environnement de production.

```bash
rails server -e production
```

Accédez à `http://localhost:3000`. Si la page se charge sans erreur, l’application Ruby on Rails se connecte à la base de données MySQL dans Azure.

Ajoutez quelques tâches dans la page.

![Ruby on Rails se connecte correctement à Azure Database pour MySQL (préversion)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

### <a name="commit-your-changes"></a>Validation de vos modifications

Exécutez les commandes Git suivantes pour valider vos modifications :

```bash
git add .
git commit -m "database.yml updates"
```

Votre application est prête à être déployée.

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application Rails connectée à MySQL dans Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

Dans Cloud Shell, créez une application web dans le plan App Service `myAppServicePlan` avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). 

Dans l’exemple suivant, remplacez `<app_name>` par un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). Le runtime est défini sur `RUBY|2.3`, qui déploie l’[image Ruby par défaut](https://hub.docker.com/r/appsvc/ruby/). Pour voir tous les runtimes, exécutez [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vous avez créé une application web vide, avec le déploiement Git activé.

> [!NOTE]
> L’URL du Git distant est indiquée dans la propriété `deploymentLocalGitUrl`, avec le format `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Enregistrez cette URL, car vous en aurez besoin ultérieurement.
>

### <a name="configure-database-settings"></a>Configuration des paramètres de la base de données

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell.

La commande Cloud Shell suivante configure les paramètres d’application `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` et `DB_PASSWORD`. Remplacez les espaces réservés _&lt;appname>_ et _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Configuration des variables d’environnement Rails

Dans le terminal local, générez une nouvelle clé secrète pour l’environnement de production Rails dans Azure.

```bash
rails secret
```

Configurez les variables requises par l’environnement de production Rails.

Dans la commande Cloud Shell suivante, remplacez les deux espaces réservés _&lt;output_of_rails_secret>_ par la nouvelle clé secrète que vous avez générée dans le terminal local.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` indique le conteneur Ruby par défaut à utiliser pour précompiler les ressources à chaque déploiement Git.

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

Dans la fenêtre du terminal local, ajoutez un référentiel distant Azure à votre dépôt Git local.

```bash
git remote add azure <paste_copied_url_here>
```

Effectuez une transmission de type push vers le référentiel distant Azure pour déployer l’application Ruby on Rails. Le mot de passe que vous avez fourni précédemment dans le cadre de la création de l’utilisateur du déploiement vous est demandé.

```bash
git push azure master
```

Au cours du déploiement, Azure App Service communique sa progression avec Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à `http://<app_name>.azurewebsites.net` et ajoutez quelques tâches à la liste.

![Application Ruby on Rails s’exécutant dans Azure App Service](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Félicitations, vous exécutez une application Ruby on Rails orientée données dans Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Mettre à jour et redéployer un modèle localement

Dans cette étape, vous allez apporter une modification simple au modèle de données `task` et à l’application web, puis publier la mise à jour sur Azure.

Pour le scénario des tâches, vous modifiez l’application afin de pouvoir marquer une tâche comme terminée.

### <a name="add-a-column"></a>Ajout d’une colonne

Dans le terminal, accédez à la racine du référentiel Git.

Générez une nouvelle migration qui ajoute une colonne booléenne appelée `Done` à la table `Tasks` :

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Cette commande génère un nouveau fichier de migration dans le répertoire _db/migrate_.


Dans le terminal, exécutez les migrations de base de données Rails pour apporter la modification dans la base de données locale.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Mise à jour de la logique d’application

Ouvrez le fichier *app/controllers/tasks_controller.rb*. Ajoutez la ligne suivante à la fin du fichier :

```rb
params.require(:task).permit(:Description)
```

Modifiez cette ligne pour inclure le nouveau paramètre `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Mise à jour des vues

Ouvrez le fichier *app/views/tasks/_form.html.erb*, qui est le formulaire d’édition.

Recherchez la ligne `<%=f.error_span(:Description) %>` et insérez le code suivant juste en dessous :

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Ouvrez le fichier *app/views/tasks/show.html.erb*, qui est la page de vue à enregistrement unique. 

Recherchez la ligne `<dd><%= @task.Description %></dd>` et insérez le code suivant juste en dessous :

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Ouvrez le fichier *app/views/tasks/index.html.erb*, qui est la page d’index pour tous les enregistrements.

Recherchez la ligne `<th><%= model_class.human_attribute_name(:Description) %></th>` et insérez le code suivant juste en dessous :

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Dans le même fichier, recherchez la ligne `<td><%= task.Description %></td>` et insérez le code suivant juste en dessous :

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Test des modifications en local

Dans le terminal local, exécutez le serveur Rails.

```bash
rails server
```

Pour afficher le changement d’état de la tâche, accédez à `http://localhost:3000`, puis ajoutez ou modifiez des éléments.

![Case à cocher ajoutée à la tâche](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Pour arrêter le serveur Rails, tapez `Ctrl + C` dans le terminal.

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans le terminal, exécutez les migrations de base de données Rails pour l’environnement de production afin d’apporter la modification dans la base de données Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Une fois le `git push` terminé, accédez à l’application web Azure et essayez la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Si vous avez ajouté des tâches, celles-ci sont conservées dans la base de données. Les mises à jour appliquées au schéma de données n’affectent pas les données existantes.

## <a name="manage-the-azure-web-app"></a>Gérer l’application web Azure

Accédez au [Portail Azure](https://portal.azure.com) pour gérer l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/tutorial-php-mysql-app/access-portal.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez effectuer des tâches de gestion de base (arrêter, démarrer, redémarrer, parcourir et supprimer).

Le menu de gauche fournit des pages vous permettant de configurer votre application.

![Page App Service du Portail Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application Ruby on Rails à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à une application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](../app-service-web-tutorial-custom-domain.md)
