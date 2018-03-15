---
title: "Créer une application web PHP et MySQL dans Azure | Microsoft Docs"
description: "Découvrez comment faire fonctionner une application PHP dans Azure en établissant une connexion à une base de données MySQL dans Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 39bfc4e6a4f4066e8aeda0da387fe570525b6086
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Créer une application web PHP et MySQL dans Azure

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour déployer une application App Service sur _Linux_, consultez [Créer une application web PHP et MySQL dans Azure App Service sur Linux](./containers/tutorial-php-mysql-app.md).
>

[Azure Web Apps](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel vous montre comment créer une application web PHP dans Azure et comment la connecter à une base de données MySQL. Une fois terminé, vous disposerez d’une application [Laravel](https://laravel.com/) s’exécutant dans Azure App Service Web Apps.

![Application PHP exécutée dans Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application PHP à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

* [Installez Git](https://git-scm.com/)
* [Installez PHP 5.6.4 ou version ultérieure](http://php.net/downloads.php)
* [Installez Composer](https://getcomposer.org/doc/00-intro.md)
* Activation des extensions PHP suivantes requises par Laravel : OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
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

### <a name="create-a-database-locally"></a>Créer une base de données locale

À l’invite `mysql`, créez une base de données.

```sql 
CREATE DATABASE sampledb;
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Créer une application PHP locale
Dans cette étape, vous allez créer un exemple d’application Laravel, configurer sa connexion à la base de données et l’exécuter localement. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, `cd` vers un répertoire de travail.

Exécutez la commande suivante pour cloner l’exemple de référentiel :

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd` vers votre répertoire cloné.
Installez les packages requis.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configuration de la connexion MySQL

À la racine du référentiel, créez un fichier texte nommé *.env*. Copiez les variables suivantes dans le fichier *.env*. Remplacez l’espace réservé _&lt;root_password>_ par le mot de passe de l’utilisateur racine de MySQL.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Pour en savoir plus sur la manière dont Laravel utilise ce fichier _.env_, consultez [Configuration de l’environnement Laravel](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Télécharger l’exemple localement

Exécutez les [migrations de base de données Laravel](https://laravel.com/docs/5.4/migrations) pour créer les tables requises par l’application. Pour voir quelles tables sont créées dans les migrations, consultez le répertoire _database/migrations_ dans le référentiel Git.

```bash
php artisan migrate
```

Générez une nouvelle clé d’application Laravel.

```bash
php artisan key:generate
```

Exécutez l'application.

```bash
php artisan serve
```

Dans un navigateur, accédez à `http://localhost:8000`. Ajoutez quelques tâches dans la page.

![PHP se connecte correctement à MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Pour arrêter le serveur PHP, tapez `Ctrl + C` dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Création de MySQL dans Azure

Dans cette étape, vous allez créer une base de données MySQL dans [Azure Database pour MySQL (Version préliminaire)](/azure/mysql). Ensuite, vous configurerez l’application PHP pour la connexion à cette base de données.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Création d’un serveur MySQL

Dans Cloud Shell, créez un serveur dans Azure Database pour MySQL (préversion) avec la commande [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé _&lt;mysql_server_name>_ (les caractères valides sont `a-z`, `0-9` et `-`). Ce nom fait partie du nom d’hôte du serveur MySQL (`<mysql_server_name>.database.windows.net`) et doit donc être globalement unique.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

> [!NOTE]
> Dans la mesure où ce didacticiel utilise plusieurs informations d’identification, `--admin-user` et `--admin-password` sont définies sur des valeurs factices afin d’éviter toute confusion. Dans un environnement de production, suivez les meilleures pratiques de sécurité pour choisir un nom d’utilisateur et un mot de passe corrects pour votre serveur MySQL dans Azure.
>
>

Lorsque le serveur MySQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Dans Cloud Shell, créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create).

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Pour le moment, Azure Database pour MySQL (version préliminaire) ne limite pas les connexions aux services Azure seulement. Étant donné que les adresses IP sont affectées dynamiquement dans Azure, il est préférable d’activer toutes les adresses IP. Le service est en version préliminaire. Les meilleures méthodes de sécurisation de votre base de données sont planifiées.
>
>

### <a name="connect-to-production-mysql-server-locally"></a>Se connecter au serveur de production MySQL localement

Dans la fenêtre du terminal local, connectez-vous au serveur MySQL dans Azure. Utilisez la valeur spécifiée précédemment pour _&lt;mysql_server_name>_. Quand une invite de mot de passe apparaît, utilisez _My5up3r$tr0ngPa$w0rd!_, que vous avez spécifié au cours de la création de la base de données dans Azure.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Création d’une base de données de production

À l’invite `mysql`, créez une base de données.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données nommé _phpappuser_ et accordez-lui tous les privilèges dans la base de données `sampledb`. Là encore, par souci de simplicité de ce didacticiel, utilisez _MySQLAzure2017_ comme mot de passe.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Quittez la connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Connexion de l’application à Azure MySQL

Dans cette étape, vous allez connecter l’application PHP à la base de données MySQL que vous avez créée dans Azure Database pour MySQL (version préliminaire).

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurer la connexion à la base de données

À la racine du référentiel, créez un fichier _.env.production_ et copiez-y les variables suivantes. Remplacez l’espace réservé _&lt;mysql_server_name>_ dans *DB_HOST* et *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Enregistrez les modifications.

> [!TIP]
> Pour sécuriser vos informations de connexion MySQL, ce fichier est déjà exclu du référentiel Git (consultez _.gitignore_ dans la racine du référentiel). Vous apprendrez ultérieurement à configurer les variables d’environnement dans App Service pour vous connecter à votre base de données dans Azure Database pour MySQL (version préliminaire). Avec les variables d’environnement, vous n’avez pas besoin du fichier *.env* dans App Service.
>

### <a name="configure-ssl-certificate"></a>Configuration du certificat SSL

Par défaut, la base de données Azure pour MySQL applique les connexions SSL à partir des clients. Pour vous connecter à votre base de données MySQL dans Azure, vous devez utiliser le certificat [_.pem_ fourni par Azure Database pour MySQL](../mysql/howto-configure-ssl.md).

Ouvrez _config/database.php_ et ajoutez les paramètres `sslmode` et `options` à `connections.mysql`, comme illustré dans le code suivant.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

Le certificat `BaltimoreCyberTrustRoot.crt.pem` est fourni dans le référentiel pour des raisons pratiques dans ce didacticiel. 

### <a name="test-the-application-locally"></a>Tester localement l’application

Exécutez les migrations de base de données Laravel avec _.env.production_ comme fichier d’environnement pour créer les tables dans votre base de données MySQL dans Azure Database pour MySQL (version préliminaire). N’oubliez pas que _. env.production_ contient les informations de connexion à votre base de données MySQL dans Azure.

```bash
php artisan migrate --env=production --force
```

_.env.production_ n’a pas encore de clé d’application valide. Générez-en une pour lui dans le terminal.

```bash
php artisan key:generate --env=production --force
```

Exécutez l’exemple d’application avec _.env.production_ comme fichier d’environnement.

```bash
php artisan serve --env=production
```

Accédez à `http://localhost:8000`. Si la page se charge sans erreur, l’application PHP se connecte à la base de données MySQL dans Azure.

Ajoutez quelques tâches dans la page.

![PHP se connecte correctement à Azure Database pour MySQL (version préliminaire)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Pour arrêter PHP, tapez `Ctrl + C` dans le terminal.

### <a name="commit-your-changes"></a>Validation de vos modifications

Exécutez les commandes Git suivantes pour valider vos modifications :

```bash
git add .
git commit -m "database.php updates"
```

Votre application est prête à être déployée.

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application PHP connectée à MySQL dans Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Configuration des paramètres de la base de données

Comme indiqué précédemment, vous pouvez vous connecter à votre base de données Azure MySQL en utilisant des variables d’environnement dans App Service.

Dans Cloud Shell, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

La commande suivante configure les paramètres d’application `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` et `DB_PASSWORD`. Remplacez les espaces réservés _&lt;appname>_ et _&lt;mysql_server_name>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Vous pouvez utiliser la méthode PHP [getenv](http://www.php.net/manual/function.getenv.php) pour accéder aux paramètres. Votre code Laravel utilise un wrapper [env](https://laravel.com/docs/5.4/helpers#method-env) sur le PHP `getenv`. Par exemple, la configuration MySQL dans _config/database.php_ ressemble au code suivant :

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configuration des variables d’environnement Laravel

Laravel a besoin d’une clé d’application dans App Service. Vous pouvez la configurer avec les paramètres d’application.

Dans la fenêtre de terminal local, utilisez `php artisan` pour générer une nouvelle clé d’application sans l’enregistrer dans _.env_.

```bash
php artisan key:generate --show
```

Dans Cloud Shell, définissez la clé d’application dans l’application web App Service en utilisant la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set). Remplacez les espaces réservés _&lt;appname>_ et _&lt;outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` indique à Laravel de renvoyer les informations de débogage lorsque l’application web déployée rencontre des erreurs. Lorsque vous exécutez une application de production, affectez-lui la valeur `false`, qui est plus sécurisée.

### <a name="set-the-virtual-application-path"></a>Définition du chemin d’accès de l’application virtuelle

Définissez le chemin d’accès de l’application virtuelle pour l’application web. Cette étape est requise car le [cycle de vie de l’application Laravel](https://laravel.com/docs/5.4/lifecycle) commence dans le répertoire _public_ et non pas dans le répertoire racine de votre application. Les autres infrastructures PHP dont le cycle de vie démarre dans le répertoire racine peuvent fonctionner sans configuration manuelle du chemin d’accès à l’application virtuelle.

Dans Cloud Shell, définissez le chemin d’accès à l’application virtuelle en utilisant la commande [`az resource update`](/cli/azure/resource#az_resource_update). Remplacez l’espace réservé _&lt;appname>_.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Par défaut, Azure App Service pointe le chemin d’accès à l’application virtuelle (_/_) vers le répertoire racine des fichiers d’application déployés (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

> [!NOTE]
> Vous remarquerez peut-être que le processus de déploiement installe les packages [Composer](https://getcomposer.org/) à la fin. App Service n’exécute pas ces automatisations pendant le déploiement par défaut. Cet exemple de référentiel possède donc trois fichiers supplémentaires dans son répertoire racine pour l’activer :
>
> - `.deployment` : ce fichier indique à App Service d’exécuter `bash deploy.sh` en tant que script de déploiement personnalisé.
> - `deploy.sh` : le script de déploiement personnalisé. Si vous examinez le fichier, vous verrez qu’il exécute `php composer.phar install` après `npm install`.
> - `composer.phar` - Le Gestionnaire de package Composer.
>
> Vous pouvez utiliser cette approche pour ajouter une étape à votre déploiement Git sur App Service. Pour plus d'informations, consultez le [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à `http://<app_name>.azurewebsites.net` et ajoutez quelques tâches à la liste.

![Application PHP exécutée dans Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Félicitations, vous exécutez une application PHP orientée données dans Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Mettre à jour et redéployer un modèle localement

Dans cette étape, vous allez apporter une modification simple au modèle de données `task` et à l’application web, puis publier la mise à jour sur Azure.

Pour le scénario des tâches, vous modifiez l’application afin de pouvoir marquer une tâche comme terminée.

### <a name="add-a-column"></a>Ajout d’une colonne

Dans la fenêtre de terminal local, accédez à la racine du référentiel Git.

Générez une nouvelle migration de base de données pour la table `tasks` :

```bash
php artisan make:migration add_complete_column --table=tasks
```

Cette commande affiche le nom du fichier de migration qui est généré. Recherchez ce fichier dans _database/migrations_ et ouvrez-le.

Remplacez la méthode `up` par le code suivant :

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Le code ci-dessus ajoute une colonne booléenne dans la table `tasks` nommée `complete`.

Remplacez la méthode `down` par le code suivant pour l’action de restauration :

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Dans la fenêtre de terminal local, exécutez les migrations de base de données Laravel pour apporter la modification dans la base de données locale.

```bash
php artisan migrate
```

Selon la [convention d’affectation de noms Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), le modèle `Task` (voir _app/Task.php_) est mappé à la table `tasks` par défaut.

### <a name="update-application-logic"></a>Mise à jour de la logique d’application

Ouvrez le fichier *routes/web.php*. L’application définit ici ses itinéraires et sa logique métier.

À la fin du fichier, ajoutez un itinéraire avec le code suivant :

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Le code ci-dessus effectue une simple mise à jour du modèle de données en basculant la valeur de `complete`.

### <a name="update-the-view"></a>Mise à jour de la vue

Ouvrez le fichier *resources/views/tasks.blade.php*. Recherchez la balise d’ouverture `<tr>` et remplacez-la par :

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Le code ci-dessus modifie la couleur de la ligne selon que la tâche est terminée ou non.

La ligne suivante contient le code suivant :

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Remplacez la ligne entière par le code suivant :

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Le code ci-dessus ajoute le bouton Envoyer qui fait référence à l’itinéraire défini précédemment.

### <a name="test-the-changes-locally"></a>Test des modifications en local

Dans la fenêtre de terminal local, exécutez le serveur de développement à partir du répertoire racine du référentiel Git.

```bash
php artisan serve
```

Pour voir l’évolution de l’état de la tâche, accédez à `http://localhost:8000` et activez la case à cocher.

![Case à cocher ajoutée à la tâche](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

Pour arrêter PHP, tapez `Ctrl + C` dans le terminal.

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans la fenêtre de terminal local, exécutez les migrations de base de données Laravel avec la chaîne de connexion de production pour apporter la modification dans la base de données Azure.

```bash
php artisan migrate --env=production --force
```

Validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Une fois le `git push` terminé, accédez à l’application web Azure et essayez la nouvelle fonctionnalité.

![Modifications du modèle et de la base de données publiées dans Azure](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Si vous avez ajouté des tâches, celles-ci sont conservées dans la base de données. Les mises à jour appliquées au schéma de données n’affectent pas les données existantes.

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

Pendant l’exécution de l’application PHP dans Azure App Service, vous pouvez acheminer les journaux de la console vers votre terminal. De cette façon, vous pouvez obtenir les mêmes messages de diagnostic pour vous aider à déboguer les erreurs d’application.

Pour démarrer la diffusion de journaux, utilisez la commande [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) dans Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Une fois que la diffusion a démarré, actualisez l’application web Azure dans le navigateur pour générer un trafic web. Vous pouvez maintenant voir les journaux de la console acheminés vers le terminal. Si vous ne voyez pas les journaux de la console, attendez 30 secondes et vérifiez à nouveau.

Pour arrêter la diffusion de journaux à tout moment, tapez `Ctrl`+`C`.

> [!TIP]
> Une application PHP peut utiliser la commande [error_log()](http://php.net/manual/function.error-log.php) pour envoyer le résultat vers la console. L’exemple d’application utilise cette approche dans _app/Http/routes.php_.
>
> [Laravel utilise Monolog](https://laravel.com/docs/5.4/errors) comme fournisseur de journalisation, de la même manière qu’une infrastructure web. Pour savoir comment faire en sorte que Monolog envoie les messages vers la console, consultez [PHP: How to use monolog to log to console (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) (PHP : utilisation de Monolog pour envoyer le journal vers la console (php://out)).
>
>

## <a name="manage-the-azure-web-app"></a>Gérer l’application web Azure

Accédez au [Portail Azure](https://portal.azure.com) pour gérer l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez effectuer des tâches de gestion de base (arrêter, démarrer, redémarrer, parcourir et supprimer).

Le menu de gauche fournit des pages vous permettant de configurer votre application.

![Page App Service du Portail Azure](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connexion d’une application PHP à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à une application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
