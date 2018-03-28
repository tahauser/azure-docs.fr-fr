---
title: Préparer des serveurs Hyper-V locaux à la récupération d’urgence de machines virtuelles Hyper-V vers Azure | Microsoft Docs
description: Découvrez comment préparer des machines virtuelles Hyper-V locales non gérées par System Center VMM à la récupération d’urgence vers Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1290a186ca8e83b09f53b286e80c5ce75f08d88c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Préparer des serveurs Hyper-V locaux à la récupération d’urgence vers Azure

Ce didacticiel vous montre comment préparer votre infrastructure Hyper-V locale quand vous souhaitez répliquer des machines virtuelles Hyper-V vers Azure, dans le cadre de la récupération d’urgence. Les hôtes Hyper-V peuvent être gérés par System Center Virtual Machine Manager (VMM), mais ce n’est pas une condition obligatoire.  Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Examinez les exigences pour Hyper-V et VMM, le cas échéant.
> * Préparez VMM, le cas échéant
> * Vérifiez l’accès Internet aux emplacements Azure
> * Préparez les machines virtuelles afin que vous puissiez y accéder après le basculement vers Azure

Ce didacticiel est le deuxième de la série. Assurez-vous d’avoir[configuré les composants Azure](tutorial-prepare-azure.md) comme décrit dans le didacticiel précédent.



## <a name="review-requirements-and-prerequisites"></a>Réviser les exigences et les composants requis

Vérifiez que les machines virtuelles et hôtes VMware respectent les exigences.

1. [Vérifiez](hyper-v-azure-support-matrix.md#on-premises-servers) les exigences relatives au serveur local.
2. [Vérifiez les exigences](hyper-v-azure-support-matrix.md#replicated-vms) relatives aux machines virtuelles Hyper-V à répliquer vers Azure.
3. Vérifiez la [mise en réseau](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) de l’hôte Hyper-V ; et la prise en charge du [stockage](hyper-v-azure-support-matrix.md#hyper-v-host-storage) de l’hôte et de l’invité pour les hôtes Hyper-V locaux.
4. Vérifiez ce qui est pris en charge pour la [mise en réseau Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), le [stockage](hyper-v-azure-support-matrix.md#azure-storage) et le [calcul](hyper-v-azure-support-matrix.md#azure-compute-features) après le basculement.
5. Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les [exigences relatives aux machines virtuelles Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Préparer VMM (facultatif)

Si les hôtes Hyper-V sont gérés par VMM, vous devez préparer le serveur VMM local. 

- Vérifiez que le serveur VMM a un moins un cloud, avec un ou plusieurs groupes hôtes. L’hôte Hyper-V sur lequel les machines virtuelles s’exécutent doivent se trouver dans le cloud.
- Préparez le serveur VMM pour le mappage réseau.

### <a name="prepare-vmm-for-network-mapping"></a>Préparer VMM pour le mappage réseau

Si vous utilisez VMM, le [mappage réseau](site-recovery-network-mapping.md) effectue un mappage entre les réseaux de machines virtuelles VMM locales et les réseaux virtuels Azure. Le mappage garantit que les machines virtuelles Azure soient connectées au réseau adéquat lorsqu’elles sont créées après le basculement.

Préparez VMM pour le mappage réseau comme suit :

1. Vérifiez que vous disposez d’un [réseau logique VMM](https://docs.microsoft.com/system-center/vmm/network-logical) qui est associé au cloud dans lequel se trouvent les hôtes Hyper-V.
2. Assurez-vous d’avoir un [réseau de machines virtuelles](https://docs.microsoft.com/system-center/vmm/network-virtual) liées au réseau logique.
3. Dans VMM, connectez les machines virtuelles au réseau de machines virtuelles.

## <a name="verify-internet-access"></a>Vérifiez l’accès à Internet

1. Pour les besoins de ce didacticiel, la configuration la plus simple pour les hôtes Hyper-V et le serveur VMM, le cas échéant, est d’accéder directement à internet sans utiliser de proxy. 
2. Assurez-vous que les hôtes Hyper-V et le serveur VMM, le cas échéant, peuvent accéder à ces URL : 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Assurez-vous que :
    - Toutes les règles de pare-feu basées sur une adresse IP doivent autoriser les communications vers Azure.
    - Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Durant un scénario de basculement, vous pouvez vous connecter à votre réseau local répliqué.

Pour vous connecter à des machines virtuelles Windows à l’aide de RDP après le basculement, effectuez les opérations suivantes :

1. Pour accéder via Internet, activez RDP sur la machine virtuelle locale avant le basculement. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public**, et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour accéder via un VPN de site à site, activez RDP sur la machine locale. RDP doit être autorisé dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**.
   Vérifiez que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](https://support.microsoft.com/kb/3031135) Aucune mise à jour de Windows ne doit être en attente sur la machine virtuelle quand vous déclenchez un basculement. S’il y en a, vous ne pouvez pas vous connecter à la machine virtuelle avant la fin de la mise à jour.
3. Sur la machine virtuelle Azure Windows, après le basculement, vérifiez les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution et lisez ces [conseils de résolution des problèmes](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Après le basculement, vous pouvez accéder à des machines virtuelles Azure à l’aide de la même adresse IP en tant que machine virtuelle répliquée locale ou une autre adresse IP. [En savoir plus](concepts-on-premises-to-azure-networking.md) sur la configuration d’adressage IP pour le basculement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence dans Azure pour les machines virtuelles Hyper-V](tutorial-hyper-v-to-azure.md)
> [Configurer la récupération d’urgence dans Azure pour les machines virtuelles Hyper-V dans les clouds VMM](tutorial-hyper-v-vmm-to-azure.md)
