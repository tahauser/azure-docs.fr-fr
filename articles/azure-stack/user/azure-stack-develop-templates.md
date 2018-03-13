---
title: "Développer des modèles pour Azure Stack | Microsoft Docs"
description: "Découvrir les meilleures pratiques en matière de modèles Azure Stack"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: bbd6cc68f1c16d48380cf498d6b089abe923e95a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="azure-resource-manager-template-considerations"></a>Considérations relatives au modèle Azure Resource Manager

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Lorsque vous développez votre application, il est important de garantir la portabilité du modèle entre Azure et Azure Stack. Cet article présente certaines considérations relatives au développement de [modèles](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) Azure Resource Manager, afin que vous puissiez mettre au point un prototype de votre déploiement d’application et de test dans Azure sans avoir accès à un environnement Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilité du fournisseur de ressources
Le modèle que vous envisagez de déployer ne doit utiliser que les services Microsoft Azure déjà disponibles ou en préversion dans Azure Stack.

## <a name="public-namespaces"></a>Espaces de noms publics
Comme Azure Stack est hébergé dans votre centre de données, il dispose d’espaces de noms de point de terminaison de service autres que le cloud public Azure. Par conséquent, les points de terminaison publics codés en dur dans les modèles Azure Resource Manager échouent lorsque vous essayez de les déployer sur Azure Stack. Au lieu de cela, vous pouvez utiliser la fonction de *référence* et de *concaténation* pour générer dynamiquement le point de terminaison de service en fonction des valeurs récupérées à partir du fournisseur de ressources lors du déploiement. Par exemple, au lieu de spécifier *blob.core.windows.net* dans votre modèle, vous devez récupérer l’objet [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) pour définir dynamiquement le point de terminaison *osDisk.URI* :

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Gestion des versions d’API
Les versions des services Azure peuvent différer entre Azure et Azure Stack. Chaque ressource requiert l’attribut apiVersion, qui définit les fonctionnalités proposées. Pour assurer la compatibilité des versions d’API dans Azure Stack, voici les versions d’API valides pour chaque fournisseur de ressources :

| Fournisseur de ressources | apiVersion |
| --- | --- |
| Calcul |`'2015-06-15'` |
| Réseau |`'2015-06-15'`, `'2015-05-01-preview'` |
| Stockage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Fonctions des modèles de gestionnaire des ressources Azure
Les [fonctions](../../azure-resource-manager/resource-group-template-functions.md) Azure Resource Manager offrent les fonctionnalités nécessaires pour créer des modèles dynamiques. Par exemple, vous pouvez utiliser des fonctions pour des tâches telles que :

* la concaténation ou la troncation des chaînes ; 
* le référencement de valeurs d’autres ressources ;
* l’itération sur les ressources pour déployer plusieurs instances. 

Ces fonctions ne sont pas disponibles dans Azure Stack :

* Skip
* Take

## <a name="resource-location"></a>Emplacement des ressources
Les modèles Azure Resource Manager utilisent un attribut d’emplacement pour placer les ressources pendant le déploiement. Dans Azure, les emplacements font référence à une région, par exemple, Ouest des États-Unis ou Amérique du Sud. Dans Azure Stack, les emplacements sont différents, car Azure Stack est situé dans votre centre de données. Pour garantir que les modèles sont transférables entre Azure et Azure Stack, vous devez référencer l’emplacement du groupe de ressources lorsque vous déployez des ressources individuelles. Vous pouvez pour cela utiliser `[resourceGroup().Location]` afin de veiller à ce que toutes les ressources héritent de l’emplacement du groupe de ressources. L’extrait suivant est un exemple d’utilisation de cette fonction lors du déploiement d’un compte de stockage :

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Étapes suivantes
* [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
* [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
* [Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)

