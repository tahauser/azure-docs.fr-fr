---
title: "Démarrage rapide avec Azure Application Insights | Microsoft Docs"
description: "Fournit des instructions permettant de configurer rapidement une application web Java pour l’analyse avec Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: daf7fb88b41ca21ea08e1368215578971032e47d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="start-monitoring-your-java-web-application"></a>Démarrer l’analyse de votre application web Java

Azure Application Insights vous permet d’analyser facilement la disponibilité, les performances et l’utilisation de votre application web. De plus, vous pouvez rapidement identifier et diagnostiquer les erreurs dans votre application sans attendre qu’un utilisateur ne les signale. Avec le Kit de développement logiciel (SDK) Java Application Insights, vous pouvez analyser les packages tiers courants, y compris MongoDB, MySQL et Redis.

Ce guide de démarrage rapide vous accompagne tout au long de l’ajout du Kit de développement logiciel (SDK) Application Insights dans un projet web dynamique Java existant.

## <a name="prerequisites"></a>configuration requise

Pour effectuer ce démarrage rapide :

- Installez Oracle JRE 1.6 ou version ultérieure, ou Zulu JRE 1.6 ou version ultérieure.
- Installez l’[Environnement de développement intégré Eclipse gratuit pour développeurs Java EE](http://www.eclipse.org/downloads/). Ce guide de démarrage rapide utilise Eclipse Oxygen (4.7).
- Vous avez besoin d’un abonnement Azure et d’un projet web dynamique Java existant.
 
Si vous n’avez pas de projet web dynamique Java, vous pouvez en créer un à l’aide du [guide de démarrage rapide pour la création d’une application web Java](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-java).

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Activer Application Insights

Application Insights permet de recueillir les données de télémétrie à partir de n’importe quelle application connectée à Internet, qu’elle soit exécutée localement ou dans le cloud. Suivez les étapes ci-dessous pour lancer l’affichage de ces données.

1. Sélectionnez **Créer une ressource** > **Surveillance + gestion** > **Application Insights**.

   ![Ajout d’une ressource Application Insights](./media/app-insights-java-quick-start/001-j.png)

   Une boîte de configuration s’affiche. Utilisez le tableau suivant pour remplir les champs d’entrée.

    | Paramètres        | Valeur           | Description  |
   | ------------- |:-------------|:-----|
   | **Name**      | Valeur globalement unique | Nom identifiant l’application que vous analysez |
   | **Type d’application** | Application web Java | Type de l’application que vous analysez |
   | **Groupe de ressources**     | myResourceGroup      | Nom du nouveau groupe de ressources pour héberger les données Application Insights |
   | **Lieu** | Est des États-Unis | Choisissez un emplacement près de chez vous ou proche de l’endroit où votre application est hébergée |

2. Cliquez sur **Créer**.

## <a name="install-app-insights-plugin"></a>Installer le plug-in Application Insights

1. Lancez **Eclipse** > cliquez sur **Aide** > sélectionnez **Installer un nouveau logiciel**.

   ![Formulaire de nouvelle ressource Application Insights](./media/app-insights-java-quick-start/000-j.png)

2. Copiez ```http://dl.microsoft.com/eclipse``` dans le champ « Travail avec » > cochez **Kit de ressources Azure pour Java** > sélectionnez le **plug-in Application Insights pour Java** > **désactivez** l’option permettant de contacter tous les sites de mise à jour au cours de l’installation pour rechercher les logiciels requis.

3. Une fois l’installation terminée, vous êtes invité à **redémarrer Eclipse**.

## <a name="configure-app-insights-plugin"></a>Configurer le plug-in Application Insights

1. Lancez **Eclipse** > ouvrez votre **projet** > cliquez avec le bouton droit de la souris sur le nom du projet dans l’**Explorateur de projets** > sélectionnez **Azure** > Cliquez sur **Se connecter**.

2. Sélectionnez la méthode d’authentification **interactive** > cliquez sur **Se connecter** > lorsque vous y êtes invité, entrez vos **informations d’identification Azure** > sélectionnez votre **abonnement Azure**.

3. Cliquez avec le bouton droit de la souris sur le nom de votre projet dans l’**Explorateur de projets** > sélectionnez **Azure** > cliquez sur **Configurer Application Insights**.

4. Cochez **Activer la télémétrie avec Application Insights** > sélectionnez la ressource Application Insights et la **clé d’instrumentation** associée que vous souhaitez lier à votre application Java.

   ![Menu de configuration Eclipse Azure](./media/app-insights-java-quick-start/0007-j.png)

> [!NOTE]
> Le Kit de développement logiciel (SDK) Application Insights pour Java permet de capturer et de visualiser les métriques en temps réel. Cependant, la première fois que vous activez la collecte des données de télémétrie, les données peuvent prendre quelques minutes avant d’apparaître dans le portail. S’il s’agit d’une application de test avec un trafic faible, gardez à l’esprit que la plupart des métriques sont capturées uniquement lorsqu’il existe des demandes ou des opérations actives.

## <a name="start-monitoring-in-the-azure-portal"></a>Démarrer l’analyse dans le portail Azure

1. Vous pouvez à présent rouvrir la page de **présentation** d’Application Insights dans le portail Azure où vous avez récupéré votre clé d’instrumentation afin d’afficher les détails sur votre application en cours d’exécution.

   ![Menu Vue d'ensemble Application Insights](./media/app-insights-java-quick-start/0008-j.png)

2. Cliquez sur **Mise en correspondance d’applications** pour obtenir une présentation visuelle des relations de dépendance entre les composants de votre application. Chaque composant affiche des indicateurs de performance clés comme la charge, les performances, les échecs et les alertes.

   ![Mise en correspondance d'applications](./media/app-insights-java-quick-start/005-j.png)

3. Cliquez sur l’icône **Analyse d’application** ![icône Mise en correspondance d’applications](./media/app-insights-java-quick-start/006.png). Vous ouvrez ainsi **Application Insights - Analyses**, qui fournit un langage de requête enrichi permettant d’analyser toutes les données collectées par Application Insights. Dans ce cas, une requête est générée et affiche le nombre de demandes sous forme de graphique. Vous pouvez écrire vos propres requêtes pour analyser d’autres données.

   ![Graphique analytique des demandes d’utilisateur au cours d’une période donnée](./media/app-insights-java-quick-start/0010-j.png)

4. Revenez à la page **Vue d’ensemble** et examinez la **chronologie de la vue d’ensemble de l’intégrité**.  Ce tableau de bord fournit des statistiques sur l’intégrité de votre application, y compris le nombre de demandes entrantes, la durée de ces demandes et les éventuelles erreurs qui se produisent.

   ![Graphiques chronologiques de la vue d’ensemble de l’intégrité](./media/app-insights-java-quick-start/0009-j.png)

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
        instrumentationKey:"<instrumentation key>"
    });

    window.appInsights=appInsights;
    appInsights.trackPageView();
   </script>
    ```

5. Cliquez sur **Flux temps réel**. Vous y trouverez des métriques en temps réel portant sur les performances de votre application web Java. L’option **Flux de métriques temps réel** inclut des données relatives au nombre de demandes entrantes, à la durée de ces demandes et aux erreurs qui se produisent. Vous pouvez également surveiller les métriques de performances critiques comme le processeur et la mémoire en temps réel.

   ![Graphiques des métriques de serveur](./media/app-insights-java-quick-start/livemetricsjava.png)

Pour en savoir plus sur l’analyse de Java, consultez la [documentation supplémentaire sur Application Insights pour Java](.\app-insights-java-get-started.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez d’utiliser ces autres guides de démarrage rapide ou les didacticiels, ne supprimez pas les ressources créées dans ce guide de démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées par ce démarrage rapide dans le portail Azure.

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myResourceGroup** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Rechercher et diagnostiquer des problèmes de performances](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)