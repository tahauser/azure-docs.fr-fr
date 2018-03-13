---
title: "Répondre aux événements avec les alertes Azure Log Analytics | Microsoft Docs"
description: "Ce didacticiel vous aide à comprendre les alertes dans Log Analytics qui vous permettent d’identifier les informations importantes dans votre référentiel OMS, vous avertissent à un stade précoce de l’existence de problèmes ou appellent des actions pour tenter de les corriger."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fcfaa849f67ffcfa69672d116837e96d318c2124
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Répondre aux événements avec les alertes Log Analytics
Les alertes identifient des informations importantes dans votre référentiel Log Analytics. Elles sont créées par des règles d’alerte qui exécutent automatiquement des recherches dans les journaux à intervalles réguliers. Si les résultats de la recherche dans les journaux correspondent à des critères particuliers, un enregistrement d’alerte est créé et peut être configuré pour exécuter une réponse automatisée.  Ce didacticiel est la suite du didacticiel [Créer et partager des tableaux de bord de données Log Analytics](log-analytics-tutorial-dashboards.md).   

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d'une règle d'alerte
> * Configurer une règle d’alerte qui envoie une notification par e-mail

Pour effectuer l’exemple de ce didacticiel, vous devez disposer d’une machine virtuelle [connectée à l’espace de travail Log Analytics](log-analytics-quick-collect-azurevm.md).  

## <a name="log-in-to-azure-portal"></a>Connexion au portail Azure
Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Créez des alertes

Les alertes sont créées par des règles dédiées qui exécutent automatiquement des recherches dans les journaux à intervalles réguliers.  Vous pouvez créer des alertes sur la base de métriques de performances spécifiques ou quand certains événements sont créés, quand un événement fait défaut ou quand un certain nombre d’événements sont créés dans une fenêtre de temps donnée.  Par exemple, les alertes peuvent servir à vous avertir que l’utilisation moyenne du processeur dépasse un certain seuil ou qu’un événement est généré pour cause de non exécution d’un service Windows spécifique ou du démon Linux.   Si les résultats de la recherche répondent à des critères particuliers, un enregistrement d’alerte est généré. La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive. 

Dans l’exemple suivant, vous allez créer une règle d’alerte de mesure de métrique qui va générer une alerte pour chaque objet ordinateur de la requête dont la valeur dépasse un seuil de 90 %.

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Lancez le portail OMS en sélectionnant Portail OMS puis, dans la page **Vue d’ensemble**, sélectionnez **Recherche dans les journaux**.  
3. Sélectionnez **Favoris** dans la partie supérieure du portail puis, dans le volet **Recherches enregistrées** à droite, sélectionnez la requête *Machines virtuelles Azure - Utilisation des processeurs*.  
4. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**.  
5. Configurez la règle d’alerte avec les informations suivantes :  
   a. Attribuez un **Nom** à votre alerte, en l’occurrence *VM processor utilization exceeded >90*  
   b. Dans **Fenêtre de temps**, spécifiez l’intervalle de temps de la requête, soit *30*.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution.  
   c. **Fréquence des alertes** spécifie la fréquence d’exécution de la requête.  Pour cet exemple, spécifiez *5* minutes pour permettre une exécution dans la fenêtre de temps que nous avons spécifiée.  
   d. Sélectionnez **Mesure de métriques**, entrez la valeur *90* pour **Valeur d’agrégat**, puis la valeur *3* pour **Déclencher l’alerte selon**   
   e. Sous **Actions**, désactivez E-mail de notification.
6. Cliquez sur **Enregistrer** pour terminer la règle d’alerte. Son exécution démarre immédiatement.<br><br> ![Exemple de règle d’alerte](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Les enregistrements d’alerte créés par les règles d’alerte dans Log Analytics sont de type **Alerte** et ont pour système source **OMS**.<br><br> ![Exemple d’événements d’alerte générés](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>Actions d’alerte
Les alertes vous permettent d’effectuer des actions avancées, par exemple créer une notification par e-mail, lancer un [Runbook Automation](../automation/automation-runbook-types.md) ou utiliser un Webhook pour créer un enregistrement d’incident dans votre système de gestion des incidents ITSM. Des réponses analogues peuvent être apportées par la [solution IT Service Management Connector ](log-analytics-itsmc-overview.md) quand les critères d’alerte sont réunis.   

Les actions par e-mail envoient un message électronique contenant les détails de l’alerte à un ou plusieurs destinataires. Vous pouvez spécifier l’objet du message, mais son contenu répond à un format standard construit par Log Analytics.  Mettons à jour la règle d’alerte créée précédemment et configurons-la pour qu’elle vous envoie un e-mail de notification au lieu d’effectuer une surveillance active des enregistrements d’alerte avec une recherche dans les journaux.     

1. Dans le portail OMS, dans le menu supérieur, sélectionnez **Paramètres**, puis sélectionnez **Alertes**.
2. Dans la liste des règles d’alerte, cliquez sur l’icône de crayon en regard de l’alerte créée précédemment.
3. Sous la section **Actions**, activez les notifications par e-mail.
4. Indiquez l’**Objet** de l’e-mail, en l’occurrence *Processor utilization exceeded threshold >90*.
5. Ajoutez l’adresse du ou des destinataires de l’e-mail dans le champ **Destinataires**.  Si vous spécifiez plusieurs adresses, séparez-les par un point-virgule (;).
6. Cliquez sur **Enregistrer** pour terminer la règle d’alerte. Son exécution démarre immédiatement.<br><br> ![Règle d’alerte avec notification par e-mail](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez découvert en quoi les règles d’alerte permettaient d’identifier et de répondre à un stade précoce à un problème déterminé quand elles exécutent des recherches dans les journaux à intervalles réguliers et qu’elles correspondent à des critères particuliers.  

Suivez ce lien pour examiner des exemples de scripts Log Analytics prédéfinis.  

> [!div class="nextstepaction"]
> [Exemples de scripts Log Analytics](powershell-samples.md)