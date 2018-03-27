---
title: Préparer des serveurs VMware locaux à la récupération d’urgence de machines virtuelles VMware vers Azure | Microsoft Docs
description: Découvrez comment préparer des serveurs VMware à la récupération d’urgence vers Azure avec le service Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6898f725d1d3cbf3f8d9d90faeafc13fbc8cb201
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Préparer des serveurs VMware locaux à la récupération d’urgence vers Azure

Ce didacticiel vous montre comment préparer votre infrastructure VMware locale quand vous souhaitez répliquer des machines virtuelles VMware vers Azure. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer un compte sur le serveur vCenter ou sur l’hôte vSphere ESXi pour automatiser la détection de machines virtuelles
> * Préparer un compte pour l’installation automatique du service Mobilité sur les machines virtuelles VMware
> * Vérifier les exigences des serveurs VMware
> * Vérifier les exigences des machines virtuelles VMware

Dans cette série de didacticiels, nous expliquons comment sauvegarder une seule machine virtuelle avec Azure Site Recovery. Si vous envisagez de protéger plusieurs machines virtuelles VMware, vous devez télécharger [l’outil Planificateur de déploiement](https://aka.ms/asr-deployment-planner) pour la réplication de VMware. Cet outil donne des informations sur la compatibilité des machines virtuelles, le nombre de disques par machine virtuelle et l’activité des données par disque. L’outil couvre également les exigences en bande passante du réseau et l’infrastructure Azure requise pour la réussite de la réplication et du test de basculement. [En savoir plus](site-recovery-deployment-planner.md) sur l’exécution de cet outil.

Ce didacticiel est le deuxième de la série. Assurez-vous d’avoir[configuré les composants Azure](tutorial-prepare-azure.md) comme décrit dans le didacticiel précédent.

## <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Détecter automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Vous avez besoin d’un compte pouvant exécuter des opérations telles que la création et la suppression de disques ou le démarrage de machines virtuelles.

Créez le compte comme suit :

1. Pour utiliser un compte dédié, créez un rôle au niveau du vCenter. Donnez un nom au rôle, tel que **Azure_Site_Recovery**.
2. Attribuez au rôle les autorisations résumées dans le tableau ci-dessous.
3. Créez un utilisateur sur le serveur vCenter ou sur l’hôte vSphere. Affectez le rôle à l’utilisateur.

### <a name="vmware-account-permissions"></a>Autorisations du compte VMware

**Tâche** | **Rôle/Autorisations** | **Détails**
--- | --- | ---
**Détection des machines virtuelles** | Au moins un utilisateur en lecture seule<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
**Réplication complète, basculement, restauration automatique** |  Créez un rôle (Azure_Site_Recovery) avec les autorisations nécessaires, puis attribuez le rôle à un utilisateur ou à un groupe d’utilisateurs VMware<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur la machine virtuelle que vous souhaitez répliquer. Site Recovery installe ce service automatiquement quand vous activez la réplication pour la machine virtuelle. Pour une installation automatique, vous devez préparer un compte qui sera utilisé par Site Recovery pour accéder à la machine virtuelle. Vous devez spécifier ce compte quand vous configurez la récupération d’urgence dans la console Azure.

1. Préparez un domaine ou un compte local avec les autorisations nécessaires pour l’installation sur la machine virtuelle.
2. Pour une installation sur des machines virtuelles Windows, si vous n’utilisez pas un compte de domaine, désactivez le contrôle d’accès des utilisateurs distants sur la machine locale.
   - Dans le Registre, sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** avec une valeur de 1.
3. Pour une installation sur des machines virtuelles Linux, préparez un compte racine sur le serveur Linux source.


## <a name="check-vmware-requirements"></a>Vérifier les conditions requises VMware

Vérifiez que les machines virtuelles et serveurs VMware respectent les conditions requises.

1. [Vérifiez](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) les conditions requises pour les serveurs VMware.
2. Pour Linux, [vérifiez](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) les conditions requises en matière de système de fichiers et stockage. 
3. Vérifiez la prise en charge du [réseau](vmware-physical-azure-support-matrix.md#network) et du [stockage](vmware-physical-azure-support-matrix.md#storage) locaux. 
4. Vérifiez ce qui est pris en charge pour la [mise en réseau Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), le [stockage](vmware-physical-azure-support-matrix.md#azure-storage) et le [calcul](vmware-physical-azure-support-matrix.md#azure-compute) après le basculement.
5. Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les [conditions requises pour les machines virtuelles Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Préparer la connexion aux machines virtuelles Azure après le basculement

Durant un scénario de basculement, imaginons que vous souhaitiez vous connecter à des machines virtuelles répliquées dans Azure à partir de votre réseau local.

Pour vous connecter à des machines virtuelles Windows à l’aide de RDP après le basculement, effectuez les opérations suivantes :

1. Pour accéder via Internet, activez RDP sur la machine virtuelle locale avant le basculement. Vérifiez que des règles TCP et UDP sont ajoutées pour le profil **Public**, et que RDP est autorisé dans **Pare-feu Windows** > **Applications autorisées** pour tous les profils.
2. Pour accéder via un VPN de site à site, activez RDP sur la machine locale. RDP doit être autorisé dans **Pare-feu Windows** -> **Applications et fonctionnalités autorisées** pour les réseaux **Domaine et Privé**.
   Vérifiez que la stratégie SAN du système d’exploitation est définie sur **OnlineAll**. [Plus d’informations](https://support.microsoft.com/kb/3031135) Aucune mise à jour de Windows ne doit être en attente sur la machine virtuelle quand vous déclenchez un basculement. S’il y en a, vous ne pouvez pas vous connecter à la machine virtuelle avant la fin de la mise à jour.
3. Sur la machine virtuelle Azure Windows, après le basculement, vérifiez les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle. Si vous ne pouvez pas vous connecter, vérifiez que la machine virtuelle est en cours d’exécution et lisez ces [conseils de résolution des problèmes](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Pour vous connecter à des machines virtuelles Linux à l’aide de SSH après le basculement, effectuez les opérations suivantes :

1. Sur la machine locale, avant le basculement, vérifiez que le service Secure Shell est configuré pour démarrer automatiquement au démarrage du système. Vérifiez que les règles de pare-feu autorisent une connexion SSH.

2. Sur la machine virtuelle Azure, après le basculement, autorisez les connexions entrantes au port SSH pour les règles des groupes de sécurité réseau sur la machine virtuelle basculée, et pour le sous-réseau Azure auquel elle est connectée.
   [Ajoutez une adresse IP publique](site-recovery-monitoring-and-troubleshooting.md) pour la machine virtuelle. Vous pouvez vérifier les **Diagnostics de démarrage** pour afficher une capture d’écran de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware](vmware-azure-tutorial.md)
