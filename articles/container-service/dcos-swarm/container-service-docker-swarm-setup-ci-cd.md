---
title: CI/CD avec Azure Container Service et Swarm
description: "Utilisez Azure Container Service avec Docker Swarm, un Registre de conteneurs Azure et Visual Studio Team Services pour fournir en continu une application .NET Core à conteneurs multiples"
services: container-service
author: jcorioland
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 38877afb63e993eeaab723a6ea5f4c40d3c956a5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pipeline CI/CD complet pour déployer une application à conteneurs multiples sur Azure Container Service avec Docker Swarm à l’aide de Visual Studio Team Services

Un des plus grands défis lors du développement d’applications modernes pour le cloud est de pouvoir fournir ces applications en continu. Dans cet article, vous allez apprendre à implémenter un pipeline d’intégration et de déploiement complets et continus (CI/CD) à l’aide d’Azure Container Service avec Docker Swarm, le Registre de conteneurs Azure et la gestion automatisée de la build et de la mise en production de Visual Studio Team Services.

Cet article est basé sur une application simple, disponible sur [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), développée avec ASP.NET Core. L’application est composée de quatre services différents : trois API web et un serveur web frontal :

![Exemple d’application MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

L’objectif est de fournir en continu cette application dans un cluster Docker Swarm, à l’aide de Visual Studio Team Services. La figure suivante présente ce pipeline de distribution en continu :

![Exemple d’application MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Voici une brève explication de ces étapes :

1. Les modifications du code sont validées dans le référentiel de code source (ici, GitHub). 
2. GitHub déclenche une build dans Visual Studio Team Services. 
3. Visual Studio Team Services obtient la version la plus récente des sources et crée toutes les images qui composent l’application. 
4. Visual Studio Team Services envoie chaque image dans un registre Docker créé à l’aide du service de Registre de conteneurs Azure. 
5. Visual Studio Team Services déclenche une nouvelle mise en production. 
6. La version exécute quelques commandes à l’aide de SSH sur le nœud principal du cluster Azure Container Service. 
7. Docker Swarm sur le cluster extrait la dernière version des images. 
8. La nouvelle version de l’application est déployée à l’aide de Docker Compose. 

## <a name="prerequisites"></a>Composants requis

Avant de commencer ce didacticiel, vous devez effectuer les tâches suivantes :

- [Création d’un cluster Swarm dans Azure Container Service](container-service-deployment.md)
- [Connexion avec le cluster Swarm dans Azure Container Service](../container-service-connect.md)
- [Utilisation d’un Registre de conteneurs Azure](../../container-registry/container-registry-get-started-portal.md)
- [Création d’un compte et d’un projet d’équipe Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Bifurcation du référentiel GitHub sur votre compte GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Vous devez également posséder un ordinateur Ubuntu (14.04 ou 16.04) avec Docker installé. Cet ordinateur est utilisé par Visual Studio Team Services lors des processus de build et mise en production. Pour créer cette ordinateur, vous pouvez par exemple utiliser l’image disponible dans la [Place de marché Azure](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Étape 1 : configuration de votre compte Visual Studio Team Services 

Dans cette section, vous allez configurer votre compte Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Configuration d’un agent de build Linux Visual Studio Team Services

Pour créer des images Docker et envoyer ces images dans un Registre de conteneurs Azure à partir d’une build Visual Studio Team Services, vous devez inscrire un agent Linux. Vous avez le choix entre les options d’installation suivantes :

* [Déploiement d’un agent sur Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Utilisation de Docker pour exécuter l’agent VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Installation de l’extension VSTS de l’Intégration à Docker

Microsoft fournit une extension VSTS pour utiliser Docker dans les processus de build et mise en production. Cette extension est disponible dans la [Place de marché VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Cliquez sur **Install** (Installer) pour ajouter cette extension à votre compte VSTS :

![Installation de l’Intégration à Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Vous êtes invité à vous connecter à votre compte VSTS à l’aide de vos informations d’identification. 

### <a name="connect-visual-studio-team-services-and-github"></a>Connexion entre Visual Studio Team Services et GitHub

Configurez une connexion entre votre projet VSTS et votre compte GitHub.

1. Dans votre projet Visual Studio Team Services, cliquez sur l’icône **Paramètres** dans la barre d’outils, puis sélectionnez **Services**.

    ![Visual Studio Team Services - Connexion externe](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. À gauche, cliquez sur **Nouveau point de terminaison du service** > **GitHub**.

    ![Visual Studio Team Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Pour autoriser VSTS à fonctionner avec votre compte GitHub, cliquez sur **Autoriser** et suivez la procédure décrite dans la fenêtre qui s’ouvre.

    ![Visual Studio Team Services - Autoriser GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Connexion de VSTS à votre Registre de conteneurs Azure et votre cluster Azure Container Service

Les dernières étapes avant d’aborder le pipeline CI/CD consistent à configurer les connexions externes à votre Registre de conteneurs et votre cluster Docker Swarm dans Azure. 

1. Dans les paramètres **Services** de votre projet Visual Studio Team Services, ajoutez un point de terminaison de service de type **Registre Docker**. 

2. Dans le menu contextuel qui s’ouvre, entrez l’URL et les informations d’identification de votre Registre de conteneurs Azure.

    ![Visual Studio Team Services - Registre Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Pour le cluster Docker Swarm, ajoutez un point de terminaison de type **SSH**. Ensuite, entrez les informations de connexion SSH de votre cluster Swarm.

    ![Visual Studio Team Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toute la configuration est à présent effectuée. Dans les étapes suivantes, vous allez créer le pipeline CI/CD qui génère et déploie l’application sur le cluster Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Étape 2 : création de la définition de la build

Dans cette étape, vous configurez une définition de build pour votre projet VSTS et définissez le flux de travail pour vos images de conteneur.

### <a name="initial-definition-setup"></a>Configuration de la définition initiale

1. Pour créer une définition de build, connectez-vous à votre projet Visual Studio Team Services et cliquez sur **Build et mise en production**. 

2. Dans la section **Définitions de build** , cliquez sur **+ Nouveau**. Sélectionnez le modèle **Vide**.

    ![Visual Studio Team Services - Nouvelle définition de build](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Configurez la nouvelle build avec une source de référentiel GitHub, cochez **Intégration continue**, puis sélectionnez la file d’attente de l’agent où vous avez enregistré votre agent Linux. Cliquez sur **Créer** pour créer la définition de la build.

    ![Visual Studio Team Services - Création de la définition de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Dans la page **Définitions de build**, ouvrez d’abord l’onglet **Référentiel** et configurez la build pour utiliser la bifurcation du projet MyShop que vous avez préalablement créé. Assurez-vous de sélectionner *acs-docs* comme **Branche par défaut**.

    ![Visual Studio Team Services - Configuration du référentiel de la build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Sous l’onglet **Déclencheurs**, configurez la build devant être déclenchée après chaque validation. Sélectionnez **Intégration continue** et **Modifications par lots**.

    ![Visual Studio Team Services - Configuration du déclencheur de la build](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Définition du flux de travail de la build
Les étapes suivantes définissent le flux de travail de la build. Il existe cinq images de conteneur à générer pour l’application *MyShop*. Chaque image est générée à l’aide du fichier Docker situé dans les dossiers du projet :

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Vous devez ajouter deux étapes Docker pour chaque image, une pour créer l’image et une autre pour envoyer l’image dans le Registre de conteneurs Azure. 

1. Pour ajouter une étape dans le flux de travail de la build, cliquez sur **+ Ajouter une étape de build** et sélectionnez **Docker**.

    ![Visual Studio Team Services - Ajout d’étapes de build](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Pour chaque image, configurez une étape qui utilise la commande `docker build`.

    ![Visual Studio Team Services - Build Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Pour l’opération de génération, sélectionnez votre Registre de conteneurs Azure, l’action **Build an image** (Générer une image) et le fichier Docker qui définit chaque image. Choisissez comme **Build context** (Contexte de génération) le répertoire racine du fichier Docker et définissez le **Nom de l’image**. 
    
    Comme indiqué dans l’écran précédent, faites commencer le nom de l’image par l’URI de votre Registre de conteneurs Azure. (Vous pouvez également utiliser une variable de build pour paramétrer la balise de l’image, telle que l’identificateur de build dans cet exemple).

3. Pour chaque image, configurez une seconde étape qui utilise la commande `docker push`.

    ![Visual Studio Team Services - Envoi du fichier Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Pour l’opération push, sélectionnez votre Registre de conteneurs Azure, l’action **Push an image** (Envoyer une image), puis entrez le **Nom de l’image** généré à l’étape précédente.

4. Après avoir configuré les étapes de build et push pour chacune des cinq images, ajoutez deux étapes supplémentaires dans le flux de travail de la build.

    a. Une tâche de ligne de commande qui utilise un script bash pour remplacer l’occurrence *BuildNumber* dans le fichier docker-compose.yml par l’ID de la build actuelle. Consultez l’écran suivant pour plus de détails.

    ![Visual Studio Team Services - Mise à jour du fichier Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Une tâche qui supprime le fichier Compose mis à jour comme un artefact de build afin qu’il puisse être utilisé dans la mise en production. Consultez l’écran suivant pour plus de détails.

    ![Visual Studio Team Services - Publication du fichier Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Cliquez sur **Enregistrer** et affectez un nom à votre définition de build.

## <a name="step-3-create-the-release-definition"></a>Étape 3 : création de la définition de mise en production

Visual Studio Team Services vous permet de [gérer les mises en production dans les différents environnements](https://www.visualstudio.com/team-services/release-management/). Vous pouvez activer le déploiement continu pour vous assurer que votre application est déployée sur vos différents environnements (par exemple, développement, test, pré-production et production) de façon fluide. Vous pouvez créer un nouvel environnement qui représente votre cluster Docker Swarm Azure Container Service.

![Visual Studio Team Services - Mise en production vers ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuration de la mise en production initiale

1. Pour créer une définition de mise en production, cliquez sur **Mises en production** > **+ Mise en production**.

2. Pour configurer la source de l’artefact, cliquez sur **Artefacts** > **Lier une source d’artefact**. Ici, liez cette nouvelle définition de mise en production de la build que vous avez définie à l’étape précédente. Ainsi, le fichier docker-compose.yml devient disponible dans le processus de mise en production.

    ![Visual Studio Team Services - Artefacts de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Pour configurer le déclencheur de la mise en production, cliquez sur **Déclencheurs** et sélectionnez **Déploiement continu**. Définissez le déclencheur sur la même source d’artefact. Ce paramètre garantit qu’une nouvelle mise en production commence dès que la build se termine correctement.

    ![Visual Studio Team Services - Déclencheurs de mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Définition du flux de travail de mise en production

Le flux de travail de mise en production est composé de deux tâches que vous ajoutez.

1. Configurez une tâche pour copier en toute sécurité le fichier compose dans un dossier *deploy* sur le nœud principal Docker Swarm à l’aide de la connexion SSH que vous avez configurée précédemment. Consultez l’écran suivant pour plus de détails.

    ![Visual Studio Team Services - SCP mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Configurez une deuxième tâche pour exécuter une commande bash afin d’exécuter les commandes `docker` et `docker-compose` sur le nœud principal. Consultez l’écran suivant pour plus de détails.

    ![Visual Studio Team Services - Bash mise en production](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    La commande exécutée sur le nœud principal utilise les interfaces de ligne de commande Docker et Docker-Compose pour effectuer les tâches suivantes :

    - connexion au Registre de conteneurs Azure (il utilise trois variables de build qui sont définies sous l’onglet **Variables**) ;
    - définition de la variable **DOCKER_HOST** pour fonctionner avec le point de terminaison Swarm (:2375) ;
    - accès au dossier *deploy* qui a été créé par la tâche de copie sécurisée précédente et qui contient le fichier docker-compose.yml ; 
    - exécution des commandes `docker-compose` qui extraient les nouvelles images, arrêtent les services, suppriment les services et créent les conteneurs.

    >[!IMPORTANT]
    > Comme indiqué dans l’écran précédent, laissez la case **Échec sur STDERR** désactivée. C’est un paramètre important, car `docker-compose` imprime plusieurs messages de diagnostic, par exemple pour les conteneurs en cours d’arrêt ou de suppression, sur la sortie d’erreur standard. Si vous activez la case à cocher, Visual Studio Team Services signale que des erreurs se sont produites lors de la mise en production, même si tout s’est bien passé.
    >
3. Enregistrez cette nouvelle définition de mise en production.


>[!NOTE]
>Ce déploiement inclut des temps d’arrêt, car nous arrêtons les anciens services pour exécuter le nouveau. Il est possible d’éviter ce problème en effectuant un déploiement bleu-vert.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Étape 4. test du pipeline CI/CD

Maintenant que vous avez terminé la configuration, il est temps de tester ce nouveau pipeline CI/CD. La façon la plus simple de tester consiste à mettre à jour le code source et à valider les modifications dans votre référentiel GitHub. Quelques secondes après avoir envoyé le code, vous verrez une nouvelle build s’exécutant dans Visual Studio Team Services. Une fois l’opération terminée avec succès, une nouvelle build sera déclenchée et déploiera la nouvelle version de l’application sur le cluster Azure Container Service.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur CI/CD avec Visual Studio Team Services, consultez la [vue d’ensemble de la build VSTS](https://www.visualstudio.com/docs/build/overview).
