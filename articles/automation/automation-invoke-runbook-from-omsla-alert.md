---
title: "Appel d’un runbook Azure Automation à partir d’une alerte Log Analytics | Microsoft Docs"
description: "Cet article présente une vue d’ensemble de l’appel d’un runbook Automation à partir d’une alerte Microsoft OMS Log Analytics."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
ms.openlocfilehash: 10b445f8fcaa80182119e47f37ffb11240a46869
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Appel d’un runbook Azure Automation à partir d’une alerte OMS Log Analytics

Lorsqu’une alerte est configurée dans Log Analytics pour créer un enregistrement d’alerte si les résultats correspondent aux critères, par exemple un pic d’utilisation processeur prolongé ou l’échec d’un processus d’application essentiel au fonctionnement d’une application métier et l’écriture d’un événement correspondant dans le journal des événements Windows, cette alerte peut exécuter automatiquement un runbook Automation dans une tentative de résolution automatique du problème.  

Deux méthodes permettent d’appeler un runbook lorsque vous configurez une alerte :

1. Utilisation d’un webhook.
   * Il s’agit de la seule option disponible si votre espace de travail OMS n’est pas lié à un compte Automation.
   * Si vous possédez déjà un compte Automation lié à un espace de travail OMS, cette option est toujours disponible.  

2. Sélection directe d’un runbook.
   * Cette option est disponible uniquement si l’espace de travail OMS est lié à un compte Automation.

## <a name="calling-a-runbook-using-a-webhook"></a>Appel d’un runbook à l’aide d’un webhook

Un webhook vous permet de démarrer un runbook particulier dans Azure Automation via une simple requête HTTP. Avant de configurer l’[alerte Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) pour appeler le runbook à l’aide d’un webhook en tant qu’action d’alerte, vous devez d’abord créer un webhook pour le runbook qui est appelé à l’aide de cette méthode. Suivez les étapes de l’article relatif à la [création d’un webhook](automation-webhooks.md#creating-a-webhook) et n’oubliez pas d’enregistrer l’URL du webhook afin de pouvoir la référencer lors de la configuration de la règle d’alerte.   

## <a name="calling-a-runbook-directly"></a>Appel direct d’un runbook

Si vous avez installé et configuré l’offre Automation & Control dans votre espace de travail OMS, lors de la configuration de l’option des actions de runbook pour l’alerte, vous pouvez afficher tous les runbooks de la liste déroulante **Sélectionner un runbook**, puis sélectionner celui que vous souhaitez exécuter en réponse à l’alerte. Le runbook sélectionné peut être exécuté dans un espace de travail dans le cloud Azure ou sur un Runbook Worker hybride. Une fois l’alerte créée à l’aide de la méthode du runbook, un webhook est créé pour celui-ci. Vous pouvez voir le webhook si vous accédez au compte Automation et accédez au volet du webhook du runbook sélectionné. Si vous supprimez l’alerte, le webhook n’est pas supprimé, mais l’utilisateur peut procéder manuellement à sa suppression. Ce n’est pas un problème si le webhook n’est pas supprimé. Il s’agit simplement d’un élément orphelin qui doit finalement être supprimé afin de conserver un compte Automation organisé.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Caractéristiques d’un runbook (pour les deux méthodes)

Les deux méthodes d’appel de runbook à partir de l’alerte Log Analytics présentent des caractéristiques que vous devez comprendre avant de configurer vos règles d’alerte.

* Vous devez disposer d’un paramètre d’entrée de runbook appelé **WebhookData** qui est de type **objet**. Il peut être obligatoire ou facultatif. L’alerte transmet les résultats de recherche au runbook à l’aide de ce paramètre d’entrée.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  Vous devez disposer du code permettant de convertir le paramètre WebhookData en objet PowerShell.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    *$SearchResult* est un tableau des objets, qui contiennent chacun les champs et les valeurs d’un résultat de recherche

## <a name="example-walkthrough"></a>Exemple de procédure pas à pas

Nous allons illustrer ce processus à l’aide de l’exemple de runbook graphique suivant, qui démarre un service Windows.<br><br> ![Démarrer le runbook graphique d’un service Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Le runbook contient un paramètre d’entrée de type **objet** appelé **WebhookData** et inclut les données du webhook transmises à partir de l’alerte contenant \*.SearchResult\*.<br><br> ![Paramètres d’entrée de runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Pour cet exemple, dans Log Analytics, nous avons créé deux champs personnalisés, \*SvcDisplayName\_CF\* et \*SvcState\_CF\*, pour extraire le nom d’affichage et l’état du service (en cours d’exécution ou arrêté) à partir de l’événement écrit dans le journal des événements système. Nous créons maintenant une règle d’alerte avec la requête de recherche suivante : `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` afin de pouvoir détecter lorsque le service Spouleur d’impression est arrêté sur le système Windows. Il peut s’agir de n’importe quel service, mais pour cet exemple, nous référençons l’un des services inclus dans le système d’exploitation Windows. L’action d’alerte est configurée pour exécuter notre runbook utilisé dans cet exemple et s’exécuter sur le Runbook Worker hybride, lequel est activé sur le système cible.   

L’activité de code du runbook **Obtenir le nom du service à partir de LA** convertit la chaîne au format JSON en un type d’objet et la filtre sur l’élément \*SvcDisplayName\_CF\* pour extraire le nom d’affichage du service Windows et le transmettre à l’activité suivante, qui vérifie que le service est arrêté avant de tenter de le redémarrer. *SvcDisplayName_CF* est un [champ personnalisé](../log-analytics/log-analytics-custom-fields.md) créé dans Log Analytics pour illustrer cet exemple.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Lorsque le service s’arrête, la règle d’alerte dans Log Analytics détecte une correspondance, déclenche le runbook et envoie le contexte de l’alerte au runbook. Le runbook vérifie si le service est arrêté, et le cas échéant, tente de le redémarrer, de vérifier qu’il a démarré correctement, puis sort les résultats.     

Si vous ne possédez pas de compte Automation lié à votre espace de travail OMS, vous pouvez également configurer la règle d’alerte avec une action de webhook afin de déclencher le runbook et de le configurer pour convertir la chaîne au format JSON et la filtrer sur \*.SearchResult\* en suivant les instructions mentionnées plus haut.    

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les alertes de Log Analytics et sur leur création, consultez [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md).

* Pour comprendre comment déclencher des runbooks à l’aide d’un webhook, consultez [Webhooks Azure Automation](automation-webhooks.md).
