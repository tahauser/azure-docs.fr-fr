---
title: 'Services de domaine Azure AD : instructions de mise en réseau | Microsoft Docs'
description: Considérations relatives à la mise en réseau pour les services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: eee7905db4faedef3217118e8d491e2cb019fa30
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considérations relatives à la mise en réseau pour les services de domaine Azure AD
## <a name="how-to-select-an-azure-virtual-network"></a>Comment sélectionner un réseau virtuel Azure
Les instructions suivantes vous aident à sélectionner un réseau virtuel en vue de l’utiliser avec les services de domaine Azure AD.

### <a name="type-of-azure-virtual-network"></a>Type de réseau virtuel Azure
* **Réseaux virtuels basés sur Resource Manager** : Il est possible d’activer les services de domaine Azure AD dans les réseaux virtuels créés à l’aide de Azure Resource Manager.
* Vous ne pouvez pas activer Azure AD Domain Services dans un réseau virtuel Azure Classic.
* Vous pouvez connecter d’autres réseaux virtuels à celui dans lequel Azure AD Domain Services est activé. Pour plus d’informations, consultez la section [Connectivité réseau](active-directory-ds-networking.md#network-connectivity).

### <a name="azure-region-for-the-virtual-network"></a>Région Azure pour le réseau virtuel
* Votre domaine géré par les services de domaine Azure AD est déployé dans la même région Azure que le réseau virtuel dans lequel vous choisissez d’activer le service.
* Sélectionnez un réseau virtuel dans une région Azure prise en charge par les services de domaine Azure AD.
* Pour connaître les régions Azure dans lesquelles les services de domaine Azure AD sont disponibles, consultez la page [Services Azure par région](https://azure.microsoft.com/regions/#services/) .

### <a name="requirements-for-the-virtual-network"></a>Conditions préalables pour le réseau virtuel
* **Proximité avec les charges de travail Azure**: sélectionnez le réseau virtuel qui héberge actuellement ou qui hébergera des machines virtuelles ayant besoin d’accéder aux services de domaine Azure AD. Si vos charges de travail sont déployées dans un autre réseau virtuel que le domaine managé, vous pouvez également choisir de connecter les réseaux virtuels.
* **Serveurs DNS personnalisés/propres**: assurez-vous qu’aucun serveur DNS personnalisé n’est configuré pour le réseau virtuel. Citons comme exemple de serveur DNS personnalisé une instance de DNS Windows Server en cours d’exécution sur une machine virtuelle Windows Server déployée sur le réseau virtuel. Azure AD Domain Services ne fait l’objet d’aucune intégration avec les serveurs DNS personnalisés déployés au sein du réseau virtuel.
* **Domaines existants portant le même nom de domaine** : vérifiez qu’aucun domaine existant portant le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.

> [!WARNING]
> Vous ne pouvez pas déplacer les services de domaine vers un autre réseau virtuel une fois le service activé.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Indications pour le choix d’un sous-réseau

![Conception de sous-réseau recommandée](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Déployez les services de domaine Azure AD dans un **sous-réseau dédié distinct** au sein de votre réseau virtuel Azure.
* N’appliquez pas de groupes de sécurité réseau au sous-réseau dédié pour votre domaine géré. Si vous devez appliquer des groupes de sécurité réseau au sous-réseau dédié, veillez à **ne pas bloquer les ports requis pour l’entretien et la gestion de votre domaine**.
* Ne limitez pas de manière excessive le nombre d’adresses IP disponibles au sein du sous-réseau dédié de votre domaine géré. Cette limitation empêche le service de mettre à disposition deux contrôleurs de domaine pour votre domaine géré.
* **N’activez pas les services de domaine Azure AD dans le sous-réseau de passerelle** de votre réseau virtuel.
* Ne bloquez pas l’accès sortant du sous-réseau dans lequel votre domaine managé est activé.

> [!WARNING]
> Lorsque vous associez un groupe de sécurité réseau et un sous-réseau dans lequel les services de domaine Azure AD sont activés, vous pouvez affecter la capacité de Microsoft à gérer le domaine. En outre, la synchronisation entre votre client Azure AD et votre domaine géré est interrompue. **Le contrat de niveau de service (SLA) ne concerne pas les déploiements dans lesquels un groupe de sécurité réseau a été appliqué et qui empêche les services de domaine Azure AD de mettre à jour et de gérer votre domaine.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Ports requis pour les services de domaine Azure AD
Les ports suivants sont requis pour les services de domaine Azure AD pour l’entretien et la gestion de votre domaine géré. Assurez-vous que ces ports ne sont pas bloqués pour le sous-réseau dans lequel vous avez activé votre domaine géré.

| Numéro de port | Requis ? | Objectif |
| --- | --- | --- |
| 443 | Obligatoire |Synchronisation avec votre client Azure AD |
| 5986 | Obligatoire | Gestion de votre domaine |
| 3389 | Facultatif | Gestion de votre domaine |
| 636 | Facultatif | Sécuriser l’accès LDAP (LDAPS) à votre domaine géré |

**Port 443 (synchronisation avec Azure AD)**
* Il est utilisé pour synchroniser votre annuaire Azure AD avec votre domaine managé.
* Il est obligatoire pour autoriser l’accès à ce port dans votre groupe de sécurité réseau. Sans accès à ce port, votre domaine managé n’est pas synchronisé avec votre annuaire Azure AD. Les utilisateurs risquent de ne pas pouvoir se connecter, car les modifications apportées à leurs mots de passe ne sont pas synchronisées avec votre domaine managé.
* Vous pouvez limiter l’accès entrant à ce port aux adresses IP appartenant à la plage d’adresses IP Azure.

**Port 5986 (communication à distance PowerShell)**
* Il est utilisé pour effectuer des tâches de gestion à l’aide de la communication à distance PowerShell sur votre domaine managé.
* Il est obligatoire pour autoriser l’accès via ce port dans votre groupe de sécurité réseau. Sans accès à ce port, votre domaine managé ne peut pas être mis à jour, configuré, sauvegardé ou surveillé.
* Vous pouvez restreindre l’accès entrant à ce port aux adresses IP sources suivantes : 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161
* Généralement, les contrôleurs de domaine pour votre domaine géré n’écoutent pas sur ce port. Le service ouvre ce port sur les contrôleurs de domaine gérés uniquement lorsqu’une opération de gestion ou de maintenance doit être effectuée pour le domaine géré. Une fois l’opération terminée, le service ferme ce port sur les contrôleurs de domaine gérés.

**Port 3389 (Bureau à distance)**
* Il est utilisé pour les connexions Bureau à distance aux contrôleurs de domaine de votre domaine managé.
* L’ouverture de ce port via votre groupe de sécurité réseau est facultative.
* Ce port reste également désactivé en grande partie sur votre domaine géré. Ce mécanisme n’est pas utilisé de manière continue, car les tâches de gestion et de surveillance sont effectuées à l’aide de la communication à distance PowerShell. Ce port est utilisé uniquement dans les rares cas où Microsoft a besoin de se connecter à distance à votre domaine managé pour un dépannage avancé. Le port est fermé dès que l’opération de résolution des problèmes est terminée.

**Port 636 (LDAP sécurisé)**
* Il est utilisé pour activer ou désactiver l’accès LDAP sécurisé à votre domaine managé sur Internet.
* L’ouverture de ce port via votre groupe de sécurité réseau est facultative. Ouvrez le port uniquement si vous disposez d’un accès LDAP sécurisé sur Internet activé.
* Vous pouvez limiter l’accès entrant à ce port aux adresses IP sources à partir desquelles vous pensez vous connecter via un accès LDAP sécurisé.

**Accès sortant** : Azure AD Domain Services a besoin d’un accès sortant à différents services Azure afin de gérer, de sauvegarder et de surveiller votre domaine managé. Ne bloquez pas l’accès sortant du sous-réseau dédié dans lequel votre domaine managé est activé.


## <a name="network-security-groups"></a>Network Security Group
Un [groupe de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité par l’association d’un groupe de sécurité réseau directement à la machine virtuelle.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exemple de groupe de sécurité réseau pour les réseaux virtuels avec Azure AD Domain Services
Le tableau suivant illustre un exemple de groupe de sécurité réseau que vous pouvez configurer pour un réseau virtuel avec un domaine managé Azure AD Domain Services. Cette règle autorise le trafic entrant au travers des ports nécessaires pour garantir que votre domaine managé reste corrigé, mis à jour et peut être surveillé par Microsoft. La règle « DenyAll » par défaut s’applique à tout autre trafic entrant en provenance d’internet.

En outre, le groupe de sécurité réseau illustre comment verrouiller l’accès LDAP sécurisé sur Internet. Ignorez cette règle si vous n’avez pas activé l’accès LDAP sécurisé à votre domaine managé sur Internet. Le groupe de sécurité réseau contient un ensemble de règles qui autorisent l’accès LDAPS entrant sur le port TCP 636 uniquement à partir d’un ensemble spécifique d’adresses IP. La règle de groupe de sécurité réseau pour autoriser l’accès LDAPS via Internet à partir d’adresses IP spécifiées prend le pas sur la règle DenyAll NSG.

![Exemple de groupe de sécurité réseau pour l’accès LDAP sécurisé via internet](.\media\active-directory-domain-services-alerts\default-nsg.png)

**Informations supplémentaires** - [Créer un groupe de sécurité réseau](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).


## <a name="network-connectivity"></a>Connectivité réseau
Un domaine managé Azure AD Domain Services ne peut être activé qu’au sein d’un même réseau virtuel dans Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Scénarios de connexion de réseaux Azure
Connectez des réseaux virtuels Azure en vue d’utiliser le domaine géré dans n’importe lequel des scénarios de déploiement suivants :

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Utiliser le domaine managé dans plusieurs réseaux virtuels Azure
Vous pouvez connecter d’autres réseaux virtuels Azure à celui dans lequel vous avez activé Azure AD Domain Services. Cette connexion VPN/VNET Peering vous permet d’utiliser le domaine managé avec vos charges de travail déployées sur d’autres réseaux virtuels.

![Connexion entre des réseaux virtuels Classic](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utiliser le domaine géré dans un réseau virtuel basé sur Resource Manager
Vous pouvez connecter un réseau virtuel basé sur Resource Manager au réseau virtuel Azure Classic dans lequel vous avez activé les services de domaine Azure AD. Cette connexion vous permet d’utiliser le domaine géré avec les charges de travail déployées dans le réseau virtuel basé sur Resource Manager.

![Connexion entre un réseau virtuel Resource Manager et un réseau virtuel Classic](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Options de connexion réseau
* **Connexion entre deux réseaux virtuels à l’aide d’une homologation de réseaux virtuels**: l’homologation de réseaux virtuels est un mécanisme permettant de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau pour tous les besoins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans ces réseaux virtuels peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

    ![Connexion entre des réseaux virtuels à l’aide d’une homologation](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Plus d’informations : homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md)

* **Connexion entre deux réseaux virtuels à l’aide d’une connexion VPN de site à site**: la connexion entre deux réseaux virtuels est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE.

    ![Connexion entre des réseaux virtuels à l’aide d’une passerelle VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Plus d’informations : connecter des réseaux virtuels à l’aide d’une passerelle VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Contenu connexe
* [Homologation de réseaux virtuels Azure](../virtual-network/virtual-network-peering-overview.md)
* [Configurer une connexion de réseau virtuel à réseau virtuel pour le modèle de déploiement classique](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Groupes de sécurité réseau Azure](../virtual-network/virtual-networks-nsg.md)
* [Créer des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
