---
title: Runbook Worker hybride Linux Azure Automation | Microsoft Docs
description: "Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet dexécuter des Runbooks sur les machines Linux de votre centre de données local ou de votre environnement cloud."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 9926ceef9777032d52c7655e4c2d03f63a4a6af7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Déploiement d’un Runbook Worker hybride Linux

Dans Azure Automation, les Runbooks ne peuvent pas accéder aux ressources d’autres clouds ou dans votre environnement local car ils s'exécutent dans le cloud Azure. La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.

Cette fonctionnalité est illustrée dans l’image suivante :<br>  

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Pour obtenir un aperçu technique du rôle d’un Runbook Worker hybride, consultez [Présentation de l’architecture Automation](automation-offering-get-started.md#automation-architecture-overview). Consultez les informations suivantes concernant les [exigences matérielles et logicielles requise](automation-offering-get-started.md#hybrid-runbook-worker) et les [informations pour préparer votre réseau](automation-offering-get-started.md#network-planning) avant de commencer le déploiement d’un Runbook Worker hybride. Une fois que vous avez déployé un runbook worker avec succès, consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.     

## <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides
Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent. Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute. Les membres du groupe déterminent quel Worker traite la demande. Vous ne pouvez pas spécifier un Worker particulier.

## <a name="installing-linux-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride Linux
L’installation et la configuration d’un Runbook Worker hybride sur un ordinateur Linux est un processus simple qui permet d’installer et de configurer le rôle manuellement. Cela nécessite l’activation de la solution **Automation Hybrid Worker** dans votre espace de travail OMS, puis l’exécution d’un ensemble de commandes pour inscrire l’ordinateur en tant que Worker et l’ajouter à un groupe nouveau ou existant. 

Avant de continuer, vous devez noter l’espace de travail Log Analytics auquel votre compte Automation est lié, ainsi que la clé primaire de votre compte Automation. Vous trouverez ces deux informations sur le portail en sélectionnant votre compte Automation et l’**espace de travail** correspondant à l’ID d’espace de travail et **Clés** pour la clé primaire.  

1.  Activez la solution Automation Hybrid Worker dans OMS. Pour ce faire, vous pouvez :

   1. À partir de la Galerie de solutions du [portail OMS](https://mms.microsoft.com), activer la solution **Automation Hybrid Worker**
   2. Exécutez l’applet de commande suivante :

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  Exécutez la commande suivante en remplaçant les valeurs des paramètres *-w*, *-k*, *-g* et *-e*. Pour le paramètre *-g*, remplacez la valeur par le nom du groupe Runbook Worker hybride auquel le nouveau Runbook Worker hybride Linux doit se joindre. Si le nom n’existe pas déjà dans votre compte Automation, un nouveau groupe Runbook Worker hybride est constitué avec ce nom.
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. Une fois la commande terminée, le panneau Groupes de Workers hybrides du portail Azure affiche le nouveau groupe et le nombre de membres, ou, si le groupe existe déjà, incrémente le nombre de membres. Vous pouvez sélectionner le groupe dans la liste du panneau **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**. Le panneau **Workers hybrides** affiche chaque membre du groupe listé.  


## <a name="turning-off-signature-validation"></a>Désactivation de la validation de signature 
Par défaut, les Runbooks Workers hybrides Linux nécessitent la validation de signature. Si vous exécutez un Runbook non signé sur un rôle de travail, une erreur contenant « Échec de la validation de la signature » s’affiche. Pour désactiver la validation de signature, exécutez la commande suivante, en remplaçant le deuxième paramètre par votre ID d’espace de travail OMS :

    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <OMSworkspaceId>
    ```

## <a name="supported-runbook-types"></a>Types de Runbook pris en charge

Les Runbooks Worker hybrides ne prennent pas en charge la totalité de l’ensemble de types de Runbook disponibles dans Azure Automation.

Les types de Runbook suivants fonctionnent sur un Worker hybride Linux :

* Python 2
* PowerShell
 
Les types de Runbook suivants ne fonctionnent pas sur un Worker hybride Linux :

* Workflow PowerShell
* Graphique
* Graphique workflow PowerShell

## <a name="next-steps"></a>Étapes suivantes

* Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez [Remove Azure Automation Hybrid Runbook Workers](automation-remove-hrw.md) (Supprimer des Runbooks Worker hybrides Azure Automation).
