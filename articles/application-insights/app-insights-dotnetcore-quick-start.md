---
title: "Démarrage rapide avec Azure Application Insights | Microsoft Docs"
description: "Fournit des instructions permettant de configurer rapidement une application web ASP.NET Core pour l’analyse avec Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: ccd55633f71be172edc330459bf8610f2146ad8d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Démarrer l’analyse de votre application web ASP.NET Core

Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation de votre application web. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale. 

Ce guide de démarrage rapide vous accompagne tout au long de l’ajout du Kit de développement logiciel (SDK) Application Insights dans une application web ASP.Net Core existante. 

## <a name="prerequisites"></a>configuration requise

Pour effectuer ce démarrage rapide :

- [Installez Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
  - Développement web et ASP.NET
  - Développement Azure
- [Installez le kit de développement (SDK) .NET Core 2.0](https://www.microsoft.com/net/core)
- Vous avez besoin d’un abonnement Azure et d’une application web .NET Core existante.

Si vous n’avez pas d’application web ASP.NET Core, vous pouvez en créer une en suivant le [guide de création d’une application web ASP.NET Core](https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Activer Application Insights

Application Insights permet de recueillir les données de télémétrie à partir de n’importe quelle application connectée à Internet, qu’elle soit exécutée localement ou dans le cloud. Suivez les étapes ci-dessous pour lancer l’affichage de ces données.

1. Sélectionnez **Créer une ressource** > **Surveillance + gestion** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/app-insights-dotnetcore-quick-start/0001-dc.png)

    Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        |  Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Type d’application** | Application web ASP.NET | Type de l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | Est des États-Unis | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="configure-app-insights-sdk"></a>Configurer le Kit de développement logiciel (SDK) Application Insights

1. Ouvrez votre **projet** d’application web ASP.NET Core dans Visual Studio > cliquez avec le bouton droit de la souris sur l’AppName dans l’**Explorateur de solutions** > sélectionnez **Ajouter** > **Application Insights Telemetry**.

    ![Ajouter Application Insights Telemetry](./media/app-insights-dotnetcore-quick-start/0001.png)

2. Cliquez sur le bouton **Démarrer la version d’évaluation gratuite** > sélectionnez la **ressource existante** que vous avez créée dans le portail Azure > cliquez sur **Inscrire**.

3. Sélectionnez **Déboguer** > **Exécuter sans débogage** (Ctrl + F5) pour lancer votre application.

> [!NOTE]
> Les données prennent 3 à 5 minutes avant d’apparaître sur le portail. S’il s’agit d’une application de test avec un trafic faible, gardez à l’esprit que la plupart des métriques sont capturées uniquement lorsqu’il existe des demandes ou des opérations actives.

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure en sélectionnant **Projet** > **Application Insights** > **Ouvrir le portail Application Insights** pour afficher les détails sur votre application en cours d’exécution.

   ![Menu Vue d'ensemble Application Insights](./media/app-insights-dotnetcore-quick-start/004-Black.png)

2. Cliquez sur **Mise en correspondance d’applications** pour obtenir une présentation visuelle des relations de dépendance entre les composants de votre application. Chaque composant affiche des indicateurs de performance clés comme la charge, les performances, les échecs et les alertes.

   ![Mise en correspondance d'applications](./media/app-insights-dotnetcore-quick-start/0002-dc.png)

3. Cliquez sur l’icône **Analyse d’application** ![icône Mise en correspondance d’applications](./media/app-insights-dotnetcore-quick-start/006.png).  Vous ouvrez ainsi **Application Insights - Analyses**, qui fournit un langage de requête enrichi permettant d’analyser toutes les données collectées par Application Insights. Dans ce cas, une requête est générée et affiche le nombre de demandes sous forme de graphique. Vous pouvez écrire vos propres requêtes pour analyser d’autres données.

   ![Graphique analytique des demandes d’utilisateur au cours d’une période donnée](./media/app-insights-dotnetcore-quick-start/0007-dc.png)

4. Revenez à la page **Vue d’ensemble** et examinez la **chronologie de la vue d’ensemble de l’intégrité**.  Ce tableau de bord fournit des statistiques sur l’intégrité de votre application, y compris le nombre de demandes entrantes, la durée de ces demandes et les éventuelles erreurs qui se produisent. 

   ![Graphiques chronologiques de la vue d’ensemble de l’intégrité](./media/app-insights-dotnetcore-quick-start/0008-dc.png)

   Pour activer le graphique **Temps de chargement de la page consultée** à remplir avec les données de **télémétrie côté client**, ajoutez ce script à toutes les pages dont vous souhaitez effectuer le suivi :

   ```HTML
   <!-- 
   To collect end-user usage analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Cliquez sur **Navigateur** sous l’en-tête **Examiner**. Vous y trouverez des mesures relatives aux performances des pages de votre application. Cliquez sur **Ajouter un nouveau graphique** pour créer des vues personnalisées supplémentaires, ou sélectionnez **Modifier** afin de modifier les types de graphiques existants, leur hauteur, leur palette de couleurs, leurs regroupements et leurs métriques.

   ![Graphique des métriques de serveur](./media/app-insights-dotnetcore-quick-start/009-Black.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez d’utiliser ces autres guides de démarrage rapide ou les didacticiels, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rechercher et diagnostiquer des exceptions runtime](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
