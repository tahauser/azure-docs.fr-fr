---
title: Plug-in Azure Service Fabric pour Eclipse | Documents Microsoft
description: Prise en main du plug-in Service Fabric pour Eclipse
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2016
ms.author: saysa
ms.openlocfilehash: 90faa353a1f6b83ec0cd8b9ee2cd247905ef4524
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in Service Fabric pour le développement d’applications Java sous Eclipse
Eclipse est l’un des environnements de développement intégrés (IDE) les plus largement utilisés par les développeurs Java. Dans cet article, nous décrivons comment configurer votre environnement de développement Eclipse pour utiliser Azure Service Fabric. Découvrez comment installer le plug-in Service Fabric, créer une application Service Fabric et déployer votre application Service Fabric dans un cluster Service Fabric local ou distant sur Eclipse Neon. 

> [!NOTE]
> Le plug-in Eclipse n’est actuellement pas pris en charge sous Windows. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Installer ou mettre à jour le plug-in Service Fabric sur Eclipse Neon
Vous pouvez installer un plug-in Service Fabric sur Eclipse. Ce plug-in peut aider à simplifier le processus de génération et le déploiement des services Java.

1.  Assurez-vous que la dernière version d’Eclipse Neon et la dernière version de Buildship (1.0.17 ou une version ultérieure) sont installées :
    -   Pour vérifier les versions des composants installés, dans Eclipse Neon, accédez à **Aide** > **Détails de l’installation**.
    -   Pour mettre à jour Buildship, consultez [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship : plug-in Eclipse pour Gradle).
    -   Pour vérifier et installer les mises à jour pour Eclipse Neon, accédez à **Aide** > **Rechercher les mises à jour**.

2.  Pour installer le plug-in Service Fabric, dans Eclipse Neon, accédez à **Aide** > **Installer un nouveau logiciel**.
  1.    Dans la zone **Work with** (Utiliser avec), entrez : **http://dl.microsoft.com/eclipse**.
  2.    Cliquez sur **Add**.

         ![Plug-in Service Fabric pour Eclipse Neon][sf-eclipse-plugin-install]
  3.    Sélectionnez le plug-in Service Fabric, puis cliquez sur **Suivant**.
  4.    Terminez les étapes d’installation, puis acceptez les termes du contrat de licence du logiciel Microsoft.

Si le plug-in Service Fabric est déjà installé, assurez-vous que vous disposez de la dernière version. Pour vérifier les mises à jour disponibles, accédez à **Aide** > **Détails de l’installation**. Dans la liste des plug-ins installés, sélectionnez Service Fabric, puis cliquez sur **Mettre à jour**. Les mises à jour disponibles seront installées.

