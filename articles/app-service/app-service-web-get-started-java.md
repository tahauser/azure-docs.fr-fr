---
title: Créer votre première application web Java dans Azure
description: Découvrez comment exécuter des applications web dans App Service en déployant une application Java de base.
services: app-service\web
documentationcenter: ''
author: rmcmurray
manager: routlaw
editor: ''
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 11/08/2017
ms.author: cephalin;robmcm
ms.custom: mvc, devcenter
ms.openlocfilehash: e63f8706e28a5d34cc9774cbaecde31415a4b48e
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-your-first-java-web-app-in-azure"></a>Créer votre première application web Java dans Azure

[Azure Web Apps](app-service-web-overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce guide de démarrage rapide indique comment déployer une application web Java dans App Service à l’aide de [l’environnement de développement intégré (IDE) Eclipse pour développeurs Java EE](http://www.eclipse.org/).

À la fin de ce guide de démarrage rapide, votre application ressemble à l’illustration suivante quand vous l’affichez dans un navigateur web :

![« Hello Azure ! » Exemple d’application web](./media/app-service-web-get-started-java/browse-web-app-1.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Pour effectuer ce démarrage rapide, installez les éléments suivants :

* <a href="http://www.eclipse.org/downloads/" target="_blank">L’Environnement de développement intégré Eclipse pour développeurs Java EE</a>. Ce démarrage rapide utilise Eclipse Neon.
* Le <a href="/java/azure/eclipse/azure-toolkit-for-eclipse-installation" target="_blank">kit de ressources Azure pour Eclipse</a>.

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Créer un projet web dynamique dans Eclipse

Dans Eclipse, sélectionnez **File (Fichier)** > **New (Nouveau)** > **Dynamic Web Project (Projet web dynamique)**.

Dans la boîte de dialogue **New Dynamic Web Project (Nouveau projet web dynamique)**, nommez le projet **MyFirstJavaOnAzureWebApp**, puis sélectionnez **Finish (Terminer)**.
   
![Boîte de dialogue Projet web dynamique](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

### <a name="add-a-jsp-page"></a>Ajouter une page JSP

Si l’Explorateur de projets n’est pas affiché, restaurez-le.

![Espace de travail Java EE pour Eclipse](./media/app-service-web-get-started-java/pe.png)

Dans l’Explorateur de projets, développez le projet **MyFirstJavaOnAzureWebApp**.
Cliquez avec le bouton droit sur **WebContent**, puis sélectionnez **New (Nouveau)** > **JSP File (Fichier JSP)**.

![Menu d’un nouveau fichier JSP dans l’Explorateur de projets](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

Dans la boîte de dialogue **New JSP File (Nouveau fichier JSP)** :

* Nommez le fichier **index.jsp**.
* Sélectionnez **Terminer**.

  ![Boîte de dialogue New JSP File (Nouveau fichier JSP)](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

Dans le fichier index.jsp, remplacez l’élément `<body></body>` par le balisage suivant :

```jsp
<body>
<h1><% out.println("Hello Azure!"); %></h1>
</body>
```

Enregistrez les modifications.

## <a name="publish-the-web-app-to-azure"></a>Publier l’application web dans Azure

Dans l’Explorateur de projets, cliquez avec le bouton droit sur le projet, puis sélectionnez **Azure** > **Publish as Azure Web App (Publier en tant qu’application web Azure)**.

![Menu contextuel Publish as Azure Web App (Publier en tant qu’application web Azure)](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

Dans la boîte de dialogue **Connexion à Azure**, conservez l’option **Interactive**, puis sélectionnez **Connexion**.

Suivez les instructions de connexion.

### <a name="deploy-web-app-dialog-box"></a>Boîte de dialogue Déployer une application web

Une fois que vous vous êtes connecté à votre compte Azure, la boîte de dialogue **Déployer une application web** s’affiche.

Sélectionnez **Créer**.

![Boîte de dialogue Déployer une application web](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

### <a name="create-app-service-dialog-box"></a>Boîte de dialogue Créer App Service

La boîte de dialogue **Créer App Service** apparaît avec les valeurs par défaut. La valeur **170602185241** illustrée dans la figure précédente diffère de celle indiquée dans votre boîte de dialogue.

![Boîte de dialogue Créer App Service](./media/app-service-web-get-started-java/cas1.png)

Dans la boîte de dialogue **Créer App Service** :

* Conservez le nom généré pour l’application web. Ce nom doit être unique au sein d’Azure. Le nom fait partie intégrante de l’adresse URL de l’application web. Par exemple : si l’application web porte le nom **MyJavaWebApp**, l’URL correspond à *myjavawebapp.azurewebsites.net*.
* Conservez le conteneur web par défaut.
* Sélectionnez un abonnement Azure.
* Dans l’onglet **Plan App Service** :

  * **Créer un nouveau** : conservez la valeur par défaut, qui correspond au nom du plan App Service.
  * **Emplacement** : sélectionnez **Europe de l’Ouest** ou un emplacement près de chez vous.
  * **Niveau tarifaire** : sélectionnez l’option de tarification gratuite. Pour plus d’informations sur les fonctionnalités, consultez la page [Tarification de App Service](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

   ![Boîte de dialogue Créer App Service](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

### <a name="resource-group-tab"></a>Onglet Groupe de ressources

Sélectionnez l’onglet **Groupe de ressources**. Conservez la valeur générée par défaut pour le groupe de ressources.

![Onglet Groupe de ressources](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

[!INCLUDE [resource-group](../../includes/resource-group.md)]

Sélectionnez **Créer**.

<!--
### The JDK tab

Select the **JDK** tab. Keep the default, and then select **Create**.

![Create App Service plan](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)
-->

Le kit de ressources Azure crée l’application web et affiche une boîte de dialogue indiquant la progression de l’opération.

![Boîte de dialogue indiquant la progression de la création du service App Service](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

### <a name="deploy-web-app-dialog-box"></a>Boîte de dialogue Déployer une application web

Dans la boîte de dialogue **Déployer une application web**, sélectionnez **Deploy to root (Déployer à la racine)**. Si vous disposez d’un App Service à l’emplacement *wingtiptoys.azurewebsites.net* et que vous ne choisissez pas le déploiement à la racine, l’application web nommée **MyFirstJavaOnAzureWebApp** est déployée dans *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Boîte de dialogue Déployer une application web](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

La boîte de dialogue affiche les valeurs sélectionnées pour Azure, JDK et le conteneur web.

Sélectionnez **Déployer** pour publier l’application web dans Azure.

Une fois la publication terminée, sélectionnez le lien **Publié** dans la boîte de dialogue **Journal d’activité Azure**.

![Boîte de dialogue Journal d’activité Azure](./media/app-service-web-get-started-java/aal.png)

Félicitations ! Vous avez correctement déployé votre application web dans Azure. 

![« Hello Azure ! » Exemple d’application web](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="update-the-web-app"></a>Mettre à jour l’application web

Remplacez l’exemple de code JSP par un autre message.

```jsp
<body>
<h1><% out.println("Hello again Azure!"); %></h1>
</body>
```

Enregistrez les modifications.

Dans l’Explorateur de projets, cliquez avec le bouton droit sur le projet, puis sélectionnez **Azure** > **Publish as Azure Web App (Publier en tant qu’application web Azure)**.

La boîte de dialogue **Déployer une application web** s’affiche en vous présentant le service App Service que vous avez créé précédemment. 

> [!NOTE] 
> Sélectionnez **Deploy to root (Déployer à la racine)** chaque fois que vous effectuez une publication. 
> 

Sélectionnez l’application web, puis sélectionnez **Déployer**, ce qui publie les modifications.

Lorsque le lien **Publication** apparaît, sélectionnez-le pour accéder à l’application web et voir les modifications.

## <a name="manage-the-web-app"></a>Gérer l’application web

Accédez au <a href="https://portal.azure.com" target="_blank">Portail Azure</a> pour visualiser l’application web que vous avez créée.

Dans le menu de gauche, sélectionnez **Groupes de ressources**.

![Accès aux groupes de ressources au moyen du portail](media/app-service-web-get-started-java/rg.png)

Sélectionnez le groupe de ressources. La page affiche les ressources que vous avez créées dans ce guide de démarrage rapide.

![Groupe de ressources myResourceGroup](media/app-service-web-get-started-java/rg2.png)

Sélectionnez l’application web (**webapp-170602193915** dans la figure précédente).

La page **Vue d’ensemble** s’affiche. Cette page présente un aperçu de l’application. Elle vous permet d’exécuter des tâches de gestion de base, telles que parcourir, arrêter, démarrer, redémarrer et supprimer. Les onglets figurant sur le côté gauche de la page affichent les différentes configurations que vous pouvez ouvrir. 

![Page App Service du Portail Azure](media/app-service-web-get-started-java/web-app-blade.png)

[!INCLUDE [clean-up-section-portal-web-app](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain.md)
