---
title: "Créer une machine virtuelle avec une adresse IP publique statique - Modèle Azure Resource Manager | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle avec une adresse IP publique statique à l’aide d’un modèle Azure Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d551085a-c7ed-4ec6-b4c3-e9e1cebb774c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f503aa60fdd9b7cf66ef482a1041e34c88e5c01
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-an-azure-resource-manager-template"></a>Créer une machine virtuelle avec une adresse IP publique statique à l’aide d’un modèle Azure Resource Manager

> [!div class="op_single_selector"]
> * [portail Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modèle](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (classique)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-address-resources-in-a-template-file"></a>Ressources d’adresses IP publiques dans un fichier de modèle
Vous pouvez afficher et télécharger les [exemples de modèles](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

La section ci-dessous illustre la définition de la ressource d’adresse IP publique d’après le scénario ci-dessus :

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('webVMSetting').pipName]",
  "location": "[variables('location')]",
  "properties": {
    "publicIPAllocationMethod": "Static"
  },
  "tags": {
    "displayName": "PublicIPAddress - Web"
  }
},
```

Notez la propriété **publicIPAllocationMethod** qui a la valeur *Static*. Cette propriété peut avoir la valeur *Dynamic* (par défaut) ou *Static*. La valeur Static garantit que l’adresse IP publique affectée ne changera jamais.

La section suivante illustre l’association de l’adresse IP publique à une interface réseau :

```json
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "name": "[variables('webVMSetting').nicName]",
    "location": "[variables('location')]",
    "tags": {
    "displayName": "NetworkInterface - Web"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
      "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
          "privateIPAllocationMethod": "Static",
          "privateIPAddress": "[variables('webVMSetting').ipAddress]",
          "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
          },
          "subnet": {
            "id": "[variables('frontEndSubnetRef')]"
          }
        }
      }
    ]
  }
},
```

Notez la présence de la propriété **publicIPAddress** qui pointe vers l’**Id** d’une ressource nommée **variables(’webVMSetting’).pipName**. Il s’agit du nom de la ressource IP publique indiquée ci-dessus.

Enfin, l’interface réseau ci-dessus est répertoriée dans la propriété **networkProfile** de la machine virtuelle en cours de création.

```json
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }
```

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Déployer le modèle en un clic

L’exemple de modèle disponible dans le référentiel public utilise un fichier de paramètres contenant les valeurs par défaut utilisées pour générer le scénario décrit ci-dessus. Pour déployer ce modèle en un clic, cliquez sur **Déployer dans Azure** dans le fichier Readme.md pour le modèle [Machine virtuelle avec PIP statique](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) . Remplacez les valeurs de paramètre par défaut si vous le souhaitez et entrez des valeurs pour les paramètres vides.  Suivez les instructions dans le portail pour créer une machine virtuelle avec une adresse IP publique statique.

## <a name="deploy-the-template-by-using-powershell"></a>Déployer le modèle à l’aide de PowerShell

Pour déployer le modèle téléchargé à l’aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, procédez de la manière décrite dans l’article [Installer et configurer Azure PowerShell](/powershell/azure/overview).
2. Dans une console PowerShell, exécutez l’applet de commande `New-AzureRmResourceGroup` pour créer un groupe de ressources si nécessaire. Si vous avez déjà créé un groupe de ressources, passez à l’étape 3.

    ```powershell
    New-AzureRmResourceGroup -Name PIPTEST -Location westus
    ```

    Sortie attendue :
   
        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Dans une console PowerShell, exécutez l’applet de commande `New-AzureRmResourceGroupDeployment` pour déployer le modèle.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
        -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
        -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json
    ```

    Sortie attendue :
   
        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0
   
        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         
   
        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Déployer le modèle à l’aide de l’interface de ligne de commande Azure
Pour déployer le modèle à l’aide d’Azure CLI, procédez comme suit :

1. Si vous n’avez jamais utilisé Azure CLI, procédez de la manière décrite dans l’article [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md).
2. Exécutez la commande `azure config mode` pour passer en mode Resource Manager, comme illustré ci-dessous.

    ```azurecli
    azure config mode arm
    ```

    Voici la sortie attendue de la commande ci-dessus :

        info:    New mode is arm

3. Ouvrez le [fichier de paramètres](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), sélectionnez son contenu et enregistrez-le dans un fichier sur votre ordinateur. Pour cet exemple, les paramètres sont enregistrés dans un fichier nommé *parameters.json*. Modifiez les valeurs de paramètre dans le fichier si vous le souhaitez. Il est recommandé de modifier au minimum la valeur du paramètre adminPassword en un mot de passe complexe unique.
4. Exécutez la commande `azure group deployment create` pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. Dans la commande ci-dessous, remplacez <path> par le chemin d’accès dans lequel vous avez enregistré le fichier. 

    ```azurecli
    azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json
    ```

    Sortie attendue (répertorie les valeurs de paramètre utilisées) :

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/[Subscription ID]/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

