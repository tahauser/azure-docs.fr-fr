---
title: "Connecter des réseaux virtuels classiques à des réseaux virtuels Resource Manager : PowerShell | Microsoft Docs"
description: "Découvrez comment créer une connexion VPN entre des réseaux virtuels classiques et des réseaux virtuels Resource Manager à l’aide d’une passerelle VPN et de PowerShell"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
ms.openlocfilehash: da5bddba3a1fad74b2ee08fd2f34d1b01c7345c8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Connecter des réseaux virtuels utilisant des modèles de déploiement différents à l’aide de PowerShell



Cet article vous explique comment connecter des réseaux virtuels classiques à des réseaux virtuels Resource Manager afin de permettre aux ressources situées dans les modèles de déploiement distincts de communiquer entre elles. Les étapes décrites dans cet article utilisent PowerShell, mais vous pouvez également créer cette configuration à l’aide du portail Azure en sélectionnant l’article dans cette liste.

> [!div class="op_single_selector"]
> * [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

La connexion d’un réseau virtuel classique à un réseau virtuel Resource Manager est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Vous pouvez créer une connexion entre des réseaux virtuels situés dans des abonnements différents et des régions différentes. Vous pouvez également connecter des réseaux virtuels qui disposent déjà de connexions à des réseaux locaux, à condition que la passerelle avec laquelle ils ont été configurés soit dynamique ou basée sur un itinéraire. Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez le [Forum Aux Questions sur l’interconnexion de réseaux virtuels](#faq) à la fin de cet article. 

Si vos réseaux virtuels sont situés dans la même région, vous souhaiterez peut-être plutôt utiliser VNet Peering pour les connecter. L’homologation de réseaux virtuels (ou VNet Peering) n’utilise pas de passerelle VPN. Pour plus d’informations, consultez l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md). 

## <a name="before"></a>Avant de commencer

Les étapes suivantes vous guident à travers les paramétrages nécessaires pour configurer une passerelle dynamique ou basée sur un itinéraire pour chaque réseau virtuel et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basées sur des stratégies.

### <a name="pre"></a>Configuration requise

* Les deux réseaux virtuels ont déjà été créés.
* Les plages d’adresses des réseaux virtuels ne se chevauchent pas ou ne chevauchent aucune des plages des autres connexions susceptibles d’être utilisées par les passerelles.
* Vous avez installé les dernières applets de commande PowerShell. Pour plus d’informations, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) . Veillez à installer à la fois les applets de commande de gestion des services et Resource Manager (RM). 

### <a name="exampleref"></a>Exemples de paramètres

Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article.

**Paramètres de réseau virtuel classique**

Nom du réseau virtuel = ClassicVNet  <br>
Emplacement = Ouest des États-Unis <br>
Espaces d’adressage du réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
Sous-réseau de passerelle : 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>
Type de passerelle = DynamicRouting

**Paramètres de réseau virtuel Resource Manager**

Nom du réseau virtuel = RMVNet  <br>
Groupe de ressources = RG1 <br>
Espaces d’adressage IP du réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
Sous-réseau de passerelle = 192.168.0.0/26 <br>
Emplacement = Est des États-Unis <br>
Nom d’adresse IP publique de passerelle = gwpip <br>
Passerelle de réseau local = ClassicVNetLocal <br>
Nom de passerelle de réseau virtuel = RMGateway <br>
Configuration d’adressage IP de la passerelle = gwipconfig

## <a name="createsmgw"></a>Section 1 : Configurer le réseau virtuel classique
### <a name="1-download-your-network-configuration-file"></a>1. Télécharger votre fichier de configuration réseau
1. Dans la console PowerShell avec des droits élevés, connectez-vous à votre compte Azure. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l'applet de commande télécharge vos paramètres de compte pour qu'ils soient reconnus par Azure PowerShell. Vous utilisez les applets de commande PowerShell de gestion des services pour mener à bien cette partie de la configuration.

  ```powershell
  Add-AzureAccount
  ```
