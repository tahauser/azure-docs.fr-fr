---
title: "Utiliser le validateur de modèle pour vérifier des modèles pour Azure Stack | Microsoft Docs"
description: "Vérifier des modèles pour un déploiement sur Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c30b0a78cf3421554cf8f7c887c7973c7b9f4b9c
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Vérifier vos modèles pour Azure Stack par le validateur de modèle

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser l’outil de validation des modèles pour vérifier si vos [modèles](azure-stack-arm-templates.md) Azure Resource Manager sont prêts pour Azure Stack. L’outil de validation des modèles fait partie des outils disponibles avec Azure Stack. Téléchargez les outils Azure Stack en utilisant la procédure décrite dans l’article [Télécharger des outils à partir de GitHub](azure-stack-powershell-download.md). 

Pour valider des modèles, vous utilisez les modules PowerShell suivants et le fichier JSON situé dans les dossiers **TemplateValidator** et **CloudCapabilities** : 

 - AzureRM.CloudCapabilities.psm1 crée un fichier JSON des fonctionnalités du cloud représentant les services et versions présentes dans un cloud comme Azure Stack.
 - AzureRM.TemplateValidator.psm1 utilise un fichier JSON des fonctionnalités du cloud pour tester des modèles en vue d’un déploiement dans Azure Stack.
 - AzureStackCloudCapabilities_with_AddOns_20170627.json est un fichier des fonctionnalités du cloud par défaut.  Vous pouvez créer votre propre fichier ou utiliser ce fichier pour commencer. 

Dans cette rubrique, vous aller exécuter une validation de vos modèles, et éventuellement générer un fichier des fonctionnalités du cloud.

## <a name="validate-templates"></a>Valider des modèles
Dans cette procédure, vous allez valider des modèles à l’aide du module PowerShell AzureRM.TemplateValidator. Vous pouvez utiliser vos propres modèles, ou valider les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Importez le module PowerShell AzureRM.TemplateValidator.psm1 :
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Exécutez le validateur de modèle :

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Les avertissements ou erreurs de validation de modèle sont journalisés sur la console PowerShell, ainsi que dans un fichier HTML situé dans le répertoire source. La sortie de rapport de validation peut se présenter ainsi :

![exemple de rapport de validation](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

| Paramètre | DESCRIPTION | Obligatoire |
| ----- | -----| ----- |
| TemplatePath | Spécifie le chemin pour rechercher des modèles Resource Manager de manière récursive | OUI | 
| TemplatePattern | Spécifie le nom des fichiers de modèle à faire correspondre | Non  |
| CapabilitiesPath | Spécifie le chemin du fichier JSON des fonctionnalités du cloud | OUI | 
| IncludeComputeCapabilities | Inclut l’évaluation de ressources IaaS telles que des tailles de machine virtuelle et des extensions de machine virtuelle | Non  |
| IncludeStorageCapabilities | Inclut l’évaluation de ressources de stockage comme des types de références (SKU) | Non  |
| Rapport | Spécifie le nom du rapport HTML généré | Non  |
| Détaillé | Journalise les erreurs et les avertissements dans la console | Non |


### <a name="examples"></a>Exemples
Cet exemple valide tous les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) téléchargés localement. Il valide également les tailles et extensions de machine virtuelle par rapport aux fonctionnalités du Kit de développement Azure Stack.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Générer le fichier des fonctionnalités du cloud
Les fichiers téléchargés incluent un fichier *AzureStackCloudCapabilities_with_AddOns_20170627.json* par défaut, qui décrit les versions de services disponibles dans le Kit de développement Azure Stack avec les services PaaS installés.  Si vous installez des fournisseurs de ressources supplémentaires, vous pouvez utiliser le module PowerShell AzureRM.CloudCapabilities pour générer un fichier JSON incluant les nouveaux services.  

1.  Vérifiez que vous disposez d’une connectivité à Azure Stack.  Cette procédure peut être effectuée à partir de l’hôte de kit de développement Azure Stack, ou vous pouvez utiliser un [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) pour vous connecter à partir de votre station de travail. 
2.  Importez le module PowerShell AzureRM.CloudCapabilities :

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Utilisez l’applet de commande Get-CloudCapabilities pour récupérer des versions de services et créer un fichier JSON des fonctionnalités du cloud :

    ```PowerShell
    Get-AzureRMCloudCapability -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>étapes suivantes
 - [Déployer des modèles sur Azure Stack](azure-stack-arm-templates.md)
 - [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)

