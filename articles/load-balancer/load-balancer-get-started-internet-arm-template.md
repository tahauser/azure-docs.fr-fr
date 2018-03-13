---
title: "Créer un équilibreur de charge public - modèle Azure | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge public dans Resource Manager à l’aide d’un modèle"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 21ff5a48d29117ba1d6195f308f00180b1f1edbf
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>Création d’un équilibreur de charge public à l’aide d’un modèle

> [!div class="op_single_selector"]
> * [Portail](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle en un clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, suivez [ce lien](http://go.microsoft.com/fwlink/?LinkId=544801), cliquez sur **Déployer dans Azure**, remplacez les valeurs de paramètre par défaut si nécessaire, puis suivez les instructions dans le portail.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n'avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d'Azure PowerShell](/powershell/azure/overview) et suivez les instructions jusqu'à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Pour créer un groupe de ressources à l'aide du modèle, exécutez l'applet de commande **New-AzureRmResourceGroupDeployment** .

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle à l’aide de l’interface de ligne de commande Azure, procédez comme suit.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installation et configuration de l’interface de ligne de commande Azure](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

    ```azurecli
    azure config mode arm
    ```

    Voici le résultat attendu pour la commande ci-dessus :

        info:    New mode is arm

3. Dans votre navigateur, accédez au [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copiez le contenu du fichier json et collez-le dans un nouveau fichier sur votre ordinateur. Pour ce scénario, vous allez copier les valeurs ci-dessous dans un fichier nommé **C:\lb\azuredeploy.parameters.json**.
4. Exécutez l’applet de commande **azure group deployment create** pour déployer le nouvel équilibreur de charge à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus tôt. La liste affichée après le résultat présente les différents paramètres utilisés.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
