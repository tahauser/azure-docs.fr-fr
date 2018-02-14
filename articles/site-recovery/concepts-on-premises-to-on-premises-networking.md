---
title: "Configurer l’adressage IP pour se connecter après le basculement vers un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment configurer l’adressage IP pour se connecter à des machines virtuelles après le basculement sur un site secondaire avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 6baeda08b1c41cc024a02f51ca27be2829c46962
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-a-secondary-site"></a>Configurer l’adressage IP pour se connecter après le basculement sur un site secondaire

Après avoir passé en revue les prérequis pour le déploiement, lisez cet article pour planifier la mise en réseau lors de la réplication de machines virtuelles Hyper-V gérées dans les clouds System Center Virtual Machines Manager (VMM) sur un site secondaire à l’aide [d’Azure Site Recovery](site-recovery-overview.md) dans le portail Azure. 

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d'ensemble

Lorsque vous planifiez votre stratégie de basculement et de réplication, l’une des questions clés est de savoir comment se connecter au réplica après le basculement. Vous disposez de deux options : 

- **Utiliser une adresse IP différente** : vous pouvez choisir d’utiliser une adresse IP différente pour la machine virtuelle répliquée. Dans ce scénario, la machine virtuelle reçoit une nouvelle adresse IP après le basculement, et une mise à jour DNS est nécessaire.
- **Conserver la même adresse IP** : vous pouvez choisir d’utiliser la même adresse IP pour la machine virtuelle réplica. La conservation des mêmes adresses IP simplifie la récupération en réduisant les problèmes liés au réseau après le basculement. 

## <a name="retaining-ip-addresses"></a>Conservation des adresses IP

Si vous souhaitez conserver les adresses IP à partir du site principal après le basculement vers le site secondaire, vous pouvez effectuer un basculement de sous-réseau complet et mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP, ou vous pouvez déployer un sous-réseau étiré entre le site principal et le site de récupération.

### <a name="stretched-subnet"></a>Sous-réseau étiré

Dans un sous-réseau étiré, le sous-réseau est disponible simultanément sur le site principal et le site secondaire. Si vous déplacez un serveur et sa configuration IP (couche 3) sur le site secondaire, le réseau achemine automatiquement le trafic vers le nouvel emplacement. 

En ce qui concerne la couche 2 (couche de liaison de données), vous devez disposer d’un équipement réseau qui peut gérer un VLAN étiré. De plus, en étirant le VLAN, le domaine d’erreur potentielle s’étend aux deux sites, et devient essentiellement un point de défaillance unique. Bien que cela soit peu probable, il est possible qu’une tempête de diffusion commence sans pouvoir être isolée. 


### <a name="subnet-failover"></a>Basculement de sous-réseau

Vous pouvez exécuter un basculement de sous-réseau pour bénéficier des avantages du sous-réseau étiré, sans réellement l’étirer. Dans cette solution, un sous-réseau est disponible sur le site source ou cible, mais pas les deux simultanément. Pour conserver l'espace d'adressage IP en cas de basculement, vous pouvez mettre en place un programme pour que l'infrastructure du routeur déplace les sous-réseaux d'un site à un autre. Après le basculement, les sous-réseaux se déplacent avec les machines virtuelles associées. Le principal inconvénient est qu’en cas de défaillance, vous devez déplacer le sous-réseau tout entier.

### <a name="example"></a>Exemple

Voici un exemple de basculement de sous-réseau complet. Le site principal possède des applications qui s'exécutent dans le sous-réseau 192.168.1.0/24. Lors du basculement, toutes les machines virtuelles dans ce sous-réseau sont basculées sur le site secondaire et conservent leurs adresses IP. Les itinéraires doivent être modifiés pour refléter le fait que toutes les machines virtuelles appartenant au sous-réseau 192.168.1.0/24 ont été déplacées vers le site secondaire.

Les graphiques suivants illustrent les sous-réseaux avant et après le basculement :