2. Exportez votre fichier de configuration réseau Azure en exécutant la commande suivante. Vous pouvez modifier l’emplacement d’exportation du fichier si nécessaire.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Ouvrez le fichier .xml que vous avez téléchargé pour le modifier. Pour obtenir un exemple de fichier de configuration réseau, consultez le [schéma de configuration réseau](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Vérifier le sous-réseau de passerelle
Dans l’élément **VirtualNetworkSites**, ajoutez un sous-réseau de passerelle à votre réseau virtuel si vous n’en avez pas déjà créé un. Lorsque vous travaillez avec le fichier de configuration réseau, le sous-réseau de passerelle DOIT être nommé « GatewaySubnet ». Sinon, Azure ne peut pas le reconnaître et l’utiliser comme sous-réseau de passerelle.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemple :**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. Ajouter le site de réseau local
Le site de réseau local que vous ajoutez représente le réseau virtuel RM auquel vous souhaitez vous connecter. Ajoutez un élément **LocalNetworkSites** au fichier s’il n’en existe pas déjà un. À ce stade de la configuration, n’importe quelle adresse IP publique valide peut être utilisée pour l’élément VPNGatewayAddress, car nous n’avons pas encore créé la passerelle pour le réseau virtuel Resource Manager. Une fois la passerelle créée, nous remplacerons cette adresse IP temporaire par l’adresse IP publique qui a été affectée à la passerelle RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Associer le réseau virtuel au site de réseau local
Dans cette section, nous spécifions le site de réseau local auquel vous souhaitez connecter le réseau virtuel. Dans ce cas, il s’agit du réseau virtuel Resource Manager que vous avez référencé plus tôt. Assurez-vous que les noms correspondent. Cette étape ne vise pas à créer une passerelle. Elle spécifie le réseau local auquel la passerelle se connectera.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Enregistrer et charger le fichier
Enregistrez le fichier, puis importez-le dans Azure en exécutant la commande ci-dessous. Assurez-vous que vous modifiez le chemin d'accès selon les besoins de votre environnement.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Vous verrez un résultat similaire indiquant que l’importation a réussi.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Créer la passerelle

Avant d’exécuter cet exemple, consultez le fichier de configuration de réseau que vous avez téléchargé pour les noms exacts qu’Azure s’attend à voir. Ce fichier de configuration réseau contient les valeurs de vos réseaux virtuels classiques. Parfois, les noms de réseau virtuel classique sont modifiés dans le fichier de configuration réseau lors de la création des paramètres du réseau virtuel classique dans le portail Azure en raison de différences dans les modèles de déploiement. Par exemple, si vous avez utilisé le portail Azure pour créer un réseau virtuel nommé « Classic VNet » dans un groupe de ressources nommé « ClassicRG », le nom figurant dans le fichier de configuration du réseau est converti en « Group ClassicRG Classic VNetl ». Lorsque vous spécifiez le nom d’un réseau virtuel qui contient des espaces, entourez la valeur avec des guillemets.


L’exemple suivant montre comment créer une passerelle de routage dynamique :

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Vous pouvez vérifier l’état de la passerelle à l’aide de l’applet de commande **Get-AzureVNetGateway**.

## <a name="creatermgw"></a>Section 2 - Configurer la passerelle du réseau virtuel Resource Manager
Pour créer une passerelle VPN pour le réseau virtuel RM, suivez les instructions suivantes. Ne commencez pas cette procédure avant d’avoir récupéré l’adresse IP publique de la passerelle du réseau virtuel classique. 

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Les applets de commande suivantes vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés pour être reconnus par Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ``` 
   
  Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Spécifiez l’abonnement que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Créer une passerelle de réseau local. Dans un réseau virtuel, la passerelle de réseau local fait généralement référence à votre emplacement local. Dans ce cas, la passerelle de réseau local fait référence à votre réseau virtuel classique. Donnez à cet emplacement un nom auquel Azure pourra se référer, puis spécifiez le préfixe de l’espace d’adressage. Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers votre emplacement local. Si vous avez besoin d’ajuster ces informations ultérieurement, avant de créer votre passerelle, vous pouvez modifier les valeurs et exécuter à nouveau l’exemple.
   
   **-Name** est le nom à affecter pour faire référence à la passerelle de réseau local.<br>
   **-AddressPrefix** est l’espace d’adressage de votre réseau virtuel classique.<br>
   **-GatewayIpAddress** est l’adresse IP publique de la passerelle du réseau virtuel classique. Veillez à modifier l’exemple suivant pour refléter l’adresse IP correcte.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Demandez qu’une adresse IP publique soit allouée à la passerelle de réseau virtuel pour le réseau virtuel Resource Manager. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. L’adresse IP est allouée de façon dynamique à la passerelle de réseau virtuel. Néanmoins, cela ne signifie pas que l’adresse IP est modifiée. L’adresse IP de la passerelle de réseau virtuel change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de la passerelle.

  Dans cette étape, nous définissons également une variable qui sera utilisée à une étape ultérieure.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Vérifiez que votre réseau virtuel possède un sous-réseau de passerelle. S’il n’existe aucun sous-réseau de passerelle, ajoutez-en un. Veillez à nommer le sous-réseau de passerelle *GatewaySubnet*.
5. Récupérez le sous-réseau utilisé pour la passerelle en exécutant la commande suivante. À cette étape, nous définissons également une variable qui sera utilisée à l’étape suivante.
   
   **-Name** est le nom de votre réseau virtuel Resource Manager.<br>
   **-ResourceGroupName** est le groupe de ressources auquel le réseau virtuel est associé. Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé *GatewaySubnet* .<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Créez la configuration de l’adressage IP de la passerelle. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple suivant pour créer la configuration de votre passerelle.

  Dans cette étape, la propriété ID du sous-réseau et des objets d’adresse IP doit être transmise aux paramètres **-SubnetId** et **-PublicIpAddressId**. Vous ne pouvez pas utiliser une chaîne simple. Ces variables sont définies à l’étape de demande d’une adresse IP publique et à l’étape de récupération du sous-réseau.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Créez la passerelle de réseau virtuel Resource Manager en exécutant la commande suivante. `-VpnType` doit être défini sur *RouteBased*. La création de la passerelle peut prendre 45 minutes ou davantage.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Copiez l’adresse IP publique une fois que la passerelle VPN a été créée. Elle vous servira lors de la configuration des paramètres de réseau local pour votre réseau virtuel classique. Vous pouvez utiliser l’applet de commande suivante pour récupérer l’adresse IP publique. L’adresse IP publique est répertoriée dans la réponse en tant que *IpAddress*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>Section 3 - Modifier les paramètres de site local du réseau virtuel classique

Dans cette section, vous travaillez avec le réseau virtuel classique. Vous remplacez l’adresse IP avec espace réservé que vous avez utilisée lorsque vous avez spécifié les paramètres du site local qui vous permettront de vous connecter à la passerelle de réseau virtuel Resource Manager. 

1. Exportez le fichier de configuration réseau.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. À l’aide d’un éditeur de texte, modifiez la valeur de l’élément VPNGatewayAddress. Remplacez l’adresse IP avec espace réservé par l’adresse IP publique de la passerelle Resource Manager, puis enregistrez les modifications.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importez le fichier de configuration réseau modifié dans Azure.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>Section 4 - Créer une connexion entre les passerelles
La création d’une connexion entre les passerelles nécessite PowerShell. Vous devrez peut-être ajouter votre compte Azure pour utiliser la version classique des applets de commande PowerShell. Pour ce faire, utilisez **Add-AzureAccount**.

1. Dans la console PowerShell, définissez votre clé partagée. Avant d’exécuter les applets de commande, recherchez dans le fichier de configuration réseau que vous avez téléchargé les noms exacts qu’Azure s’attend à voir. Lorsque vous spécifiez le nom d’un réseau virtuel qui contient des espaces, encadrez la valeur avec des guillemets.<br><br>Dans cet exemple, **-VNetName** est le nom du réseau virtuel classique et **-LocalNetworkSiteName** est le nom que vous avez spécifié pour le site du réseau local. **-SharedKey** est une valeur que vous pouvez générer et spécifier. Dans l’exemple, nous avons utilisé « abc123 », mais vous pouvez générer et utiliser quelque chose de plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée à l’étape suivante lors de la création de votre connexion. La réponse doit afficher **État : Réussi**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. Créez la connexion VPN en exécutant les commandes suivantes :
   
  Définissez les variables.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Créez la connexion. Notez que le type de connexion **-ConnectionType** est IPsec et pas Vnet2Vnet.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Section 5 - Vérifier vos connexions

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Pour vérifier la connexion de votre réseau virtuel classique à votre réseau virtuel Resource Manager

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Portail Azure

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Pour vérifier la connexion de votre réseau virtuel Resource Manager à votre réseau virtuel classique

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Portail Azure

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

