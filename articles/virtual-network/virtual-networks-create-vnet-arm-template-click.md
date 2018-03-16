---
title: "Création d’un réseau virtuel | Modèle Azure Resource Manager | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel à l’aide d’un modèle Azure Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Créer un réseau virtuel à l’aide d’un modèle Azure Resource Manager

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Cet article explique comment créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du modèle Azure Resource Manager. Vous pouvez également créer un réseau virtuel via Resource Manager à l’aide d’autres outils ou créer un réseau virtuel via le modèle de déploiement classique en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
- [Portail](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [INTERFACE DE LIGNE DE COMMANDE](quick-create-cli.md)
- [Modèle](virtual-networks-create-vnet-arm-template-click.md)
- [Portail (classique)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (classique)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Interface de ligne de commande (classique)](virtual-networks-create-vnet-classic-cli.md)

Découvrez comment télécharger et modifier un modèle Azure Resource Manager existant à partir de GitHub, et apprendrez à déployer le modèle à partir de GitHub, de PowerShell et de l’interface de ligne de commande Azure.

Si vous déployez simplement le modèle Azure Resource Manager directement à partir de GitHub sans rien modifier, passez à la section [Déployer un modèle à partir de GitHub](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Téléchargement et découverte du modèle Azure Resource Manager
Vous pouvez télécharger le modèle existant pour créer un réseau virtuel et deux sous-réseaux sur GitHub, apporter les modifications souhaitées, puis le réutiliser. Pour ce faire, effectuez les opérations suivantes :

1. Accédez à la [page d’exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Cliquez sur **azuredeploy.json**, puis sur **RAW**.
3. Enregistrez le fichier dans un dossier local sur votre ordinateur.
4. Si vous connaissez déjà les modèles, passez à l’étape 7.
5. Ouvrez le fichier que vous avez enregistré et consultez le contenu sous **parameters** à la ligne 5. Les paramètres du modèle Azure Resource Manager fournissent un espace réservé pour les valeurs à remplir lors du déploiement.
   
   | Paramètre | Description |
   | --- | --- |
   | **vnetName** |Nom du nouveau réseau virtuel |
   | **addressPrefix** |Espace d’adressage du réseau virtuel, au format CIDR |
   | **subnet1Name** |Nom du premier réseau virtuel |
   | **subnet1Prefix** |Bloc CIDR du premier sous-réseau |
   | **subnet2Name** |Nom du deuxième réseau virtuel |
   | **subnet2Prefix** |Bloc CIDR du deuxième sous-réseau |
   
   > [!IMPORTANT]
   > Les modèles Azure Resource Manager de GitHub sont susceptibles d’évoluer. Vérifiez le modèle avant de l’utiliser.
   > 
   > 
6. Vérifiez le contenu sous **resources** et notez les éléments suivants :
   
   * **type**. Type de ressource créée par le modèle. Dans ce cas, **Microsoft.Network/virtualNetworks**, qui représente un réseau virtuel.
   * **name**. Nom de la ressource. Remarquez l’utilisation de **[parameters('vnetName')]**, ce qui signifie que le nom sera fourni par l’utilisateur ou un fichier de paramètres au cours du déploiement.
   * **properties**. Liste des propriétés de la ressource. Ce modèle utilise les propriétés d’espace d’adressage et de sous-réseau lors de la création du réseau virtuel.
7. Retournez à la [page d’exemples de modèles](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Cliquez sur **azuredeploy-paremeters.json**, puis cliquez sur **RAW**.
9. Enregistrez le fichier dans un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous venez d’enregistrer et modifiez les valeurs des paramètres. Utilisez les valeurs suivantes pour déployer le réseau virtuel décrit dans le scénario :

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Enregistrez le fichier .


## <a name="deploy-the-template-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Suivez les étapes ci-dessous pour déployer le modèle téléchargé à l’aide de PowerShell :

1. Installez et configurez Azure PowerShell en suivant les instructions de l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
2. Utilisez la commande suivante pour créer un groupe de ressources :

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    La commande suivante crée un groupe de ressources appelé *TestRG* dans la région Azure *États-Unis du Centre*. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Sortie attendue :

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Exécutez la commande suivante pour déployer le nouveau réseau virtuel en utilisant le modèle et les fichiers de paramètres que vous avez téléchargés et modifiés plus haut :

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Sortie attendue :
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Exécutez la commande suivante pour afficher les propriétés du nouveau réseau virtuel :

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Sortie attendue :

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Déployer le modèle en un clic

Vous pouvez réutiliser les modèles Azure Resource Manager prédéfinis sur le référentiel GitHub géré par Microsoft et ouvert à la communauté. Ces modèles peuvent être déployés directement depuis GitHub ou téléchargés et modifiés selon vos besoins. Pour déployer un modèle qui crée un réseau virtuel avec deux sous-réseaux, suivez les étapes ci-dessous :

1. Dans un navigateur, accédez à [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Parcourez la liste des modèles, puis cliquez sur **101-vnet-two-subnets**. Consultez le fichier **README.md** , comme illustré ci-dessous.

    ![Fichier READEME.md dans GitHub](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Cliquez sur **Déployer dans Azure**. Si nécessaire, entrez vos informations d’identification Azure. 
4. Dans le panneau **Paramètres**, entrez les valeurs que vous souhaitez utiliser pour créer votre réseau virtuel, puis cliquez sur **OK**. La figure suivante montre les valeurs du scénario :
   
    ![Paramètres de modèle ARM](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources auquel ajouter le réseau virtuel ou cliquez sur **Créer** pour ajouter le réseau virtuel à un groupe de ressources. La figure suivante illustre les paramètres du nouveau groupe de ressources **TestRG** :

    ![Groupe de ressources](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Si nécessaire, modifiez les paramètres **Abonnement** et **Emplacement** de votre réseau virtuel.
7. Si vous ne souhaitez pas voir le réseau virtuel sous forme de mosaïque dans le **Tableau d’accueil**, désactivez **Épingler au tableau d’accueil**.
8. Cliquez sur **Conditions juridiques**, lisez les termes du contrat, puis cliquez sur **Acheter** si vous êtes d’accord. 
9. Pour créer le réseau virtuel, cliquez sur **Créer**.
   
    ![Mosaïque d’envoi d’un déploiement dans le portail en version préliminaire](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. Une fois le déploiement terminé, dans le portail Azure, cliquez sur **Tous les services**, tapez *réseaux virtuels* dans la zone de filtre qui s’affiche, puis cliquez sur Réseaux virtuels pour voir le panneau des réseaux virtuels. Dans le panneau , cliquez sur *TestVNet*. Dans le panneau *TestVNet*, cliquez sur **Sous-réseaux** pour afficher les sous-réseaux créés, comme indiqué dans l’image suivante :
    
     ![Créer un réseau virtuel dans le portail en version préliminaire](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Étapes suivantes

Apprenez à connecter :

- Une machine virtuelle (VM) à un réseau virtuel en lisant les articles [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Créer une machine virtuelle Linux](../virtual-machines/linux/quick-create-portal.md). Au lieu de créer un réseau virtuel et un sous-réseau comme indiqué dans les procédures de ces articles, vous pouvez sélectionner un réseau virtuel et un sous-réseau existants pour établir la connexion à une machine virtuelle.
- Le réseau virtuel à d’autres réseaux virtuels en lisant l’article sur la [connexion des réseaux virtuels](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Le réseau virtuel à un réseau local à l’aide d’un réseau privé virtuel (VPN) site à site ou d’un circuit ExpressRoute. Découvrez comment en lisant les articles [Connecter un réseau virtuel à un réseau local à l’aide d’un VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) et [Lier un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md).
