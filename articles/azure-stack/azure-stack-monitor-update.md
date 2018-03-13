---
title: "Surveiller les mises à jour dans Azure Stack à l'aide du point de terminaison privilégié | Microsoft Docs"
description: "Découvrez comment utiliser le point de terminaison privilégié pour surveiller l’état des mises à jour pour les systèmes intégrés Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 96eebf340f13f2f5e9e922fee8032d04fce1d130
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Surveiller les mises à jour dans Azure Stack à l'aide du point de terminaison privilégié

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez utiliser le point de terminaison privilégié pour surveiller la progression de l'exécution d’une mise à jour Azure Stack et reprendre une mise à jour ayant échoué à partir de la dernière étape réussie si le portail de Azure Stack n’est plus disponible.  Utiliser le portail de Azure Stack est la méthode recommandée pour gérer les mises à jour dans Azure Stack.

Les nouvelles applets de commande PowerShell suivantes pour la gestion des mises à jour sont incluses dans la mise à jour 1710 des systèmes intégrés Azure Stack.

| Applet de commande  | Description  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Renvoie l’état de la mise à jour en cours, terminée ou qui a échoué. Fournit l’état de haut niveau de l’opération de mise à jour ainsi qu'un document XML qui décrit l’étape actuelle et l’état correspondant. |
| `Get-AzureStackUpdateVerboseLog` | Renvoie les journaux détaillés générés par la mise à jour. |
| `Resume-AzureStackUpdate` | Reprend une mise à jour ayant échoué au point où elle a échoué. Dans certains scénarios, vous devrez peut-être prendre des mesures d’atténuation des risques avant de reprendre la mise à jour.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Vérifier que les applets de commande sont disponibles
Comme les applets de commande sont nouvelles dans le package de mise à jour 1710 d’Azure Stack, le processus de mise à jour 1710 a besoin d'atteindre un certain point avant que la fonctionnalité de surveillance soit disponible. En règle générale, les applets de commande sont disponibles si l’état dans le portail d’administration indique que la mise à jour 1710 se trouve à l'étape **Redémarrer les hôtes de stockage**. Plus précisément, la mise à jour de l’applet de commande survient durant **l'étape : exécution de l’étape 2.6 - Mettre à jour la liste blanche PrivilegedEndpoint**.

Vous pouvez également déterminer si les applets de commande sont disponibles par programmation en interrogeant la liste des commandes à partir du point de terminaison privilégié. Pour cela, exécutez les commandes suivantes à partir de l’hôte de cycle de vie du matériel ou d’une station de travail avec accès privilégié. Assurez-vous également que le point de terminaison privilégié est un hôte approuvé. Pour plus d’informations, consultez l’étape 1 [Accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Créez une session PowerShell sur une des machines virtuelles ERCS de votre environnement Azure Stack (*Prefix*-ERCS01, *Prefix*-ERCS02 ou *Prefix*-ERCS03). Remplacez *Prefix* par la chaîne de préfixe de la machine virtuelle spécifique à votre environnement.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Lorsque vous êtes invité à saisir vos informations d’identification, utilisez le &lt;*domaine Azure Stack*&gt;\compte cloudadmin, ou un compte qui est membre du groupe CloudAdmins. Pour le compte CloudAdmin, entrez le mot de passe fourni pendant l’installation pour le compte d’administrateur de domaine AzureStackAdmin.

2. Obtenez la liste complète des commandes disponibles dans le point de terminaison privilégié. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Déterminez si le point de terminaison privilégié a été mis à jour.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Liste des commandes spécifiques au module Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Par exemple : 
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Utiliser les applets de commande de gestion des mises à jour

> [!NOTE]
> Exécutez les commandes suivantes à partir de l’hôte de cycle de vie du matériel ou d’une station de travail avec accès privilégié. Assurez-vous également que le point de terminaison privilégié est un hôte approuvé. Pour plus d’informations, consultez l’étape 1 [Accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Se connecter au point de terminaison privilégié et attribuer une variable de session

Exécutez les commandes suivantes pour créer une session PowerShell sur une des machines virtuelles ERCS de votre environnement Azure Stack (*Prefix*-ERCS01, *Prefix*-ERCS02 ou *Prefix*-ERCS03), et pour attribuer une variable de session.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Lorsque vous êtes invité à saisir vos informations d’identification, utilisez le &lt;*domaine Azure Stack*&gt;\compte cloudadmin, ou un compte qui est membre du groupe CloudAdmins. Pour le compte CloudAdmin, entrez le mot de passe fourni pendant l’installation pour le compte d’administrateur de domaine AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obtenir l’état de haut niveau de l’exécution de la mise à jour actuelle 

Pour obtenir un état de haut niveau de l’exécution de la mise à jour actuelle, exécutez les commandes suivantes : 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Les valeurs possibles incluent :

- Exécution
- Completed
- Échec 
- Canceled

Vous pouvez exécuter ces commandes à plusieurs reprises pour afficher l’état le plus récent. Vous n’êtes pas obligé de rétablir la connexion à chaque fois.

### <a name="get-the-full-update-run-status-with-details"></a>Obtenir l’état détaillé complet de l'exécution de la mise à jour 

Vous pouvez obtenir le résumé complet de l'exécution de la mise à jour sous forme d'une chaîne XML. Vous pouvez enregistrer la chaîne dans un fichier pour l'examiner, ou la convertir en un document XML et utiliser PowerShell pour l’analyser. La commande suivante analyse le code XML pour obtenir une liste hiérarchique des étapes en cours d’exécution.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

Dans l’exemple suivant, l’étape de niveau supérieur (mise à jour du cloud) dispose d’un plan enfant pour mettre à jour et redémarrer les hôtes de stockage. Cela indique que le plan de redémarrage des hôtes de stockage met à jour le service de Stockage Blob sur un des hôtes.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Obtenir le journal détaillé de la progression

Vous pouvez enregistrer le journal dans un fichier pour l'examiner. Cela peut vous aider à diagnostiquer un échec de mise à jour.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Afficher activement la journalisation détaillée

Pour afficher activement le journal détaillé pendant une mise à jour et accéder aux entrées les plus récentes, exécutez les commandes suivantes pour faire basculer la session en mode interactif et afficher le journal :

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
Le journal est mis à jour toutes les 60 secondes, et le nouveau contenu (si disponible) est enregistré dans la console. 

Lorsque de longs processus sont exécutés en arrière-plan, la sortie de la console peut ne pas s'enregistrer dans la console pendant un certain temps. Pour annuler la sortie interactive, appuyez sur Ctrl+C. 

### <a name="resume-a-failed-update-operation"></a>Reprendre une opération de mise à jour ayant échoué

Si la mise à jour échoue, vous pouvez la reprendre là où elle s’est arrêtée.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Résolution des problèmes

Le point de terminaison privilégié est disponible sur toutes les machines virtuelles ERCS dans l’environnement Azure Stack. Comme la connexion n’est pas établie vers un point de terminaison hautement disponible, vous risquez de rencontrer des interruptions occasionnelles, des avertissements ou des messages d’erreur. Ces messages peuvent indiquer que la session a été déconnectée ou qu’une erreur de communication avec le service ECE s'est produite. Il s’agit du comportement attendu. Vous pouvez réessayer l’opération dans quelques minutes ou créer une session de point de terminaison privilégié sur l’une des autres machines virtuelles ERCS. 

## <a name="next-steps"></a>étapes suivantes

- [Gestion des mises à jour dans Azure Stack](azure-stack-updates.md) 


