---
title: "Créer une application web Java et MySQL dans Azure"
description: "Découvrez comment obtenir une application Java qui se connecte au service de base de données Azure MySQL dans Azure App Service."
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 2df08c8e3dbadbfc1a9d2cfb3adcda4f5bae2851
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Créer une application web Java et MySQL dans Azure

> [!NOTE]
> Cet article explique comment déployer une application sur App Service sous Windows. Pour effectuer un déploiement sur App Service sous _Linux_, consultez la page [Déployer une application Spring Boot en conteneur sur Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin).
>

Ce didacticiel vous montre comment créer une application web Java dans Azure et comment la connecter à une base de données MySQL. Lorsque vous avez terminé, une application [Spring Boot](https://projects.spring.io/spring-boot/) stockera des données dans [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) s’exécutant sous [Azure App Service Web Apps](app-service-web-overview.md).

![Application Java s’exécutant dans Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connecter une application exemple à la base de données
> * Déploiement de l’application dans Azure
> * Mise à jour et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Surveiller l’application dans le portail Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

1. [Téléchargement et installation de Git](https://git-scm.com/)
1. [Téléchargement et installation du JDK Java 7 ou version ultérieure](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [Téléchargement, installation et démarrage de MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Préparation du MySQL local 

Dans cette étape, vous allez créer une base de données dans un serveur MySQL local pour tester l’application en local sur votre ordinateur.

### <a name="connect-to-mysql-server"></a>Connexion au serveur MySQL

Dans une fenêtre de terminal, connectez-vous à votre serveur MySQL local. Vous pouvez utiliser cette fenêtre de terminal pour exécuter toutes les commandes de ce didacticiel.

```bash
mysql -u root -p
```

Si vous êtes invité à entrer un mot de passe, tapez le mot de passe du compte `root`. Si vous avez oublié votre mot de passe de compte racine, consultez [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) (MySQL : réinitialisation du mot de passe racine).

Si la commande est exécutée correctement, votre serveur MySQL est déjà en cours d’exécution. Dans le cas contraire, assurez-vous que votre serveur MySQL local est démarré en suivant ces [étapes consécutives à l’installation de MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database"></a>Créer une base de données 

Dans l’invite `mysql`, créez une base de données et une table pour les éléments d’action.

```sql
CREATE DATABASE tododb;
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>Création et exécution de l’application exemple 

Dans cette étape, vous allez cloner l’application Spring Boot, la configurer pour utiliser la base de données locale MySQL et l’exécuter sur votre ordinateur. 

### <a name="clone-the-sample"></a>Clonage de l’exemple

Dans la fenêtre de terminal, accédez à un répertoire de travail et clonez l’exemple de référentiel. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>Configuration de l’application pour utiliser la base de données MySQL

Mettez à jour `spring.datasource.password` et la valeur dans *spring-boot-mysql-todo/src/main/resources/application.properties* avec le mot de passe racine utilisé pour ouvrir l’invite MySQL :

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Générez et exécutez l’exemple à l’aide du wrapper Maven inclus dans le référentiel :

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

Ouvrez votre navigateur sur `http://localhost:8080` pour voir l’exemple en action. Lorsque vous ajoutez des tâches à la liste, utilisez les commandes SQL suivantes dans l’invite MySQL pour afficher les données stockées dans MySQL.

```SQL
use testdb;
select * from todo_item;
```

Arrêtez l’application en appuyant sur `Ctrl`+`C` dans le terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Création d’une base de données Azure MySQL

À cette étape, vous allez créer une instance [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) à l’aide de [l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Vous configurez l’exemple d’application pour utiliser cette base de données par la suite dans le didacticiel.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [`az group create`](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique où les ressources associées comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé dans la région Europe du Nord :

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

Pour connaître les valeurs possibles que vous pouvez utiliser pour `--location`, utilisez la commande [`az appservice list-locations`](/cli/azure/appservice#list-locations).

### <a name="create-a-mysql-server"></a>Création d’un serveur MySQL

Dans Cloud Shell, créez un serveur dans Azure Database pour MySQL (préversion) avec la commande [`az mysql server create`](/cli/azure/mysql/server#az_mysql_server_create). Indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé `<mysql_server_name>`. Ce nom fait partie du nom d’hôte de votre serveur MySQL, `<mysql_server_name>.mysql.database.azure.com`, et doit donc être globalement unique. Remplacez également `<admin_user>` et `<admin_password>` par vos propres valeurs.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

Lorsque le serveur MySQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Dans Cloud Shell, créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create). 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure Database pour MySQL (version préliminaire) ne permet pas encore les connexions automatiques à partir des services Azure. Étant donné que les adresses IP sont affectées dynamiquement dans Azure, il est préférable d’activer toutes les adresses IP pour le moment. Le service est pour le moment disponible en version préliminaire, mais de meilleures méthodes de sécurisation de votre base de données seront activées.

## <a name="configure-the-azure-mysql-database"></a>Configurer la base de données Azure MySQL

Dans la fenêtre du terminal local, connectez-vous au serveur MySQL dans Azure. Utilisez la valeur que vous avez spécifiée précédemment pour `<admin_user>` et `<mysql_server_name>`.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>Créer une base de données 

Dans l’invite `mysql`, créez une base de données et une table pour les éléments d’action.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>Création d’un utilisateur avec des autorisations

Créez un utilisateur de base de données et accordez-lui tous les privilèges dans la base de données `tododb`. Remplacez les espaces réservés `<Javaapp_user>` et `<Javaapp_password>` avec le nom de votre propre application unique.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

Quittez votre connexion au serveur en tapant `quit`.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Déployer l’exemple dans Azure App Service

Créez un plan Azure App Service avec le niveau tarifaire **GRATUIT** à l’aide de la commande CLI [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create). Le plan App Service définit les ressources physiques utilisées pour héberger vos applications. Toutes les applications affectées à un plan App Service partagent ces ressources, ce qui vous permet de réduire les coûts lors de l’hébergement de plusieurs applications. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Lorsque le plan est prêt, l’interface de ligne de commande Azure affiche une sortie similaire à l’exemple suivant :

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Création d’une application web Azure

Dans Cloud Shell, utilisez la commande CLI [`az webapp create`](/cli/azure/appservice/web#az_appservice_web_create) pour créer une définition d’application web dans le plan App Service `myAppServicePlan`. La définition d’application web fournit une URL pour accéder à votre application et permet de configurer plusieurs options pour déployer votre code dans Azure. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

Remplacez l’espace réservé `<app_name>` avec votre propre nom d’application unique. Ce nom unique est utilisé dans le nom de domaine par défaut de l’application web. Pour cette raison, ce nom doit être unique sur l’ensemble des applications dans Azure. Vous pouvez mapper une entrée de nom de domaine personnalisée vers l’application web avant de l’exposer à vos utilisateurs.

Une fois que la définition de l’application web est prête, l’interface de ligne de commande Azure affiche des informations similaires à celles de l’exemple suivant : 

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

### <a name="configure-java"></a>Configurer Java 

Dans Cloud Shell, définissez la configuration runtime Java dont votre application a besoin avec la commande [`az webapp config set`](/cli/azure/webapp/config#az_webapp_config_set).

La commande suivante configure l’application web de manière à ce qu’elle s’exécute sur un JDK Java 8 récent et sur [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Configuration de l’application pour utiliser Azure SQL Database

Avant d’exécuter l’exemple d’application, définissez les paramètres de l’application sur l’application web pour utiliser la base de données Azure MySQL que vous avez créée dans Azure. Ces propriétés sont exposées à l’application web en tant que variables d’environnement et remplacent les valeurs définies dans les propriétés d’application dans l’application web empaquetée. 

Dans Cloud Shell, définissez les paramètres d’application avec la commande [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) dans l’interface CLI :

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>Obtention des informations d’identification de déploiement FTP 
Vous pouvez déployer votre application dans Azure App Service de plusieurs façons, notamment FTP, Git local ainsi que GitHub, Visual Studio Team Services et BitBucket. Pour cet exemple, FTP pour déployer le fichier .WAR basé précédemment sur votre ordinateur local dans Azure App Service.

Pour déterminer les informations d’identification à transmettre dans une commande ftp à l’application web, utilisez la commande [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) dans Cloud Shell : 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>Télécharger l’application via FTP

Utilisez votre outil FTP préféré pour déployer le fichier .WAR dans le dossier */site/wwwroot/webapps* sur l’adresse du serveur extraite du champ `URL` de la commande précédente. Supprimez le répertoire existant de l’application (racine) par défaut et remplacez le ROOT.war existant avec le fichier .WAR créé plus haut dans ce didacticiel.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Tester l’application web

Accédez à `http://<app_name>.azurewebsites.net/` et ajoutez quelques tâches à la liste. 

![Application Java s’exécutant dans Azure App Service](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**Félicitations !** Vous exécutez une application Java pilotée par les données dans Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Mise à jour de l’application et redéploiement

Mettez à jour l’application pour inclure une colonne supplémentaire dans la liste des tâches pour le jour où l’élément a été créé. Spring Boot gère la mise à jour du schéma de base de données pour vous, pendant que le modèle de données change sans modifier vos enregistrements de base de données existants.

1. Sur votre système local, ouvrez *src/main/java/com/example/fabrikam/TodoItem.java* et ajoutez les importations suivantes à la classe :   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. Ajouter une `String` propriété `timeCreated` à *src/main/java/com/example/fabrikam/TodoItem.java*, en l’initialisant avec un horodatage lors de la création de l’objet. Ajoutez des méthodes getter et setter pour la nouvelle propriété `timeCreated` lorsque vous modifiez ce fichier.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. Mettez à jour *src/main/java/com/example/fabrikam/TodoDemoController.java* avec une ligne dans la méthode `updateTodo` pour définir l’horodatage :

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Ajoutez la prise en charge pour le nouveau champ dans le modèle `Thymeleaf`. Mettez à jour *src/main/resources/templates/index.html* avec un nouvel en-tête de table pour l’horodatage et un nouveau champ pour afficher la valeur de l’horodatage dans chaque ligne de données de table.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. Régénérez l’application :

    ```bash
    mvnw clean package 
    ```

6. Téléchargez en FTP le fichier .WAR mis à jour comme auparavant, en supprimant le répertoire existant *site/wwwroot/webapps/ROOT* et *ROOT.war*, puis en téléchargeant le fichier .WAR mis à jour en tant que ROOT.war. 

Lorsque vous actualisez l’application, une colonne **Heure de création** est maintenant visible. Lorsque vous ajoutez une nouvelle tâche, l’application remplit automatiquement l’horodatage. Vos tâches existantes restent inchangées et fonctionnent avec l’application même si le modèle de données sous-jacent a changé. 

![Mise à jour d’application Java avec une nouvelle colonne](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic 

Pendant l’exécution de votre application Java dans Azure App Service, vous pouvez acheminer les journaux de la console directement vers votre terminal. De cette façon, vous pouvez obtenir les mêmes messages de diagnostic pour vous aider à déboguer les erreurs d’application.

Pour démarrer la diffusion de journaux, utilisez la commande [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) dans Cloud Shell.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Service**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/app-service-web-tutorial-java-mysql/access-portal.png)

Par défaut, la page de votre application web affiche la page **Présentation**. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion (arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

Ces onglets affichent les nombreuses fonctionnalités exceptionnelles que vous pouvez ajouter à votre application web. La liste suivante fournit quelques exemples des possibilités :
* Mapper un nom DNS personnalisé
* Lier un certificat SSL personnalisé
* Configurer le déploiement continu
* Montée en puissance et augmentation de la taille des instances
* Ajouter une authentification utilisateur

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources pour un autre didacticiel (voir [Étapes suivantes](#next)), vous pouvez les supprimer en exécutant la commande suivante dans Cloud Shell : 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="checklist"]
> * Création d’une base de données MySQL dans Azure
> * Connecter un exemple d’application Java à MySQL
> * Déploiement de l’application dans Azure
> * Mise à jour et redéploiement de l’application
> * Diffusion des journaux de diagnostic à partir d’Azure
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à l’application.

> [!div class="nextstepaction"] 
> [Mapper un nom DNS personnalisé existant à des applications web Azure](app-service-web-tutorial-custom-domain.md)
