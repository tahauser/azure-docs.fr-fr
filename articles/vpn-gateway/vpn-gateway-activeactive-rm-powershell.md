---
title: "Configurer des connexions VPN S2S en mode actif/actif pour des passerelles VPN : Azure Resource Manager : PowerShell | Microsoft Docs"
description: "Cet article vous guide dans la configuration de connexions en mode actif/actif avec des passerelles VPN Azure à l’aide d’Azure Resource Manager et de PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: yushwang
ms.openlocfilehash: 41cca764335f21bed60fe968288bc8b8274f3215
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Configurer des connexions VPN S2S en mode actif/actif avec des passerelles VPN Azure

Cet article vous guide dans les étapes de création de connexions intersites en mode actif/actif, et de connexions de réseau virtuel à réseau virtuel à l’aide du modèle de déploiement Resource Manager et de PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>À propos des connexions intersites hautement disponibles
Pour obtenir une haute disponibilité des connexions intersites et de réseau virtuel à réseau virtuel, vous devez déployer plusieurs passerelles VPN, et établir plusieurs connexions parallèles entre vos réseaux et Azure. Pour une vue d’ensemble des options de connectivité et de topologie, voir [Configuration haute disponibilité pour la connectivité entre les réseaux locaux et la connectivité entre deux réseaux virtuels](vpn-gateway-highlyavailable.md).

Cet article fournit des instructions de configuration d’une connexion VPN intersite en mode actif/actif, et d’une connexion en mode actif/actif entre deux réseaux virtuels.

