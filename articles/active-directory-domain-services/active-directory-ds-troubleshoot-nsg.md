---
title: 'Azure Active Directory Domain Services : Dépannage de la configuration du groupe de sécurité réseau | Microsoft Docs'
description: Dépannage de la configuration du groupe de sécurité réseau pour les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: ergreenl
ms.openlocfilehash: ca3292f1b89fc461950a47116126b6f5338fb381
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Résoudre les problèmes de configuration de mise en réseau non valide pour votre domaine géré
Cet article vous aide à dépanner et résoudre les erreurs liées à la configuration réseau qui produisent le message d’alerte suivant :

## <a name="alert-aadds104-network-error"></a>Alerte AADDS104 : Erreur réseau
**Message d’alerte :** *Microsoft ne peut pas atteindre les contrôleurs de domaine pour ce domaine géré. Cela peut se produire si un groupe de sécurité réseau (NSG) configuré sur votre réseau virtuel bloque l’accès à un domaine géré. Une autre raison possible est s’il existe un itinéraire défini par l’utilisateur qui bloque le trafic entrant à partir d’Internet.*

Les configurations de groupe de sécurité réseau non valides sont la cause la plus courante d’erreurs réseau pour Azure AD Domain Services. Le groupe de sécurité réseau (NSG) configuré pour votre réseau virtuel doit autoriser l’accès à des [ports spécifiques](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Si ces ports sont bloqués, Microsoft ne peut pas analyser ou mettre à jour votre domaine géré. En outre, la synchronisation entre votre répertoire Azure AD et votre domaine géré est interrompue. Lors de la création de votre groupe de sécurité réseau, gardez ces ports ouverts pour éviter les interruptions de service.


## <a name="sample-nsg"></a>Exemple de groupe de sécurité réseau
Le tableau suivant illustre un exemple de groupe de sécurité réseau qui sécuriserait votre domaine géré tout en permettant à Microsoft de surveiller, gérer et mettre à jour ses informations.

![exemple de groupe de sécurité réseau](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Les services de domaine Azure AD nécessitent un accès sortant non restreint depuis le réseau virtuel. Nous recommandons de ne pas créer de règle de groupe de sécurité réseau supplémentaire pouvant restreindre l’accès sortant pour le réseau virtuel.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Ajout d’une règle à un groupe de sécurité réseau à l’aide du portail Azure
Si vous ne souhaitez pas utiliser PowerShell, vous pouvez ajouter manuellement des règles uniques pour les groupes de sécurité réseau à l’aide du portail Azure. Pour créer des règles dans votre groupe de sécurité réseau, procédez comme suit :

1. Dans le Portail Azure, accédez à la page [Groupes de sécurité réseau](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)
2. À partir de la table, choisissez le groupe de sécurité réseau associé au sous-réseau dans lequel votre domaine géré est activé.
3. Sous **Paramètres** dans le panneau de gauche, cliquez sur **Règles de sécurité de trafic entrant** ou **Règles de sécurité sortantes**.
4. Créez la règle en cliquant sur **Ajouter** et en remplissant les informations. Cliquez sur **OK**.
5. Vérifiez que votre règle a été créée en la recherchant dans la table de règles.


## <a name="create-an-nsg-for-azure-ad-domain-services-using-powershell"></a>Créer un groupe de sécurité réseau pour les services de domaine Azure AD à l’aide de PowerShell
Ce groupe de sécurité réseau est configuré pour autoriser le trafic entrant vers les ports requis par les Azure AD Domain Services, tout en refusant les autres accès entrants indésirables.

**Condition préalable : Installer et configurer Azure PowerShell** Suivez les instructions pour [Installer le module Azure PowerShell et vous connecter à votre abonnement Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

>[!TIP]
> Nous vous recommandons d’utiliser la dernière version du module Azure PowerShell. Si vous avez déjà une version antérieure de ce module Azure PowerShell, mettez le module à jour vers la dernière version.
>

Procédez comme suit pour créer un groupe de sécurité réseau avec PowerShell.
1. Connectez-vous à votre abonnement Azure.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

2. Créez un groupe de sécurité réseau avec trois règles. Le script suivant définit trois règles pour le groupe de sécurité réseau qui autorisent l’accès aux ports nécessaires à l’exécution des services de domaine Azure AD. Ensuite, le script crée un nouveau groupe de sécurité réseau qui contient ces règles. Pour ajouter des règles supplémentaires qui autorisent le reste du trafic entrant, si cela est requis par les charges de travail déployées dans le réseau virtuel, utilisez le même format.

  ```PowerShell
  # Allow inbound HTTPS traffic to enable synchronization to your managed domain.
  $SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443

  # Allow management of your domain over port 5986 (PowerShell Remoting)
  $PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986

  #The following two rules are optional and needed only in certain situations.

  # Allow management of your domain over port 3389 (remote desktop).
  $RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389

  # Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
  $SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 636

  # Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule
  ```

3. Enfin, associez le groupe de sécurité réseau au réseau virtuel et au sous-réseau de votre choix.

  ```PowerShell
  # Find vnet and subnet
  $Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
  $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

  # Set the nsg to the subnet and save the changes
  $Subnet.NetworkSecurityGroup = $Nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
  ```

## <a name="full-script-to-create-and-apply-an-nsg-for-azure-ad-domain-services"></a>Script complet pour créer et appliquer un groupe de sécurité réseau pour Azure AD Domain Services
>[!TIP]
> Nous vous recommandons d’utiliser la dernière version du module Azure PowerShell. Si vous avez déjà une version antérieure de ce module Azure PowerShell, mettez le module à jour vers la dernière version.
>

```PowerShell
# Change the following values to match your deployment
$ResourceGroup = "ResourceGroupName"
$Location = "westus"
$VnetName = "exampleVnet"
$SubnetName = "exampleSubnet"

# Log in to your Azure subscription.
Login-AzureRmAccount

# Allow inbound HTTPS traffic to enable synchronization to your managed domain.
$SyncRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowSyncWithAzureAD -Description "Allow synchronization with Azure AD" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

# Allow management of your domain over port 5986 (PowerShell Remoting)
$PSRemotingRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowPSRemoting -Description "Allow management of domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 5986

# The following two rules are optional and needed only in certain situations.

# Allow management of your domain over port 3389 (remote desktop).
$RemoteDesktopRule = New-AzureRmNetworkSecurityRuleConfig -Name AllowRD -Description "Allow management of domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix 207.68.190.32/27, 13.106.78.32/27, 10.254.32.0/20, 10.97.136.0/22, 13.106.174.32/27, 13.106.4.96/27 -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

# Secure LDAP rule, it is recommended to change the source address prefix to include only the IP addresses
$SecureLDAPRule = New-AzureRmNetworkSecurityRuleConfig -Name SecureLDAP -Description "Allow access through secure LDAP port" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 104 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 636

# Create the NSG with the rules above (if you need the remote desktop rule and secure ldap rule, add it below)
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "AADDomainServices-NSG" -SecurityRules $SyncRule, $PSRemotingRule

# Find vnet and subnet
$Vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroup -Name $VnetName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $Vnet -Name $SubnetName

# Set the nsg to the subnet and save the changes
$Subnet.NetworkSecurityGroup = $Nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $Vnet
```


## <a name="need-help"></a>Vous avez besoin d’aide ?
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
