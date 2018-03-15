---
title: "Créer un service fiable Azure Service Fabric avec C#"
description: "Créer, déployer et déboguer une application Reliable Services basée sur Azure Service Fabric avec Visual Studio."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: c3655b7b-de78-4eac-99eb-012f8e042109
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: 43f77a1a2e1bbe28bb646aa23c28c253c20e8dda
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Création de votre première application Reliable Services avec état C# Service Fabric

Découvrez comment déployer votre première application Azure Service Fabric pour .NET sur Windows en quelques minutes. Une fois l’opération terminée, vous obtenez un cluster local fonctionnant avec une application Reliable Services.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement](service-fabric-get-started.md). Ce processus inclut l’installation du Kit de développement logiciel (SDK) de Service Fabric et de Visual Studio 2017 ou 2015.

## <a name="create-the-application"></a>Création de l’application

1. Démarrez Visual Studio en tant qu’administrateur.

2. Créez un projet en sélectionnant Ctrl + Maj + N.

3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Cloud** > **Application Service Fabric**.

4. Appelez l’application **MyApplication**. Sélectionnez ensuite **OK**.

   ![Boîte de dialogue Nouveau projet dans Visual Studio][1]

5. Vous pouvez créer n’importe quel type d’application Service Fabric à partir de la boîte de dialogue suivante. Pour ce démarrage rapide, choisissez **Service avec état**.

6. Appelez le service **MyStatefulService**. Sélectionnez ensuite **OK**.

    ![Boîte de dialogue Nouveau service dans Visual Studio][2]

    Visual Studio crée le projet d’application et le projet de service avec état. Puis il les affiche dans l’Explorateur de solutions.

    ![Explorateur de solutions après la création d’une application de service avec état][3]

    Le projet d’application (**MyApplication**) ne contient pas de code. Au lieu de cela, il fait référence à un ensemble de projets de service. Il contient également trois autres types de contenu :

    * **Profils de publication**  
    Profils pour le déploiement sur différents environnements.

    * **Scripts**  
    Des scripts PowerShell de déploiement ou de mise à niveau de votre application.

    * **Définition d’application**  
Inclut le fichier ApplicationManifest.xml sous *ApplicationPackageRoot* qui décrit la composition de votre application. Les fichiers de paramètres d’application associés se trouvent sous le dossier *ApplicationParameters*, qui peut être utilisé pour spécifier des paramètres spécifiques à l’environnement. Visual Studio sélectionne un fichier de paramètres d’application qui est spécifié dans le profil de publication associé.
    