> [!NOTE]
> La lenteur de l’installation ou de la mise à jour du plug-in Service Fabric peut être due à un paramètre d’Eclipse. Eclipse collecte des métadonnées sur toutes les modifications pour mettre à jour les sites enregistrés avec votre instance d’Eclipse. Pour accélérer le processus de vérification et l’installation d’une mise à jour du plug-in Service Fabric, accédez à **Sites logiciels disponibles**. Désactivez les cases à cocher pour tous les sites, sauf pour celui qui pointe vers l’emplacement du plug-in Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Si Eclipse ne fonctionne pas comme prévu sur votre Mac ou exige vous soyez un super utilisateur pour l’exécuter, accédez au dossier **ECLIPSE_INSTALLATION_PATH**, puis au sous-dossier **Eclipse.app/Contents/MacOS**. Démarrez Eclipse en exécutant `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Créer une application Service Fabric dans Eclipse

1.  Dans Eclipse Neon, accédez à **Fichier** > **Nouveau** > **Autre**. Sélectionnez **Projet Service Fabric**, puis cliquez sur **Suivant**.

    ![Nouveau projet Service Fabric, page 1][create-application/p1]

2.  Saisissez un nom pour votre projet, puis cliquez sur **Suivant**.

    ![Nouveau projet Service Fabric, page 2][create-application/p2]

3.  Dans la liste des modèles, sélectionnez **Modèle de service**. Sélectionnez votre type de modèle de service (acteur, sans état, conteneur ou binaire invité), puis cliquez sur **Suivant**.

    ![Nouveau projet Service Fabric, page 3][create-application/p3]

4.  Saisissez le nom du service et les détails du service, puis cliquez sur **Terminer**.

    ![Nouveau projet Service Fabric, page 4][create-application/p4]

5. Lorsque vous créez votre premier projet Service Fabric, dans la boîte de dialogue **Ouvrir la perspective associée**, cliquez sur **Oui**.

    ![Nouveau projet Service Fabric, page 5][create-application/p5]

6.  Votre nouveau projet ressemble à ceci :

    ![Nouveau projet Service Fabric, page 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Créer et déployer une application Service Fabric dans Eclipse

1.  Cliquez avec le bouton droit de la souris sur votre nouvelle application Service Fabric, puis sélectionnez **Service Fabric**.

    ![Menu contextuel Service Fabric][publish/RightClick]

2. Dans le sous-menu, sélectionnez l’option souhaitée :
    -   Pour créer l’application sans suppression, cliquez sur **Créer l’application**.
    -   Pour supprimer, puis générer l’application, cliquez sur **Régénérer l’application**.
    -   Pour supprimer l’application des artefacts générés, cliquez sur **Nettoyer l’application**.

3.  Dans ce menu, vous pouvez également déployer, annuler le déploiement et publier votre application :
    -   Pour effectuer le déploiement dans votre cluster local, cliquez sur **Déployer l’application**.
    -   Dans la boîte de dialogue **Publication de l’application**, sélectionnez un profil de publication :
        -  **Local.json**
        -  **Cloud.json**

     Ces fichiers JavaScript Object Notation (JSON) stockent des informations (par exemple, les points de terminaison de connexion et les informations de sécurité) requises pour se connecter à votre cluster local ou cloud (Azure).

  ![Menu Publier de Service Fabric][publish/Publish]

Une autre méthode pour déployer votre application Service Fabric consiste à utiliser des configurations d’exécution Eclipse.

  1.    Accédez à **Exécuter** > **Configurations d’exécution**.
  2.    Sous **Projet Gradle**, sélectionnez la configuration d’exécution **ServiceFabricDeployer**.
  3.    Dans le volet de droite, sous l’onglet **Arguments**, pour **publishProfile**, sélectionnez **local** ou **cloud**.  La valeur par défaut est **local**. Pour effectuer un déploiement dans un cluster cloud ou distant, sélectionnez **cloud**.
  4.    Pour vous assurer que les informations appropriées sont renseignées dans les profils de publication, modifiez **Local.json** ou **Cloud.json** selon les besoins. Vous pouvez ajouter ou mettre à jour les détails du point de terminaison et les informations d’identification de sécurité.
  5.    Vérifiez que **Répertoire de travail** pointe vers l’application que vous souhaitez déployer. Pour modifier l’application, cliquez sur le bouton **Espace de travail**, puis sélectionnez l’application souhaitée.
  6.    Cliquez sur **Appliquer**, puis sur **Exécuter**.

Votre application est générée et déployée en quelques instants. Vous pouvez contrôler le statut de déploiement dans Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Ajouter un service Service Fabric à votre application Service Fabric

Pour ajouter un service Service Fabric à une application Service Fabric existante, procédez comme suit :

1.  Cliquez avec le bouton droit de la souris sur le projet auquel vous souhaitez ajouter un service, puis cliquez sur **Service Fabric**.

    ![Ajouter un service Service Fabric, page 1][add-service/p1]

2.  Cliquez sur **Ajouter un service Service Fabric** et terminez les d’étapes pour ajouter un service au projet.
3.  Sélectionnez le modèle de service que vous souhaitez ajouter à votre projet et cliquez sur **Suivant**.

    ![Ajouter un service Service Fabric, page 2][add-service/p2]

4.  Entrez le nom du service (et autres détails, si nécessaire), puis cliquez sur le bouton **Ajouter un service**.  

    ![Ajouter un service Service Fabric, page 3][add-service/p3]

5.  Une fois le service ajouté, la structure globale de votre projet ressemble au projet suivant :

    ![Ajouter un service Service Fabric, page 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Modifier les versions de manifeste de votre application Java Service Fabric

Pour modifier les versions de manifeste, cliquez avec le bouton droit sur le projet, accédez à **Service Fabric** et sélectionnez **Modifier les versions de manifeste...**  à partir de la liste déroulante du menu. Dans l’Assistant, vous pouvez mettre à jour les versions du manifeste d’application, du manifeste de service et des packages **Code**, **Config** et **Data**.

Si vous activez l’option **Mettre à jour automatiquement les versions des applications et des services**, puis que vous mettez à jour une version, les versions des manifestes seront automatiquement mises à jour. Par exemple, vous commencez par cocher la case, vous changez la version du **Code** de 0.0.0 à 0.0.1, puis vous cliquez sur **Terminer**. La version du manifeste de service et du manifeste d’application sera automatiquement mise à jour à 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Mettre à niveau votre application Java Service Fabric

Prenons l’exemple d’un scénario de mise à niveau. Admettons que vous avez créé le projet **App1** à l’aide du plug-in Service Fabric dans Eclipse. Vous l’avez déployé à l’aide du plug-in pour créer une application nommée **fabric:/App1Application**. Le type d’application est **App1AppicationType**, et la version de l’application est 1.0. Vous souhaitez à présent mettre à niveau votre application sans interrompre la disponibilité.

Tout d’abord, apportez les éventuelles modifications à votre application, puis reconstruisez le service modifié. Mettez à jour le fichier manifeste du service modifié (ServiceManifest.xml) avec les versions mises à jour du service (Code, Config ou Données, comme il convient). Modifiez également le manifeste de l’application (ApplicationManifest.xml) avec le numéro de version mis à jour de l’application et le service modifié.  

Pour mettre à niveau votre application à l’aide d’Eclipse Neon, vous pouvez créer un profil de configuration d’exécution en double. Ensuite, utilisez-le pour mettre à niveau votre application selon vos besoins.

1.  Accédez à **Exécuter** > **Configurations d’exécution**. Dans le volet de gauche, cliquez sur la petite flèche à gauche de **Projet Gradle**.
2.  Cliquez avec le bouton droit de la souris sur **ServiceFabricDeployer**, puis sélectionnez **Dupliquer**. Entrez un nouveau nom pour cette configuration, par exemple, **ServiceFabricUpgrader**.
3.  Dans le volet de droite, sous l’onglet **Arguments**, remplacez **-Pconfig='deploy'** par **-Pconfig='upgrade'**, puis cliquez sur **Appliquer**.

Ce processus crée et enregistre un profil de configuration d’exécution que vous pouvez utiliser à tout moment pour mettre à niveau votre application. Il permet également d’obtenir la dernière version mise à jour du type d’application à partir du fichier de manifeste d’application.

La mise à niveau de l’application prend quelques minutes. Vous pouvez contrôler la mise à niveau de l’application dans Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migration d’anciennes applications Java Service Fabric à utiliser avec Maven
Nous avons récemment déplacé les bibliothèques Java Service Fabric vers un référentiel Maven depuis le Kit de développement logiciel (SDK) Java Service Fabric. Tandis que les nouvelles applications que vous générez à l’aide d’Eclipse génèrent les derniers projets mis à jour (en mesure de fonctionner avec Maven), vous pouvez mettre à jour vos services sans état Service Fabric existants ou vos applications Java d’acteur existantes ayant utilisé le Kit de développement logiciel (SDK) précédemment, afin d’utiliser les dépendances Java Service Fabric à partir de Maven. Suivez les étapes mentionnées [ici](service-fabric-migrate-old-javaapp-to-use-maven.md) pour vous assurer qu’une version plus ancienne de votre application fonctionne avec Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
