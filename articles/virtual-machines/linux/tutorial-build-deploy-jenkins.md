---
title: "Intégration continue/déploiement continu de Jenkins vers des machines virtuelles Azure avec Team Services | Microsoft Docs"
description: "Configurer l’intégration continue (CI) et le déploiement continu (CD) d’une application Node.js en utilisant Jenkins sur des machines virtuelles Azure à partir de Release Management dans Visual Studio Team Services ou Microsoft Team Foundation Server"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: bfda0475b58556db1236c8b051c59393384720f7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-your-app-to-linux-vms-by-using-jenkins-and-team-services"></a>Déployer votre application sur des machines virtuelles Linux à l’aide de Jenkins et Team Services

L’intégration continue (CI) et le déploiement continu (CD) constituent un pipeline via lequel vous pouvez générer, mettre en production et déployer votre code. Visual Studio Team Services fournit un ensemble complet d’outils d’automatisation CI/CD pour le déploiement sur Azure. Jenkins est un outil serveur CI/CD tiers populaire qui propose également l’automatisation CI/CD. Vous pouvez utiliser Team Services et Jenkins ensemble pour personnaliser la façon dont vous proposez votre service ou application cloud.

Dans ce didacticiel, vous allez utiliser Jenkins pour générer une application web Node.js. Vous utiliserez ensuite Team Services ou Team Foundation Server pour la déployer dans un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) qui contient des machines virtuelles Linux.

Vous allez :

> [!div class="checklist"]
> * Obtenir l’exemple d’application.
> * Configurer les plug-ins Jenkins.
> * Configurer un projet libre (Freestyle) Jenkins pour Node.js.
> * Configurer Jenkins pour l’intégration Team Services.
> * Créer un point de terminaison de service Jenkins.
> * Créer un groupe de déploiement pour les machines virtuelles Azure.
> * Créer une définition de mise en production Team Services.
> * Exécuter des déploiements déclenchés manuellement ou par CI.

## <a name="before-you-begin"></a>Avant de commencer

