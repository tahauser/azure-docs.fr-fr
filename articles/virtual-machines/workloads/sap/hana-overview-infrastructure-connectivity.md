---
title: Infrastructure et connectivité à SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Configurez l’infrastructure de connectivité requise pour utiliser SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d94e491d12ac43a4d85a638c79bcd3b24a4bc0ef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infrastructure et connectivité à SAP HANA (grandes instances) sur Azure 

Quelques définitions avant de lire ce guide. Dans [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), nous avons présenté deux classes différentes d’unités de grande instance HANA avec :

- S72, S72m, S144, S144m, S192 et S192m, que nous appelons la « Classe Type I » des références SKU ;
- S384, S384m, S384xm, S576, S768 et S960, que nous appelons la « Classe Type II » des références SKU.

Les spécificateurs de classe sont utilisé dans la documentation relative à la grande instance HANA pour désigner différentes fonctionnalités et exigences en fonction des références SKU de grande instance HANA.

Nous utilisons fréquemment les autres définitions suivantes :
- **Tampon de grande instance :** une pile d’infrastructure matérielle certifiée SAP HANA TDI et dédiée à l’exécution des instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA TDI certifié qui est déployé dans les tampons de grande instance dans différentes régions Azure. Le terme associé **Grande instance HANA** est la forme abrégée de SAP HANA sur Azure (grandes instances) largement utilisée dans ce guide de déploiement technique.
 

Une fois l’achat de SAP HANA sur Azure (grandes instances) finalisé entre vous et l’équipe Microsoft responsable des comptes d’entreprise, Microsoft a besoin des informations suivantes pour déployer des unités de grande instance HANA :

