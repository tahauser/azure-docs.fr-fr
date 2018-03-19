---
title: "Connexion d’Azure Stack à Azure à l’aide d’ExpressRoute"
description: "Découvrez comment connecter des réseaux virtuels dans Azure Stack à des réseaux virtuels dans Azure à l’aide d’ExpressRoute."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 544fc1bcc9212fd38938d58447f5050df2a08796
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Connexion d’Azure Stack à Azure à l’aide d’ExpressRoute

*S’applique aux systèmes intégrés Azure Stack et au Kit de développement Azure Stack*

Deux méthodes vous permettent de connecter des réseaux virtuels Azure Stack à des réseaux virtuels Azure :
   * **De site à site**

     Connexion VPN sur IPsec (IKE v1 et IKE v2). Ce type de connexion requiert un périphérique VPN ou le service RRAS. Pour en savoir plus, consultez l’article relatif à la [connexion d’Azure Stack à Azure à l’aide d’un VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Connexion directe à Azure depuis votre déploiement Azure Stack. ExpressRoute n’est **pas** une connexion VPN établie via le réseau Internet public. Pour en savoir plus sur ExpressRoute, consultez la rubrique [Présentation d’ExpressRoute](../expressroute/expressroute-introduction.md).

Cet article montre comment utiliser ExpressRoute pour connecter Azure Stack à Azure.
## <a name="requirements"></a>Configuration requise
Pour connecter Azure Stack et Azure à l’aide d’ExpressRoute, tenez compte des critères suivants :
* Vous devez disposer d’un abonnement Azure pour créer un circuit ExpressRoute et des réseaux virtuels dans Azure.
* Un circuit ExpressRoute doit être configuré via un [fournisseur de connectivité](../expressroute/expressroute-locations.md).
* Un routeur avec le circuit ExpressRoute connecté à ses ports WAN.
* Le côté LAN du routeur doit être lié à la passerelle multi-locataire d’Azure Stack.
* Le routeur doit prendre en charge les connexions VPN de site à site entre son interface LAN et la passerelle multi-locataire d’Azure Stack.
* Si plus d’un locataire est ajouté à votre déploiement Azure Stack, le routeur doit être en mesure de créer plusieurs VRF (Virtual Routing and Forwarding).

Le schéma suivant représente la mise en réseau conceptuelle une fois la configuration terminée :

![Schéma conceptuel](media/azure-stack-connect-expressroute/Conceptual.png)

**Schéma 1**

Le schéma d’architecture suivant montre comment plusieurs locataires se connectent à Azure depuis l’infrastructure Azure Stack par le biais du routeur ExpressRoute au niveau de la périphérie Microsoft :

![Diagramme de l’architecture](media/azure-stack-connect-expressroute/Architecture.png)

**Schéma 2 :**

L’exemple présenté dans cet article utilise la même architecture pour établir une connexion à Azure via l’homologation privée ExpressRoute. Pour ce faire, une connexion VPN de site à site est établie entre la passerelle de réseau virtuel dans Azure Stack et un routeur ExpressRoute. Les étapes suivantes vous expliquent comment créer une connexion de bout en bout entre deux réseaux virtuels, depuis deux locataires différents dans Azure Stack, et leurs réseaux virtuels respectifs dans Azure. Vous pouvez soit ajouter autant de réseaux virtuels locataires que vous le souhaitez et répéter les étapes pour chaque locataire, soit utiliser cet exemple pour déployer uniquement un seul réseau virtuel locataire.

## <a name="configure-azure-stack"></a>Configurer Azure Stack
Maintenant, vous devez créer les ressources dont vous avez besoin pour configurer votre environnement Azure Stack comme réseau locataire. Les étapes suivantes vous montrent ce que vous devez faire. Ces instructions expliquent comment créer des ressources à l’aide du portail Azure Stack, mais vous pouvez également utiliser PowerShell.

![Étapes de création des ressources réseau](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Avant de commencer
Avant de passer à la configuration, vous devez disposer des éléments suivants :
* Un déploiement Azure Stack.

   Pour en savoir plus sur le déploiement du Kit de développement Azure Stack, consultez la rubrique relative au [démarrage rapide du déploiement du Kit de développement Azure Stack](azure-stack-deploy-overview.md).
* Une offre sur Azure Stack à laquelle votre utilisateur peut souscrire.

  Pour obtenir des instructions, consultez la rubrique relative à la [mise à disposition de machines virtuelles à vos utilisateurs Azure Stack](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Créer des ressources réseau dans Azure Stack

Suivez les procédures ci-dessous pour créer les ressources réseau nécessaires dans Azure Stack pour chaque locataire :

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Créer le réseau virtuel et le sous-réseau de machine virtuelle
1. Connectez-vous au portail utilisateur avec un compte d’utilisateur (locataire).

2. Dans le portail, cliquez sur **Nouveau**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Sélectionnez **Mise en réseau** dans le menu Marketplace.

4. Cliquez sur **Réseau virtuel** dans le menu.

5. Saisissez les valeurs correspondant à chaque champ à l’aide du tableau suivant :

   |Champ  |Valeur  |
   |---------|---------|
   |NOM     |Tenant1VNet1         |
   |Espace d’adressage     |10.1.0.0/16|
   |Nom du sous-réseau     |Tenant1-Sub1|
   |Plage d’adresses de sous-réseau     |10.1.1.0/24|

6. Vous devez voir l’abonnement que vous avez créé précédemment rempli dans le champ **Abonnement**.

    a. Dans le champ Groupe de ressources, créez un groupe de ressources ou, si vous en avez déjà un, sélectionnez **Utiliser existant**.

    b. Vérifiez l’emplacement par défaut.

    c. Cliquez sur **Épingler au tableau de bord**.

    d. Cliquez sur **Créer**.



#### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle
1. Ouvrez la ressource de réseau virtuel que vous avez créée (Tenant1VNet1) depuis le tableau de bord.
2. Dans la section Paramètres, sélectionnez **Sous-réseaux**.
3. Cliquez sur **Sous-réseau de passerelle** pour ajouter un sous-réseau de passerelle au réseau virtuel.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. Par défaut, le nom du sous-réseau est défini sur **GatewaySubnet**.
   Les sous-réseaux de passerelle sont des éléments spéciaux et doivent porter ce nom pour fonctionner correctement.
5. Dans le champ **Plage d’adresses**, vérifiez que **10.1.0.0/24** est bien l’adresse indiquée.
6. Cliquez sur **OK** pour créer le sous réseau de passerelle.

#### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel
1. Dans le portail utilisateur Azure Stack, cliquez sur **Nouveau**.
   
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Passerelle de réseau virtuel** dans la liste des ressources réseau.
4. Dans le champ **nom**, entrez **GW1**.
5. Cliquez sur l’élément **Réseau virtuel** pour choisir un réseau virtuel.
   Sélectionnez **Tenant1VNet1** dans la liste.
6. Cliquez sur l’élément de menu **Adresse IP publique**. Lorsque la section **Choisir une adresse IP publique** s’affiche, cliquez sur **Créer**.
7. Dans le champ **Nom**, entrez **GW1-PiP**, puis cliquez sur **OK**.
8. Par défaut, le **type de VPN** doit être défini sur **Basé sur un itinéraire**.
    Conservez ce paramètre.
9. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Vous pouvez épingler la ressource au tableau de bord si vous le souhaitez. Cliquez sur **Créer**.

#### <a name="create-the-local-network-gateway"></a>Créer la passerelle de réseau local

La passerelle de réseau local indique la passerelle distante présente à l’autre extrémité de la connexion VPN. Pour cet exemple, la partie distante est la sous-interface LAN du routeur ExpressRoute. L’adresse distante de Tenant 1 (locataire 1) est 10.60.3.255, comme indiqué dans le schéma 2.

1. Connectez-vous à l’ordinateur physique Azure Stack.
2. Connectez-vous au portail utilisateur avec votre compte d’utilisateur, puis cliquez sur **Nouveau**.
3. Sélectionnez **Mise en réseau** dans le menu Marketplace.
4. Sélectionnez **Passerelle de réseau local** dans la liste des ressources.
5. Dans le champ **Nom**, saisissez **ER-Router-GW**.
6. Pour le champ **Adresse IP**, reportez-vous au schéma 2. L’adresse IP de la sous-interface LAN du routeur ExpressRoute pour le locataire 1 est 10.60.3.255. Pour votre propre environnement, entrez l’adresse IP de l’interface correspondante de votre routeur.
7. Dans le champ **Espace d’adressage**, entrez l’espace d’adressage des réseaux virtuels auxquels se connecter dans Azure. Pour cet exemple, reportez-vous au schéma 2. Pour le locataire 1, les sous-réseaux requis sont **192.168.2.0/24** (il s’agit du réseau virtuel Hub dans Azure) et **10.100.0.0/16** (il s’agit du réseau virtuel Spoke dans Azure). Entrez les sous-réseaux correspondants pour votre environnement.
   > [!IMPORTANT]
   > Cet exemple suppose que vous utilisiez des itinéraires statiques pour la connexion VPN de site à site entre la passerelle Azure Stack et le routeur ExpressRoute.

8. Vérifiez que les champs **Abonnement**, **Groupe de ressources** et **Emplacement** sont corrects, puis cliquez sur **Créer**.

#### <a name="create-the-connection"></a>Créer la connexion
1. Dans le portail utilisateur Azure Stack, cliquez sur **Nouveau**.
2. Sélectionnez **Mise en réseau** dans le menu Marketplace.
3. Sélectionnez **Connexion** dans la liste des ressources.
4. Dans la section des paramètres **De base**, choisissez **Site à site (IPSec)** comme **Type de connexion**.
5. Sélectionnez l’**abonnement**, le **groupe de ressources** et l’**emplacement**, puis cliquez sur **OK**.
6. Dans la section **Paramètres**, cliquez sur **Passerelle de réseau virtuel**, puis sur **GW1**.
7. Cliquez sur **Passerelle de réseau local**, puis sur **ER Router GW**.
8. Dans le champ **Nom de la connexion**, entrez **ConnectToAzure**.
9. Dans le champ **Clé partagée (PSK)**, entrez **abc123**, puis cliquez sur **OK**.
10. Dans la section **Résumé** , cliquez sur **OK**.

    Une fois la connexion créée, l’adresse IP publique est utilisée par la passerelle de réseau virtuel. Pour rechercher l’adresse dans le portail Azure Stack, accédez à votre passerelle de réseau virtuel. Dans **Vue d’ensemble**, recherchez l’**adresse IP publique**. Notez cette adresse ; vous devrez la renseigner dans *Adresse IP interne* dans la section suivante (si votre déploiement l’exige).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Pour valider les données transitant via la connexion VPN, vous avez besoin de machines virtuelles pour envoyer et recevoir des données dans le réseau virtuel d’Azure Stack. Créez d’abord une machine virtuelle, puis ajoutez-la au sous-réseau de machine virtuelle de votre réseau virtuel.

1. Dans le portail utilisateur Azure Stack, cliquez sur **Nouveau**.
2. Sélectionnez **Machines virtuelles** dans le menu Marketplace.
3. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2016 Datacenter Eval**, puis cliquez sur **Créer**.
4. Dans la section **De base**, dans le champ **Nom**, entrez **VM01**.
5. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte pour vous connecter à la machine virtuelle une fois qu’elle aura été créée.
6. Remplissez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis cliquez sur **OK**.
7. Dans la section **Taille**, cliquez sur une taille de machine virtuelle pour cette instance, puis sur **Sélectionner**.
8. Dans la section **Paramètres**, acceptez les valeurs par défaut. Toutefois, vérifiez bien que le réseau virtuel sélectionné est **Tenant1VNet1** et que le sous-réseau est défini sur **10.1.1.0/24**. Cliquez sur **OK**.
9. Vérifiez les paramètres dans la section **Résumé**, puis cliquez sur **OK**.

Pour chaque réseau virtuel locataire à connecter, répétez les étapes précédentes de la section **Créer le réseau virtuel et le sous-réseau de machine virtuelle** à la section **Créer une machine virtuelle**.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configurer la machine virtuelle NAT pour la traversée de passerelle
> [!IMPORTANT]
> Cette section concerne uniquement les déploiements du Kit de développement Azure Stack. La traduction d’adresses réseau (NAT) n’est pas nécessaire pour les déploiements à plusieurs nœuds.

Le Kit de développement Azure Stack est autonome et isolé du réseau sur lequel est déployé l’hôte physique. Par conséquent, le réseau VIP « Externe » auquel les passerelles sont connectées n’est pas externe. Il est masqué derrière un routeur qui procède à la traduction d’adresses réseau (NAT).
 
Le routeur est une machine virtuelle Windows Server (**AzS-BGPNAT01**) qui exécute le rôle RRAS (Routing and Remote Access Services) dans l’infrastructure du Kit de développement Azure Stack. Vous devez configurer la traduction d’adresses réseau sur la machine virtuelle AzS-BGPNAT01 pour permettre à la connexion VPN de site à site de se connecter aux deux extrémités.

#### <a name="configure-the-nat"></a>Configurer NAT

1. Connectez-vous à l’ordinateur physique Azure Stack avec votre compte Administrateur.
2. Copiez et modifiez le script PowerShell suivant et exécutez-le dans une fenêtre Windows PowerShell ISE avec des privilèges élevés. Remplacez votre mot de passe administrateur. L’adresse renvoyée est votre *adresse BGPNAT externe*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Pour configurer NAT, copiez et modifiez le script PowerShell suivant et exécutez-le dans une fenêtre Windows PowerShell ISE avec des privilèges élevés. Modifiez le script pour remplacer l’*adresse BGPNAT externe* et l’*adresse IP interne* (que vous avez notée dans la section **Créer la connexion**).

   Dans les schémas, l’*adresse BGPNAT externe* est 10.10.0.62 et l’*adresse IP interne* est 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configuration d’Azure
Une fois Azure Stack configuré, vous pouvez déployer certaines ressources Azure. Le schéma suivant représente un réseau virtuel locataire dans Azure. Vous pouvez utiliser n’importe quel nom et schéma d’adressage pour désigner votre réseau virtuel dans Azure. Toutefois, la plage d’adresses des réseaux virtuels dans Azure et Azure Stack doit être unique et ne doit pas se chevaucher.

![Réseaux virtuels Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Schéma 3**

Les ressources que vous déployez dans Azure sont semblables aux ressources déployées dans Azure Stack. Dans les deux environnements, vous déployez :
* Des réseaux virtuels et des sous-réseaux
* Un sous-réseau de passerelle
* Une passerelle de réseau virtuel
* Une connexion
* Un circuit ExpressRoute

L’infrastructure réseau Azure donnée en exemple est configurée de la façon suivante :
* Un modèle de réseau virtuel Hub (192.168.2.0/24) et Spoke (10.100.0.0./16) standard est utilisé.
* Les charges de travail sont déployées dans le réseau virtuel Spoke et le circuit ExpressRoute est connecté au réseau virtuel Hub.
* Ces deux réseaux virtuels sont connectés à l’aide de la fonctionnalité VNet Peering.

### <a name="configure-vnets"></a>Configurer les réseaux virtuels
1. Connectez-vous au portail Azure avec vos informations d’identification Azure.
2. Créez le réseau virtuel Hub en utilisant l’espace d’adressage 192.168.2.0/24. Créez un sous-réseau à l’aide de la plage d’adresses 192.168.2.0/25 et ajoutez un sous-réseau de passerelle à l’aide de la plage d’adresses 192.168.2.128/27.
3. Créez le réseau virtuel Spoke et le sous-réseau en utilisant la plage d’adresses 10.100.0.0/16.


Pour en savoir plus sur la création de réseaux virtuels dans Azure, consultez la rubrique [Créer un réseau virtuel](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configurer un circuit ExpressRoute

1. Passez en revue la configuration requise d’ExpressRoute dans [Configuration requise pour ExpressRoute et liste de contrôle](../expressroute/expressroute-prerequisites.md).
2. Suivez les étapes décrites dans la rubrique [Création et modification d’un circuit ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) pour créer un circuit ExpressRoute via votre abonnement Azure.
3. Partagez la clé de service obtenue à l’étape précédente via votre fournisseur/hébergeur pour approvisionner l’extrémité de votre circuit ExpressRoute.
4. Suivez les étapes décrites dans la rubrique [Créer et modifier l’homologation pour un circuit ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) pour configurer l’homologation privée sur le circuit ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

* Suivez les étapes décrites dans la rubrique [Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) pour créer une passerelle de réseau virtuel pour ExpressRoute dans le réseau virtuel Hub.

### <a name="create-the-connection"></a>Créer la connexion

* Pour connecter le circuit ExpressRoute au réseau virtuel Hub, suivez les étapes décrites dans [Connecter un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Homologuer les réseaux virtuels

* Homologuez les réseaux virtuels Hub et Spoke en suivant les étapes décrites dans la rubrique [Créer une homologation de réseaux virtuels - Resource Manager - Même abonnement](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Lors de la configuration de VNet Peering, sélectionnez les options suivantes :
   * De Hub à Spoke : **Autoriser le transit par passerelle**
   * De Spoke à Hub : **Utiliser la passerelle distante par défaut**

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

* Déployez les machines virtuelles de votre charge de travail dans le réseau virtuel Spoke.

Répétez ces étapes pour connecter d’autres réseaux virtuels locataires dans Azure via leurs circuits ExpressRoute respectifs.

## <a name="configure-the-router"></a>Configuration du routeur

Vous pouvez vous référer au schéma suivant pour configurer votre routeur ExpressRoute. Ce schéma représente deux locataires (Tenant 1 et Tenant 2) avec leurs circuits ExpressRoute respectifs. Chaque locataire est connecté à son propre VRF (Virtual Routing and Forwarding) du côté LAN et WAN du routeur ExpressRoute pour garantir une isolation de bout en bout entre les deux locataires. Notez les adresses IP utilisées dans les interfaces du routeur au fur et à mesure de la configuration.

![Schéma de bout en bout](media/azure-stack-connect-expressroute/EndToEnd.png)

**Schéma 4**

Vous pouvez utiliser n’importe quel routeur qui prend en charge le VPN IKEv2 et BGP pour mettre fin à la connexion VPN de site à site établie depuis Azure Stack. Le même routeur est utilisé pour se connecter à Azure via un circuit ExpressRoute. 

Voici un exemple de configuration d’un routeur Cisco ASR 1000 qui prend en charge l’infrastructure réseau représentée dans le schéma 4 :

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Tester la connexion

Lorsque la connexion de site à site et le circuit ExpressRoute sont établis, testez votre connexion. Pour cela, rien de plus simple.  Connectez-vous à l’une des machines virtuelles créées dans votre réseau virtuel Azure et effectuez un test ping sur la machine virtuelle créée dans l’environnement Azure Stack, ou vice versa. 

Pour vérifier que vous envoyez bien le trafic via les connexions site à site et ExpressRoute, vous devez effectuer un test ping aux deux extrémités avec l’adresse IP dédiée de la machine virtuelle, et non avec son adresse IP virtuelle. Pour cela, vous devez rechercher et noter l’adresse à l’autre extrémité de la connexion.

### <a name="allow-icmp-in-through-the-firewall"></a>Autoriser la transmission du protocole ICMP via le pare-feu
Par défaut, Windows Server 2016 n’autorise pas la transmission des paquets ICMP via le pare-feu. Par conséquent, pour chaque machine virtuelle utilisée dans le test, exécutez la cmdlet suivante dans une fenêtre PowerShell avec des privilèges élevés :


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Effectuer un test ping sur la machine virtuelle Azure Stack

1. Connectez-vous au portail utilisateur Azure Stack avec un compte locataire.
2. Cliquez sur **Machines virtuelles** dans la barre de navigation de gauche.
3. Recherchez la machine virtuelle créée précédemment et cliquez dessus.
4. Dans la section de la machine virtuelle, cliquez sur **Se connecter**.
5. Ouvrez une fenêtre PowerShell avec des privilèges élevés et tapez **ipconfig /all**.
6. Recherchez l’adresse IPv4 dans la sortie et notez-la. Effectuez un test ping à partir de la machine virtuelle dans le réseau virtuel Azure. Dans l’environnement représenté en exemple, l’adresse provient du sous-réseau 10.1.1.x/24. Dans votre environnement, l’adresse peut être différente. Toutefois, elle doit figurer dans le sous-réseau que vous avez créé pour le sous-réseau du réseau virtuel locataire.


### <a name="view-data-transfer-statistics"></a>Afficher les statistiques de transfert de données

Si vous souhaitez connaître le volume de trafic qui transite via votre connexion, accédez à la section Connexion du portail utilisateur Azure Stack. Ainsi, vous pourrez également vérifier que le test ping que vous venez d’effectuer est passé par les connexions VPN et ExpressRoute.

1. Connectez-vous au portail utilisateur Microsoft Azure Stack avec votre compte locataire.
2. Accédez au groupe de ressources dans lequel votre passerelle VPN a été créée et sélectionnez le type d’objet **Connexions**.
3. Cliquez sur la connexion **ConnectToAzure** dans la liste.
4. Dans la section **Connexion**, vous pouvez visualiser les statistiques de **données entrantes** et de **données sortantes**. Vous devriez voir des valeurs non nulles.

   ![Données entrantes et sortantes](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Étapes suivantes
[Déployer des applications sur Azure et Azure Stack](azure-stack-solution-pipeline.md)
