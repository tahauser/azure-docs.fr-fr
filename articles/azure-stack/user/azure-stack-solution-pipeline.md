---
title: "Déployer votre application sur Azure et Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer des applications sur Azure et Azure Stack avec un pipeline CI/CD hybride."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 6292a846a2c3d7e112558ef0d2b62b3a3fdd3c51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Déployer des applications sur Azure et Azure Stack
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Un pipeline CI/CD ([intégration continue](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[livraison continue](https://www.visualstudio.com/learn/what-is-continuous-delivery/)) hybride vous permet de générer, de tester et de déployer votre application sur plusieurs clouds.  Dans ce didacticiel, vous générez un exemple d’environnement pour voir comment un pipeline CI/CD hybride peut vous permettre d’effectuer les opérations suivantes :
 
> [!div class="checklist"]
> * Lancer une nouvelle build basée sur des validations de code dans votre dépôt VSTS (Visual Studio Team Services).
> * Déployer automatiquement votre code nouvellement généré sur Azure pour le test d’acceptation des utilisateurs.
> * Une fois que votre code a réussi le test, effectuer automatiquement le déploiement sur Azure Stack. 


## <a name="prerequisites"></a>Prérequis
Certains composants sont nécessaires pour créer un pipeline CI/CD hybride, et leur préparation peut prendre un certain temps.  Si vous disposez déjà de certains de ces composants, vérifiez qu’ils répondent aux conditions requises avant de commencer.

Cette rubrique suppose également que vous connaissez déjà Azure et Azure Stack. Si vous voulez en savoir plus avant de continuer, veillez à commencer par les rubriques suivantes :

- [Présentation de Microsoft Azure](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Concepts clés d’Azure Stack](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
 - Créez une [application web](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md), puis configurez-la pour la [publication FTP](../../app-service/app-service-deploy-ftp.md).  Notez la nouvelle URL de l’application web, car nous l’utiliserons ultérieurement.


### <a name="azure-stack"></a>Azure Stack
 - [Déployez Azure Stack](../azure-stack-run-powershell-script.md).  Comme l’installation prend généralement quelques heures, adaptez votre planification en conséquence.
 - Déployez les services PaaS [App Service](../azure-stack-app-service-deploy.md) sur Azure Stack.
 - Créez une application web, puis configurez-la pour la [publication FTP](../azure-stack-app-service-enable-ftp.md).  Notez la nouvelle URL de l’application web, car nous l’utiliserons ultérieurement.  

### <a name="developer-tools"></a>Outils de développeur
 - Créez un [espace de travail VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  Le processus d’inscription crée un projet nommé « MyFirstProject ».  
 - [Installez Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio), puis [connectez-vous à VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio).
 - Connectez-vous au projet et [créez un clone local](https://www.visualstudio.com/docs/git/gitquickstart).
 - Créez un [pool d’agents](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) dans VSTS.
 - Installez Visual Studio et déployez un [agent de build VSTS](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) sur une machine virtuelle dans Azure Stack. 
 

## <a name="create-app--push-to-vsts"></a>Créer une application et l’envoyer (push) à VSTS

### <a name="create-application"></a>Créer une application
Dans cette section, vous allez créer une application ASP.NET simple et l’envoyer (push) à VSTS.  Cette procédure représente le flux de travail normal d’un développeur et elle peut être adaptée aux outils et langages de développement. 

1.  Ouvrez Visual Studio.
2.  Dans l’espace Team Explorer et dans la zone **Solutions...**, cliquez sur **Nouveau**.
3.  Sélectionnez **Visual C#** > **Web** > **Application web ASP.NET (.NET Framework)**.
4.  Entrez un nom pour l’application et cliquez sur **OK**.
5.  Dans l’écran suivant, conservez les valeurs par défaut (Web Forms) et cliquez sur **OK**.

### <a name="commit-and-push-changes-to-vsts"></a>Valider et envoyer (push) les changements à VSTS
1.  À l’aide de Team Explorer dans Visual Studio, sélectionnez la liste déroulante et cliquez sur **Modifications**.
2.  Indiquez un message de validation, puis sélectionnez **Valider tout**. Vous serez peut-être invité à enregistrer le fichier solution. Cliquez sur Oui pour tout enregistrer.
3.  Une fois la validation effectuée, Visual Studio propose de synchroniser les modifications apportées à votre projet. Sélectionnez **Synchroniser**.

    ![image montrant l’écran de validation une fois la validation terminée](./media/azure-stack-solution-pipeline/image1.png)

4.  Sous l’onglet Synchronisation, sous *Validations sortantes*, vous voyez votre nouvelle validation.  Sélectionnez **Push** pour synchroniser le changement avec VSTS.

    ![image montrant les étapes de la synchronisation](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Revoir le code dans VSTS
Une fois que vous avez validé un changement et que vous l’avez envoyé (push) à VSTS, vérifiez votre code à partir du portail VSTS.  Sélectionnez **Code**, puis **Fichiers** dans le menu déroulant.  Vous pouvez voir la solution que vous avez créée.

## <a name="create-build-definition"></a>Créer une définition de build
Le processus de génération définit la façon dont votre application est créée et empaquetée pour un déploiement à chaque validation de modifications du code. Dans notre exemple, nous utilisons le modèle inclus pour configurer le processus de génération d’une application ASP.NET, même s’il est possible d’adapter cette configuration en fonction de votre application.

1.  Connectez-vous à votre espace de travail VSTS à partir d’un navigateur web.
2.  Dans la bannière, sélectionnez **Build et mise en production**, puis **Builds**.
3.  Sélectionnez **+ Nouvelle définition**.
4.  Dans la liste de modèles, sélectionnez **ASP.NET (préversion)**, puis sélectionnez **Appliquer**.
5.  Remplacez le champ *Arguments MSBuild* de l’étape *Générer la solution* par :

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Sélectionnez l’onglet **Options**, puis la file d’attente d’agents pour l’agent de build que vous avez déployé sur une machine virtuelle dans Azure Stack. 
7.  Sélectionnez l’onglet **Déclencheurs**, puis activez **Intégration continue**.
7.  Cliquez sur **Enregistrer et mettre en file d’attente**, puis sélectionnez **Enregistrer** dans la liste déroulante. 

## <a name="create-release-definition"></a>Créer une définition de mise en production
Le processus de mise en production définit la façon dont les builds de l’étape précédente sont déployées dans un environnement.  Dans ce didacticiel, nous publions notre application ASP.NET avec le protocole FTP sur une application web Azure. Pour configurer une mise en production sur Azure, effectuez la procédure suivante :

1.  Dans la bannière VSTS, sélectionnez **Build et mise en production**, puis **Mises en production**.
2.  Cliquez sur le **+ Nouvelle définition** vert.
3.  Sélectionnez **Vide** et cliquez sur **Suivant**.
4.  Cochez la case *Déploiement continu* et cliquez sur **Créer**.

Maintenant que vous avez créé une définition de mise en production vide et que vous l’avez liée à la build, nous ajoutons des étapes pour l’environnement Azure :

1.  Cliquez sur le signe **+** vert pour ajouter des tâches.
2.  Sélectionnez **Tout**, puis dans la liste, ajoutez **Chargement FTP** et sélectionnez **Fermer**.
3.  Sélectionnez la tâche **Chargement FTP** que vous venez d’ajouter, puis configurez les paramètres suivants :
    
    | Paramètre | Valeur |
    | ----- | ----- |
    |Méthode d'authentification| Entrer les informations d’identification|
    |URL du serveur | URL FTP de l’application web récupérée à partir du portail Azure |
    |Nom d’utilisateur | Nom d’utilisateur que vous avez configuré lors de la création des informations d’identification FTP pour l’application web |
    |Mot de passe | Mot de passe que vous avez créé lors de la définition des informations d’identification FTP pour l’application web|
    |Répertoire source | $(System.DefaultWorkingDirectory)\**\ |
    |Répertoire distant | /site/wwwroot/ |
    |Conserver les chemins de fichiers | Activé (coché)|

4.  Cliquez sur **Enregistrer**.

Enfin, configurez la définition de mise en production pour utiliser le pool d’agents contenant l’agent déployé en effectuant la procédure suivante :
1.  Sélectionnez la définition de mise en production et cliquez sur **Modifier**.
2.  Sélectionnez **Exécuter sur l’agent** dans la colonne du milieu.  Dans la colonne de droite, sélectionnez la file d’attente d’agents contenant l’agent de build en cours d’exécution sur Azure Stack.  
    ![image montrant la configuration de la définition de mise en production pour utiliser une file d’attente spécifique](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Déployer votre application sur Azure
Cette étape utilise votre pipeline CI/CD nouvellement généré pour déployer l’application ASP.NET sur une application web sur Azure. 

1.  Dans la bannière dans VSTS, sélectionnez **Build et mise en production**, puis **Builds**.
2.  Cliquez sur **...** sur la définition de build créée précédemment, puis sélectionnez **Mettre en file d’attente une nouvelle build**.
3.  Acceptez les valeurs par défaut et cliquez sur **OK**.  La génération démarre et affiche la progression.
4.  Une fois la génération terminée, vous pouvez suivre l’état en sélectionnant **Build et mise en production**, puis en sélectionnant **Mises en production**.
5.  Une fois la génération terminée, visitez le site web à l’aide de l’URL notée lors de la création de l’application web.    


## <a name="add-azure-stack-to-pipeline"></a>Ajouter Azure Stack à un pipeline
Maintenant que vous avez testé votre pipeline CI/CD en effectuant un déploiement sur Azure, il est temps d’ajouter Azure Stack au pipeline.  Dans les étapes suivantes, vous allez créer un environnement et ajouter une tâche Chargement FTP pour déployer votre application sur Azure Stack.  Vous allez aussi ajouter un approbateur de mise en production, qui est un moyen de simuler une « déconnexion » sur une mise en production de code sur Azure Stack.  

1.  Dans la définition de mise en production, sélectionnez **+ Ajouter un environnement** et **Créer un environnement**.
2.  Sélectionnez **Vide** et cliquez sur **Suivant**.
3.  Sélectionnez **Utilisateurs spécifiques** et spécifiez votre compte.  Sélectionnez **Créer**.
4.  Renommez l’environnement en sélectionnant le nom existant et en tapant *Azure Stack*.
5.  Sélectionnez maintenant l’environnement Azure Stack, puis **Ajouter des tâches**.
6.  Sélectionnez la tâche **Chargement FTP**, **Ajouter**, puis **Fermer**.


### <a name="configure-ftp-task"></a>Configurer une tâche FTP
Maintenant que vous avez créé une mise en production, vous devez configurer les étapes nécessaires pour la publication sur l’application web dans Azure Stack.  Exactement comme vous avez configuré la tâche Chargement FTP pour Azure, vous configurez la tâche pour Azure Stack :

1.  Sélectionnez la tâche **Chargement FTP** que vous venez d’ajouter, puis configurez les paramètres suivants :
    
    | Paramètre | Valeur |
    | -----     | ----- |
    |Méthode d'authentification| Entrer les informations d’identification|
    |URL du serveur | URL FTP de l’application web récupérée à partir du portail Azure Stack |
    |Nom d’utilisateur | Nom d’utilisateur que vous avez configuré lors de la création des informations d’identification FTP pour l’application web |
    |Mot de passe | Mot de passe que vous avez créé lors de la définition des informations d’identification FTP pour l’application web|
    |Répertoire source | $(System.DefaultWorkingDirectory)\**\ |
    |Répertoire distant | /site/wwwroot/|
    |Conserver les chemins de fichiers | Activé (coché)|

2.  Cliquez sur **Enregistrer**.

Enfin, configurez la définition de mise en production pour utiliser le pool d’agents contenant l’agent déployé en effectuant la procédure suivante :
1.  Sélectionnez la définition de mise en production et cliquez sur **Modifier**.
2.  Sélectionnez **Exécuter sur l’agent** dans la colonne du milieu. Dans la colonne de droite, sélectionnez la file d’attente d’agents contenant l’agent de build en cours d’exécution sur Azure Stack.  
    ![image montrant la configuration de la définition de mise en production pour utiliser une file d’attente spécifique](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Déployer un nouveau code
Vous pouvez maintenant tester le pipeline CI/CD hybride avec la dernière étape de publication sur Azure Stack.  Dans cette section, vous modifiez le pied de page du site et vous commencez le déploiement à travers le pipeline.  Quand ces opérations sont terminées, vous voyez vos changements déployés sur Azure pour revue. Une fois que vous approuvez la mise en production, ils sont publiés sur Azure Stack.

1. Dans Visual Studio, ouvrez le fichier *site.master*, puis remplacez cette ligne :
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    par ceci :

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  Validez les changements, puis synchronisez-les avec VSTS.  
4.  À partir de l’espace de travail VSTS, vérifiez l’état de la build en sélectionnant **Build et mise en production** > **Mise en production**.
5.  Vous voyez une build en cours d’exécution.  Double-cliquez sur l’état. Vous pouvez alors observer la progression de la build.  Une fois que vous voyez « Build terminée » dans la console, poursuivez en vérifiant la mise en production dans **Build et mise en production** > **Mise en production**.  Double-cliquez sur la mise en production.
6.  Vous recevrez une notification indiquant qu’une mise en production doit être examinée. Contrôlez l’URL de l’application web et vérifiez que les nouveaux changements sont présents.  Approuvez la mise en production dans VSTS.
    ![image montrant la configuration de la définition de mise en production pour utiliser une file d’attente spécifique](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Vérifiez que la publication sur Azure Stack est terminée en vous connectant au site web avec l’URL notée lors de la création de l’application web.
    ![image montrant l’application ASP.NET avec le pied de page modifié](./media/azure-stack-solution-pipeline/image5.png)


Vous pouvez maintenant utiliser votre nouveau pipeline CI/CD hybride comme module pour d’autres modèles de cloud hybrides.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à générer un pipeline CI/CD hybride qui :

> [!div class="checklist"]
> * Lance une nouvelle build basée sur des validations de code dans votre dépôt VSTS (Visual Studio Team Services).
> * Déploie automatiquement votre code nouvellement généré sur Azure pour le test d’acceptation des utilisateurs.
> * Une fois que votre code a réussi le test, effectue automatiquement le déploiement sur Azure Stack. 

Maintenant que vous avez un pipeline CI/CD hybride, poursuivez en apprenant à développer des applications pour Azure Stack.

> [!div class="nextstepaction"]
> [Développer pour Azure Stack](azure-stack-developer.md)


