---
title: "Exécuter des Runbooks sur Runbook Worker hybride d’Azure Automation | Microsoft Docs"
description: "Cet article fournit des informations sur l’exécution de Runbooks sur des machines dans votre fournisseur cloud ou centre de données local avec le rôle Runbook Worker hybride."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: magoedte
ms.openlocfilehash: ca63f9c7a11bc072bd73c3d61c63a8d603020e68
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Exécution de Runbooks sur un Runbook Worker hybride 
Il n'existe aucune différence entre la structure des Runbooks qui s'exécutent dans Azure Automation et celle des Runbooks qui s'exécutent sur un Runbook Worker hybride. Toutefois, il est très possible que les Runbooks que vous utilisez avec chaque structure diffèrent considérablement, car les Runbooks ciblant un Runbook Worker hybride gèrent généralement les ressources sur l’ordinateur local ou par rapport aux ressources de l’environnement local dans lequel il est déployé, tandis que les Runbooks dans Azure Automation gèrent généralement les ressources dans le cloud Azure.

Vous pouvez modifier un Runbook pour le Runbook Worker hybride dans Azure Automation, mais vous pouvez rencontrer des difficultés si vous essayez de tester le Runbook dans l'éditeur.  Les modules PowerShell qui accèdent aux ressources locales ne peuvent pas être installés dans votre environnement Azure Automation, car sinon, le test échoue.  Si vous installez les modules requis, le Runbook s'exécute, mais il ne peut pas accéder aux ressources locales pour un test complet.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Démarrage d’un Runbook sur Runbook Worker hybride
[Démarrage d'un Runbook dans Azure Automation](automation-starting-a-runbook.md) décrit différentes méthodes de démarrage d'un Runbook.  La fonctionnalité Runbook Worker hybride ajoute une option **RunOn** qui vous permet de spécifier le nom d'un groupe de Runbooks Workers hybrides.  Si un groupe est spécifié, le Runbook est récupéré et exécuté par les Workers de ce groupe.  Si cette option n'est pas spécifiée, il est exécuté dans Azure Automation comme habituellement.

Quand vous démarrez un Runbook sur le Portail Azure, une option **Exécuter sur** vous permet de sélectionner **Azure** ou **Worker hybride**.  Si vous sélectionnez **Worker hybride**, vous pouvez sélectionner le groupe souhaité dans une liste déroulante.

Utilisez le paramètre **Exécuter sur**.  Vous pouvez utiliser la commande suivante pour démarrer un Runbook nommé Test-Runbook sur un groupe de Runbooks Workers hybrides nommé MyHybridGroup avec Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Le paramètre **RunOn** a été ajouté à l’applet de commande **Start-AzureAutomationRunbook** dans la version 0.9.1 de Microsoft Azure PowerShell.  Vous devez [télécharger la version la plus récente](https://azure.microsoft.com/downloads/) si une version antérieure est installée.  Il vous suffit d’installer cette version sur une station de travail sur laquelle vous démarrez le Runbook à partir de Windows PowerShell.  Il n'est pas nécessaire de l'installer sur l'ordinateur de travail, sauf si vous avez l'intention de démarrer les Runbooks dessus.  Actuellement, vous ne pouvez pas démarrer un Runbook sur un Runbook Worker hybride à partir d'un autre Runbook, car cela nécessiterait que la version la plus récente d'Azure PowerShell soit installée dans votre compte Automation.  Bientôt, la version la plus récente sera automatiquement mise à jour dans Azure Automation et lancée automatiquement dans les Workers.
>
>

## <a name="runbook-permissions"></a>Autorisations de Runbook
Les Runbooks exécutés sur un Runbook Worker hybride ne peuvent pas utiliser la méthode généralement utilisée pour l’authentification des Runbooks auprès des ressources Azure, car ils accèdent à des ressources extérieures à Azure.  Le Runbook peut fournir sa propre authentification aux ressources locales, mais vous pouvez également spécifier un compte RunAs pour fournir un contexte utilisateur pour l’ensemble des Runbooks.

### <a name="runbook-authentication"></a>Authentification des Runbooks
Par défaut, les Runbooks s’exécutent dans le contexte du compte Système local de l’ordinateur local. De ce fait, ils doivent fournir leur propre authentification auprès des ressources auxquelles ils accèdent.  

Vous pouvez utiliser les ressources [Informations d’identification](http://msdn.microsoft.com/library/dn940015.aspx) et [Certificat](http://msdn.microsoft.com/library/dn940013.aspx) dans votre Runbook avec des applets de commande qui vous permettent de spécifier des informations d’identification pour que vous puissiez vous authentifier auprès de différentes ressources.  L'exemple suivant présente une partie d'un Runbook qui redémarre un ordinateur.  Il récupère des informations d'identification à partir d'une ressource d'informations d'identification et le nom de l'ordinateur à partir d'une ressource de variable, puis il utilise ces valeurs avec l'applet de commande Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Vous pouvez également exploiter [InlineScript](automation-powershell-workflow.md#inlinescript), qui vous permet d’exécuter des blocs de code sur une autre machine avec les informations d’identification spécifiées par le [paramètre commun PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Compte RunAs
Plutôt que de laisser les Runbooks fournir leur propre authentification aux ressources locales, vous pouvez spécifier un compte **RunAs** pour un groupe de Workers hybrides.  Vous spécifiez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales, après quoi tous les Runbooks utilisent ces informations d’identification au moment de leur exécution sur un Runbook Worker hybride du groupe.  

Le nom d’utilisateur doit utiliser l’un des formats suivants :

* domaine\nom d’utilisateur
* username@domain
* nom d’utilisateur (pour les comptes locaux sur l’ordinateur local)

Utilisez la procédure suivante pour spécifier un compte RunAs pour un groupe de Workers hybrides :

1. Créez une [ressource d’information d’identification](automation-credentials.md) ayant accès aux ressources locales.
2. Dans le portail Azure, ouvrez le compte Automation.
3. Sélectionnez la vignette **Groupes de Workers hybrides** , puis sélectionnez le groupe.
4. Sélectionnez **Tous les paramètres**, puis **Paramètres du groupe Worker hybride**.
5. Sous **Exécuter en tant que**, remplacez **Par défaut** par **Personnalisé**.
6. Sélectionnez les informations d’identification et cliquez sur **Enregistrer**.

### <a name="automation-run-as-account"></a>Compte d’identification Automation
Dans le cadre de votre processus de génération automatisé pour le déploiement de ressources dans Azure, vous pouvez avoir besoin d’accéder aux systèmes locaux pour prendre en charge une tâche ou un ensemble d’étapes de votre séquence de déploiement.  Pour prendre en charge l’authentification auprès d’Azure avec le compte d’identification, vous devez installer le certificat du compte d’identification.  

Le Runbook PowerShell suivant, *RunAsCertificateToHybridWorker-Export*, exporte le certificat d’identification de votre compte Azure Automation, le télécharge et l’importe dans le magasin de certificats de l’ordinateur local sur un Worker hybride connecté au même compte.  Une fois cette étape terminée, il vérifie que le Worker peut s’authentifier auprès d’Azure avec le compte d’identification.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Generate the password used for this certificate
    Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
    $Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)
    
    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select-Object AutomationAccountName

Enregistrez le Runbook *Export-RunAsCertificateToHybridWorker* sur votre ordinateur avec une extension `.ps1`.  Importez-le dans votre compte Automation et modifiez le Runbook, en remplaçant la valeur de la variable `$Password` par votre mot de passe.  Publiez, puis exécutez le Runbook ciblant le groupe de Workers hybrides qui exécute et authentifie les Runbooks avec le compte d’identification.  Le flux de travaux signale la tentative d’importer le certificat dans le magasin de l’ordinateur local et complète par plusieurs lignes, en fonction du nombre de comptes Automation définis dans votre abonnement et du résultat de l’authentification.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Résolution de problèmes de runbooks sur un Runbook Worker hybride
Les journaux sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Le Worker hybride enregistre également les erreurs et les événements dans le journal des événements Windows sous **Journaux des applications et des services\Microsoft-SMA\Opérationnel**.  Les événements liés aux Runbooks exécutés sur le Worker sont écrits dans **Journaux des applications et des services\Microsoft-Automation\Opérationnel**.  Le journal **Microsoft-SMA** inclut beaucoup plus d’événements liés au travail du Runbook envoyé vers le Worker et au traitement du Runbook.  Alors que le journal des événements **Microsoft-Automation** ne compte pas beaucoup d’événements avec des détails facilitant le dépannage de l’exécution du Runbook, vous trouverez au moins les résultats du travail du Runbook.  

[Sortie et messages de Runbooks](automation-runbook-output-and-messages.md) : ils sont envoyés à Azure Automation à partir de Workers hybrides tout comme les tâches de Runbook exécutées dans le cloud.  Vous pouvez également activer les flux Détaillé et Progression comme vous le feriez pour d’autres runbooks.  

Si vos Runbooks ne se terminent pas correctement et que le résumé du travail affiche l’état **Interrompu**, consultez l’article de dépannage [Runbook Worker hybride : un travail de Runbook se termine avec l’état suspendu](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les différentes méthodes qui peuvent être utilisées pour démarrer un Runbook, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md).  
* Pour comprendre les différentes procédures pour utiliser des Runbooks PowerShell et Workflow PowerShell dans Azure Automation à l’aide de l’éditeur de texte, consultez [Modifier des Runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md)
