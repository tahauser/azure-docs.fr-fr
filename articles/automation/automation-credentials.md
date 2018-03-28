---
title: Ressources d’informations d’identification dans Azure Automation
description: Les ressources d’informations d’identification dans Azure Automation contiennent des informations d’identification de sécurité qui peuvent être utilisées pour s’authentifier auprès des ressources auxquelles le Runbook ou la configuration DSC a accès. Cet article décrit comment créer des ressources d’informations d’identification et les utiliser dans un Runbook ou une configuration DSC.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: bb6aec41813930cbf74ab989cc4ce6b526b9c6b6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="credential-assets-in-azure-automation"></a>Ressources d’informations d’identification dans Azure Automation
Une ressource d’informations d’identification Automation conserve un objet [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) qui contient les informations d’identification de sécurité comme un nom d’utilisateur et un mot de passe. Les Runbooks et les configurations DSC peuvent utiliser les applets de commande qui acceptent un objet PSCredential pour l’authentification, ou ils peuvent extraire le nom d’utilisateur et le mot de passe de l’objet PSCredential pour les fournir à l’application ou au service nécessitant l’authentification. Les propriétés d’informations d’identification sont stockées de manière sécurisée dans Azure Automation et sont accessibles dans le Runbook ou la configuration DSC avec l’activité [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) .

>[!NOTE]
>Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans Key Vault. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource.

## <a name="azure-classic-powershell-cmdlets"></a>Applets de commande PowerShell Azure Classic
Les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell.  Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview) , utilisable dans les Runbooks Automation et les configurations DSC.

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Récupère des informations sur une ressource d’informations d’identification. Vous pouvez uniquement récupérer les informations d’identification elles-mêmes à partir de l’activité **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Crée de nouvelles informations d’identification Automation. |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Supprime des informations d’identification Automation. |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Définit les propriétés d’informations d’identification Automation existantes. |

## <a name="azurerm-powershell-cmdlets"></a>Applets de commande AzureRM PowerShell
Pour AzureRM, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell.  Elles sont fournies avec le [module AzureRM.Automation](/powershell/azure/overview), utilisable dans les runbooks Automation et les configurations DSC.

| Applets de commande | Description |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Récupère des informations sur une ressource d’informations d’identification.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Crée de nouvelles informations d’identification Automation. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Supprime des informations d’identification Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Définit les propriétés d’informations d’identification Automation existantes. |

## <a name="activities"></a>Activités
Les activités dans le tableau suivant sont utilisées pour accéder aux informations d’identification dans un Runbook ou dans des configurations DSC.

| Activités | Description |
|:--- |:--- |
| Get-AutomationPSCredential |Obtient les informations d’identification à utiliser dans un Runbook ou dans une configuration DSC. Renvoie un objet [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) . |

> [!NOTE]
> Évitez d’utiliser des variables dans le paramètre –Name de Get-AutomationPSCredential, car cela complique la découverte des dépendances entre les Runbooks ou configurations DSC et les ressources d’informations d’identification au moment de la conception.

## <a name="python2-functions"></a>Fonctions Python2
La fonction dans le tableau suivant est utilisée pour accéder aux informations d’identification dans un runbook Python2.

| Fonction | Description |
|:---|:---|
| automationassets.get_automation_credential | Récupère des informations sur une ressource d’informations d’identification. |

> [!NOTE]
> Vous devez importer le module « automationassets » en haut de votre runbook Python afin d’accéder aux fonctions des ressources.

## <a name="creating-a-new-credential-asset"></a>Création d’une ressource d’informations d’identification

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Pour créer une ressource d’informations d’identification avec le portail Azure
1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d’ouvrir le panneau **Ressources**.
2. Cliquez sur la partie **Informations d’identification** afin d’ouvrir le panneau **Informations d’identification**.
3. Cliquez sur **Ajouter des informations d’identification** en haut du panneau.
4. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer les nouvelles informations d’identification.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Pour créer une ressource d’informations d’identification avec Windows PowerShell
Les exemples de commandes suivants montrent comment créer des informations d’identification Automation. Un objet PSCredential est d’abord créé avec le nom et le mot de passe, puis il est utilisé pour créer la ressource d’informations d’identification. Vous pouvez également utiliser l’applet de commande **Get-Credential** pour être invité à saisir un nom et un mot de passe.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## <a name="using-a-powershell-credential"></a>Utilisation des informations d’identification PowerShell
Vous récupérez une ressource d’informations d’identification dans un Runbook ou une configuration DSC avec l’activité **Get-AutomationPSCredential**. Cette propriété renvoie un [objet PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que vous pouvez utiliser avec une activité ou une applet de commande nécessitant un paramètre PSCredential. Vous pouvez également récupérer les propriétés de l’objet d’informations d’identification pour les utiliser individuellement. L’objet possède une propriété pour le nom d’utilisateur et le mot de passe sécurisé. Vous pouvez également utiliser la méthode **GetNetworkCredential** pour retourner un objet [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) fournissant une version non sécurisée du mot de passe.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel
Les exemples de commandes suivants montrent comment utiliser les informations d’identification PowerShell dans un Runbook. Dans cet exemple, les informations d’identification sont récupérées et le nom d’utilisateur et le mot de passe affectés à des variables.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Exemple de Runbook graphique
Vous ajoutez une activité **Get-AutomationPSCredential** à un Runbook graphique en cliquant avec le bouton droit sur les informations d’identification dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.

![Ajout d’informations d’identification à la zone de dessin](media/automation-credentials/credential-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’informations d’identification dans un Runbook graphique.  Dans ce cas, il est utilisé pour l’authentification d’un Runbook auprès des ressources Azure, comme décrit dans [Authentifier des Runbooks avec un compte d’utilisateur Azure AD](automation-create-aduser-account.md).  La première activité récupère les informations d’identification ayant accès à l’abonnement Azure.  L’activité **Add-AzureAccount** utilise ensuite ces informations d’identification pour fournir l’authentification pour toutes les activités qui la suivent.  C’est un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) , étant donné que **Get-AutomationPSCredential** attend un objet unique.  

![Ajout d’informations d’identification à la zone de dessin](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Utilisation des informations d’identification PowerShell dans une configuration DSC
Bien que les configurations DSC dans Azure Automation puissent se rapporter à des ressources d’informations d’identification utilisant **Get-AutomationPSCredential**, les ressources d’information d’identification peuvent également transmises via des paramètres, si vous le souhaitez. Pour plus d’informations, consultez [Compilation de configurations dans Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Utilisation des informations d’identification dans Python2
L’exemple suivant montre un exemple d’accès aux informations d’identification dans les runbooks Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les liens lors de la création graphique, consultez [Liens lors de la création graphique](automation-graphical-authoring-intro.md#links-and-workflow)
* Pour comprendre les différentes méthodes d’authentification avec Automation, consultez [Sécurité dans Azure Automation](automation-security-overview.md)
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md) 
* Pour commencer avec les runbooks Python2, consultez [My first Python2 runbook](automation-first-runbook-textual-python2.md) (Mon premier Runbook Python2). 