Pour avoir une vue d’ensemble du contenu du projet de service, consultez l’article [Prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Déployer et déboguer l’application.

Maintenant que vous avez une application, exécutez, déployez et déboguez la en procédant comme suit.

1. Dans Visual Studio, sélectionnez F5 pour déployer l’application en vue d’un débogage.

    >[!NOTE]
    >La première fois que vous exécutez et déployez l’application localement, Visual Studio crée un cluster local pour le débogage. Cette opération peut prendre un certain temps. L’état de la création du cluster est affiché dans la fenêtre Sortie Visual Studio.

    Une fois le cluster prêt, vous obtenez une notification de la part de l’application de gestionnaire de la barre d’état système de cluster local incluse avec le kit de développement logiciel.
    
    ![Notification de barre d’état système de cluster local][4]

    Après que l’application a démarré, Visual Studio affiche automatiquement l’Observateur d’événements de diagnostic, où vous pouvez voir la sortie de suivi depuis vos services.
    
    ![Observateur d’événements de diagnostic][5]

    >[!NOTE]
    >Les événements démarrent automatiquement le suivi dans l’Observateur d’événements de Diagnostic. Si vous devez configurer manuellement l’Observateur d’événements de Diagnostic, vous devez tout d’abord ouvrir le fichier `ServiceEventSource.cs`, qui se trouve dans le projet **MyStatefulService**. Copiez la valeur de l’attribut `EventSource` en haut de la classe `ServiceEventSource`. Dans le prochain exemple, l’événement source est appelé `"MyCompany-MyApplication-MyStatefulService"`, qui peut être différent dans votre cas.
>
    >![Rechercher le nom de la source de l’événement de service][service-event-source-name]


2. Ensuite, ouvrez la boîte de dialogue **Fournisseurs ETW**. Puis sélectionnez l’icône d’engrenage qui se trouve sur l’onglet **Événements de diagnostic**. Collez le nom de la source de l’événement que vous avez copié dans la zone de saisie **Fournisseurs ETW**. Ensuite, sélectionnez le bouton **Appliquer**. Le suivi des événements démarre alors automatiquement.

    ![Définir le nom la source des événements de diagnostic][setting-event-source-name]

    Vous devriez maintenant voir les événements s’afficher dans la fenêtre Événements de diagnostic.

    Le modèle de service avec état indique une valeur du compteur incrémentée dans la méthode `RunAsync` de **MyStatefulService.cs**.

3. Pour plus d’informations, développez un des événements, et notamment le nœud sur lequel le code s’exécute. Dans ce cas, il s’agit du **\_Node\_0,** bien que cela puisse être différent sur votre ordinateur.
   
    ![Détails de l’Observateur d’événements de diagnostic][6]

4. Le cluster local contient cinq nœuds qui sont hébergés sur un seul ordinateur. Dans un environnement de production, chaque nœud est hébergé sur une machine virtuelle ou physique distincte. Pour simuler la perte d’une machine alors que vous testez le débogueur Visual Studio, examinons l’un des nœuds du cluster local.

5. Dans la fenêtre de **l’Explorateur de solutions**, ouvrez **MyStatefulService.cs**. 

6. Recherchez la méthode `RunAsync`, puis définissez un point d’arrêt sur la première ligne de la méthode.

    ![Point d’arrêt dans la méthode RunAsync de service avec état][7]

7. Démarrez l’outil Service Fabric Explorer en faisant un clic droit sur l’application du **gestionnaire du cluster local** dans la zone de notification, puis en choisissant **Gérer le cluster local**.

    ![Démarrer Service Fabric Explorer à partir du gestionnaire de cluster local][systray-launch-sfx]

    [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) offre une représentation visuelle d’un cluster. Il inclut l’ensemble des applications qui y sont déployées, ainsi que le jeu de nœuds physiques qui le composent.

8. Dans le volet de gauche, développez **Cluster** > **Nœuds** et recherchez le nœud sur lequel votre code s’exécute. Ensuite, pour simuler le redémarrage de l’ordinateur, sélectionnez **Actions** > **Désactiver (redémarrer)**.

    ![Arrêter un nœud Service Fabric Explorer][sfx-stop-node]

    Vous devez voir momentanément le point d’arrêt atteint dans Visual Studio, car le calcul vous faisiez sur un nœud bascule en toute transparence vers un autre.

9. Revenez ensuite à l’Observateur d’événements de diagnostic et prenez connaissance des messages. Le compteur a continué de s’incrémenter, même si les événements proviennent en fait d’un autre nœud.

    ![Observateur d’événements de diagnostic après basculement][diagnostic-events-viewer-detail-post-failover]

## <a name="clean-up-the-local-cluster-optional"></a>Nettoyage du cluster local (facultatif)

N’oubliez pas que ce cluster local est réel. L’arrêt du débogueur supprime votre instance d’application et annule l’inscription du type d’application. Le cluster continue toutefois de s’exécuter en arrière-plan. Lorsque vous êtes prêt à arrêter le cluster local, vous avez le choix entre deux options.

### <a name="keep-application-and-trace-data"></a>Conserver les données d’application et de trace

Fermez le cluster en effectuant un clic droit sur l’application de **Gestionnaire du cluster local** dans la barre système, puis en sélectionnant **Arrêter le cluster local**.

### <a name="delete-the-cluster-and-all-data"></a>Supprimer le cluster et toutes les données

Supprimez le cluster en effectuant un clic droit sur l’application de **Gestionnaire du cluster local** dans la barre système. Choisissez ensuite **Supprimer le cluster Local**. 

Si vous choisissez cette option, Visual Studio redéploiera le cluster la prochaine fois que vous exécuterez l’application. Optez pour cette option si vous n’envisagez pas d’utiliser le cluster local pendant un certain temps ou si vous avez besoin de libérer des ressources.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les services [Reliable Services](service-fabric-reliable-services-introduction.md).
<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
[switch-cluster-mode]: ./media/service-fabric-create-your-first-application-in-visual-studio/switch-cluster-mode.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[service-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/event-source-attribute-value.png
[setting-event-source-name]: ./media/service-fabric-create-your-first-application-in-visual-studio/setting-event-source-name.png
