---
title: "Connecter un ordinateur à un réseau virtuel Azure à l’aide d’une connexion point à site et de l’authentification par certificat Azure native : PowerShell | Microsoft Docs"
description: "Connectez des clients Windows et Mac OS X en toute sécurité à un réseau virtuel Azure à l’aide d’une connexion P2S et de certificats auto-signés ou délivrés par une autorité de certification. Cet article se base sur PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: ab171a97855090302148651e8e9c3d0d8b91a33a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Configurer une connexion point à site à un réseau virtuel à l’aide de l’authentification par certificat Azure native : PowerShell

Cet article vous permet de connecter en toute sécurité des clients individuels qui exécutent Windows ou Mac OS X à un réseau virtuel Azure. Les connexions VPN point à site sont utiles lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un emplacement distant, par exemple lorsque vous travaillez à distance depuis votre domicile ou en conférence. La connexion P2S est une solution alternative au VPN de site à site lorsque seul un nombre restreint de clients doivent se connecter à un réseau virtuel. Les connexions de point à site ne nécessitent pas de périphérique VPN ou d’adresse IP publique. La connexion P2S crée la connexion VPN via SSTP (Secure Socket Tunneling Protocol) ou IKEv2. Pour plus d’informations sur le VPN de point à site, consultez l’article [À propos du VPN de point à site](point-to-site-about.md).

![Diagramme de connexion d’un ordinateur à un réseau virtuel Azure à l’aide d’une passerelle point à site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)


## <a name="architecture"></a>Architecture

Les connexions d’authentification avec certificat Azure natif de point à site utilisent les éléments suivants, que vous pouvez configurer dans cet exercice :

* Une passerelle VPN RouteBased.
* La clé publique (fichier .cer) d’un certificat racine, chargée sur Azure. Une fois le certificat chargé, il est considéré comme un certificat approuvé et est utilisé pour l’authentification.
* Un certificat client généré à partir du certificat racine. Le certificat client installé sur chaque ordinateur client qui se connecte au réseau virtuel. Ce certificat est utilisé pour l’authentification du client.
* Une configuration du client VPN. Les fichiers de configuration du client VPN contiennent les informations nécessaires permettant au client de se connecter sur le réseau virtuel. Les fichiers configurent le client VPN existant qui est natif au système d’exploitation. Chaque client qui se connecte doit être configuré à l’aide des paramètres dans les fichiers de configuration.

## <a name="before-you-begin"></a>Avant de commencer

* Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).
* Installez la dernière version des cmdlets PowerShell de Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Ceci est important, car les versions antérieures des cmdlets ne contiennent pas les valeurs actuelles dont vous avez besoin pour cet exercice.

### <a name="example"></a>Exemples de valeurs

