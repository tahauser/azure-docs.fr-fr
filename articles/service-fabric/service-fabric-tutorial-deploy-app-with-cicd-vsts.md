---
title: "Déployer une application Azure Service Fabric avec intégration continue (Team Services) | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrez comment configurer l’intégration et le déploiement continus pour une application Service Fabric à l’aide de Visual Studio Team Services.  Déployez une application sur un cluster Service Fabric dans Azure."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3f5ccd40e2b46cc68b4f7aeb67577fb66dbd5355
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Didacticiel : Déployer une application avec l’intégration et le déploiement continus sur un cluster Service Fabric
Troisième d’une série, ce didacticiel explique comment configurer l’intégration et le déploiement continus d’une application Microsoft Azure Service Fabric à l’aide de Visual Studio Team Services.  Une application Service Fabric existante est requise. L’application créée dans le didacticiel [Générer une application .NET](service-fabric-tutorial-create-dotnet-app.md) est utilisée à titre d’exemple.

Dans ce troisième volet, vous apprenez à :

> [!div class="checklist"]
> * Ajouter le contrôle de code source à votre projet
> * Créer une définition de build dans Team Services
> * Créer une définition de mise en production dans Team Services
> * Déployer et mettre à niveau une application automatiquement

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Déployer l’application sur un cluster distant](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services
> * [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md).
- Créez un cluster Service Fabric Windows sur Azure, par exemple en suivant [ce didacticiel](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Créez un compte [Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote
Si vous n’avez pas généré l’exemple d’application de vote lors de la [première partie de cette série de didacticiels](service-fabric-tutorial-create-dotnet-app.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Préparer un profil de publication
Maintenant que vous avez [créé une application](service-fabric-tutorial-create-dotnet-app.md) et [déployé l’application dans Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md), vous êtes prêt à configurer l’intégration continue.  Tout d’abord, préparez au sein de votre application un profil de publication destiné au processus de déploiement qui s’exécute dans Team Services.  Le profil de publication doit être configuré pour cibler le cluster que vous avez précédemment créé.  Démarrez Visual Studio et ouvrez un projet d’application Service Fabric existant.  Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur l’application, puis sélectionnez **Publier...**.

Choisissez au sein de votre projet d’application un profil cible à utiliser pour votre flux de travail d’intégration continue (Cloud, par exemple).  Spécifiez le point de terminaison de connexion du cluster.  Sélectionnez la case à cocher **Mettre à niveau l’application** pour que votre application soit mise à niveau pour chaque déploiement dans Team Services.  Cliquez sur le lien hypertexte **Enregistrer** pour enregistrer les paramètres dans le profil de publication, puis cliquez sur **Annuler** pour fermer la boîte de dialogue.  

![Profil d’envoi (push)][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Partager votre solution Visual Studio dans un nouveau dépôt Git Team Services
Partagez les fichiers source de votre application dans un projet d’équipe Team Services pour pouvoir générer des builds.  

Créez un dépôt Git local pour votre projet en sélectionnant **Ajouter au contrôle de code source** -> **Git** dans la barre d’état située dans le coin inférieur droit de Visual Studio. 

Dans la vue **Envoi** de **Team Explorer**, sélectionnez le bouton **Publier le dépôt Git** sous **Push sur Visual Studio Team Services**.

![Envoi (push) du dépôt Git][push-git-repo]

Vérifiez votre adresse e-mail et sélectionnez votre compte dans la liste déroulante **Domaine Team Services**. Entrez le nom de votre dépôt et sélectionnez **Publier le dépôt**.

![Envoi (push) du dépôt Git][publish-code]

La publication du dépôt entraîne la création d’un projet d’équipe portant le même nom que le dépôt local dans votre compte. Pour créer le dépôt dans un projet d’équipe existant, cliquez sur **Avancé** en regard de **Nom du dépôt**, puis sélectionnez un projet d’équipe. Vous pouvez afficher votre code sur le web en sélectionnant **See it on the web** (Visualiser sur le web).

## <a name="configure-continuous-delivery-with-vsts"></a>Configurer la livraison continue avec VSTS
Une définition de build Team Services décrit un flux de travail qui se compose d’un ensemble d’étapes de génération exécutées séquentiellement. Créez une définition de build qui produit un package d’application Service Fabric et les autres artefacts à déployer sur un cluster Service Fabric. Vous trouverez plus d’informations sur les définitions de build Team Services [ici](https://www.visualstudio.com/docs/build/define/create). 

Une définition de mise en production Team Services décrit un flux de travail qui déploie un package d’application sur un cluster. Lorsqu’elles sont utilisées ensemble, la définition de version et la définition de génération exécutent le flux de travail dans son ensemble, depuis le déploiement des fichiers source jusqu’à l’exécution de l’application dans votre cluster. Plus d’informations sur les [définitions de version](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)Team Services.

### <a name="create-a-build-definition"></a>Créer une définition de build
Ouvrez un navigateur web et accédez à votre nouveau projet d’équipe à l’adresse : [https://&lt;moncompte&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Sélectionnez l’onglet **Générer et publier**, **Builds**, puis **+ Nouvelle définition**.  Dans **Sélectionner un modèle**, sélectionnez le modèle **Azure Service Fabric Application** et cliquez sur **Appliquer**. 

![Choisir le modèle de build][select-build-template] 

Dans **Tâches**, entrez « Hébergée VS2017 » pour **File d’attente d’agents**. 

![Sélectionner les tâches][save-and-queue]

Sous **Déclencheurs**, activez l’intégration continue en définissant **Déclencher l’état**.  Sélectionnez **Enregistrer et mettre en file d’attente** pour lancer une build manuellement.  

![Sélectionnez les déclencheurs][save-and-queue2]

Les builds sont également déclenchées par envoi (push) ou par archivage. Pour vérifier la progression de votre build, basculez vers l’onglet **Builds**.  Une fois que vous avez vérifié que la build s’exécute correctement, spécifiez une définition de mise en production assurant le déploiement de votre application sur un cluster. 

### <a name="create-a-release-definition"></a>Création d’une définition de version  

Sélectionnez l’onglet **Générer et publier**, **Mises en production**, puis **+ Nouvelle définition**.  Sous **Sélectionner un modèle**, sélectionnez le modèle **Déploiement Azure Service Fabric** dans la liste, puis **Appliquer**.  

![Choisissez le modèle de template][select-release-template]

Sélectionnez **Tâches**->**Environnement 1**, puis **+ Nouveau** pour ajouter une nouvelle connexion de cluster.

![Ajouter une connexion au cluster][add-cluster-connection]

Sur la vue **Ajouter une nouvelle connexion Service Fabric**, sélectionnez l’authentification **Basée sur les certificats** ou **Azure Active Directory**.  Spécifiez le nom de connexion « mysftestcluster » et le point de terminaison de cluster « tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000 » (ou celui du cluster sur lequel porte le déploiement). 

Pour l’authentification basée sur les certificats, ajoutez **l’empreinte du certificat de serveur** utilisé pour créer le cluster.  Dans **Certificat client**, ajoutez le codage en base 64 du fichier de certificat client. Consultez la fenêtre contextuelle d’aide de ce champ pour savoir comment obtenir cette représentation codée en base 64 du certificat. Ajoutez également le **Mot de passe** du certificat.  Vous pouvez utiliser le certificat de serveur ou de cluster si vous ne disposez pas d’un certificat client distinct. 

Dans les informations d’identification d’Azure Active Directory, ajoutez **l’empreinte du certificat de serveur** utilisé pour créer le cluster et les informations d’identification à utiliser pour se connecter au cluster dans les champs **Nom d’utilisateur** et **Mot de passe**. 

Cliquez sur **Ajouter** pour enregistrer la connexion du cluster.

Ensuite, ajoutez un artefact de build au pipeline afin de permettre à la définition de mise en production de trouver la sortie de la build. Sélectionnez **Pipeline** et **Artefacts**->**+ Ajouter**.  Dans **Source (définition de build)**, sélectionnez la définition de build que vous avez créée précédemment.  Cliquez sur **Ajouter** pour enregistrer l’artefact de la build.

![Ajoutez un artefact][add-artifact]

Activez un déclencheur de déploiement continu afin qu’une version soit créée automatiquement à la fin de la génération. Cliquez sur l’icône en forme d’éclair de l’artefact, activez le déclencheur, puis cliquez sur **Enregistrer** pour enregistrer la définition de mise en production.

![Activez le déclencheur][enable-trigger]

Sélectionnez **+Mise en production** -> **Créer une mise en production** -> **Créer** pour créer manuellement une mise en production.  Vérifiez que le déploiement a réussi et que l’application est en cours d’exécution dans le cluster.  Ouvrez un navigateur web et accédez à [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Notez la version de l’application. Dans cet exemple, il s’agit de la version 1.0.0.20170616.3. 

## <a name="commit-and-push-changes-trigger-a-release"></a>Valider et envoyer les modifications, déclencher une mise en production
Vous pouvez vérifier que le pipeline d’intégration continue fonctionne en archivant certaines modifications de code dans Team Services.    

Lorsque vous écrivez votre code, vos modifications sont suivies automatiquement par Visual Studio. Validez les modifications dans votre dépôt Git local en sélectionnant l’icône de modifications en attente (![Pending][pending]) dans la barre d’état en bas à droite.

Dans la vue **Modifications** de Team Explorer, ajoutez un message décrivant votre mise à jour, puis validez vos modifications.

![Valider tout][changes]

Sélectionnez l’icône de modifications non publiées (![Modifications non publiées][unpublished-changes]) dans la barre d’état ou la vue Synchronisation de Team Explorer. Sélectionnez **Envoi** pour mettre à jour votre code dans Team Services/TFS.

![Envoi (push) des modifications][push]

L’envoi (push) des modifications dans Team Services déclenche automatiquement une build.  Une fois la définition de build terminée, une mise en production est créée automatiquement et commence la mise à niveau de l’application sur le cluster.

Pour vérifier la progression de votre build, basculez vers l’onglet **Builds** de **Team Explorer** dans Visual Studio.  Une fois que vous avez vérifié que la build s’exécute correctement, spécifiez une définition de mise en production assurant le déploiement de votre application sur un cluster.

Vérifiez que le déploiement a réussi et que l’application est en cours d’exécution dans le cluster.  Ouvrez un navigateur web et accédez à [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Notez la version de l’application. Dans cet exemple, il s’agit de la version 1.0.0.20170815.3.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Mettre à jour l’application
Apportez des modifications au code de l’application.  Enregistrez et validez les modifications en suivant les étapes décrites précédemment.

Une fois que la mise à niveau de l’application a commencé, vous pouvez suivre la progression dans Service Fabric Explorer :

![Service Fabric Explorer][sfx2]

La mise à niveau de l’application peut prendre plusieurs minutes. Une fois la mise à niveau terminée, l’application exécute la version suivante.  Dans cet exemple, 1.0.0.20170815.4.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Ajouter le contrôle de code source à votre projet
> * Créer une définition de build
> * Création d’une définition de version
> * Déployer et mettre à niveau une application automatiquement

Passez au didacticiel suivant :
> [!div class="nextstepaction"]
> [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
