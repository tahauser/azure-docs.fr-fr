---
title: "Appeler un runbook Azure Automation à partir d’une alerte Log Analytics | Microsoft Docs"
description: "Cet article présente une vue d’ensemble de l’appel d’un runbook Automation à partir d’une alerte Log Analytics dans Operations Management Suite."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: a10be867965eef9746a0f4cc9b14c4fc429f6e35
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Appeler un runbook Azure Automation à partir d’une alerte Log Analytics

Vous pouvez configurer une alerte dans Azure Log Analytics pour créer un enregistrement d’alerte lorsque les résultats correspondent à vos critères. Cette alerte peut ensuite exécuter automatiquement un runbook Azure Automation dans une tentative de correction automatique du problème. 

Par exemple, une alerte peut indiquer un pic prolongé dans l’utilisation du processeur. Elle peut également indiquer l’échec d’un processus d’application essentiel à la fonctionnalité d’une application métier. Un runbook peut ensuite écrire un événement correspondant dans le journal des événements Windows.  

Deux méthodes permettent d’appeler un runbook lorsque vous configurez une alerte :

* Utilisation d’un Webhook.
   * Il s’agit de la seule option disponible si votre espace de travail Operations Management Suite n’est pas lié à un compte Automation.
   * Si vous possédez déjà un compte Automation lié à un espace de travail Operations Management Suite, cette option est toujours disponible.  

* Sélection directe d’un runbook.
   * Cette option est disponible uniquement si l’espace de travail Operations Management Suite est lié à un compte Automation.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Appel d’un runbook à l’aide d’un Webhook

Un Webhook vous permet de démarrer un runbook particulier dans Azure Automation via une simple requête HTTP. Avant de configurer l’[alerte Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) pour appeler le runbook à l’aide d’un Webhook en tant qu’action d’alerte, vous devez [créer un Webhook](automation-webhooks.md#creating-a-webhook) pour le runbook appelé via cette méthode. N’oubliez pas d’enregistrer l’URL du Webhook afin de pouvoir la référencer lors de la configuration de la règle d’alerte.   

## <a name="calling-a-runbook-directly"></a>Appel direct d’un runbook

Vous pouvez installer et configurer l’offre Automation & Control dans votre espace de travail Operations Management Suite. Alors que vous configurez l’option des actions de runbook pour l’alerte, vous pouvez afficher tous les runbooks de la liste déroulante **Sélectionner un runbook**, puis sélectionner celui que vous souhaitez exécuter en réponse à l’alerte. Le runbook sélectionné peut être exécuté dans un espace de travail Azure ou sur un Runbook Worker hybride. 

Une fois l’alerte créée à l’aide de la méthode du runbook, un Webhook est créé pour celui-ci. Vous pouvez voir le Webhook si vous accédez au compte Automation et ouvrez le volet du Webhook du runbook sélectionné. 

La suppression de l’alerte n’entraîne pas celle du Webhook. Ce n’est pas un problème. Le Webhook devient simplement un élément orphelin que vous devrez finir par supprimer manuellement pour maintenir un compte Automation organisé.  

## <a name="characteristics-of-a-runbook"></a>Caractéristiques d’un runbook

Les deux méthodes d’appel de runbook à partir de l’alerte Log Analytics présentent des caractéristiques que vous devez comprendre avant de configurer vos règles d’alerte. 

Les données d’alerte sont au format JSON dans une propriété unique appelée **SearchResult**. Ce format est dédié aux actions de runbook et webhook avec une charge utile standard. Pour les actions de Webhook avec des charges utiles personnalisées (notamment **IncludeSearchResults:True** dans **RequestBody**), la propriété est **SearchResults**.

Vous devez disposer d’un paramètre d’entrée de runbook appelé **WebhookData** qui est un type **objet**. Il peut être obligatoire ou facultatif. L’alerte transmet les résultats de recherche au runbook à l’aide de ce paramètre d’entrée.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
Vous devez également disposer du code permettant de convertir le paramètre **WebhookData** en objet PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** est un tableau d’objets. Chaque objet contient les champs et les valeurs d’un résultat de recherche.


## <a name="example-walkthrough"></a>Exemple de procédure pas à pas

L’exemple suivant d’un runbook graphique illustre le fonctionnement de ce processus. Il démarre un service Windows.

![Runbook graphique permettant de démarrer un service Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Le runbook possède un paramètre d’entrée de type **objet** appelé **WebhookData**. Il inclut les données de Webhook transmises à partir de l’alerte contenant **SearchResult**.

![Paramètres d’entrée de Runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

Pour cet exemple, nous avons créé deux champs personnalisés dans Log Analytics : **SvcDisplayName_CF** et **SvcState_CF**. Ces champs extraient le nom d’affichage du service et l’état du service (en cours d’exécution ou arrêté) à partir de l’événement écrit dans le journal des événements système. Nous avons créé ensuite une règle d’alerte avec la requête de recherche suivante afin de pouvoir détecter lorsque le service Spouleur d’impression est arrêté sur le système Windows :

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Ce peut être n’importe quel service intéressant. Pour cet exemple, nous référençons l’un des services préexistants inclus dans le système d’exploitation Windows. L’action d’alerte est configurée pour exécuter le runbook utilisé dans cet exemple et s’exécuter sur le Runbook Worker hybride, lequel est activé sur le système cible.   

L’activité de code de runbook **Get Service Name from LA** convertit la chaîne de format JSON en type d’objet et effectue un filtrage sur l’élément **SvcDisplayName_CF**. Elle extrait le nom d’affichage du service Windows et transmet cette valeur à l’activité suivante, qui vérifie que le service est arrêté avant de tenter de le redémarrer. **SvcDisplayName_CF** est un [champ personnalisé](../log-analytics/log-analytics-custom-fields.md) que nous avons créé dans Log Analytics pour illustrer cet exemple.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Lorsque le service s’arrête, la règle d’alerte dans Log Analytics détecte une correspondance, déclenche le runbook et lui envoie le contexte de l’alerte. Le runbook essaie de vérifier que le service est arrêté. Le cas échéant, le runbook tente de redémarrer le service, de vérifier qu’il a démarré correctement et d’afficher les résultats.     

Si vous êtes dépourvu de compte Automation lié à votre espace de travail Operations Management Suite, vous pouvez également configurer la règle d’alerte avec une action de Webhook. L’action de Webhook déclenche le runbook. Elle configure également le runbook pour convertir la chaîne de format JSON et appliquer un filtre sur **SearchResult** en suivant les instructions mentionnées plus haut.    

>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), la charge utile du Webhook a changé. Vous trouverez les détails du format dans la page [API REST Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les alertes de Log Analytics et sur leur création, consultez [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md).

* Pour découvrir comment déclencher des runbooks à l’aide d’un Webhook, consultez [Webhooks Azure Automation](automation-webhooks.md).