- Avant le basculement, le sous-réseau 192.168.0.1/24 est actif sur le site source et devient actif sur le site secondaire après le basculement.
- Les itinéraires existant entre le site principal et le site de récupération, le troisième site et le site principal, et entre le troisième site et le site de récupération doivent être modifiés comme il convient.

**Avant le basculement**

![Avant le basculement](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Après le basculement**

![Après le basculement](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Voici ce qui se passe après le basculement :

- Site Recovery alloue une adresse IP pour chaque interface réseau sur la machine virtuelle à partir du pool d'adresses IP statiques défini sur le réseau approprié pour chaque instance VMM.
- Si le pool d’adresses IP sur le site secondaire est identique à celui sur le site source, Site Recovery alloue la même adresse IP (de la machine virtuelle source) à la machine virtuelle réplica. L’adresse IP est réservée dans VMM, mais n’est pas définie comme l’adresse IP de basculement sur l’hôte Hyper-V. L’adresse IP de basculement sur un hôte Hyper-V est définie juste avant le basculement.
- Si la même adresse IP n'est pas disponible, Site Recovery alloue une autre adresse IP disponible du pool.
- Si les machines virtuelles utilisent DHCP, Site Recovery ne gère pas les adresses IP. Vous devez vérifier que le serveur DHCP sur le site secondaire peut allouer une adresse à partir de la même plage que le site source.

### <a name="validate-the-ip-address"></a>Valider l’adresse IP

Une fois la protection activée pour une machine virtuelle, vous pouvez utiliser l’exemple de script suivant pour vérifier l’adresse attribuée à la machine virtuelle. La même adresse IP est définie comme adresse IP de basculement et est attribuée à la machine virtuelle au moment du basculement :

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>Modifier les adresses IP

Dans ce scénario, les adresses IP des machines virtuelles basculées sont modifiées. L’inconvénient de cette solution est la maintenance requise. En règle générale, DNS sera mis à jour après le démarrage des machines virtuelles de réplica. Les entrées DNS devront peut-être être modifiées ou éliminées sur le réseau, et les entrées en cache mises à jour. Cela peut entraîner un temps d’arrêt. Le temps d’arrêt peut être atténué comme suit :

- Utilisez des valeurs TTL faibles pour les applications intranet.
- Utilisez le script suivant dans un plan de récupération Site Recovery pour mettre à jour le serveur DNS et garantir une mise à jour en temps voulu. Vous n’avez pas besoin du script si vous utilisez l’inscription DNS dynamique.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exemple 

Consultons un scénario dans lequel vous envisagez d’utiliser des adresses IP différentes sur le site principal et le site de récupération. Dans cet exemple, nous avons des adresses IP différentes entre le site principal et le site secondaire, et il existe un troisième site à partir duquel il est possible d’accéder aux applications hébergées sur le site principal ou le site de récupération.

- Avant le basculement, les applications sont hébergées sur le sous-réseau 192.168.1.0/24 sur le site principal et sont configurées pour être hébergées sur le sous-réseau 172.16.1.0/24 sur le site secondaire après un basculement.
- Les itinéraires du réseau/des connexions VPN ont été correctement configurés pour que les trois sites puissent accéder l’un à l'autre.
- Après le basculement, les applications sont restaurées sur le sous-réseau de récupération. Dans ce scénario, il n’est pas nécessaire de basculer tout le sous-réseau et aucune modification n’est requise pour reconfigurer les itinéraires réseau ou VPN. Le basculement et certaines mises à jour DNS garantissent le maintien de l’accessibilité des applications.
- Si le système DNS est configuré pour autoriser des mises à jour dynamiques, les machines virtuelles peuvent alors s’inscrire à l’aide de la nouvelle adresse IP une fois qu’elles démarrent après un basculement.

**Avant le basculement**

![Autre adresse IP - avant le basculement](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Après le basculement**

![Autre adresse IP - après le basculement](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




