---
title: "Utiliser le validateur de modèle pour vérifier des modèles pour Azure Stack | Microsoft Docs"
description: "Vérifier des modèles pour un déploiement sur Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Vérifier vos modèles pour Azure Stack par le validateur de modèle

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser l’outil de validation des modèles pour vérifier si vos [modèles](azure-stack-arm-templates.md) Azure Resource Manager sont prêts pour Azure Stack. L’outil de validation des modèles fait partie des outils disponibles avec Azure Stack. Téléchargez les outils Azure Stack en utilisant la procédure décrite dans l’article [Télécharger des outils à partir de GitHub](azure-stack-powershell-download.md). 

Pour valider des modèles, vous utilisez les modules PowerShell suivants dans les dossiers **TemplateValidator** et **CloudCapabilities** : 

 - AzureRM.CloudCapabilities.psm1 crée un fichier JSON des fonctionnalités du cloud représentant les services et versions présentes dans un cloud comme Azure Stack.
 - AzureRM.TemplateValidator.psm1 utilise un fichier JSON des fonctionnalités du cloud pour tester des modèles en vue d’un déploiement dans Azure Stack.
 
Dans cet article, vous générez un fichier de fonctionnalités cloud et exécutez l’outil de validation.

## <a name="build-cloud-capabilities-file"></a>Générer le fichier des fonctionnalités du cloud
Avant d’utiliser le validateur de modèle, exécutez le module PowerShell AzureRM.CloudCapabilities pour générer un fichier JSON. Si vous mettez à jour votre système intégré ou ajoutez de nouveaux services ou extensions de machine virtuelle, vous devez également exécuter à nouveau ce module.

1.  Vérifiez que vous disposez d’une connectivité à Azure Stack. Cette procédure peut être effectuée à partir de l’hôte du kit de développement Azure Stack, ou vous pouvez utiliser un [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) pour vous connecter à partir de votre station de travail. 
2.  Importez le module PowerShell AzureRM.CloudCapabilities :

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Utilisez l’applet de commande Get-CloudCapabilities pour récupérer des versions de service et créer un fichier JSON de fonctionnalités cloud. Si vous ne spécifiez pas -OutputPath, le fichier AzureCloudCapabilities.Json est créé dans le répertoire actif. Utilisez votre emplacement réel :

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

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

Les avertissements ou erreurs de validation de modèle sont journalisés sur la console PowerShell, ainsi que dans un fichier HTML situé dans le répertoire source. Voici un exemple de rapport de validation :

![exemple de rapport de validation](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

| Paramètre | Description | Obligatoire |
| ----- | -----| ----- |
| TemplatePath | Spécifie le chemin pour rechercher des modèles Azure Resource Manager de manière récursive | OUI | 
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


## <a name="next-steps"></a>Étapes suivantes
 - [Déployer des modèles sur Azure Stack](azure-stack-arm-templates.md)
 - [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)

