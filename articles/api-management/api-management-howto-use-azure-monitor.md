---
title: "Surveiller les API publiées dans la Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à surveiller votre API dans la Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 445723242a76dcef4a6b137439728235d5d6e32a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-published-apis"></a>Surveiller les API publiées

Azure Monitor est un nouveau service Azure qui fournit une source unique d’analyse pour toutes vos ressources Azure. Avec Azure Monitor, vous pouvez visualiser, interroger, acheminer, archiver et agir sur les mesures et journaux provenant de vos ressources Azure, comme la gestion des API. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de diagnostic
> * Afficher les métriques de votre API 
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

La vidéo suivante montre comment surveiller la gestion des API à l’aide d’Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>configuration requise

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>Afficher les journaux d’activité

Les journaux d’activité fournissent des informations sur les opérations qui ont été effectuées sur les services de Gestion des API. Avec les journaux d’activité, vous pouvez déterminer « qui, quand et quoi » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur vos services de Gestion des API. 

> [!NOTE]
> Les journaux d’activité n’incluent pas les opérations de lecture (GET) ou les opérations effectuées dans le portail Azure ou utilisant les API de gestion d’origine.

Vous pouvez accéder aux journaux d’activité dans votre service de Gestion des API, ou accéder aux journaux de toutes vos ressources Azure dans Azure Monitor. 

Pour afficher les journaux d’activité :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Journal d’activité**.

## <a name="view-diagnostic-logs"></a>Afficher les journaux de diagnostic

Les journaux de diagnostic offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit ainsi qu’à des fins de dépannage. Les journaux de diagnostic diffèrent des journaux d’activité. Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur vos ressources Azure. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

Pour accéder aux journaux de diagnostic :

1. Sélectionnez votre instance de service APIM.
2. Cliquez sur **Journal de diagnostic**.

## <a name="view-metrics-of-your-apis"></a>Afficher les métriques de vos API

Le service Gestion des API émet des métriques chaque minute, pour une visibilité en quasi temps réel de l’intégrité de vos API. Voici un récapitulatif d’une partie des métriques disponibles :

* Capacité (préversion) : vous permet de prendre des décisions concernant la mise à niveau/rétrogradation de vos services APIM. La métrique est émise chaque minute et reflète la capacité de la passerelle au moment de la création de rapports. Le calcul de cette métrique, dont la valeur est comprise entre 0 et 100, repose sur les recours à la passerelle, tels que l’utilisation du processeur et de la mémoire.
* Nombre total de demandes de passerelle : le nombre de requêtes d’API dans la période. 
* Demandes de la passerelle ayant abouti : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP de succès, dont 304, 307 et toute valeur inférieure à 301 (par exemple, 200). 
* Demandes de la passerelle ayant échoué : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP d’échec, dont 400 et toute valeur supérieure à 500.
* Demandes de la passerelle non autorisées : le nombre de requêtes d’API ayant reçu des codes de réponse HTTP comprenant 401, 403 et 429. 
* Autres demandes de la passerelle : le nombre de requêtes d’API ayant reçu des codes de réponse qui n’appartiennent à aucune des catégories précédentes (par exemple, 418).

Pour accéder aux métriques :

1. Sélectionnez **Métriques** dans le menu vers le bas de la page.
2. Dans la liste déroulante, sélectionner les métriques qui vous intéressent (vous pouvez ajouter plusieurs métriques). 
    
    Par exemple, sélectionnez **Nombre total de demandes de la passerelle** et **Demandes de la passerelle ayant échoué** dans la liste des métriques.
3. Le graphique affiche le nombre total d’appels d’API. Il montre également le nombre d’appels d’API qui ont échoué. 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurer une règle d’alerte pour une demande non autorisée

Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer un e-mail de notification
* Appeler un webhook
* Appeler une application logique Azure

Pour configurer des alertes :

1. Sélectionnez **Règles d’alerte** dans la barre de menus vers le bas de la page.
2. Sélectionnez **Ajouter une alerte Métrique**.
3. Entrez un **Nom** pour cette alerte.
4. Sélectionnez **Demandes de la passerelle non autorisées** comme métrique à surveiller.
5. Sélectionnez **Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs**.
6. Appuyez sur **OK**.
7. Essayez d’appeler notre API de conférence sans clé API. En tant que propriétaire de ce service Gestion des API, vous recevez une alerte par e-mail. 

    > [!TIP]
    > La règle d’alerte peut également appeler un webhook ou une application logique Azure quand elle est déclenchée.

    ![Configurer une alerte](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Afficher les journaux d’activité
> * Afficher les journaux de diagnostic
> * Afficher les métriques de votre API 
> * Configurer une règle d’alerte quand votre API obtient des appels non autorisés

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Suivre des appels](api-management-howto-api-inspector.md)