Vous pouvez utiliser ces exemples de valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article. Les variables sont définies dans la section [1](#declare) de l’article. Vous pouvez suivre les étapes proposées en utilisant les valeurs sans les modifier ou modifier les valeurs pour les adapter à votre environnement.

* **Nom : VNet1**
* **Espace d’adressage :192.168.0.0/16** et **10.254.0.0/16**<br>Cet exemple utilise différents espaces d’adressage afin de démontrer que cette configuration fonctionne avec plusieurs espaces d’adressage. Toutefois, plusieurs espaces d’adressage ne sont pas nécessaires pour cette configuration.
* **Nom du sous-réseau : FrontEnd**
  * **Plage d’adresses de sous-réseau : 192.168.1.0/24**
* **Nom du sous-réseau : BackEnd**
  * **Plage d’adresses de sous-réseau : 10.254.1.0/24**
* **Nom du sous-réseau : GatewaySubnet**<br>Le nom du sous-réseau *GatewaySubnet* est obligatoire pour que la passerelle VPN fonctionne.
  * **Plage d’adresses de GatewaySubnet : 192.168.200.0/24** 
* **Pool d’adresses des clients VPN : 172.16.201.0/24**<br>Les clients VPN qui se connectent au réseau virtuel à l’aide de cette connexion point à site reçoivent une adresse IP de ce pool d’adresses des clients VPN.
* **Abonnement :** vérifiez que vous utilisez l’abonnement approprié si vous en possédez plusieurs.
* **Groupe de ressources : TestRG**
* **Emplacement : États-Unis de l’Est**
* **Serveur DNS : l’adresse IP** du serveur DNS que vous souhaitez utiliser pour la résolution de noms. (facultatif)
* **Nom de passerelle : Vnet1GW**
* **Nom d’adresse IP publique : VNet1GWPIP**
* **Type de VPN : RouteBased** 

## <a name="declare"></a>1. Connexion et définition des variables

Dans cette section, vous vous connectez et déclarez les valeurs utilisées pour cette configuration. Les valeurs déclarées sont utilisées dans les exemples de script. Modifiez les valeurs pour les adapter à votre propre environnement. Vous pouvez également utiliser les valeurs déclarées et suivre les étapes pour vous entraîner.

1. Ouvrez la console PowerShell avec des privilèges élevés, puis connectez-vous à votre compte Azure. Cette applet de commande vous invite à entrer vos informations d’identification de connexion. Une fois que vous êtes connecté, l'applet de commande télécharge vos paramètres de compte pour qu'ils soient reconnus par Azure PowerShell.

  ```powershell
  Login-AzureRmAccount
  ```
2. Obtenez la liste de vos abonnements Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Spécifiez l’abonnement à utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. Déclarez les variables que vous souhaitez utiliser. Utilisez l’exemple ci-dessous en utilisant vos propres valeurs si nécessaire.

  ```powershell
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>2. Configurer un réseau virtuel

1. Créez un groupe de ressources.

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. Créez les configurations de sous-réseau du réseau virtuel en les nommant *FrontEnd*, *BackEnd* et *GatewaySubnet*. Ces préfixes doivent faire partie de l’espace d’adressage du réseau virtuel que vous avez déclaré.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. Création du réseau virtuel.

  Dans cet exemple, le paramètre du serveur -DnsServer est facultatif. La définition d’une valeur n’entraîne pas la création de serveur DNS. Le serveur DNS dont vous spécifiez l’adresse IP doit pouvoir résoudre les noms des ressources auxquelles vous vous connectez depuis votre réseau virtuel. Cet exemple utilise une adresse IP privée, mais il ne s’agit probablement pas de l’adresse IP de votre serveur DNS. Veillez à utiliser vos propres valeurs. La valeur que vous spécifiez est utilisée par les ressources que vous déployez sur le réseau virtuel, et non par la connexion P2S ou le client VPN.

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Spécifiez les variables pour le réseau virtuel que vous avez créé.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. Une passerelle VPN doit avoir une adresse IP publique. Vous commencez par demander la ressource d’adresse IP, puis vous y faites référence lors de la création de votre passerelle de réseau virtuel. L’adresse IP est affectée dynamiquement à la ressource lors de la création de la passerelle VPN. Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques *dynamiques*. Vous ne pouvez pas demander d’affectation d’adresse IP publique statique. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

  Demandez une adresse IP publique attribuée dynamiquement.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```

## <a name="creategateway"></a>3. Créer la passerelle VPN

Configurez et créez la passerelle de réseau virtuel pour votre réseau virtuel.

* Le paramètre -GatewayType doit être défini sur la valeur **Vpn**, tandis que le paramètre -VpnType doit être défini sur la valeur **RouteBased**.
* Le paramètre -VpnClientProtocol est utilisé pour spécifier les types de tunnels que vous souhaitez activer. Les deux options de tunnels sont **SSTP** et **IKEv2**. Vous pouvez choisir d’en activer un des deux, ou les deux. Si vous souhaitez activer les deux, vous devez spécifier les deux noms séparés par une virgule. Le client strongSwan sur Android et Linux et le client VPN IKEv2 natif sur iOS et OSX n’utiliseront que le tunnel IKEv2 pour se connecter. Les clients Windows essaient IKEv2 en premier lieu. En cas d’échec de la connexion, ils utilisent SSTP.
* L’achèvement d’une passerelle VPN peut prendre jusqu’à 45 minutes en fonction de la [référence de passerelle](vpn-gateway-about-vpn-gateway-settings.md) que vous sélectionnez. Cet exemple utilise IKEv2.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="addresspool"></a>4. Ajouter le pool d’adresses des clients VPN

Après la création par la passerelle VPN, vous pouvez ajouter le pool d’adresses des clients VPN. Il s’agit de la plage à partir de laquelle les clients VPN reçoivent une adresse IP lorsqu’ils se connectent. Utilisez une plage d’adresses IP privées qui ne chevauche ni l’emplacement local à partir duquel vous vous connectez ni le réseau virtuel auquel vous souhaitez vous connecter. Dans cet exemple, le pool d’adresses de clients VPN est déclaré comme une [variable](#declare) à l’étape 1.

```powershell
$Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="Certificates"></a>5. Générer des certificats

Les certificats sont utilisés par Azure pour authentifier les clients VPN pour les VPN point à site. Vous chargez les informations de la clé publique du certificat racine vers Azure. La clé publique est alors considérée comme « approuvée ». Les certificats clients doivent être générés à partir du certificat racine approuvé, puis installés sur chaque ordinateur client dans le magasin de certificats Utilisateur actuel/Personnel. Le certificat permet d’authentifier le client lorsqu’il établit une connexion avec le réseau virtuel. 

Si vous utilisez des certificats auto-signés, ceux-ci doivent être créés à l’aide de paramètres spécifiques. Vous pouvez créer un certificat auto-signé en suivant les instructions pour [PowerShell et Windows 10](vpn-gateway-certificates-point-to-site.md). Si vous n’avez pas Windows 10, vous pouvez utiliser [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Il est important de suivre les étapes décrites dans les instructions lorsque vous générez des certificats racines auto-signés et des certificats clients. Dans le cas contraire, les certificats que vous générez ne seront pas compatibles avec les connexions P2S, ce qui entraînera une erreur de connexion.

### <a name="cer"></a>1. Obtenir le fichier .cer pour le certificat racine

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="generate"></a>2. Générer un certificat client

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>6. Charger les informations de la clé publique du certificat racine

Vérifiez que votre passerelle VPN a terminé la création. Une fois terminée, vous pouvez charger le fichier .cer (qui contient les informations de clé publique) pour un certificat racine approuvé dans Azure. Une fois qu’un fichier .cer est chargé, Azure peut l’utiliser pour authentifier les clients qui ont installé un certificat client généré à partir du certificat racine approuvé. Vous pouvez charger ultérieurement d’autres fichiers de certificat racine approuvé, jusqu’à un total de 20, si nécessaire.

1. Déclarez la variable pour le nom de votre certificat, en remplaçant la valeur par la vôtre.

  ```powershell
  $P2SRootCertName = "P2SRootCert.cer"
  ```
2. Remplacez le chemin d’accès de fichier par le vôtre et exécutez les applets de commande.

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```
3. Chargez les informations de clé publique vers Azure. Une fois les informations de certificat chargées, Azure considère qu’il s’agit d’un certificat racine approuvé.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
  ```

## <a name="clientcertificate"></a>7. Installer un certificat client exporté

Si vous souhaitez créer une connexion P2S à partir d’un ordinateur client différent de celui que vous avez utilisé pour générer les certificats clients, vous devez installer un certificat client. Quand vous installez un certificat client, vous avez besoin du mot de passe créé lors de l’exportation du certificat client.

Assurez-vous que le certificat client a été exporté dans un fichier .pfx avec la totalité de la chaîne du certificat (qui est la valeur par défaut). Dans le cas contraire, les informations du certificat racine ne sont pas présentes sur l’ordinateur client et le client ne pourra pas s’authentifier correctement. 

Pour la procédure d’installation, consultez [Installer un certificat client](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>8. Configurer le client VPN natif

Les fichiers de configuration du client VPN contiennent des paramètres pour configurer les appareils afin qu’ils puissent se connecter à un réseau virtuel via une connexion P2S. Pour obtenir des instructions permettant de générer et d’installer les fichiers de configuration du client VPN, consultez [Créer et installer les fichiers de configuration du client VPN pour les configurations P2S d’authentification par certificat Azure native](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>9. Connexion à Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Se connecter à partir d’un client VPN Windows

>[!NOTE]
>Vous devez disposer de droits d’administrateur sur l’ordinateur client Windows à partir duquel vous vous connectez.
>
>

1. Pour vous connecter à votre réseau virtuel, sur l’ordinateur client, accédez aux connexions VPN et recherchez celle que vous avez créée. Elle porte le même nom que votre réseau virtuel. Cliquez sur **Connecter**. Un message contextuel faisant référence à l’utilisation du certificat peut s’afficher. Cliquez sur **Continuer** pour utiliser des privilèges élevés. 
2. Dans la page de statut **Connexion**, cliquez sur **Connecter** pour démarrer la connexion. Si un écran **Sélectionner un certificat** apparaît, vérifiez que le certificat client affiché est celui que vous souhaitez utiliser pour la connexion. Dans le cas contraire, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

  ![Connexion du client VPN à Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Votre connexion est établie.

  ![Connexion établie](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Résolution des problèmes liés aux connexions P2S de client Windows

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Pour se connecter à partir d’un client VPN Mac

À partir de la boîte de dialogue Réseau, recherchez le profil de client que vous souhaitez utiliser, puis cliquez sur **Connexion**.

  ![Connexion Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Pour vérifier votre connexion

Ces instructions s’appliquent aux clients Windows.

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges, puis exécutez *ipconfig/all*.
2. Affichez les résultats. Notez que l’adresse IP que vous avez reçue est l’une des adresses du pool d’adresses de client VPN point à site que vous avez spécifiées dans votre configuration. Les résultats ressemblent à l’exemple qui suit :

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Se connecter à une machine virtuelle

Ces instructions s’appliquent aux clients Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="addremovecert"></a>Pour ajouter ou supprimer un certificat racine

Vous pouvez ajouter et supprimer des certificats racines approuvés à partir d'Azure. Lorsque vous supprimez un certificat racine, les clients qui possèdent un certificat généré à partir de la racine ne peuvent pas s’authentifier, et ne seront donc pas en mesure de se connecter. Si vous souhaitez que des clients s’authentifient et se connectent, vous devez installer un nouveau certificat client généré à partir d’un certificat racine approuvé (téléchargé) dans Azure.

### <a name="addtrustedroot"></a>Ajout d’un certificat racine approuvé

Vous pouvez ajouter jusqu’à 20 fichiers .cer de certificat racine dans Azure. La procédure ci-après vous permet d’ajouter un certificat racine :

#### <a name="certmethod1"></a>Méthode 1

Il s’agit de la méthode la plus efficace pour télécharger un certificat racine.

1. Préparez le fichier .cer à télécharger :

  ```powershell
  $filePathForCert = "C:\cert\P2SRootCert3.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
  ```
2. Chargez le fichier. Vous ne pouvez charger qu’un seul fichier à la fois.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
  ```

3. Pour vérifier que le fichier de certificat est chargé :

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

#### <a name="certmethod2"></a>Méthode 2

Cette méthode comporte davantage d’étapes que la méthode 1, mais avec le même résultat. Il est inclus pour afficher les données de certificat en cas de besoin.

1. Créez et préparez le nouveau certificat racine à ajouter à Azure. Exportez la clé publique en tant que fichier X.509 codé en Base64 (.CER) et ouvrez-la dans un éditeur de texte. Copiez les valeurs, comme illustré dans l’exemple suivant :

  ![certificat](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

  > [!NOTE]
  > Lors de la copie des données de certificat, assurez-vous que vous copiez le texte en une seule ligne continue sans retour chariot ou sauts de ligne. Vous devrez peut-être modifier l’affichage dans l’éditeur de texte en activant « Afficher les symboles/Afficher tous les caractères » pour afficher les retours chariot et sauts de ligne.
  >
  >

2. Indiquez le nom du certificat et les informations clés en tant que variable. Remplacez les informations par les vôtres, comme illustré dans l’exemple suivant :

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. Ajoutez le nouveau certificat racine. Vous ne pouvez ajouter qu’un seul certificat à la fois.

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. Vous pouvez vérifier que le nouveau certificat a été correctement ajouté à l'aide de l'exemple suivant :

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

### <a name="removerootcert"></a>Suppression d’un certificat racine

1. Déclarez les variables.

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. Supprimez le certificat.

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. Utilisez l'exemple suivant pour vérifier que le certificat a été supprimé avec succès.

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>Révocation d’un certificat client

Vous pouvez révoquer des certificats clients. La liste de révocation de certificat vous permet de refuser sélectivement la connexion point à site en fonction des certificats clients individuels. Cela est différent de la suppression d’un certificat racine approuvé. Si vous supprimez un fichier .cer de certificat racine approuvé d’Azure, vous révoquez l’accès pour tous les certificats clients générés/signés par le certificat racine révoqué. Le fait de révoquer un certificat client plutôt que le certificat racine permet de continuer à utiliser les autres certificats générés à partir du certificat racine pour l’authentification.

La pratique courante consiste à utiliser le certificat racine pour gérer l'accès au niveaux de l'équipe ou de l'organisation, tout en utilisant des certificats clients révoqués pour le contrôle d'accès précis des utilisateurs individuels.

### <a name="revokeclientcert"></a>Révocation d'un certificat client

1. Récupérez l’empreinte du certificat client. Pour plus d’informations, consultez l’article [Comment : récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copiez les informations dans un éditeur de texte et supprimez tous les espaces afin d’obtenir une chaîne continue. Cette chaîne est déclarée sous la forme d’une variable à l’étape suivante.
3. Déclarez les variables. Veillez à déclarer l’empreinte numérique que vous avez récupérée à l’étape précédente.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. Ajoutez l’empreinte numérique à la liste des certificats révoqués. Une fois l’empreinte numérique ajoutée, le message « Réussi » s’affiche.

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. Vérifiez que l'empreinte numérique a été ajoutée à la liste de révocation de certificats.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. Après avoir ajouté l’empreinte numérique, le certificat ne peut plus être utilisé pour se connecter. Les clients qui tentent de se connecter à l’aide de ce certificat reçoivent un message indiquant que le certificat n’est plus valide.

### <a name="reinstateclientcert"></a>Réactivation d’un certificat client

Vous pouvez réactiver un certificat client en supprimant l'empreinte numérique de la liste des certificats clients révoqués.

1. Déclarez les variables. Assurez-vous de déclarer la bonne empreinte numérique pour le certificat que vous souhaitez rétablir.

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. Supprimez l’empreinte numérique du certificat de la liste de révocation de certificats.

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. Vérifiez si l'empreinte numérique est supprimée de la liste de révocation.

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>Forum Aux Questions sur les connexions point à site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Pour plus d’informations sur la mise en réseau et les machines virtuelles, consultez [Vue d’ensemble du réseau de machines virtuelles Azure et Linux](../virtual-machines/linux/azure-vm-network-overview.md).

Pour plus d’informations sur la résolution des problèmes liés aux connexions de point à site, consultez l’article [Résolution des problèmes de connexion de point à site Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).