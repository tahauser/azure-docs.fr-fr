---
title: "Déployer une application Azure Service Fabric sur un cluster à partir de Visual Studio| Microsoft Docs"
description: "Découvrez comment déployer une application sur un cluster à partir de Visual Studio"
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/21/2018
ms.author: mikkelhegn
ms.custom: mvc
ms.openlocfilehash: 21c991a4e3f9ae19a4ad4a96427fdc1c91c55a1c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Didacticiel : déployer une application sur un cluster Service Fabric dans Azure
Deuxième d’une série, ce didacticiel vous montre comment déployer une application Azure Service Fabric sur un nouveau cluster dans Azure directement depuis Visual Studio.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster à partir de Visual Studio
> * Déployer une application sur un cluster distant à l’aide de Visual Studio


Dans cette série de didacticiels, nous allons aborder les points suivants :
> [!div class="checklist"]
> * [Créer une application .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * Déployer l’application sur un cluster distant
> * [Configurer l’intégration et le déploiement continus à l’aide de Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurer la surveillance et les diagnostics pour l’application](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>configuration requise
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Installez Visual Studio 2017](https://www.visualstudio.com/) et les charges de travail **Développement Azure** et **Développement web et ASP.NET**.
- [Installez le Kit de développement logiciel (SDK) Service Fabric](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Télécharger l’exemple d’application de vote
Si vous n’avez pas généré l’exemple d’application de vote lors de la [première partie de cette série de didacticiels](service-fabric-tutorial-create-dotnet-app.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>Déployer l’exemple d’application

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>Sélectionner un cluster Service Fabric pour publication
À présent que l’application est prête, vous pouvez la déployer sur un cluster directement à partir de Visual Studio.

Vous disposez de deux options pour le déploiement :
- Créer un cluster à partir de Visual Studio. Cette option vous permet de créer un cluster sécurisé directement depuis Visual Studio avec les configurations que vous voulez. Ce type de cluster est idéal pour des scénarios de test, où vous pouvez créer le cluster puis publier directement au sein de Visual Studio.
- Publier sur un cluster existant dans votre abonnement.

Ce didacticiel suit les étapes de création d’un cluster à partir de Visual Studio. Pour les autres options, vous pouvez copier et coller votre point de terminaison de connexion ou le choisir depuis votre abonnement.
> [!NOTE]
> De nombreux services utilisent le proxy inverse pour communiquer entre eux. Le proxy inverse est activé par défaut pour les clusters créés depuis Visual Studio et les clusters tiers.  Si vous utilisez un cluster existant, vous devez [activer le proxy inverse dans le cluster](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Déployer l’application sur le cluster Service Fabric

1. Cliquez avec le bouton droit sur le projet de l’application dans l’Explorateur de solutions et choisissez **Publier**.

2. Connectez-vous avec votre compte Azure afin d’avoir accès à votre ou vos abonnements. Cette étape est facultative si vous utilisez un cluster tiers.

3. Sélectionnez le menu déroulant pour le **Point de terminaison de connexion** puis l’option « <Create New Cluster...> ».
    
    ![Boîte de dialogue Publier](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. Dans la boîte de dialogue « Créer un cluster », modifiez les paramètres suivants :

    1. Spécifiez le nom de votre cluster dans le champ « Nom de cluster », ainsi que l’abonnement et l’emplacement à utiliser.
    2. Facultatif : vous pouvez modifier le nombre de nœuds. Par défaut, vous disposez de trois nœuds, qui est le minimum requis pour tester des scénarios Service Fabric.
    3. Sélectionnez l’onglet « Certificat ». Dans cet onglet, tapez un mot de passe à utiliser pour sécuriser le certificat de votre cluster. Ce certificat aide à sécuriser votre cluster. Vous pouvez aussi modifier le chemin vers l’emplacement où vous voulez enregistrer le certificat. Visual Studio peut aussi importer le certificat pour vous, car il s’agit d’une étape nécessaire à la publication de l’application dans le cluster.
    4. Sélectionnez l’onglet « Détail de la machine virtuelle ». Spécifiez le mot de passe que vous voulez utiliser pour les machines virtuelles qui forment le cluster. Le nom d’utilisateur et le mot de passe peuvent être utilisés pour se connecter aux machines virtuelles à distance. Vous devez aussi sélectionner une taille de machine virtuelle et pouvez changer l’image de la machine virtuelle si nécessaire.
    5. Facultatif : dans l’onglet « Avancé », vous pouvez modifier la liste des ports ouverts sur l’équilibreur de charge qui sera créé avec le cluster. Vous pouvez aussi ajouter une clé Application Insights existante à utiliser pour y diriger les fichiers de journal des applications.
    6. Quand vous avez terminé les modifications des paramètres, sélectionnez le bouton « Créer ». La création prend quelques minutes, la fenêtre de sortie vous informe lorsque le cluster est complètement créé.
    
    ![Boîte de dialogue Créer un cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Une fois que le cluster que vous voulez utiliser est prêt, cliquez avec le bouton droit sur le projet de l’application et choisissez **Publier**.

    Une fois la publication terminée, vous devez être en mesure d’envoyer une requête à l’application via un navigateur.

5. Ouvrez le navigateur de votre choix et saisissez l’adresse du cluster (le point de terminaison de connexion sans les informations de port ; par exemple, win1kw5649s.westus.cloudapp.azure.com).

    Vous devez maintenant obtenir le même résultat que lors de l’exécution de l’application en local.

    ![Réponse de l’API à partir du cluster](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un cluster à partir de Visual Studio
> * Déployer une application sur un cluster distant à l’aide de Visual Studio

Passez au didacticiel suivant :
> [!div class="nextstepaction"]
> [Configurer l’intégration continue avec Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