- Nom du client
- Coordonnées professionnelles (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées techniques (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées de mise en réseau technique (y compris l’adresse électronique et le numéro de téléphone)
- Région de déploiement Azure (Ouest des États-Unis, Est des États-Unis, Est de l’Australie, Sud-Est de l’Australie, Europe de l’Ouest et Europe du Nord à partir de juillet 2017)
- Confirmer la référence SKU (configuration) de SAP HANA sur Azure (grandes instances)
- Déjà décrit dans le document de présentation et d’architecture des grandes instances HANA, pour chaque région Azure déployée sur :
    - Une plage d’adresses IP /29 pour les connexions ER-P2P qui relient les réseaux virtuels Azure aux grandes instances HANA
    - Un bloc CIDR /24 utilisé pour le pool d’adresses IP du serveur de grandes instances HANA
- Les valeurs de plage d’adresses IP utilisées dans l’attribut Espace d'adressage du réseau virtuel de chaque réseau virtuel Azure qui se connecte à de grandes instances HANA
- Données pour chaque système de grande instance HANA :
  - Nom d’hôte souhaité : idéalement, avec le nom de domaine complet.
  - Adresse IP souhaitée pour la grande unité d’instance HANA en dehors de la plage d’adresses IP du pool de serveur. N’oubliez pas que les 30 premières adresses IP de la plage d’adresses IP du pool de serveur sont réservées à une utilisation interne dans les grandes instances HANA
  - Nom de SID SAP HANA pour l’instance HANA (requis pour créer les volumes de disque liés à SAP HANA nécessaires). Le SID HANA est requis pour la création des autorisations pour <sidadm> sur les volumes NFS, qui sont attachés à l’unité de grande instance HANA. Il est également utilisé comme l’un des composants de nom des volumes de disque qui sont montés. Si vous souhaitez exécuter plusieurs instances HANA sur l’unité, vous devez répertorier plusieurs SID HANA. Chacun d’eux obtient un ensemble distinct de volumes assignés.
  - L’ID de groupe détenu par l’utilisateur hana-sidadm dans le système d’exploitation Linux est nécessaire pour créer des volumes de disque liés à SAP HANA. L’installation de SAP HANA crée généralement le groupe sapsys avec l’ID de groupe 1001. L’utilisateur hana-sidadm fait partie de ce groupe
  - L’ID d’utilisateur détenu par l’utilisateur hana-sidadm dans le système d’exploitation Linux est nécessaire pour créer des volumes de disque liés à SAP HANA. Si vous exécutez plusieurs instances HANA sur l’unité, vous devez répertorier tous les <sid>utilisateurs adm 
- ID de l’abonnement Azure auquel les grandes instances HANA Azure vont être directement connectées. Cet ID d’abonnement désigne l’abonnement Azure qui va être facturé avec les unités de grande instance HANA.

Après avoir renseigné ces informations, Microsoft configure SAP HANA sur Azure (grandes instances) et renvoie les informations nécessaires pour lier vos réseaux virtuels Azure aux grandes instances HANA et pour accéder aux unités de grande instance HANA.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Connexion de machines virtuelles Azure à de grandes instances HANA

Comme mentionné précédemment dans l’article [Vue d’ensemble et architecture de SAP HANA (grandes instances) sur Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), le déploiement minimal de grandes instances HANA avec la couche d’application SAP dans Azure se présente comme suit :

![Réseau virtuel Azure connecté à SAP HANA sur Azure (grandes instances) et en local](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

En regardant de plus près du côté du réseau virtuel Azure, on s’aperçoit :

- qu’il est nécessaire de définir un réseau virtuel Azure dans lequel déployer les machines virtuelles de la couche d’application SAP ;
- que cela signifie automatiquement qu’un sous-réseau par défaut est défini dans le réseau virtuel Azure et que c’est ce sous-réseau qui sera effectivement utilisé pour le déploiement des machines virtuelles ;
- que le réseau virtuel Azure créé doit avoir au moins un sous-réseau de machines virtuelles et un sous-réseau de passerelle ExpressRoute. Ces sous-réseaux doivent se voir affecter les plages d’adresses IP spécifiées et décrites dans les sections suivantes.

Nous allons donc étudier d’un peu plus près la création du réseau virtuel Azure pour les grandes instances HANA

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Création du réseau virtuel Azure pour les grandes instances HANA

>[!Note]
>Le réseau virtuel Azure pour une grande instance HANA doit être créé à l’aide du modèle de déploiement Azure Resource Manager. L’ancien modèle de déploiement Azure, communément appelé modèle de déploiement Classic, n’est pas pris en charge avec la solution de grande instance HANA.

Le réseau virtuel peut être créé à l’aide du portail Azure, de PowerShell, d’un modèle Azure ou de l’interface de ligne de commande Azure (voir [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)). Dans l’exemple suivant, nous allons examiner un réseau virtuel créé via le portail Azure.

Dans les définitions d’un réseau virtuel Azure via le portail Azure, examinons de plus près certaines définitions et leur lien avec les éléments de la liste des différentes plages d’adresses IP. La notion **d’espace d’adressage** dont nous parlons désigne l’espace d’adressage que le réseau virtuel Azure est autorisé à utiliser. Il s’agit également de la plage d’adresses que le réseau virtuel utilise pour la propagation des itinéraires BGP. Cet **espace d’adressage** est illustré ici :

![Espace d’adressage du réseau virtuel Azure affiché dans le portail Azure](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

Dans le cas ci-dessus, avec 10.16.0.0/16, le réseau virtuel Azure a été affecté à une plage d’adresses IP relativement importante. Cela signifie que toutes les plages d’adresses IP des sous-réseaux suivants au sein de ce réseau virtuel peuvent s’inscrire dans cet espace d’adressage. Généralement, nous ne recommandons pas une plage d’adresses aussi volumineuse pour un seul réseau virtuel dans Azure. Mais allons plus loin et observons les sous-réseaux définis dans le réseau virtuel Azure :

![Sous-réseaux de réseaux virtuels Azure et plages d’adresses IP associées](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Comme vous pouvez le voir, on obtient un réseau virtuel avec un premier sous-réseau de machines virtuelles (appelé « default ») et un sous-réseau appelé « GatewaySubnet ».
Dans la section suivante, nous faisons référence à la plage d’adresses IP du sous-réseau appelé « default » dans les graphiques en tant que **plage d’adresses IP du sous-réseau de machines virtuelles Azure**. Dans les sections suivantes, nous faisons référence à la plage d’adresses IP du sous-réseau de passerelle en tant que **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**. 

Dans le cas illustré dans les deux graphiques ci-dessus, vous pouvez voir que **l’espace d’adressage de réseau virtuel** couvre à la fois la **plage d’adresses IP du sous-réseau de machines virtuelles Azure** et la **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**. 

Dans les autres cas où vous devez vous montrer prudent ou spécifique avec vos plages d’adresses IP, vous pouvez limiter **l’espace d’adressage de réseau virtuel** d’un réseau virtuel aux plages spécifiques utilisées par chaque sous-réseau. Dans ce cas, vous pouvez définir **l’espace d’adressage de réseau virtuel** d’un réseau virtuel spécifique sous forme de plusieurs plages, comme indiqué ici :

![Espace d’adressage de réseau virtuel Azure avec deux espaces](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

Dans ce cas, deux espaces sont définis pour **l’espace d’adressage de réseau virtuel**. Ceux-ci sont identiques aux plages d’adresses IP définies pour la **plage d’adresses IP du sous-réseau de machines virtuelles Azure** et la **plage d’adresses IP du sous-réseau de passerelle de réseau virtuel**.

Vous pouvez utiliser n’importe quelle norme d’affectation de noms de votre choix pour ces sous-réseaux locataires (sous-réseaux de machines virtuelles). Toutefois, **il doit toujours y avoir un seul et unique sous-réseau de passerelle pour chaque réseau virtuel** qui se connecte au circuit ExpressRoute SAP HANA sur Azure (grandes instances). Et **ce sous-réseau de passerelle doit toujours être nommé « GatewaySubnet »** pour garantir le positionnement correct de la passerelle ExpressRoute.

> [!WARNING] 
> Il est essentiel que le sous-réseau de passerelle soit toujours nommé « GatewaySubnet ».

Plusieurs sous-réseaux de machine virtuelle peuvent être utilisés, même s’ils utilisent des plages d’adresses non contiguës. Mais comme indiqué précédemment, ces plages d’adresses doivent être couvertes par **l’espace d’adressage de réseau virtuel** du réseau virtuel, soit sous forme agrégée, soit dans une liste des plages exactes des sous-réseaux de machine virtuelle et du sous-réseau de passerelle.

Voici en résumé ce qu’il faut retenir d’un réseau virtuel Azure qui se connecte à de grandes instances HANA :

- Vous devez envoyer à Microsoft **l’espace d’adressage du réseau virtuel** lorsque vous effectuez le déploiement initial de grandes instances HANA. 
- **L’espace d’adressage du réseau virtuel** peut être une plus grande plage qui couvre la ou les plages d’adresses IP du sous-réseau de machine virtuelle Azure et la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel.
- Vous pouvez aussi envoyer en tant **qu’espace d’adressage du réseau virtuel** plusieurs plages couvrant les différentes plages d’adresse IP de la ou des plages d’adresses IP du sous-réseau de machine virtuelle et de la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel.
- **L’espace d’adressage du réseau virtuel** défini est utilisé pour la propagation du routage BGP.
- Le sous-réseau de passerelle doit être nommé **GatewaySubnet**.
- **L’espace d’adressage du réseau virtuel** est utilisé comme filtre du côté de la grande instance HANA afin d’autoriser ou d’interdire le trafic entre Azure et les unités de grandes instances HANA. Vous pouvez rencontrer des problèmes de connectivité si les informations d’itinéraire BGP du sous-réseau Azure et des plages d’adresses IP configurées pour le filtrage du côté de la grande instance HANA ne correspondent pas.
- La section « Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute » fournit quelques détails supplémentaires sur le sous-réseau de passerelle.



### <a name="different-ip-address-ranges-to-be-defined"></a>Différentes plages d’adresses IP à définir 

Nous avons déjà évoqué dans des sections précédentes certaines des plages d’adresses IP nécessaires pour pouvoir déployer de grandes instances HANA. Mais d’autres plages d’adresses IP sont également importantes. Examinons-les plus en détail. Les adresses IP suivantes, qu’il n’est pas nécessaire de toutes envoyer à Microsoft, doivent être définies avant d’envoyer une demande de déploiement initial :

- **Espace d’adressage de réseau virtuel :** comme nous l’avons évoqué précédemment, il s’agit des plages d’adresses IP que vous avez affectées (ou que vous prévoyez d’affecter) à votre paramètre d’espace d’adressage dans les réseaux virtuels Azure qui se connectent à l’environnement de grande instance HANA. Il est recommandé d’utiliser ce paramètre d’espace d’adressage sous la forme d’une valeur multiligne composée des plages de sous-réseau de machines virtuelles Azure et de la plage du sous-réseau de passerelle Azure, comme illustré dans les graphismes ci-dessus. Cette plage NE doit PAS chevaucher les plages d’adresses du pool IP local ou de votre serveur ou encore les plages d’adresses ER-P2P. Comment obtenir ces plages d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doivent vous fournir une ou plusieurs plages d’adresses IP qui ne sont pas utilisées à l’intérieur de votre réseau. Exemple : si votre sous-réseau de machines virtuelles Azure (voir ci-dessus) utilise 10.0.1.0/24 et si votre sous-réseau de passerelles Azure (voir ci-dessous) utilise 10.0.2.0/28, alors votre espace d’adressage de réseau virtuel Azure devrait se trouver sur deux lignes : 10.0.1.0/24 et 10.0.2.0/28. Bien que les valeurs d’espace d’adressage peuvent être agrégées, il est recommandé de les mettre en correspondance avec les plages de sous-réseau afin d’éviter une réutilisation accidentelle de plages d’adresses IP inutilisées dans des espaces d’adressage plus grands ailleurs dans votre réseau à l’avenir. **L’espace d'adressage du réseau virtuel est un plage d’adresses IP qui doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**

- **Plage d’adresses IP du sous-réseau de machines virtuelles Azure :** cette plage d’adresses IP, déjà décrite précédemment, est celle que vous avez affectée (ou que vous prévoyez d’affecter) au paramètre du sous-réseau de réseau virtuel Azure dans votre réseau virtuel Azure qui se connecte à l’environnement de grande instance HANA. Cette plage d’adresses IP est utilisée pour affecter des adresses IP à vos machines virtuelles Azure. Les adresses IP en dehors de cette plage sont autorisées à se connecter aux serveurs de votre grande instance SAP HANA. Si nécessaire, plusieurs sous-réseaux de machines virtuelles Azure peuvent être utilisés. Microsoft recommande d’utiliser un bloc CIDR /24 pour chaque sous-réseau de machines virtuelles Azure. Cette plage d’adresses doit faire partie des valeurs utilisées dans l’espace d’adressage de réseau virtuel Azure. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau.

- **Plage d’adresses IP du sous-réseau de passerelle de réseau virtuel :** selon les fonctionnalités que vous prévoyez d’utiliser, voici la taille recommandée :
   - Passerelle ExpressRoute ultra-performante : bloc d’adresse /26 - obligatoire pour la classe de Type II des références SKU
   - Coexistence avec VPN et ExpressRoute à l’aide d’une passerelle ExpressRoute hautes performances (ou plus petites) : bloc d’adresses /27
   - Toutes les autres situations : bloc d’adresses /28 Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de « l’espace d’adressage de réseau virtuel ». Cette plage d’adresses doit faire partie des valeurs utilisées dans les valeurs de l’espace d’adressage de réseau virtuel Azure que vous devez soumettre à Microsoft. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. 

- **Plage d’adresses pour la connectivité ER-P2P :** il s’agit de la plage d’adresses IP utilisée pour votre connexion ExpressRoute (ER) P2P de grande instance HANA. Cette plage d’adresses IP doit correspondre à la plage CIDR /29. Cette plage NE doit PAS chevaucher les plages d’adresses IP locales ou Azure. Elle est utilisée pour configurer la connectivité ER entre votre passerelle ExpressRoute de réseau virtuel Azure et les serveurs de grandes instances HANA. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. **Cette plage d’adresses IP doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**
  
- **Plage d’adresses du pool d’adresses IP de serveurs :** cette plage d’adresses IP est utilisée pour affecter l’adresse IP aux serveurs de grandes instances HANA. La taille de sous-réseau recommandée est un bloc CIDR /24, mais il est possible d’utiliser un bloc plus petit pourvu qu’il fournisse au minimum 64 adresses IP. Dans cette plage, les 30 premières adresses IP sont réservées à une utilisation par Microsoft. Veillez à en tenir compte lors du choix de la taille de la plage. Cette plage NE doit PAS chevaucher les adresses IP locales ou Azure. Comment obtenir cette plage d’adresses IP ? Votre équipe réseau ou votre fournisseur de services doit vous indiquer une plage d’adresses IP qui n’est pas utilisée actuellement à l’intérieur de votre réseau. Un bloc CIDR unique /24 (recommandé) à utiliser pour affecter les adresses IP spécifiques nécessaires à SAP HANA sur Azure (grandes instances). **Cette plage d’adresses IP doit être envoyée à Microsoft lorsque vous demandez un déploiement initial**
 
Bien qu’il soit nécessaire de définir et planifier les plages d’adresses IP ci-dessus, vous n’êtes pas tenu de toutes les transmettre à Microsoft. En résumé, voici les plages d’adresses IP que vous devrez nommer à Microsoft :

- Espace(s) d’adressage du réseau virtuel Azure
- Plage d’adresses pour les connexions ER-P2P
- Plage d’adresses du pool d’adresses IP de serveur

Si vous ajoutez d’autres réseaux virtuels qui doivent se connecter à des grandes instances HANA, vous devrez soumettre à Microsoft le nouvel espace d’adressage de réseau virtuel Azure que vous ajoutez. 

Voici un exemple des différentes plages ainsi que certains exemples de plages que vous devez configurer et éventuellement fournir à Microsoft. Comme vous pouvez le voir, la valeur de l’espace d’adressage de réseau virtuel Azure n’est pas agrégée dans le premier exemple, mais définie à partir des plages de la première plage d’adresses IP de sous-réseau de machines virtuelles Azure et de la plage d’adresses IP du sous-réseau de passerelle de réseau virtuel. Si vous utilisez plusieurs sous-réseaux de machines virtuelles dans le réseau virtuel Azure, vous devrez de la même manière configurer et soumettre les autres plages d’adresses IP des sous-réseaux de machines virtuelles supplémentaires dans le cadre de l’espace d’adressage de réseau virtuel Azure.

![Plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Vous avez également la possibilité d’agréger les données que vous envoyez à Microsoft. Dans ce cas, l’espace d’adressage du réseau virtuel Azure comprend un seul espace. Reprenons les plages d’adresses IP utilisées dans l’exemple précédent. Cet espace d’adressage de réseau virtuel agrégé pourrait ressembler à ceci :

![Deuxième possibilité de plages d’adresses IP nécessaires pour le déploiement minimal de SAP HANA sur Azure (grandes instances)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Comme vous pouvez le voir ci-dessus, au lieu des deux petites plages qui définissaient l’espace d’adressage du réseau virtuel Azure, nous avons maintenant une plus grande plage qui couvre les adresses IP 4096. En définissant un espace d’adressage aussi large, des plages relativement importantes restent inutilisées. Étant donné que les valeurs de l’espace d’adressage du réseau virtuel sont utilisées pour la propagation du routage BGP, l’utilisation des plages inutilisées en local ou ailleurs dans votre réseau peut provoquer des problèmes de routage. Il est donc recommandé de faire en sorte que l’espace d’adressage reste étroitement aligné avec l’espace d’adressage de sous-réseau réellement utilisé. Au besoin, vous pouvez toujours ajouter par la suite de nouvelles valeurs d’espace d’adressage sans que cela occasionne de temps d'arrêt sur le réseau virtuel.
 
> [!IMPORTANT] 
> Les plages d’adresses IP de l’espace d’adressage ER-P2P, du pool d’adresses IP du serveur et du réseau virtuel Azure **NE PEUVENT PAS** se chevaucher ou chevaucher d’autres plages utilisées ailleurs sur votre réseau. Chacune doit être isolée et, comme l’indiquent les deux graphiques ci-dessus, ne peut pas être un sous-réseau d’une autre plage. En cas de chevauchement entre les plages, le réseau virtuel Azure risque de ne pas pouvoir se connecter à un circuit ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Étapes à suivre après la définition des plages d’adresses
Une fois les plages d’adresses IP définies, vous devez procéder comme suit :

1. Envoyer les plages d’adresses IP correspondant à l’espace d’adressage du réseau virtuel Azure, à la connectivité ER-P2P et à la plage d’adresses du pool d’adresses IP du serveur, ainsi que les autres informations détaillées au début du présent document. À ce stade, vous pouvez également commencer à créer le réseau virtuel et les sous-réseaux de machines virtuelles. 
2. Un circuit Express Route est créé par Microsoft entre votre abonnement Azure et le tampon de grande instance HANA.
3. Un réseau locataire est créé sur le tampon de grande instance par Microsoft.
4. Microsoft configure la mise en réseau dans l’infrastructure de SAP HANA sur Azure (grandes instances) pour accepter les adresses IP de votre espace d’adressage de réseau virtuel Azure qui communiquent avec des grandes instances HANA.
5. En fonction de la référence SAP HANA sur Azure (grandes instances) spécifique achetée, Microsoft attribue une unité de calcul dans un réseau de locataire, allouez et montez le stockage et installez le système d’exploitation (SUSE ou Red Hat Linux). Les adresses IP correspondant à ces unités sont retirées de la plage d’adresses du pool d’adresses IP du serveur que vous avez envoyée à Microsoft.

À la fin du processus de déploiement, Microsoft vous transmet les données suivantes :
- Informations nécessaires pour connecter vos réseaux virtuels Azure au circuit ExpressRoute qui relie les réseaux virtuels Azure aux grandes instances HANA :
     - Clé(s) d’autorisation
     - PeerID ExpressRoute
- Données d’accès aux grandes instances HANA une fois le circuit ExpressRoute et le réseau virtuel Azure établis.

Vous trouverez également la séquence de connexion de grandes instances HANA dans le document [Installation de bout en bout des grandes instances SAP HANA](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Bon nombre des étapes suivantes sont illustrées par un exemple de déploiement dans ce document. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Connexion d’un réseau virtuel à une grande instance HANA ExpressRoute

Puisque vous avez défini toutes les plages d’adresses IP et que vous avez reçu les données de Microsoft, vous pouvez maintenant commencer à connecter le réseau virtuel créé aux grandes instances HANA. Une fois le réseau virtuel Azure créé, vous devez créer une passerelle ExpressRoute sur le réseau virtuel pour lier le réseau virtuel au circuit ExpressRoute qui se connecte au locataire du client sur la grande instance.

> [!NOTE] 
> Cette étape peut prendre jusqu'à 30 minutes, dans la mesure où la nouvelle passerelle est créée dans l’abonnement Azure désigné, puis connectée au réseau virtuel Azure spécifié.

S’il existe déjà une passerelle, vérifiez s’il s’agit d’une passerelle ExpressRoute. Si ce n’est pas le cas, la passerelle doit être supprimée et recréée comme passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, étant donné que le réseau virtuel Azure est déjà connecté au circuit ExpressRoute pour la connectivité locale, passez à la section Liaison des réseaux virtuels ci-dessous.

- Utilisez soit le (nouveau) [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
  - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel**, puis sélectionnez **ExpressRoute** comme type de passerelle.
  - Si vous avez choisi PowerShell, téléchargez et utilisez la dernière version du [kit de développement logiciel (SDK) Azure PowerShell](https://azure.microsoft.com/downloads/) pour garantir une expérience optimale. Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ correspond à des variables définies par l’utilisateur, qui doivent être mises à jour avec vos propres informations.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Dans cet exemple, la référence SKU de passerelle HighPerformance a été utilisée. Vous pouvez opter pour HighPerformance ou pour UltraPerformance, les seules références SKU de passerelle prises en charge pour SAP HANA sur Azure (grandes instances).

> [!IMPORTANT]
> Pour les grandes instances HANA dont les types de références SKU sont S384, S384m, S384xm, S576, S768 et S960 (références SKU de classe de Type II), l’utilisation de la référence SKU de passerelle UltraPerformance est obligatoire.

### <a name="linking-vnets"></a>Liaison de réseaux virtuels

Maintenant que le réseau virtuel Azure dispose d’une passerelle ExpressRoute, utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit ExpressRoute SAP HANA sur Azure (grandes instances) créé pour cette connexion. Vous pouvez effectuer cette opération à l’aide du portail Azure ou de PowerShell. L’utilisation du portail est recommandée, mais vous trouverez ci-dessous les instructions pour PowerShell. 

- Vous exécutez les commandes suivantes pour chaque passerelle de réseau virtuel, en utilisant un paramètre AuthGUID différent pour chaque connexion. Les deux premières entrées dans le script ci-dessous proviennent des informations fournies par Microsoft. En outre, le paramètre AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle. Cela signifie que, si vous souhaitez ajouter un autre réseau virtuel Azure, vous devez obtenir un autre AuthID pour votre circuit ExpressRoute qui connecte les grandes instances HANA dans Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Si vous souhaitez connecter la passerelle à plusieurs circuits ExpressRoute associés à votre abonnement, vous devrez peut-être exécuter plusieurs fois cette étape. Par exemple, vous allez probablement connecter la même passerelle de réseau virtuel au circuit ExpressRoute qui relie le réseau virtuel à votre réseau local.

## <a name="adding-more-ip-addresses-or-subnets"></a>Ajout d’adresses IP ou sous-réseaux supplémentaires

Utilisez le portail Azure, PowerShell ou l’interface de ligne de commande pour ajouter des adresses IP ou des sous-réseaux.

Dans ce cas, nous vous recommandons d’ajouter la nouvelle plage d’adresses IP en tant que nouvelle plage à l’espace d’adressage du réseau virtuel, au lieu de générer une nouvelle plage agrégée. Dans les deux cas, vous devez envoyer cette modification à Microsoft pour autoriser la connectivité depuis cette plage d’adresses IP vers les unités de grandes instances HANA dans votre client. Vous pouvez ouvrir une demande de support Azure pour obtenir l’ajout du nouvel espace d’adressage de réseau virtuel. Après avoir reçu la confirmation, effectuez les étapes suivantes.

Pour créer un sous-réseau supplémentaire à partir du portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), et pour le créer à partir de PowerShell, consultez [Créer un réseau virtuel à l’aide de PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Ajout de réseaux virtuels

Après la connexion initiale d’un ou plusieurs réseaux virtuels Azure, vous voudrez peut-être ajouter d’autres réseaux virtuels qui accèdent à SAP HANA sur Azure (grandes instances). Tout d’abord, envoyez une demande de support Azure, dans laquelle vous incluez à la fois les informations propres au déploiement Azure et la ou les plages d’espace d’adressage IP de l’espace d’adressage du réseau virtuel Azure. SAP HANA sur Azure Service Management fournit alors les informations nécessaires à la connexion des réseaux virtuels supplémentaires et d’ExpressRoute. Pour chaque réseau virtuel, vous avez besoin d’une clé d’autorisation unique pour connecter le circuit ExpressRoute à de grandes instances HANA.

Procédure d’ajout d’un nouveau réseau virtuel Azure :

1. Effectuez la première étape du processus d’intégration ; voir la section _Création d’un réseau virtuel Azure_.
2. Effectuez la deuxième étape du processus d’intégration ; voir la section _Création d’un sous-réseau de passerelle_.
3. Pour connecter vos réseaux virtuels supplémentaires au circuit ExpressRoute de grande instance HANA, ouvrez une demande de support Azure contenant les informations sur le nouveau réseau virtuel, et demandez une nouvelle clé d’autorisation.
4. Une fois informé que l’autorisation est accordée, utilisez les informations d’autorisation fournies par Microsoft pour finaliser la troisième étape du processus d’intégration, voir la section _Liaison de réseaux virtuels_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Augmentation de la bande passante d’un circuit ExpressRoute

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’augmenter la bande passante du circuit ExpressRoute de SAP HANA sur Azure (grandes instances), créez une demande de support Azure. (Vous pouvez demander une augmentation maximale de 10 Gbits/s pour une seule bande passante de circuit.) Vous recevrez ensuite une notification une fois l’opération terminée ; aucune action supplémentaire n’est nécessaire pour activer cette vitesse supérieure dans Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Ajout d’un circuit ExpressRoute supplémentaire

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’ajouter un circuit ExpressRoute supplémentaire, envoyez une demande de support Azure pour créer un nouveau circuit ExpressRoute (et pour obtenir les informations d’autorisation permettant de s’y connecter). L’espace d’adressage qui va être utilisé sur les réseaux virtuels doit être défini avant d’effectuer la demande, afin que SAP HANA sur Azure Service Management puisse fournir l’autorisation.

Une fois le nouveau circuit créé et la configuration de SAP HANA sur Azure Service Management terminée, vous allez recevoir une notification avec les informations nécessaires pour continuer. Suivez les étapes ci-dessus pour créer et connecter le nouveau réseau virtuel à ce circuit supplémentaire. Vous ne pouvez pas connecter des réseaux virtuels Azure à ce circuit supplémentaire s’ils sont déjà connectés à un autre circuit ExpressRoute SAP HANA sur Azure (grande instance) dans la même région Azure.

## <a name="deleting-a-subnet"></a>Suppression d’un sous-réseau

Pour supprimer un sous-réseau de réseau virtuel, vous pouvez utiliser le portail Azure, PowerShell ou l’interface de ligne de commande. Si votre plage d’adresses IP de réseau virtuel Azure/espace d’adressage de réseau virtuel Azure était une plage agrégée, vous ne recevrez aucun suivi de la part de Microsoft. Toutefois, le réseau virtuel continue à propager l’espace d’adressage de l’itinéraire BGP qui inclut le sous-réseau supprimé. Si vous avez défini la plage d’adresses IP de réseau virtuel Azure/l’espace d’adressage de réseau virtuel Azure sous la forme de plusieurs plages d’adresses IP, dont l’une a été affectée à votre sous-réseau supprimé, vous devrez la supprimer de votre espace d’adressage de réseau virtuel et en informer l’équipe de gestion des services SAP HANA sur Azure pour qu’elle la supprime des plages avec lesquelles les grandes instances SAP HANA sur Azure sont autorisées à communiquer.

Pour supprimer un sous-réseau, consultez [Supprimer un sous-réseau](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) pour plus d’informations sur la création de sous-réseaux.

## <a name="deleting-a-vnet"></a>Suppression d’un réseau virtuel

Pour supprimer un réseau virtuel, consultez [Supprimer un réseau virtuel](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). L’équipe de gestion des services SAP HANA sur Azure supprime les autorisations existantes sur le circuit ExpressRoute de SAP HANA sur Azure (grandes instances) et supprime la plage d’adresses IP du réseau virtuel Azure/l’espace d’adressage du réseau virtuel Azure pour la communication avec les grandes instances HANA.

Une fois que le réseau virtuel a été supprimé, ouvrez une demande de support Azure pour fournir la ou les plages d’adresses IP à supprimer.

Pour vous assurer que tout est supprimé, supprimez les éléments suivants :

- La connexion ExpressRoute, la passerelle de réseau virtuel, l’adresse IP publique de la passerelle de réseau virtuel et le réseau virtuel

## <a name="deleting-an-expressroute-circuit"></a>Suppression d’un circuit ExpressRoute

Pour supprimer un circuit supplémentaire ExpressRoute de SAP HANA sur Azure (grandes instances), ouvrez une demande de support Azure avec SAP HANA sur Azure Service Management et demandez à ce que le circuit soit supprimé. Dans l’abonnement Azure, vous pouvez supprimer ou conserver le réseau virtuel, en fonction des besoins. Toutefois, vous devez supprimer la connexion entre le circuit ExpressRoute des grandes instances HANA et la passerelle de réseau virtuel liée.

Si vous souhaitez également supprimer un réseau virtuel, suivez les instructions du paragraphe Suppression d’un réseau virtuel dans la section précédente.


