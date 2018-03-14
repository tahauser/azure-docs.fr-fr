---
title: Configurer un cluster Linux Azure Service Fabric sur Windows | Microsoft Docs
description: "Cet article explique comment configurer des clusters Linux Service Fabric exécutés sur des machines de développement Windows. Cette méthode est particulièrement utile pour le développement multiplateforme."
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: db6ad8b83ce34a8b86de822bc074e8a13345a1b4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurer un cluster Linux Service Fabric sur votre machine de développement Windows

Ce document explique comment configurer une infrastructure Linux Service Fabric locale sur des machines de développement Windows. La configuration d'un cluster Linux local est utile pour tester rapidement les applications ciblées pour les clusters Linux mais développées sur une machine Windows.

## <a name="prerequisites"></a>Prérequis
Les clusters Service Fabric basés sur Linux ne s'exécutent pas nativement sous Windows. Pour exécuter un cluster Service Fabric local, une image de conteneur Docker préconfigurée est fournie. Avant de commencer, vous avez besoin des éléments suivants :

* Au moins 4 Go de RAM
* Dernière version de [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)

>[!TIP]
> * Vous pouvez suivre les étapes indiquées dans la [documentation](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) officielle de Docker pour installer Docker sur Windows. 
> * Une fois l’installation terminée, suivez les étapes indiquées [ici](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) pour vérifier que l’installation s’est déroulée correctement.


## <a name="create-a-local-container-and-setup-service-fabric"></a>Créer un conteneur local et configurer Service Fabric
Pour configurer un conteneur Docker local et y exécuter un cluster Service Fabric, procédez comme suit :

1. Extrayez l’image du référentiel Docker Hub :

    ```powershell
    docker pull microsoft/service-fabric-onebox
    ```

2. Mettez à jour la configuration du démon Docker sur votre ordinateur hôte avec les informations suivantes, puis redémarrez le démon Docker : 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    La méthode conseillée pour la mise à jour est la suivante : accédez à l’icône Docker > Réglages > Démon > Avancé et effectuez la mise à jour. Redémarrez ensuite le démon Docker pour que les modifications prennent effet. 

3. Démarrez une instance de conteneur Service Fabric One-Box avec l’image :

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    > * Le fait de nommer votre instance de conteneur vous permet de la gérer plus facilement. 
    > * Si votre application écoute sur certains ports, il doit être spécifié à l’aide de balises -p supplémentaires. Par exemple, si votre application écoute sur le port 8080, exécutez docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox

4. Connectez-vous au conteneur Docker en mode SSH interactif :

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Exécutez le script de configuration qui va extraire les dépendances requises, puis démarrer le cluster sur le conteneur.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Une fois l’étape 5 terminée, vous pouvez accéder à ``http://localhost:19080`` à partir de votre machine Windows. Vous devriez alors voir l’Explorateur Service Fabric. À ce stade, vous pouvez vous connecter à ce cluster à l'aide des outils de votre machine de développement Windows et déployer une application ciblée pour les clusters Linux Service Fabric. 

    > [!NOTE]
    > Le plug-in Eclipse n’est actuellement pas pris en charge sous Windows. 

## <a name="next-steps"></a>étapes suivantes
* Prise en main d'[Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Consulter d’autres [exemples Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png