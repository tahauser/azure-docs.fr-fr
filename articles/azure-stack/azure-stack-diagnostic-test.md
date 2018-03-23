---
title: Exécuter un test de validation dans Azure Stack | Microsoft Docs
description: Comment collecter des fichiers journaux de diagnostics dans Azure Stack
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: 4f86397d4db5a0e67b294befd92087166d6b8109
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Exécuter un test de validation pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*
 
Vous pouvez valider l’état de votre système Azure Stack. Lorsque vous rencontrez un problème, contactez les services de support technique Microsoft. Votre interlocuteur vous demandera d’exécuter l’applet de commande Test-AzureStack à partir de votre nœud de gestion. Le test de validation isole le problème. Votre interlocuteur peut ensuite analyser les journaux détaillés, se concentrer sur la zone où l’erreur s’est produite et résoudre avec vous le problème.

## <a name="run-test-azurestack"></a>Exécuter l’applet de commande Test-AzureStack

Lorsque vous rencontrez un problème, contactez les services de support technique Microsoft, puis exécutez **Test-AzureStack**.

1. Vous rencontrez un problème.
2. Contactez les services de support technique Microsoft.
3. Exécutez **Test-AzureStack** à partir du point de terminaison privilégié.
    1. Accédez au point de terminaison privilégié. Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Connectez-vous en tant que **AzureStack\CloudAdmin** sur l’hôte de gestion.
    3. Ouvrez PowerShell ISE en tant qu’administrateur.
    4. Exécuter : `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Exécuter : `Test-AzureStack`
4. Si l’un des tests signale une erreur, exécutez : `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` L’applet de commande collecte les journaux à partir de Test-AzureStack. Pour plus d’informations sur les journaux de diagnostic, voir [Outils de diagnostics Azure Stack](azure-stack-diagnostics.md).
5. Envoyez les journaux **SeedRing** aux services de support technique Microsoft. Les services de support technique Microsoft résoudront ensuite le problème avec votre aide.

## <a name="reference-for-test-azurestack"></a>Informations de référence pour Test-AzureStack

Cette section présente l’applet de commande Test-AzureStack et fournit un résumé du rapport de validation.

### <a name="test-azurestack"></a>Test-AzureStack

Valide l’état du système Azure Stack. L’applet de commande décrit l’état de vos composants matériels et logiciels Azure Stack. L’équipe du support Microsoft peut utiliser ce rapport pour traiter plus rapidement les cas de support concernant Azure Stack.

> [!Note]  
> L’applet de commande Test-AzureStack peut détecter les erreurs qui n’entraînent pas de défaillance du cloud, telles qu’une panne d’un disque ou une défaillance d’un nœud d’hôte physique.

#### <a name="syntax"></a>Syntaxe

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parameters

| Paramètre               | Valeur           | Obligatoire | Default |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Non        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Non        | FALSE   |
| AdminCredential         | PSCredential    | Non        | N/D      |
<!-- | StorageConnectionString | Chaîne          | Non        | N/D      | non pris en charge dans 1802-->
| Liste                    | SwitchParameter | Non        | FALSE   |
| Ignorer                  | Chaîne          | Non        | N/D      |
| Inclure                 | Chaîne          | Non        | N/D      |

L’applet de commande Test-AzureStack prend en charge les paramètres courants : Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable et OutVariable. Pour plus d’informations, voir [About Common Parameters](http://go.microsoft.com/fwlink/?LinkID=113216) (À propos des paramètres courants). 

### <a name="examples-of-test-azurestack"></a>Exemples d’applet de commande Test-AzureStack

Les exemples suivants supposent que vous êtes connecté en tant que **CloudAdmin** et que vous accédez au point de terminaison privilégié (PEP). Pour obtenir des instructions, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Exécuter Test-AzureStack de manière interactive sans scénario de cloud

Dans une session PEP, exécutez :

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Exécuter Test-AzureStack avec des scénarios de cloud

Vous pouvez utiliser Test-AzureStack pour exécuter des scénarios de cloud dans votre système Azure Stack. Ces scénarios sont les suivants :

 - Création de groupes de ressources
 - Création de plans
 - Création d’offres
 - Création de comptes de stockage
 - Création d’une machine virtuelle
 - Effectuer des opérations d’objet blob à l’aide du compte de stockage créé dans le scénario de test
 - Effectuer des opérations de file d’attente à l’aide du compte de stockage créé dans le scénario de test
 - Effectuer des opérations de table à l’aide du compte de stockage créé dans le scénario de test

Les scénarios de cloud nécessitent des informations d’identification d’administrateur de cloud. 
> [!Note]  
> Vous ne pouvez pas exécuter les scénarios de cloud à l’aide des informations d’identification des services de fédération Active Directory (AD FS). L’applet de commande **Test-AzureStack** est uniquement accessible via le point de terminaison privilégié. Toutefois, le point de terminaison privilégié n’accepte pas les informations d’identification AD FS.

Saisissez le nom d’utilisateur d’administrateur de cloud au format UPN serviceadmin@contoso.onmicrosoft.com (AAD). Lorsque vous y êtes invité, saisissez le mot de passe du compte d’administrateur de cloud.

Dans une session PEP, exécutez :

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Exécuter Test-AzureStack sans scénario de cloud

Dans une session PEP, exécutez :

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Liste des scénarios de test disponibles :

Dans une session PEP, exécutez :

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Exécuter un test spécifique

Dans une session PEP, exécutez :

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Pour exclure les tests spécifiques :

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Test de validation

Le tableau suivant répertorie les tests de validation exécutés par Test-AzureStack.

| NOM                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Résumé de l’infrastructure d’hébergement cloud Azure Stack                                                                                  |
| Résumé des services de stockage Azure Stack                                                                                              |
| Résumé des instances de rôle d’infrastructure Azure Stack                                                                                  |
| Utilisation de l’infrastructure d’hébergement cloud Azure Stack                                                                              |
| Capacité de l’infrastructure Azure Stack                                                                                               |
| Résumé de l’API et du portail Azure Stack                                                                                                |
| Résumé du certificat Azure Resource Manager Azure Stack                                                                                               |
| Rôles d’infrastructure associés au contrôleur de gestion d’infrastructure, au contrôleur de réseau, aux services de stockage et au point de terminaison privilégié          |
| Instances de rôle d’infrastructure associées au contrôleur de gestion d’infrastructure, au contrôleur de réseau, aux services de stockage et au point de terminaison privilégié |
| Résumé des rôles d’infrastructure Azure Stack                                                                                           |
| Services Cloud Service Fabric Azure Stack                                                                                         |
| Performances des instances de rôle d’infrastructure Azure Stack                                                                              |
| Résumé des performances d’hébergement cloud Azure Stack                                                                                        |
| Résumé de la consommation des ressources de service Azure Stack                                                                                  |
| Événements critiques d’unité d’échelle Azure Stack (au cours des 8 dernières heures)                                                                             |
| Résumé des disques physiques des services de stockage Azure Stack                                                                               |

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur les outils de diagnostic Azure Stack et l’enregistrement des problèmes, voir [Outils de diagnostic Azure Stack](azure-stack-diagnostics.md).
 - Pour plus d’informations sur la résolution des problèmes, voir [Résolution des problèmes de Microsoft Azure Stack](azure-stack-troubleshooting.md).