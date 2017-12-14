---
title: "Diagnostiquer des exceptions runtime à l’aide d’Azure Application Insights | Microsoft Docs"
description: "Didacticiel décrivant comment rechercher et diagnostiquer des exceptions runtime dans votre application à l’aide d’Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: f6844dd6747854a60ff8eb8be0d913b73ca2bdb2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Rechercher et diagnostiquer des exceptions runtime avec Azure Application Insights

Azure Application Insights collecte la télémétrie de votre application pour identifier et diagnostiquer des exceptions runtime.  Ce didacticiel vous guide dans ce processus avec votre application.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Modifier votre projet pour activer le suivi des exceptions
> * Identifier des exceptions pour les différents composants de votre application
> * Afficher les détails d’une exception
> * Télécharger une capture instantanée de l’exception vers Visual Studio à des fins de débogage
> * Analyser les détails des demandes ayant échoué à l’aide du langage de requête
> * Créer un élément de travail pour corriger le code défectueux


## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel :

- Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
    - Développement web et ASP.NET
    - Développement Azure
- Téléchargez et installez le [débogueur d'instantané de Visual Studio](http://aka.ms/snapshotdebugger).
- Activez le [débogueur de capture instantanée de Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger).
- Déployez une application .NET pour Azure et [activez le Kit SDK Application Insights](app-insights-asp-net.md). 
- Le didacticiel effectuant le suivi de l’identification d’une exception dans votre application, modifiez le code dans votre environnement de développement ou de test afin de générer une exception. 

## <a name="log-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analyser les échecs
Application Insights collecte les échecs dans votre application et vous permet d’afficher leur fréquence dans différentes opérations afin de vous aider à concentrer vos efforts sur les échecs dont l’impact est le plus élevé.  Vous pouvez explorer les détails de ces échecs pour en identifier la cause racine.   

1. Sélectionnez **Application Insights**, puis votre abonnement.  
1. Pour ouvrir le panneau **Échecs**, sélectionnez **Échecs** dans le menu **Examiner**, ou cliquez sur le graphique **Demandes ayant échoué**.

    ![Demandes ayant échoué](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. Le panneau **Demandes ayant échoué** indique le nombre de demandes ayant échoué et le nombre d’utilisateurs affectés pour chaque opération de l’application.  En triant ces informations par utilisateur, vous pouvez identifier les échecs qui ont le plus d’impact sur les utilisateurs.  Dans cet exemple, les opérations **GET Employees/Create** et **GET Customers/Details** sont probablement de bonnes candidates pour examiner la cause en raison du nombre important d’échecs générés et d’utilisateurs affectés.  La sélection d’une opération a pour effet d’afficher des informations supplémentaires sur celle-ci dans le volet droit.

    ![Panneau des demandes ayant échoué](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Réduisez la fenêtre de temps pour zoomer sur la période pendant laquelle le taux d’échec présente un pic.

    ![Fenêtre des demandes ayant échoué](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Cliquez sur **Afficher les détails** pour voir les détails de l’opération.  Ceux-ci incluent un diagramme de Gantt présentant deux dépendances en échec dont l’exécution a pris près d’une demie seconde.  Pour plus d’informations sur l’analyse des problèmes de performances, voir le didacticiel [Rechercher et diagnostiquer les problèmes de performances à l’aide d’Azure Application Insights](app-insights-tutorial-performance.md).

    ![Détails des demandes ayant échoué](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. Le détail des opérations montre également une exception FormatException qui semble être à l’origine de l’échec.  Cliquez sur l’exception ou sur **Top 3 exception types** (3 principaux types d’exceptions) pour afficher les détails de l’exception.  Vous pouvez voir que celle-ci est due à un code postal non valide.

    ![Détails de l’exception](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Identifier le code défaillant
Le débogueur d'instantané collecte des captures instantanées des exceptions les plus fréquentes dans votre application pour vous aider à diagnostiquer leur cause racine en production.  Vous pouvez afficher les captures instantanées de débogage dans le portail pour consulter la pile des appels et inspecter les variables à chaque frame de pile des appels. Vous pouvez ensuite déboguer le code source en téléchargeant l’instantané et en l’ouvrant dans Visual Studio 2017.

1. Dans les propriétés de l’exception, cliquez sur **Ouvrir l'instantané de débogage**.
2. Le panneau **Déboguer l'instantané** s’ouvre, affichant la pile des appels pour la demande.  Cliquez sur une méthode pour afficher les valeurs de toutes les variables locales au moment de la demande.  En partant de la méthode du haut dans cet exemple, nous pouvons voir les variables locales qui n’ont aucune valeur.

    ![Déboguer l'instantané](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. Le premier appel dont les valeurs sont valides est **ValidZipCode**, et nous voyons qu’un code postal a été fourni, qui contient des lettres ne pouvant pas être traduites en chiffres.  Il semble qu’il s’agisse de l’erreur de code à corriger.

    ![Déboguer l'instantané](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Pour télécharger cet instantané dans Visual Studio où il est possible de localiser le code réel à corriger, cliquez sur **Télécharger l’instantané**.
6. L’instantané est chargé dans Visual Studio.
7. Vous pouvez maintenant exécuter une session de débogage dans Visual Studio, qui permet d’identifier rapidement la ligne de code ayant provoqué l’exception.

    ![Exception dans le code](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Utiliser des données Analytics
Toutes les données collectées par Application Insights sont stockées dans Azure Log Analytics qui offre un langage de requête riche permettant d’analyser les données de plusieurs façons.  Nous pouvons utiliser ces données pour analyser les demandes à l’origine de l’exception que nous examinons. 

8. Cliquez sur les informations CodeLens au-dessus du code pour afficher la télémétrie fournie par Application Insights.

    ![Code](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Cliquez sur **Analyser l’impact** pour ouvrir Application Insights Analytics.  Application Insights Analytics comprend plusieurs requêtes qui fournissent des détails sur les demandes ayant échoué, tels que les utilisateurs, les navigateurs et les régions concernés.<br><br>![Analyse](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Ajouter un élément de travail
Si vous connectez Application Insights à un système de suivi tel que Visual Studio Team Services ou GitHub, vous pouvez créer un élément de travail directement à partir d’Application Insights.

1. Revenez au panneau **Propriétés d'exception** dans Application Insights.
2. Cliquez sur **Nouvel élément de travail**.
3. Le panneau **Nouvel élément de travail** s’ouvre, affichant les détails relatifs à l’exception.  Vous pouvez ajouter des informations avant de l’enregistrer.

    ![Nouvel élément de travail](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris à identifier les exceptions runtime, passez au didacticiel suivant pour apprendre à identifier et à diagnostiquer les problèmes de performances.

> [!div class="nextstepaction"]
> [Identifier les problèmes de performances](app-insights-tutorial-performance.md)