---
title: Gestion des erreurs dans les runbooks graphiques Azure Automation | Microsoft Docs
description: "Cet article décrit comment implémenter une logique de gestion des erreurs dans les runbooks graphiques Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/26/2016
ms.author: magoedte
ms.openlocfilehash: 86d62ba7fb12b09a2c19b4689af38bb8c121880b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Gestion des erreurs dans les runbooks graphiques Azure Automation

Un principe de conception clé des runbooks à prendre en compte consiste à identifier les différents problèmes qu’un runbook peut rencontrer. Ces problèmes peuvent être liés à la réussite, aux états d’erreur attendus et aux conditions d’erreur inattendue.

Les runbooks doivent inclure une gestion des erreurs. Avec les runbooks graphiques, si vous souhaitez valider la sortie d’une activité ou gérer une erreur, nous vous recommandons d’utiliser une activité de code Windows PowerShell, de définir la logique conditionnelle sur le lien de sortie de l’activité ou d’appliquer une autre méthode.          

Souvent, s’il existe une erreur sans fin d’exécution dans une activité de runbook, toute activité qui suit est traitée indépendamment de l’erreur. L’erreur est susceptible de générer une exception, mais l’activité suivante peut toujours s’exécuter. Tel est la façon dont PowerShell gère les erreurs.    

Les erreurs PowerShell qui peuvent survenir lors de l’exécution sont avec ou sans fin d’exécution. Les différences entre les erreurs sans fin d’exécution et celles avec fin d’exécution sont les suivantes :

* **Erreur avec fin d’exécution** : erreur grave pendant l’exécution qui arrête entièrement la commande (ou l’exécution du script). Il peut s’agir de cmdlets inexistantes, d’erreurs de syntaxe qui empêchent une cmdlet de s’exécuter ou d’autres erreurs irrécupérables.

* **Erreur sans fin d’exécution** : erreur sans gravité qui permet de continuer l’exécution malgré le dysfonctionnement. Il peut s’agit d’erreurs opérationnelles comme un fichier introuvable et de problèmes d’autorisation.

Les runbooks graphiques Azure Automation ont été améliorés avec la possibilité d’inclure une gestion des erreurs. Vous pouvez maintenant convertir les exceptions en erreurs sans fin d’exécution et créer des liens d’erreur entre les activités. Cela permet à un auteur de runbook d’intercepter les erreurs et de gérer les conditions réalisées ou inattendues.  

## <a name="when-to-use-error-handling"></a>Quand utiliser la gestion des erreurs

Lorsqu’une activité critique génère une erreur ou une exception, il est important d’empêcher le traitement de l’activité suivante dans votre runbook et de gérer l’erreur comme il se doit. Cela est tout particulièrement vital lorsque vos runbooks prennent en charge un processus d’entreprise ou de service.

Pour chaque activité susceptible de générer une erreur, l’auteur du runbook peut ajouter un lien d’erreur pointant vers une autre activité. L’activité de destination peut être de tout type : activité de code, appel d’une cmdelt, appel d’un autre runbook, etc.

En outre, l’activité de destination peut également avoir des liens sortants. Ces liens peuvent être des liens réguliers ou des liens d’erreur. Cela signifie que l’auteur du runbook peut implémenter une logique complexe de gestion des erreurs sans avoir recours à une activité de code. La pratique recommandée consiste à créer un runbook de gestion des erreurs dédié avec des fonctionnalités communes, mais cela n’a rien d’obligatoire. L’introduction d’une logique de gestion des erreurs dans un code d’activité PowerShell n’est pas la seule option proposée.  

Par exemple, envisagez un runbook qui tente de démarrer une machine virtuelle et d’y installer une application. Si l’ordinateur virtuel ne démarre pas correctement, il effectue deux actions :

1. Il envoie une notification concernant le problème.
2. Il démarre un autre runbook qui configure automatiquement une nouvelle machine virtuelle.

Une solution consiste à avoir un lien d’erreur qui pointe vers une activité qui gère l’étape une. Par exemple, vous pouvez connecter la cmdlet **Write-Warning** sur une activité pour l’étape deux, comme la cmdlet **Start-AzureRmAutomationRunbook**.

Vous pouvez également généraliser ce comportement dans de nombreux runbooks et intégrer ces deux activités dans un runbook de gestion des erreurs séparé, conformément aux instructions proposées précédemment. Avant d’appeler ce runbook de gestion des erreurs, vous pouvez construire un message personnalisé à partir des données du runbook d’origine, puis le transmettre en tant que paramètre au runbook de gestion des erreurs.

## <a name="how-to-use-error-handling"></a>Quand utiliser la gestion des erreurs

Chaque activité possède un paramètre de configuration permettant de transformer les exceptions en erreurs sans fin d’exécution. Par défaut, ce paramètre est désactivé. Nous vous recommandons de l’activer sur l’activité sur laquelle vous souhaitez gérer les erreurs.  

En activant cette configuration, vous êtes sûr que les erreurs avec et sans fin d’exécution dans l’activité sont traitées comme des erreurs sans fin d’exécution et qu’elles peuvent ensuite être gérées avec un lien d’erreur.  

Lorsque ce paramètre est configuré, vous créez une activité qui gère l’erreur. Si une activité génère une erreur, les liens d’erreur sortants sont suivis contrairement aux liens normaux, même si l’activité a également produit une sortie normale.<br><br> ![Exemple de lien d’erreur d’un runbook Automation](media/automation-runbook-graphical-error-handling/error-link-example.png)

Dans l’exemple suivant, un runbook récupère une variable contenant le nom d’ordinateur d’une machine virtuelle. Il tente ensuite de démarrer la machine virtuelle avec l’activité suivante.<br><br> ![Exemple de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

L’activité **Get-AutomationVariable** et **AzureRmVm-Start** sont configurés pour transformer des exceptions en erreurs. Si l’obtention de la variable ou le démarrage de la machine virtuelle pose problème, des erreurs sont générées.<br><br> ![Paramètres d’activité de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

Les liens d’erreur transitent depuis ces activités vers une seule activité de **gestion des erreurs** (code d’activité). Cette activité est configurée avec une simple expression PowerShell qui utilise le mot clé *Throw* pour arrêter le traitement, ainsi qu’avec *$Error.Exception.Message* pour obtenir le message qui décrit l’exception actuelle.<br><br> ![Exemple de code de gestion des erreurs d’un runbook Automation](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les liens et les types de lien dans les runbooks graphiques, voir [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md#links-and-workflow).

* Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
