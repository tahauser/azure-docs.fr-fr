---
title: "Créer un service fiable Azure Service Fabric avec C#"
description: "Créer, déployer et déboguer une application Reliable Service basée sur Azure Service Fabric avec Visual Studio."
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
ms.openlocfilehash: 2ecb8f8068043936d00f2c9752666490137414e3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-your-first-c-service-fabric-stateful-reliable-services-application"></a>Création de votre première application de services fiables avec état c# Service Fabric

Découvrez comment déployer votre première application Service Fabric pour .NET sur Windows en quelques minutes. Une fois l’opération terminée, vous disposez d’un cluster local fonctionnant avec une application de service fiable.

## <a name="prerequisites"></a>configuration requise

Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement](service-fabric-get-started.md). Cela inclut l’installation du SDK de Service Fabric et de Visual Studio 2017 ou 2015.

## <a name="create-the-application"></a>Création de l’application

Lancez Visual Studio en tant qu’**administrateur**.

Créer un projet avec `CTRL`+`SHIFT`+`N`

Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Cloud > Application Service Fabric**.

Appelez l’application **MonApplication**, puis cliquez sur **OK**.

   
![Boîte de dialogue Nouveau projet dans Visual Studio][1]

Vous pouvez créer n’importe quel type d’application Service Fabric à partir de la boîte de dialogue suivante. Pour ce démarrage rapide, choisissez **Service avec état**.

Appelez le service **MyStatefulService**, puis cliquez sur **OK**.

![Boîte de dialogue Nouveau service dans Visual Studio.][2]


Visual Studio crée le projet d’application et le projet de service avec état et les affiche dans l’Explorateur de solutions.

![Explorateur de solutions après la création de l’application de service avec état][3]

Le projet d’application (**MyApplication**) ne contient pas de code directement. Au lieu de cela, il fait référence à un ensemble de projets de service. En outre, il contient trois autres types de contenu :

* **Profils de publication**  
Profils pour le déploiement sur différents environnements.

* **Scripts**  
Script PowerShell de déploiement/mise à niveau de votre application.

* **Définition d’application**  
Inclut le fichier ApplicationManifest.xml sous *ApplicationPackageRoot* qui décrit la composition de votre application. Les fichiers de paramètres d’application associés se trouvent sous le dossier *ApplicationParameters*, qui peut être utilisé pour spécifier des paramètres spécifiques à l’environnement. Visual Studio sélectionne un fichier de paramètres d’application qui est spécifié dans le profil de publication associé au cours du déploiement dans un environnement spécifique.
    