* [Partie 1 : créer et configurer votre passerelle VPN Azure en mode actif/actif](#aagateway)
* [Partie 2 : établir des connexions intersites en mode actif/actif](#aacrossprem)
* [Partie 3 : établir des connexions de réseau virtuel à réseau virtuel en mode actif/actif](#aav2v)

Si vous avez déjà une passerelle VPN, vous pouvez :
* [mettre à jour une passerelle VPN existante du mode actif/passif au mode actif-actif, ou vice versa](#aaupdate).

Vous pouvez combiner ces instructions afin de créer une topologie de réseau plus complexe et hautement disponible correspondant à vos besoins.

> [!IMPORTANT]
> Notez que le mode actif/actif utilise uniquement les références SKU suivantes : 
  * VpnGw1, VpnGw2, VpnGw3
  * HigPerformance (pour les anciennes références héritées)
> 
> 

## <a name ="aagateway"></a>Partie 1 : créer et configurer des passerelles VPN en mode actif/actif
Les étapes suivantes permettent de configurer votre passerelle VPN Azure en modes actif/actif. Les principales différences entre les passerelles en modes actif/actif et actif/passif sont les suivantes :

* Vous devez créer deux configurations IP de passerelle avec deux adresses IP publiques.
* Vous devez définir l’indicateur EnableActiveActiveFeature.
* La référence (SKU) de la passerelle doit être VpnGw1, VpnGw2, VpnGw3 ou HigPerformance (référence héritée).

Les autres propriétés sont les mêmes que celles des passerelles en mode actif/actif. 

### <a name="before-you-begin"></a>Avant de commencer
* Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous aurez besoin d’installer les applets de commande PowerShell Azure Resource Manager. Consultez [Présentation d’Azure PowerShell](/powershell/azure/overview) pour plus d’informations sur l’installation des applets de commande PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Étape 1 – Créer et configurer le réseau virtuel VNet1
#### <a name="1-declare-your-variables"></a>1. Déclarer vos variables
Dans cet exercice, nous allons commencer par déclarer les variables. L’exemple suivant déclare les variables avec les valeurs de cet exercice. Veillez à les remplacer par vos valeurs lors de la configuration dans un contexte de production. Vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez-les dans la console PowerShell.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Se connecter à votre abonnement et créer un groupe de ressources
Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Créer TestVNet1
L’exemple ci-dessous crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux nommés GatewaySubnet, FrontEnd et Backend. Lorsque vous remplacez les valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Étape 2 : créer la passerelle VPN pour TestVNet1 avec le mode actif/actif
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Créer les adresses IP publiques et les configurations IP de passerelle
Demandez l’allocation de deux adresses IP publiques à la passerelle que vous allez créer pour votre réseau virtuel. Vous allez également définir les configurations requises (IP et sous-réseau).

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Créer la passerelle VPN avec une configuration en mode actif/actif
Créez la passerelle de réseau virtuel pour TestVNet1. Notez qu’il existe deux entrées GatewayIpConfig, et que l’indicateur EnableActiveActiveFeature est défini. La création d’une passerelle peut prendre un certain temps (45 minutes ou plus).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Obtenir les adresses IP publiques de passerelle et l’adresse IP d’homologue BGP
Une fois la passerelle créée, vous devez obtenir l’adresse IP de l’homologue BGP sur la passerelle VPN Azure. Cette adresse est nécessaire pour configurer la passerelle VPN Azure comme un homologue BGP pour vos périphériques VPN locaux.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Utilisez les applets de commande suivantes pour afficher les deux adresses IP publiques allouées à votre passerelle VPN, ainsi que leurs adresses IP d’homologue BGP correspondantes pour chaque instance de passerelle :

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

L’ordre des adresses IP publiques pour les instances de passerelle et celui des adresses d’homologation BGP correspondantes sont identiques. Dans cet exemple, la machine virtuelle de passerelle dont l’adresse IP publique est 40.112.190.5 utilise 10.12.255.4 en tant qu’adresse d’homologation BGP, et la passerelle dont l’adresse est 138.91.156.129 utilise 10.12.255.5. Ces informations sont nécessaires lorsque vous configurez vos périphériques VPN locaux se connectant à la passerelle en mode actif/actif. La passerelle est présentée dans le diagramme ci-dessous avec toutes les adresses :

![active-active-gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Une fois la passerelle créée, vous pouvez l’utiliser pour établir une connexion intersite ou de réseau virtuel à réseau virtuel en mode actif/actif. Les sections suivantes détaillent les étapes à effectuer pour terminer l’exercice.

## <a name ="aacrossprem"></a>Partie 2 : établir une connexion intersite en mode actif/actif
Pour établir une connexion intersite, vous devez créer une passerelle de réseau local pour représenter votre périphérique VPN local, et une connexion entre la passerelle VPN Azure et la passerelle du réseau local. Dans cet exemple, la passerelle VPN Azure est en mode actif/actif. Par conséquent, même s’il n’existe qu’un seul périphérique VPN local (passerelle de réseau local) et une seule ressource de connexion, les deux instances de passerelle VPN Azure établissent des tunnels VPN S2S avec l’appareil local.

Avant de poursuivre, vérifiez que vous avez terminé la [Partie 1](#aagateway) de cet exercice.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Étape 1 - Créer et configurer la passerelle de réseau local
#### <a name="1-declare-your-variables"></a>1. Déclarer vos variables
Cet exercice continue à générer la configuration représentée dans le diagramme. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Quelques points à noter concernant les paramètres de la passerelle de réseau local :

* La passerelle de réseau local peut se trouver dans les mêmes emplacement et groupe de ressources que la passerelle VPN ou dans un emplacement et un groupe de ressources différents. Cet exemple les montre dans différents groupes de ressources, mais dans le même emplacement Azure.
* S’il n’existe qu’un seul périphérique VPN local, comme ci-dessus, la connexion en mode actif/actif peut fonctionner avec ou sans le protocole BGP. Cet exemple utilise le protocole BGP pour la connexion intersite.
* Si le protocole BGP est activé, le préfixe à déclarer pour la passerelle de réseau local est l’adresse d’hôte de l’adresse IP de votre homologue BGP sur votre périphérique VPN. Dans ce cas, il s’agit d’un préfixe /32 de « 10.52.255.253/32 ».
* À titre de rappel, vous devez utiliser différents ASN BGP entre vos réseaux locaux et le réseau virtuel Azure. S’ils sont identiques, vous devez modifier l’ASN de votre réseau si votre périphérique VPN local utilise déjà cet ASN pour s’homologuer avec d’autres voisins BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Créer la passerelle de réseau local pour le site 5
Avant de continuer, assurez-vous que vous êtes toujours connecté à l’abonnement 1. Créez le groupe de ressources s’il n’est pas encore créé.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Étape 2 - Connecter la passerelle de réseau virtuel et la passerelle de réseau local
#### <a name="1-get-the-two-gateways"></a>1. Obtenir les deux passerelles

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Créer la connexion entre TestVNet1 et Site5
À cette étape, vous créez la connexion de TestVNet1 à Site5_1, avec « EnableBGP » défini sur $True.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Paramètres VPN et BGP pour votre périphérique VPN local
L’exemple ci-dessous répertorie les paramètres que vous devez saisir dans la section de configuration de BGP sur votre périphérique VPN local pour cet exercice :

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

La connexion doit s’établir après quelques minutes, et la session d’homologation BGP débute une fois la connexion IPsec établie. Cet exemple a jusqu’à présent configuré un seul périphérique VPN local, produisant le diagramme ci-dessous :

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Étape 3 : connecter deux appareils VPN locaux à la passerelle VPN en mode actif/actif
Si vous avez deux appareils VPN sur le même réseau local, vous pouvez obtenir une redondance double en connectant la passerelle VPN Azure au deuxième périphérique VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Créer la deuxième passerelle de réseau local pour Site5
L’adresse IP de la passerelle, le préfixe d’adresse et l’adresse d’homologation BGP pour la seconde passerelle de réseau local ne doivent pas chevaucher la passerelle de réseau local précédente pour le même réseau local.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Connecter la passerelle de réseau virtuel et la deuxième passerelle de réseau local
Créer la connexion de TestVNet1 à Site5_2 avec « EnableBGP » défini sur $True

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Paramètres VPN et BGP pour votre deuxième périphérique VPN local
De même, ci-dessous figurent les paramètres vous allez entrer pour le deuxième périphérique VPN :

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Une fois la connexion (les tunnels) établie, vous disposez d’appareils VPN à double redondance et de tunnels reliant votre réseau local et Azure :

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Partie 3 : établir une connexion de réseau virtuel à réseau virtuel en mode actif/actif
Cette section décrit comment créer une connexion de réseau virtuel à réseau virtuel en mode actif/actif avec le protocole BGP. 

Les instructions ci-dessous sont la suite des étapes précédentes répertoriées plus haut. Pour créer et configurer TestVNet1 et la passerelle VPN avec le protocole BGP, vous devez achever la [Partie 1](#aagateway) . 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Étape 1 - Créer TestVNet2 et la passerelle VPN
Il est important de s’assurer que l’espace d’adresse IP du nouveau réseau virtuel, TestVNet2, n’empiète sur aucune de vos plages de réseau virtuel.

Dans cet exemple, les réseaux virtuels appartiennent au même abonnement. Vous pouvez configurer des connexions de réseau virtuel à réseau virtuel entre différents abonnements. Pour en savoir plus, voir [Configurer une connexion de réseau virtuel à réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md). Veillez à ajouter l’argument « -EnableBgp $True » lors de la création de connexions pour activer BGP.

#### <a name="1-declare-your-variables"></a>1. Déclarer vos variables
Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Créer TestVNet2 dans le nouveau groupe de ressources

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Créer la passerelle VPN en mode actif/actif pour TestVNet2
Demandez l’allocation de deux adresses IP publiques à la passerelle que vous allez créer pour votre réseau virtuel. Vous allez également définir les configurations requises (IP et sous-réseau).

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Créez la passerelle VPN avec le numéro AS et l’indicateur « EnableActiveActiveFeature ». Notez que vous devez substituer la valeur par défaut de l’ASN sur vos passerelles VPN Azure. Les ASN des réseaux virtuels connectés doivent être différents pour activer BGP et le routage de transit.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Étape 2 - Connecter les passerelles TestVNet1 et TestVNet2
Dans cet exemple, les deux passerelles sont dans le même abonnement. Vous pouvez effectuer cette opération dans la même session PowerShell.

#### <a name="1-get-both-gateways"></a>1. Accéder aux deux passerelles
Veillez à ouvrir une session et à vous connecter à Abonnement 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Créer les deux connexions
Dans cette étape, vous allez créer la connexion de TestVNet1 à TestVNet2 et la connexion de TestVNet2 à TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Veillez à activer BGP pour les deux connexions.
> 
> 

Une fois ces étapes terminées, la connexion s’établit en quelques minutes, et la session d’homologation BGP est ouverte dès la connexion de réseau virtuel à réseau virtuel établie avec une redondance double :

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Mise à jour d’une passerelle VPN existante

La section vous aide à configurer une passerelle VPN Azure existante pour passer du mode actif/passif au mode actif/actif, ou inversement.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Transformer une passerelle en mode actif/passif en passerelle en mode actif/actif

L’exemple suivant convertit une passerelle en mode actif/passif en passerelle en mode actif/actif. Lorsque vous transformez une passerelle de type actif/passif en passerelle de type actif/actif, vous créez une autre adresse IP publique, puis ajoutez une deuxième configuration IP de la passerelle.

#### <a name="1-declare-your-variables"></a>1. Déclarer vos variables

Remplacer les paramètres suivants utilisés pour les exemples par les paramètres dont vous avez besoin pour votre propre configuration, puis déclarer ces variables.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Après la déclaration des variables, vous pouvez copier et coller cet exemple sur votre console PowerShell.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Créer l’adresse IP publique, puis ajouter la deuxième configuration IP de passerelle

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Activer le mode actif/actif et mettre à jour la passerelle

À cette étape, vous activez le mode actif/actif et mettez à jour la passerelle. Dans l’exemple, la passerelle VPN utilise actuellement une référence SKU standard héritée. Toutefois, le mode actif/actif ne prend pas en charge la référence SKU s+tandard. Pour redimensionner la référence SKU héritée de manière à ce qu’elle soit prise en charge (dans ce cas, HighPerformance), il suffit de spécifier la référence SKU héritée prise en charge que vous souhaitez utiliser.

* Vous ne pouvez pas transformer une référence SKU héritée en nouvelle référence SKU à cette étape. Vous pouvez uniquement redimensionner une référence SKU héritée pour obtenir une autre référence héritée prise en charge. Par exemple, vous ne pouvez pas transformer la référence SKU de standard à VpnGw1 (même si VpnGw1 est pris en charge pour le mode actif/actif), car standard est une référence SKU héritée, alors que VpnGw1 est une référence SKU actuelle. Pour plus d’informations sur le redimensionnement et la migration de références SKU, voir [Références SKU de passerelle](vpn-gateway-about-vpngateways.md#gwsku).

* Si vous souhaitez redimensionner une référence SKU actuelle, par exemple de VpnGw1 à VpnGw3, vous pouvez le faire à cette étape, car les références SKU se trouvent dans la même famille de références SKU. Pour ce faire, vous utilisez la valeur : ```-GatewaySku VpnGw3```

Lorsque vous utilisez cela dans votre environnement, si vous n’avez pas besoin de redimensionner la passerelle, vous n’aurez pas à spécifier le -GatewaySku. Notez qu’à cette étape, vous devez définir l’objet passerelle dans PowerShell pour déclencher la mise à jour réelle. Cette mise à jour peut prendre 30 à 45 minutes, même si vous ne redimensionnez pas votre passerelle.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Transformer une passerelle en mode actif/actif en passerelle en mode actif/passif
#### <a name="1-declare-your-variables"></a>1. Déclarer vos variables

Remplacer les paramètres suivants utilisés pour les exemples par les paramètres dont vous avez besoin pour votre propre configuration, puis déclarer ces variables.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Après la déclaration des variables, accédez au nom de la configuration IP que vous souhaitez supprimer.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Supprimer la configuration IP de passerelle et désactiver le mode actif/actif

Utilisez cet exemple pour supprimer la configuration IP de la passerelle et désactiver le mode actif/actif. Notez que vous devez définir l’objet passerelle dans PowerShell pour déclencher la mise à jour réelle.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Cette mise à jour peut prendre de 30 à 45 minutes.

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour connaître les différentes étapes.