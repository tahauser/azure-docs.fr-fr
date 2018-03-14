---
title: "Démarrage rapide avec Azure Application Insights | Microsoft Docs"
description: "Fournit des instructions permettant de configurer rapidement une application web Node.js pour l’analyse avec Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 30b75f577b5e68614131e6476586921a752768dc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="start-monitoring-your-nodejs-web-application"></a>Démarrer l’analyse de votre application web Node.js

Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation de votre application web. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale. À partir de la version 0.20 du Kit de développement logiciel (SDK), vous pouvez analyser des packages tiers courants, y compris MongoDB, MySQL et Redis.

Ce guide de démarrage rapide vous accompagne tout au long de l’ajout du Kit de développement logiciel (SDK) Application Insights version 0.22 pour Node.js dans une application web Node.js existante.

## <a name="prerequisites"></a>configuration requise

Pour effectuer ce démarrage rapide :

- Vous avez besoin d’un abonnement Azure et d’une application web Node.js existante.

Si vous n’avez pas d’application web Node.js, vous pouvez en créer une en suivant le [guide de démarrage rapide de création d’une application web Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) suivant.
 
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Activer Application Insights

Application Insights permet de recueillir les données de télémétrie à partir de n’importe quelle application connectée à Internet, qu’elle soit exécutée localement ou dans le cloud. Suivez les étapes ci-dessous pour lancer l’affichage de ces données.

1. Sélectionnez **Créer une ressource** > **Surveillance + gestion** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/app-insights-nodejs-quick-start/001-u.png)

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Type d’application** | Application Node.js | Type de l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | Est des États-Unis | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="configure-app-insights-sdk"></a>Configurer le Kit de développement logiciel (SDK) Application Insights

1. Sélectionnez **Vue d’ensemble** > **Éléments principaux** > copiez la **clé d’instrumentation** de votre application.

   ![Formulaire de nouvelle ressource Application Insights](./media/app-insights-nodejs-quick-start/003-Black.png)

2. Ajoutez le Kit de développement logiciel (SDK) Application Insights pour Node.js dans votre application. À partir du dossier racine de votre application, exécutez ce qui suit :

   ```bash
   npm install applicationinsights --save
   ```

3. Modifiez le premier fichier .js de votre application et ajoutez les deux lignes ci-dessous dans la partie supérieure de votre script. Si vous utilisez l’[application de démarrage rapide Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), vous modifiez le fichier index.js. Remplacez &lt;instrumentation_key&gt; par la clé d’instrumentation de votre application. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Redémarrez votre application.

> [!NOTE]
> Les données prennent 3 à 5 minutes avant d’apparaître sur le portail. S’il s’agit d’une application de test avec un trafic faible, gardez à l’esprit que la plupart des métriques sont capturées uniquement lorsqu’il existe des demandes ou des opérations actives.

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure où vous avez récupéré votre clé d’instrumentation afin d’afficher les détails sur votre application en cours d’exécution.

   ![Menu Vue d'ensemble Application Insights](./media/app-insights-nodejs-quick-start/004-Black.png)

2. Cliquez sur **Mise en correspondance d’applications** pour obtenir une présentation visuelle des relations de dépendance entre les composants de votre application. Chaque composant affiche des indicateurs de performance clés comme la charge, les performances, les échecs et les alertes.

   ![Mise en correspondance d'applications](./media/app-insights-nodejs-quick-start/005-Black.png)

3. Cliquez sur l’icône **Analyse d’application** ![icône Mise en correspondance d’applications](./media/app-insights-nodejs-quick-start/006.png).  Vous ouvrez ainsi **Application Insights - Analyses**, qui fournit un langage de requête enrichi permettant d’analyser toutes les données collectées par Application Insights. Dans ce cas, une requête est générée et affiche le nombre de demandes sous forme de graphique. Vous pouvez écrire vos propres requêtes pour analyser d’autres données.

   ![Graphique analytique des demandes d’utilisateur au cours d’une période donnée](./media/app-insights-nodejs-quick-start/007-Black.png)

4. Revenez à la page **Vue d’ensemble** et examinez la **chronologie de la vue d’ensemble de l’intégrité**.  Ce tableau de bord fournit des statistiques sur l’intégrité de votre application, y compris le nombre de demandes entrantes, la durée de ces demandes et les éventuelles erreurs qui se produisent. 

   ![Graphiques chronologiques de la vue d’ensemble de l’intégrité](./media/app-insights-nodejs-quick-start/008-Black.png)

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

5. Cliquez sur **Navigateur** sous l’en-tête **Examiner**. Vous y trouverez des métriques relatives aux performances des pages de votre application. Cliquez sur **Ajouter un nouveau graphique** pour créer des vues personnalisées supplémentaires, ou sélectionnez **Modifier** afin de modifier les types de graphiques existants, leur hauteur, leur palette de couleurs, leurs regroupements et leurs métriques.

   ![Graphique des métriques de serveur](./media/app-insights-nodejs-quick-start/009-Black.png)

Pour plus d’informations sur l’analyse de Node.js, consultez la [documentation supplémentaire sur Application Insights pour Node.js](app-insights-nodejs.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez d’utiliser ces autres guides de démarrage rapide ou les didacticiels, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Rechercher et diagnostiquer des problèmes de performances](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