Pour avoir une vue d’ensemble du contenu du projet de service, consultez l’article [Prise en main de Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="deploy-and-debug-the-application"></a>Déployer et déboguer l’application.

Maintenant que vous disposez d’une application, exécutez-la.

Appuyez sur `F5` dans Visual Studio pour déployer l’application en vue d’un débogage.

>[!NOTE]
>La première fois que vous exécutez et déployez l’application localement, Visual Studio crée un cluster local pour le débogage. Cette opération peut prendre un certain temps. L’état de la création du cluster est affiché dans la fenêtre Sortie de Visual Studio.

Une fois le cluster prêt, vous obtenez une notification de la part de l’application de gestionnaire de la barre d’état système de cluster local incluse avec le kit de développement logiciel.
   
![Notification de barre d’état système de cluster local][4]

Une fois que l’application a démarré, Visual Studio affiche automatiquement **l’Observateur d’événements de diagnostic**, où vous pouvez voir la sortie de suivi depuis vos services.
   
![Observateur d’événements de diagnostic][5]

>[!NOTE]
>Les événements doivent automatiquement démarrer le suivi dans l’Observateur d’événements de diagnostic, mais si vous devez le configurer manuellement, commencez par ouvrir le fichier `ServiceEventSource.cs`, situé dans le projet **MyStatefulService**. Copiez la valeur de l’attribut `EventSource` en haut de la classe `ServiceEventSource`. Dans l’exemple ci-dessous, l’événement source est appelé `"MyCompany-MyApplication-MyStatefulService"`, qui peut être différent dans votre cas.
>
>![Rechercher le nom de la source de l’événement de service][service-event-source-name]
>
>Ensuite, cliquez sur l’icône représentant un engrenage situé dans l’onglet de Observateur d’événements de diagnostic pour ouvrir la boîte de dialogue **Fournisseurs ETW**. Collez le nom de la source de l’événement que vous venez de copier dans la zone de saisie **Fournisseurs ETW**. Puis, cliquez sur le bouton **Appliquer**. Le suivi des événements démarre alors automatiquement.
>
>![Définir le nom la source des événements de diagnostic][setting-event-source-name]
>
>Vous devriez maintenant voir les événements s’afficher dans la fenêtre Événements de diagnostic.

Le modèle de service avec état utilisé indique une valeur du compteur incrémentée dans la méthode `RunAsync` de **MyStatefulService.cs**.

Pour plus d’informations, développez un des événements, et notamment le nœud sur lequel le code s’exécute. Dans ce cas, il s’agit du \_Node\_0, bien que cela puisse être différent sur votre ordinateur.
   
![Détails de l’Observateur d’événements de diagnostic][6]

Le cluster local contient cinq nœuds hébergés sur un seul ordinateur. Dans un environnement de production, chaque nœud est hébergé sur une machine virtuelle ou physique distincte. Pour simuler la perte d’une machine tout en testant le débogueur Visual Studio, examinons l’un des nœuds du cluster local.

Dans la fenêtre de **l’Explorateur de solutions**, ouvrez **MyStatefulService.cs**. 

Recherchez la méthode `RunAsync` et définissez un point d’arrêt sur la première ligne de la méthode.

![Point d’arrêt dans la méthode RunAsync de service avec état][7]

Lancez l’outil **Service Fabric Explorer** en faisant un clic droit sur l’application du **gestionnaire du cluster local** dans la zone de notification, puis choisissez **Gérer le cluster local**.

![Lancer l’Explorateur de Fabric Service à partir du Gestionnaire de cluster Local.][systray-launch-sfx]

[**Service Fabric Explorer**](service-fabric-visualizing-your-cluster.md) offre une représentation visuelle d’un cluster. Il inclut l’ensemble des applications qui y sont déployées, ainsi que le jeu de nœuds physiques qui le composent.

Dans le volet de gauche, développez **Cluster > Nœuds** et recherchez le nœud sur lequel votre code s’exécute.

Cliquez sur **Actions > Désactiver (Redémarrer)** pour simuler le redémarrage de l’ordinateur.

![Arrêter un nœud Service Fabric Explorer][sfx-stop-node]

Vous devez voir momentanément le point d’arrêt atteint dans Visual Studio, car le calcul vous faisiez sur un nœud bascule en toute transparence vers un autre.


Revenez ensuite à l’Observateur d’événements de diagnostic et prenez connaissance des messages. Le compteur a continué de s’incrémenter, même si les événements proviennent en fait d’un autre nœud.

![Observateur d’événements de diagnostic après basculement][diagnostic-events-viewer-detail-post-failover]

## <a name="cleaning-up-the-local-cluster-optional"></a>Nettoyage du cluster local (facultatif)

N’oubliez pas que ce cluster local est réel. L’arrêt du débogueur supprime votre instance d’application et annule l’inscription du type d’application. Le cluster continue toutefois de s’exécuter en arrière-plan. Lorsque vous êtes prêt à arrêter le cluster local, vous avez le choix entre deux options.

### <a name="keep-application-and-trace-data"></a>Conserver les données d’application et de trace

Fermez le cluster en effectuant un clic droit sur l’application de **gestionnaire du cluster local** dans la barre système, puis sélectionnez **Arrêter le cluster local**.

### <a name="delete-the-cluster-and-all-data"></a>Supprimer le cluster et toutes les données

Supprimez le cluster en effectuant un clic droit sur l’application de **gestionnaire du cluster local** dans la barre système, puis sélectionnez **Supprimer le cluster local**. 

Si vous choisissez cette option, Visual Studio redéploiera le cluster la prochaine fois que vous exécuterez l’application. Optez pour cette option si vous n’envisagez pas d’utiliser le cluster local pendant un certain temps ou si vous avez besoin de libérer des ressources.

## <a name="next-steps"></a>étapes suivantes
En savoir plus sur les [services fiables](service-fabric-reliable-services-introduction.md).
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
