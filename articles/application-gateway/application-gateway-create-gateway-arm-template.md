---
title: "Créer une passerelle Azure Application Gateway - modèles | Microsoft Docs"
description: "Cette page fournit des instructions pour la création d’une passerelle Azure Application Gateway à l’aide du modèle Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 0aa16e9d7472d2d8c3c251e60a506a7f4223ac1d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Création d’une passerelle d’application à l’aide du modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre des serveurs locaux ou dans le cloud. Application Gateway offre de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC) : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, voir [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

Cet article vous guidera dans le téléchargement et la modification d’un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) existant à partir de GitHub, et dans son déploiement à partir de GitHub, de PowerShell et de l’interface Azure CLI.

Si vous déployez simplement le modèle directement à partir de GitHub, sans rien modifier, passez à la section Déployer un modèle à partir de GitHub.

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez :

* créer une passerelle d’application avec le pare-feu d’applications web ;
* créer un réseau virtuel nommé VirtualNetwork1 avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
* installer deux adresses IP terminales précédemment configurées pour les serveurs web pour lesquels vous souhaitez équilibrer la charge du trafic. Dans cet exemple de modèle, nous allons utiliser les adresses IP terminales 10.0.1.10 et 10.0.1.11.

> [!NOTE]
> Ce sont les paramètres de ce modèle. Pour personnaliser le modèle, vous pouvez modifier les règles, l’écouteur, le protocole SSL et d’autres options en ouvrant le fichier azuredeploy.json.

