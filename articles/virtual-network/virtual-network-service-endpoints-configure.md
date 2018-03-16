---
title: "Configurer des points de terminaison de service de réseau virtuel Azure | Microsoft Docs"
description: "Apprenez à activer et désactiver des points de terminaison de service de votre réseau virtuel."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: ee01b83101d4ab4ee8665c32aca22a793b41ac0d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-virtual-network-service-endpoints"></a>Configurer des points de terminaison de service de réseau virtuel

Les points de terminaison de service de réseau virtuel vous permettent de sécuriser les ressources du service Azure dans votre réseau virtuel Azure, en retirant tout accès Internet à ces ressources. Ils offrent une connexion directe depuis votre réseau virtuel à un service Azure, ce qui vous permet d’utiliser l’espace de l’adresse privée de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux services Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure. En savoir plus sur les [« Points de terminaison de service de réseau virtuel »](virtual-network-service-endpoints-overview.md)

Cet article détaille les étapes à suivre pour activer et désactiver des points de terminaison de service. Après l’activation des points de terminaison sur le sous-réseau d’un service Azure, vous pouvez sécuriser des ressources de service spécifiques à un réseau virtuel.

Les points de terminaison peuvent être configurés à l’aide du [portail Azure](#azure-portal), d’[Azure PowerShell](#azure-powershell), d’[Azure CLI](#azure-cli) ou d’un [modèle](#resource-manager-template) Azure Resource Manager.

>[!NOTE]
Pendant la version préliminaire, la fonctionnalité de points de terminaison de service de réseau virtuel est prise en charge dans certaines régions. Pour obtenir la liste des régions compatibles, consultez la page des [Mises à jour de réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Vue d’ensemble de la configuration d’un point de terminaison de service

- Les points de terminaison de service ne peuvent être configurés que sur des réseaux virtuels déployés via le modèle de déploiement Azure Resource Manager.

- Ils sont définis sur chaque sous-réseau d’un réseau virtuel.

- Dans un sous-réseau, vous ne pouvez configurer qu’un point de terminaison par service. Vous pouvez configurer plusieurs points de terminaison de service pour différents services (par exemple, le Stockage Azure, Azure SQL).

- Vous pouvez activer les points de terminaison sur un sous-réseau nouveau ou existant.

- L’emplacement d’un point de terminaison est configuré automatiquement. Par défaut, les points de terminaison de service sont configurés dans la région du réseau virtuel. Pour le Stockage Azure, afin de prendre en charge les scénarios de basculements régionaux, les points de terminaison sont configurés automatiquement à des [régions jumelées Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

  >[!NOTE]
  L’activation d’un point de terminaison de service peut prendre du temps, en fonction de la taille du réseau virtuel/sous-réseau. Veillez à ce qu’aucune tâche critique ne soit en cours lors de l’activation de point de terminaison de service. Les points de terminaison de service changent de routage sur chaque interface réseau de votre sous-réseau et peuvent mettre fin à toute connexion TCP ouverte. 

- L’appel du point de terminaison de service retourne le message « Succès » après que les flux de trafic du service sur toutes les interfaces réseau du sous-réseau ont été changés vers les adresses IP privées du réseau virtuel.

- Routages effectifs pour vérifier la configuration du point de terminaison :

   Pour vérifier si le point de terminaison de service a bien été configuré, les « routages effectifs » de chaque interface réseau du sous-réseau affichent un nouveau routage « par défaut » avec nextHopType: VirtualNetworkServiceEndpoint, par service et par région. En savoir plus sur la [résolution des problèmes liés aux routages effectifs](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   Les routages effectifs ne peuvent être affichés que si vous disposez d’une ou plusieurs interfaces numériques configurées et associées à une machine virtuelle en marche dans le sous-réseau.

## <a name="azure-portal"></a>Portail Azure

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Configuration d’un point de terminaison sur un sous-réseau pendant la création d’un réseau virtuel

1. Ouvrez le [portail Azure](https://portal.azure.com/).
Connectez-vous à votre compte Azure. Si vous n’en avez pas, vous pouvez demander un essai gratuit. Le compte doit avoir les [autorisations](#provisioning) nécessaires pour créer un réseau virtuel et un point de terminaison de service.
2. Cliquez sur **Créer une ressource** > **Mise en réseau** > **Réseau virtuel** > **+Ajouter**.
3. Dans « Créer un réseau virtuel », entrez les valeurs suivantes, puis cliquez sur **Créer** :

Paramètre | Valeur
------- | -----
NOM    | myVnet
Espace d’adressage | 10.0.0.0/16
Nom du sous-réseau|mySubnet
Plage d’adresses de sous-réseau|10.0.0.0/24
Groupe de ressources|Laissez l’option Créer activée, puis entrez un nom.
Lieu|Toute région prise en charge. Est de l’Australie, par exemple
Abonnement|Sélectionnez votre abonnement.
__Points de terminaison de service__|activé
__Services__ | Sélectionnez un ou tous les services disponibles. Services pris en charge : __« Microsoft.Storage », « Microsoft.Sql »__.

Sélectionnez les services pour les points de terminaison : ![Sélectionnez les services pour les points de terminaison de service](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Vérifiez que tous les paramètres sont corrects, puis cliquez sur « Créer ».

![Définir un point de terminaison de service](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Pour terminer la sécurisation des ressources de service Azure dans votre réseau virtuel, cliquez sur la documentation de service dans [Étapes suivantes](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Vérification de la configuration d’un point de terminaison de service

Pour vérifier que les points de terminaison de service sont configurés, procédez comme suit :

- Dans les ressources, cliquez sur « Réseaux virtuels ». Rechercher le réseau virtuel.
- Cliquez sur le nom du réseau virtuel et accédez à « Points de terminaison de service ».
- Les points de terminaison configurés affichent le message « Succès ». Les emplacements configurés automatiquement peuvent aussi être affichés

![Vérifier la configuration du point de terminaison de service](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Routages effectifs pour vérifier la configuration du point de terminaison

Pour afficher le routage effectif d’une interface réseau dans le sous-réseau, cliquez sur n’importe quelle interface réseau du sous-réseau. Sous « Prise en charge + Résolution des problèmes », cliquez sur « Routages effectifs ». Si le point de terminaison est configuré, un nouveau routage « par défaut » s’affiche, avec les préfixes d’adresse du service comme destination, et nextHopType comme « VirtualNetworkServiceEndpoint ».

![Routages effectifs pour les points de terminaison de service](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Configuration des points de terminaison de service pour les sous-réseaux existants d’un réseau virtuel

1. Dans les ressources, cliquez sur « Réseaux virtuels » et cherchez n’importe quel réseau virtuel existant.
2. Cliquez sur le nom du réseau virtuel et accédez à « Points de terminaison de service ».
3. Cliquez sur « Ajouter ». Sélectionnez « Service ». Vous ne pouvez créer qu’un point de terminaison par service à la fois. 
4. Sélectionnez tous les sous-réseaux dans lesquels vous souhaitez appliquer le point de terminaison. Cliquez sur « Ajouter ».

![Configuration du point de terminaison de service d’un sous-réseau](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Suppression de points de terminaison de service
1. Dans les ressources, cliquez sur « Réseaux virtuels ». Recherchez un réseau virtuel existant en filtrant par nom de réseau virtuel.
2. Cliquez sur le nom du réseau virtuel et accédez à « Points de terminaison de service ».
3. Cliquez sur le nom du service et faites un clic droit sur l’entrée du point de terminaison de service.
4. Sélectionnez « Supprimer ».

![Suppression d’un point de terminaison de service](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Conditions préalables pour la configuration :

- Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pour démarrer une session PowerShell, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
- Dans PowerShell, connectez-vous à Azure en entrant la commande `login-azurermaccount`. Le compte doit avoir les [autorisations](#provisioning) nécessaires pour créer un réseau virtuel et un point de terminaison de service.

### <a name="get-available-service-endpoints-for-azure-region"></a>Obtenir des points de terminaison de service disponibles pour une région Azure

Utilisez la commande ci-dessous pour obtenir la liste des services pris en charge pour les points de terminaison, en fonction de la région Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Output: 
NOM | ID | type
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Ajouter un point de terminaison du service de Stockage Azure à un sous-réseau *mySubnet* pendant la création du réseau virtuel *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Vous pouvez activer plusieurs services en séparant chaque liste de noms de service par une virgule.
Exemple : « Microsoft.Storage », « Microsoft.Sql »

Sortie attendue :
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Pour terminer la sécurisation des ressources de service Azure dans votre réseau virtuel, cliquez sur la documentation de service dans [Étapes suivantes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Ajouter plusieurs points de terminaison de service à un sous-réseau existant

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Sortie attendue : 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Afficher les points de terminaison de service configurés sur un sous-réseau

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Output:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Supprimer des points de terminaison de service sur un sous-réseau
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>Azure CLI

Conditions préalables pour la configuration :
- Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/reference-index#az_login) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - Le compte doit avoir les [autorisations](#provisioning) nécessaires pour créer un réseau virtuel et un point de terminaison de service.

 Pour voir la liste complète des commandes pour les réseaux virtuels, consulter [Commandes Azure CLI pour réseau virtuel](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>Obtenir des points de terminaison de service disponibles pour une région Azure

Utilisez la commande ci-dessous pour obtenir la liste des services pris en charge pour les points de terminaison, en fonction de la région Azure. « États-Unis de l’Est », par exemple.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Output:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Ajouter un point de terminaison du service de Stockage Azure à un sous-réseau *mySubnet* pendant la création du réseau virtuel *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Pour ajouter plusieurs points de terminaison : --service-endpoints Microsoft.Storage Microsoft.Sql

Output:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Pour terminer la sécurisation des ressources de service Azure dans votre réseau virtuel, cliquez sur la documentation de service dans [Étapes suivantes](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Ajouter plusieurs points de terminaison de service à un sous-réseau existant

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Sortie attendue :
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Afficher les points de terminaison de service configurés sur un sous-réseau

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Supprimer des points de terminaison de service sur un sous-réseau
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Output: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Modèle Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Sécurisation des ressources de service Azure dans des réseaux virtuels

Vous pouvez sécuriser des ressources Azure spécifiques dans vos réseaux virtuels via des points de terminaison de service.

Téléchargez l’exemple [Modèle Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) pour sécuriser un compte de stockage dans le sous-réseau d’un réseau virtuel.

Le modèle crée un réseau virtuel composé de deux sous-réseaux, et une machine virtuelle avec interface réseau dans chaque sous-réseau. Active le point de terminaison sur un sous-réseau et sécurise un compte de stockage sur ce sous-réseau.

Vous pouvez télécharger le modèle et le modifier en fonction de votre scénario.

Des instructions sont fournies avec le modèle pour le déployer à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande Azure. Veillez à posséder les [autorisations](#provisioning) nécessaires pour configurer les points de terminaison et sécuriser le compte.

Pour sécuriser les ressources Azure dans un sous-réseau, il faut :

- configurer un point de terminaison de service sur ce sous-réseau.
- sécuriser la ressource du réseau virtuel en lui ajoutant une règle de réseau virtuel.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Suppression des points de terminaison de service avec des ressources sécurisées dans le sous-réseau
Si les ressources de service Azure sont sécurisées dans le sous-réseau, et que le point de terminaison de service est supprimé, vous ne pouvez plus accéder à cette ressource depuis le sous-réseau.
La réactivation du point de terminaison ne suffira pas à restaurer l’accès aux ressources précédemment sécurisées dans le sous-réseau.

Pour sécuriser à nouveau la ressource de service dans ce sous-réseau, vous devez :

- réactiver le point de terminaison
- supprimer l’ancienne règle du réseau virtuel sur la ressource
- ajouter une nouvelle règle qui sécurise la ressource dans le sous-réseau

## <a name="provisioning"></a>Approvisionnement

Les points de terminaison de service peuvent être configurés indépendamment sur les réseaux virtuels par un utilisateur avec accès en écriture à un réseau virtuel.

Afin de sécuriser les ressources du service Azure pour un réseau virtuel, l’utilisateur doit disposer des autorisations pour « Microsoft.Network/JoinServicetoaSubnet » pour les sous-réseaux à ajouter. Cette autorisation est incluse par défaut dans les rôles d’administrateur de service fédérés et peut être modifiée en créant des rôles personnalisés.

Apprenez-en davantage sur les [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Les réseaux virtuels et les ressources du service Azure peuvent être dans des abonnements identiques ou différents. S’ils sont dans des abonnements différents, les ressources doivent être sous le même abonné Active Directory.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’instructions sur la sécurisation d’une ressource de service dans des réseaux virtuels, reportez-vous aux liens ci-dessous :

[Sécurisation de comptes de Stockage Azure dans des réseaux virtuels](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Sécurisation d’Azure SQL dans des réseaux virtuels](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
