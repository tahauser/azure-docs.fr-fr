---
title: "Azure Active Directory Domain Services : Dépannage de la configuration du groupe de sécurité réseau | Microsoft Docs"
description: "Dépannage de la configuration du groupe de sécurité réseau pour les services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Services de domaine Azure AD : Résolution des problèmes de configuration de groupe de sécurité réseau



## <a name="aadds104-network-error"></a>AADDS104 : Erreur réseau

**Message d’alerte :** *Microsoft ne peut pas atteindre les contrôleurs de domaine pour ce domaine géré. Cela peut se produire si un groupe de sécurité réseau (NSG) configuré sur votre réseau virtuel bloque l’accès à un domaine géré. Une autre raison possible est s’il existe un itinéraire défini par l’utilisateur qui bloque le trafic entrant à partir d’Internet.*

La cause la plus courante d’erreurs réseau pour les services de domaine Azure AD peut être attribuée à des configurations de groupe de sécurité réseau incorrectes. Pour vous assurer que Microsoft peut travailler sur et maintenir votre domaine géré, vous devez utiliser un groupe de sécurité réseau (NSG) pour autoriser l’accès à des [ports spécifiques](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) au sein de votre sous-réseau. Si ces ports sont bloqués, Microsoft ne peut pas accéder aux ressources dont il a besoin, ce qui peut nuire à votre service. Lors de la création de votre groupe de sécurité réseau, gardez ces ports ouverts pour éviter les interruptions de service.

## <a name="sample-nsg"></a>Exemple de groupe de sécurité réseau
Le tableau suivant illustre un exemple de groupe de sécurité réseau qui sécuriserait votre domaine géré tout en permettant à Microsoft de surveiller, gérer et mettre à jour ses informations.

![exemple de groupe de sécurité réseau](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Les services de domaine Azure AD nécessitent un accès sortant non restreint. Nous vous recommandons de ne pas créer une règle sortante supplémentaire pour vos groupes de sécurité réseau.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Ajout d’une règle à un groupe de sécurité réseau à l’aide du portail Azure
Si vous ne souhaitez pas utiliser PowerShell, vous pouvez ajouter manuellement des règles uniques pour les groupes de sécurité réseau à l’aide du portail Azure. Suivez ces étapes pour créer des règles dans votre groupe de sécurité réseau.

1. Dans le Portail Azure, accédez à la page [Groupes de sécurité réseau](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)
2. Choisissez le groupe de sécurité réseau associé à votre domaine à partir de la table.
3. Sous Paramètres dans le volet de navigation gauche, cliquez sur **Règles de sécurité de trafic entrant** ou **Règles de sécurité sortantes**.
4. Créez la règle en cliquant sur **Ajouter** et en remplissant les informations. Cliquez sur **OK**.
5. Vérifiez que votre règle a été créée en la recherchant dans la table de règles.


## <a name="create-a-default-nsg-using-powershell"></a>Créer un groupe de sécurité réseau par défaut à l’aide de PowerShell

Vous pouvez utiliser les étapes précédentes pour créer un nouveau groupe de sécurité réseau à l’aide de PowerShell qui conserve tous les ports nécessaires ouverts pour exécuter les services de domaine Azure AD Services avec refus des accès indésirables.


Cette solution nécessite l’installation et l’exécution [d’Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Connectez-vous à votre annuaire Azure AD.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Connectez-vous à votre abonnement Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Créez un groupe de sécurité réseau avec trois règles. Le script suivant définit trois règles pour le groupe de sécurité réseau qui autorisent l’accès aux ports nécessaires à l’exécution des services de domaine Azure AD. Ensuite, le script crée un nouveau groupe de sécurité réseau qui contient ces règles. Vous avez la possibilité d’ajouter des règles supplémentaires selon vos besoins en utilisant le même format.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Enfin, le script associe le groupe de sécurité réseau au réseau virtuel et au sous-réseau de votre choix.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Script complet

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Ce groupe de sécurité réseau par défaut ne bloque pas l’accès au port utilisé pour le protocole LDAP sécurisé. Pour savoir comment créer une règle pour ce port, consultez [cet article](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