![Scénario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Téléchargement et découverte du modèle Azure Resource Manager

Vous pouvez télécharger le modèle Azure Resource Manager existant pour créer un réseau virtuel et deux sous-réseaux sur GitHub, apporter les modifications souhaitées, puis le réutiliser. Pour ce faire, procédez comme suit :

1. Accédez à [Créer une passerelle d’application avec le pare-feu d’applications web activé](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Cliquez sur **azuredeploy.json**, puis sur **RAW**.
1. Enregistrez le fichier dans un dossier local sur votre ordinateur.
1. Si vous connaissez déjà les modèles Azure Resource Manager, passez à l’étape 7.
1. Ouvrez le fichier que vous avez enregistré et consultez le contenu sous **parameters** à la ligne
1. Les paramètres du modèle Azure Resource Manager fournissent un espace réservé pour les valeurs à remplir lors du déploiement.

  | Paramètre | Description |
  | --- | --- |
  | **subnetPrefix** |Bloc CIDR du sous-réseau de la passerelle Application Gateway. |
  | **applicationGatewaySize** | Taille de la passerelle Application Gateway.  Le pare-feu d’applications web autorise uniquement les tailles moyenne et grande. |
  | **backendIpaddress1** |Adresse IP du premier serveur web. |
  | **backendIpaddress2** |Adresse IP du deuxième serveur web. |
  | **wafEnabled** | Permet de déterminer si le pare-feu d’applications web est activé.|
  | **wafMode** | Mode du pare-feu d’applications web.  Les options disponibles sont **prévention** ou **détection**.|
  | **wafRuleSetType** | Type de groupe de règles du pare-feu d’applications web.  Actuellement, OWASP est la seule option prise en charge. |
  | **wafRuleSetVersion** |Version de groupe de règles. Les options prises en charge sont actuellement OWASP CRS 2.2.9 et 3.0. |

1. Vérifiez le contenu sous **ressources** et notez les propriétés suivantes :

   * **type**. Type de ressource créée par le modèle. Dans ce cas, le type qui représente une passerelle d’application est `Microsoft.Network/applicationGateways`.
   * **name**. Nom de la ressource. Remarquez l’utilisation de `[parameters('applicationGatewayName')]`, qui signifie que le nom est fourni par vous ou un fichier de paramètres lors du déploiement.
   * **properties**. Liste des propriétés de la ressource. Ce modèle utilise le réseau virtuel et une adresse IP publique lors de la création de la passerelle Application Gateway.

1. Revenez à [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Cliquez sur **azuredeploy-parameters.json**, puis sur **RAW**.
1. Enregistrez le fichier dans un dossier local sur votre ordinateur.
1. Ouvrez le fichier que vous avez enregistré et modifiez les valeurs des paramètres. Utilisez les valeurs suivantes pour déployer la passerelle Application Gateway décrite dans notre scénario.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "addressPrefix": {
            "value": "10.0.0.0/16"
            },
            "subnetPrefix": {
            "value": "10.0.0.0/28"
            },
            "applicationGatewaySize": {
            "value": "WAF_Medium"
            },
            "capacity": {
            "value": 2
            },
            "backendIpAddress1": {
            "value": "10.0.1.10"
            },
            "backendIpAddress2": {
            "value": "10.0.1.11"
            },
            "wafEnabled": {
            "value": true
            },
            "wafMode": {
            "value": "Detection"
            },
            "wafRuleSetType": {
            "value": "OWASP"
            },
            "wafRuleSetVersion": {
            "value": "3.0"
            }
        }
    }
    ```

1. Enregistrez le fichier . Vous pouvez tester le modèle JSON et le modèle de paramètres à l’aide des outils de validation JSON en ligne comme [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Déploiement du modèle Azure Resource Manager à l’aide de PowerShell

Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) et suivez les instructions pour vous connecter à Azure et sélectionner votre abonnement.

1. Connexion à PowerShell

    ```powershell
    Login-AzureRmAccount
    ```

1. Vérifiez les abonnements associés au compte.

    ```powershell
    Get-AzureRmSubscription
    ```

    Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

1. Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    ```powershell
    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
    ```

1. Au besoin, créez un groupe de ressources à l’aide de l’applet de commande **New-AzureResourceGroup** . Dans l’exemple suivant, vous allez créer un groupe de ressources appelé AppgatewayRG dans les États-Unis de l’Est.

    ```powershell
    New-AzureRmResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Exécutez l’applet de commande **New-AzureRmResourceGroupDeployment** pour déployer le nouveau réseau virtuel à l’aide du modèle précédent et des fichiers de paramètres que vous avez téléchargés et modifiés.
    
    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Déploiement du modèle Azure Resource Manager à l’aide de l’interface de ligne de commande Azure

Pour déployer le modèle Azure Resource Manager que vous avez téléchargé à l’aide de l’interface de ligne de commande Azure, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.

1. Au besoin, exécutez la commande `az group create` pour créer un groupe de ressources, comme illustré dans l’extrait de code suivant. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez la page [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (ou --name)**. Nom du nouveau groupe de ressources. Pour notre scénario, il s’agit de *appgatewayRG*.
    
    **-l (ou --location)**. Région Azure où le nouveau groupe de ressources est créé. Pour notre scénario, il s’agit de *westus*.

1. Exécutez l’applet de commande `az group deployment create` pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés lors de l’étape précédente. La liste affichée après le résultat présente les différents paramètres utilisés.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Déploiement du modèle Azure Resource Manager à l’aide de la fonctionnalité « cliquer pour déployer »

La fonctionnalité « cliquer pour déployer » offre une autre manière d’utiliser les modèles ARM. C’est un moyen facile d’utiliser des modèles avec le portail Azure.

1. Accédez à [Créer une passerelle d’application avec le pare-feu d’applications web](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Cliquez sur **Déployer dans Azure**.

    ![Déployer dans Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Renseignez les paramètres du modèle de déploiement sur le portail, puis cliquez sur **OK**.

    ![Paramètres](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus**, puis cliquez sur **Acheter**.

1. Dans le panneau Déploiement personnalisé, cliquez sur **Créer**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Fournir des données de certificat aux modèles Resource Manager

Si SSL est utilisé avec un modèle, le certificat doit être fourni dans une chaîne base64, et non chargé. Pour convertir un .pfx ou un .cer en chaîne base64, utilisez l’une des commandes suivantes. Les commandes ci-après convertissent le certificat en chaîne base64 qui pourra être fournie au modèle. La sortie attendue est une chaîne qui peut être stockée dans une variable et collée dans le modèle.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, effectuez l’une des opérations suivantes :

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureRmResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Interface de ligne de commande Azure

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez la page [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez la page [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur les options d’équilibrage de charge en général, visitez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