* Vous devez avoir accès à un serveur Jenkins. Si vous n’avez pas encore créé de serveur Jenkins, consultez [Créer un serveur Jenkins sur une machine virtuelle Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Connectez-vous à votre compte Team Services (**https://{votrecompte}.visualstudio.com**). 
  Vous pouvez obtenir un [compte Team Services gratuit](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Pour plus d’informations, consultez [Connect to Visual Studio Team Services from Eclipse, Xcode, Visual Studio, and more](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) (Se connecter à Visual Studio Team Services depuis Eclipse, Xcode, Visual Studio, etc.).

*  Vous avez besoin d’une machine virtuelle Linux pour une cible de déploiement.  Pour plus d’informations, consultez [Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Ouvrez le port d’entrée 80 pour votre machine virtuelle. Pour plus d’informations, consultez [Créer des groupes de sécurité réseau à l’aide du portail Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obtenir l’exemple d’application

Vous devez déployer une application, stockée dans un dépôt Git.
Pour ce didacticiel, nous vous recommandons d’utiliser [cet exemple d’application disponible sur GitHub](https://github.com/azooinmyluggage/fabrikam-node). Ce didacticiel contient un exemple de script qui est utilisé pour l’installation de Node.js et d’une application. Si vous souhaitez utiliser votre propre référentiel, vous devez configurer un exemple similaire.

Dupliquez cette application et notez son emplacement (URL) pour pouvoir l’utiliser ultérieurement dans ce didacticiel. Pour plus d’informations, consultez [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Dupliquer un dépôt).    

> [!NOTE]
> L’application a été générée à l’aide de [Yeoman](http://yeoman.io/learning/index.html). Elle utilise Express, bower et Grunt. Elle contient certains packages npm utilisés comme dépendances.
> L’exemple contient également un script qui configure Nginx et déploie l’application. Il est exécuté sur les machines virtuelles. Plus précisément, le script effectue les actions suivantes :
> 1. Il installe Node, Nginx et PM2.
> 2. Il configure Nginx et PM2.
> 3. Il démarre l’application Node.

## <a name="configure-jenkins-plug-ins"></a>Configurer les plug-ins Jenkins

Tout d’abord, vous devez configurer deux plug-ins Jenkins : **NodeJS** et **VS Team Services Continuous Deployment**.

1. Ouvrez votre compte Jenkins et sélectionnez **Manage Jenkins** (Gérer Jenkins).
2. Dans la page **Manage Jenkins** (Gérer Jenkins), sélectionnez **Manage Plugins** (Gérer les plug-ins).
3. Filtrez la liste pour trouver le plug-in **NodeJS**, puis sélectionnez l’option **Install without restart** (Installer sans redémarrage).
    ![Ajout du plug-in NodeJS à Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrez la liste pour trouver le plug-in **VS Team Services Continuous Deployment**, puis sélectionnez l’option **Install without restart** (Installer sans redémarrage).
5. Revenez au tableau de bord Jenkins et sélectionnez **Manage Jenkins** (Gérer Jenkins).
6. Sélectionnez **Global Tool Configuration** (Configuration globale d’outils). Recherchez **NodeJS** et sélectionnez **NodeJS installations** (Installations NodeJS).
7. Sélectionnez l’option **Install automatically** (Installer automatiquement), puis entrez un nom dans **Name**.
8. Sélectionnez **Enregistrer**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurer un projet libre (Freestyle) Jenkins pour Node.js

1. Sélectionnez **Nouvel élément**. Entrez un nom d’élément.
2. Sélectionnez **Freestyle project** (Projet libre). Sélectionnez **OK**.
3. Sous l’onglet **Source Code Management** (Gestion du code source), sélectionnez **Git** et entrez les détails du dépôt et de la branche où se trouve le code de votre application.    
    ![Ajouter un référentiel à votre build](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Sous l’onglet **Build Triggers** (Générer des déclencheurs), sélectionnez **Poll SCM** (Interroger SCM) et entrez la planification `H/03 * * * *` pour interroger le dépôt Git sur les modifications toutes les trois minutes. 
5. Sous l’onglet **Build Environment** (Générer un environnement), sélectionnez **Provide Node &amp; npm bin/ folder PATH** (Fournir le CHEMIN D’ACCÈS au dossier npm bin/ et à Node) et sélectionnez la valeur**NodeJS Installation** (Installation NodeJS). Laissez **npmrc file** (fichier npmrc) défini sur **use system default** (utiliser les valeurs système par défaut).
6. Sous l’onglet **Build** (Générer), sélectionnez **Execute shell** (Exécuter Shell) et entrez la commande `npm install` pour vous assurer que toutes les dépendances sont mises à jour.


## <a name="configure-jenkins-for-team-services-integration"></a>Configurer Jenkins pour l’intégration Team Services

> [!NOTE]
> Vérifiez que le jeton d’accès personnel (PAT) que vous allez utiliser pour les étapes suivantes contient l’autorisation de mise en production *Release* (lecture, écriture, exécution et gestion) dans Team Services.
 
1.  Créez un jeton PAT dans votre compte Team Services si vous n’en avez pas. Jenkins requiert ces informations pour accéder à votre compte Team Services. Veillez à stocker les informations sur le jeton qui seront nécessaires dans les prochaines étapes de cette section.
  
    Pour découvrir comment générer un jeton, consultez [Créer un jeton d’accès personnel pour VSTS et TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. Sous l’onglet **Post-build Actions** (Actions post-build), sélectionnez **Add post-build action** (Ajouter une action post-build). Sélectionnez **Archive the artifacts** (Archiver les artefacts).
3. Sous **Files to archive** (Fichiers à archiver), entrez `**/*` pour inclure tous les fichiers.
4. Pour créer une autre action, sélectionnez **Add post-build action** (Ajouter une action post-build).
5. Sélectionnez **Trigger release in TFS/Team Services** (Déclencher la mise en production dans TFS/Team Services). Entrez l’URI de votre compte Team Services, tel que **https://{votre-nom-de-compte}.visualstudio.com**.
6. Entrez le nom du **projet d’équipe**.
7. Choisissez un nom pour la définition de mise en production. (Vous allez créer cette définition de mise en production plus tard dans Team Services.)
8. Choisissez les informations d’identification pour vous connecter à votre environnement Team Services ou Team Foundation Server :
   - Laissez le champ **Username** (Nom d’utilisateur) vide si vous utilisez Team Services. 
   - Entrez un nom d’utilisateur et un mot de passe si vous utilisez une version locale de Team Foundation Server.    
   ![Configuration des actions post-build Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Enregistrez le projet Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Créer un point de terminaison de service Jenkins

Un point de terminaison de service permet à Team Services de se connecter à Jenkins.

1. Ouvrez la page **Services** dans Team Services, ouvrez la liste **Nouveau point de terminaison de service** et sélectionnez **Jenkins**.
   ![Ajouter un point de terminaison Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Attribuez un nom à cette connexion.
3. Entrez l’URL de votre serveur Jenkins et sélectionnez l’option **Accepter les certificats SSL non fiables**. Exemple d’URL : **http://{votre-URL-Jenkins}.westcentralus.cloudapp.azure.com**.
4. Entrez le nom d’utilisateur et le mot de passe de votre compte Jenkins.
5. Sélectionnez **Vérifier la connexion** pour vérifier que les informations sont correctes.
6. Sélectionnez **OK** pour créer le point de terminaison de service.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Créer un groupe de déploiement pour les machines virtuelles Azure

Vous avez besoin d’un [groupe de déploiement](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) pour inscrire l’agent Team Services et permettre le déploiement de la définition de mise en production sur votre machine virtuelle. Avec les groupes de déploiement, vous pouvez facilement définir des groupes logiques de machines cibles pour le déploiement et installer l’agent nécessaire sur chaque machine.

   > [!NOTE]
   > Dans la procédure suivante, assurez-vous d’installer les composants requis et de *ne pas exécuter le script avec des privilèges sudo*.

1. Ouvrez l’onglet **Mises en production** du hub **Build &amp; mise en production**, ouvrez **Groupes de déploiement** et sélectionnez **+ Nouveau**.
2. Entrez un nom pour le groupe de déploiement, et éventuellement une description. Sélectionnez ensuite **Créer**.
3. Sélectionnez le système d’exploitation pour votre machine virtuelle cible de déploiement. Par exemple, sélectionnez **Ubuntu 16.04+**.
4. Sélectionnez **Utiliser un jeton d’accès personnel dans le script pour l’authentification**.
5. Sélectionnez le lien **Prérequis système**. Installez les composants requis pour votre système d’exploitation.
6. Sélectionnez **Copier le script dans le Presse-papiers** pour copier le script.
7. Connectez-vous à votre machine virtuelle cible de déploiement et exécutez le script. N’exécutez pas le script avec des privilèges sudo.
8. Après l’installation, vous êtes invité à confirmer les étiquettes du groupe de déploiement. Acceptez les valeurs par défaut.
9. Dans Team Services, recherchez la machine virtuelle que vous venez d’inscrire dans **Cibles** sous **Groupes de déploiement**.

## <a name="create-a-team-services-release-definition"></a>Créer une définition de mise en production Team Services

Une définition de mise en production spécifie le processus que Team Services utilise pour déployer l’application. Dans cet exemple, vous exécutez un script Shell.

Pour créer la définition de mise en production dans Team Services :

1. Ouvrez l’onglet **Mises en production** du hub **Build &amp; mise en production** et sélectionnez **Créer une définition de mise en production**. 
2. Sélectionnez le modèle **Vide** en choisissant de démarrer avec un **processus Vide**.
3. Dans la section **Artefacts**, sélectionnez **+ Ajouter un artefact**, puis sélectionnez **Jenkins** sous **Type de source**. Sélectionnez votre connexion de point de terminaison de service Jenkins. Sélectionnez ensuite le travail source Jenkins, puis sélectionnez **Ajouter**.
4. Sélectionnez les points de suspension à côté de la zone **Environnement 1**. Sélectionnez **Phase d’ajout d’un groupe de déploiement**.
5. Sélectionnez votre groupe de déploiement.
5. Sélectionnez **+** pour ajouter une tâche à **Phase de groupe de déploiement**.
6. Sélectionnez la tâche **Script Shell** et sélectionnez **Ajouter**. La tâche **Script Shell** fournit la configuration d’un script à exécuter sur chaque serveur pour installer Node.js et démarrer l’application.
8. Dans **Chemin d’accès du script**, entrez **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Sélectionnez **Avancé**, puis **Spécifier le répertoire de travail**.
10. Dans **Répertoire de travail**, entrez **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Changez le nom de la définition de mise en production par le nom que vous avez spécifié sous l’onglet **Actions post-build** de l’application générée dans Jenkins. Jenkins exige que ce nom soit en mesure de déclencher une nouvelle mise en production lorsque les artefacts source sont mis à jour.
12. Sélectionnez **Enregistrer**, puis sélectionnez **OK** pour enregistrer la définition de mise en production.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Exécuter des déploiements déclenchés manuellement ou par CI

1. Sélectionnez **+ Mise en production** et sélectionnez **Créer une mise en production**.
2. Sélectionnez la build que vous avez générée dans la liste déroulante en surbrillance, puis sélectionnez **File d’attente**.
3. Dans le message qui s’affiche, sélectionnez le lien de la mise en production. Par exemple : « Mise en production **Mise en production-1** a été créé. ».
4. Ouvrez l’onglet **Journaux** pour surveiller la sortie de console de mise en production.
5. Dans votre navigateur, ouvrez l’URL de l’un des serveurs que vous avez ajoutés à votre groupe de déploiement. Par exemple, entrez **http://{adresse-ip-de-votre-serveur}**.
6. Accédez au dépôt Git source et modifiez le contenu du titre **h1** dans le fichier app/views/index.jade en apportant quelques corrections au texte.
7. Validez votre modification.
8. Après quelques minutes, vous voyez une nouvelle mise en production dans la page **Mises en production** de Team Services ou Team Foundation Server. Ouvrez la mise en production pour voir le déploiement en action. Félicitations !

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez automatisé le déploiement d’une application Azure en utilisant Jenkins pour la génération et Team Services pour la mise en production. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Générer votre application dans Jenkins.
> * Configurer Jenkins pour l’intégration Team Services.
> * Créer un groupe de déploiement pour les machines virtuelles Azure.
> * Créer une définition de mise en production qui configure les machines virtuelles et déploie l’application.

Pour découvrir comment déployer une pile LAMP (Linux, Apache, MySQL et PHP), passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Déploiement d’une pile LAMP dans Azure](tutorial-lamp-stack.md)
