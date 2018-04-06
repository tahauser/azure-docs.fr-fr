---
title: Questions courantes sur la réplication de VMware vers Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article résume les questions courantes quand vous répliquez des machines virtuelles VMware en local vers Azure à l’aide d’Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 345b73db423c6e12b56bb3308f7700917a372dda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="common-questions---vmware-to-azure-replication"></a>Questions courantes sur la réplication de VMware vers Azure

Cet article fournit des réponses aux questions courantes qui se posent lors de la réplication de machines virtuelles VMware en local vers Azure. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Généralités
### <a name="how-is-site-recovery-priced"></a>Comment les tarifs Azure Site Recovery sont-ils fixés ?
Pour plus d’informations, consultez [Tarification Site Recovery ](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Comment payer les machines virtuelles Azure ?
Lors de la réplication, les données sont répliquées vers le stockage Azure et vous ne payez aucune modification de machine virtuelle. Quand vous exécutez un basculement vers Azure, Site Recovery crée automatiquement des machines virtuelles Azure IaaS. Vous êtes ensuite facturé pour les ressources de calcul que vous consommez dans Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Que puis-je faire avec la réplication de VMware vers Azure ?
- **Récupération d’urgence** : vous pouvez configurer une récupération d’urgence complète. Dans ce scénario, vous répliquez des machines virtuelles VMware en local vers le stockage Azure. Ensuite, si votre infrastructure locale n’est pas disponible, vous pouvez basculer vers Azure. Quand vous exécutez un basculement, les machines virtuelles Azure sont créées à l’aide des données répliquées. Vous pouvez accéder à des applications et charges de travail sur les machines virtuelles Azure jusqu’à ce que votre centre de données local soit à nouveau disponible. Vous pouvez ensuite effectuer la restauration à partir d’Azure vers votre site local.
- **Migration** : vous pouvez utiliser Site Recovery pour migrer les machines virtuelles VMware en local vers Azure. Dans ce scénario, vous répliquez les machines virtuelles VMware en local vers le stockage Azure. Ensuite, vous effectuez un basculement du site local vers Azure. Après le basculement, vos applications et charges de travail sont disponibles et en cours d’exécution sur les machines virtuelles Azure.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>De quoi ai-je besoin dans Azure ?
Vous avez besoin d’un abonnement Azure, d’un coffre Recovery Services, d’un compte de stockage et d’un réseau virtuel. Le coffre, le compte de stockage et le réseau doivent se trouver dans la même région.

### <a name="what-azure-storage-account-do-i-need"></a>De quel compte de stockage Azure ai-je besoin ?
Vous devez disposer d’un compte de stockage LRS ou GRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. Le stockage Premium est pris en charge.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Est-ce que mon compte Azure a besoin d’autorisations pour créer des machines virtuelles ?
Si vous êtes un administrateur d’abonnement, vous disposez des autorisations de réplication nécessaires. Si ce n’est pas le cas, vous avez besoin d’autorisations pour créer une machine virtuelle Azure dans le groupe de ressources et le réseau virtuel que vous spécifiez quand vous configurez Site Recovery et des autorisations pour écrire dans le compte de stockage sélectionné. [Plus d’informations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)



## <a name="on-premises"></a>Local 

### <a name="what-do-i-need-on-premises"></a>De quoi ai-je besoin en local ?
En local, vous devez disposer de composants Site Recovery, installés sur une machine virtuelle VMware unique. Vous avez également besoin d’une infrastructure VMware, avec au moins un hôte ESXi, et nous vous recommandons un serveur vCenter. En outre, vous avez besoin d’une ou de plusieurs machines virtuelles VMware à répliquer. [Découvrez-en plus](vmware-azure-architecture.md) sur l’architecture VMware vers Azure.

Le serveur de configuration local peut être déployé d’une ou de deux façons

1. Le déployer à l’aide d’un modèle de machine virtuelle avec le serveur de configuration pré-installé. [En savoir plus ici](vmware-azure-tutorial.md#download-the-vm-template).
2. Le déployer à l’aide de l’installation sur une machine Windows Server 2016 de votre choix. [En savoir plus ici](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Pour découvrir la prise en main du déploiement du serveur de configuration sur vos propres machines Windows Server, dans l’objectif de protection de l’activation de la protection, choisissez **To Azure > non virtualisé/autre**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Quelle est la destination de réplication des machines virtuelles en local ?
Les données sont répliquées vers le stockage Azure. Quand vous exécutez un basculement, Site Recovery crée automatiquement des machines virtuelles Azure à partir du compte de stockage.

### <a name="what-apps-can-i-replicate"></a>Quelles applications puis-je répliquer ?
Vous pouvez répliquer toute application ou charge de travail en cours d’exécution sur une machine virtuelle VMware qui est conforme aux [conditions requises de réplication](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery assure la prise en charge de la réplication compatible avec les applications afin qu’elles puissent faire l’objet d’un basculement et être restaurées dans un état intelligent. Site Recovery s’intègre aux applications Microsoft, notamment à SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Puis-je répliquer vers Azure avec un VPN de site à site ?
Site Recovery réplique les données en local vers le stockage Azure via un point de terminaison public, ou à l’aide de l’homologation publique ExpressRoute. La réplication sur un réseau VPN de site à site n’est pas prise en charge.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Puis-je répliquer vers Azure avec ExpressRoute ?
Oui, vous pouvez utiliser ExpressRoute pour répliquer des machines virtuelles vers Azure. Site Recovery réplique les données vers un compte de stockage Azure via un point de terminaison public, et vous devez configurer [l’homologation publique](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) pour cette réplication. Une fois que les machines virtuelles basculent vers un réseau virtuel Azure, vous pouvez y accéder à l’aide de [l’homologation privée](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Pourquoi ne puis-je pas répliquer via VPN ?

Quand vous répliquez vers Azure, le trafic de réplication atteint les points de terminaison publics d’un compte de stockage Azure ; par conséquent, vous pouvez répliquer uniquement via Internet public avec ExpressRoute (homologation publique) et VPN ne fonctionne pas. 



## <a name="what-are-the-replicated-vm-requirements"></a>Quelle est la configuration requise d’une machine virtuelle répliquée ?

Pour la réplication, une machine virtuelle VMware doit exécuter un système d’exploitation pris en charge. En outre, la machine virtuelle doit respecter la configuration requise pour les machines virtuelles Azure. [Découvrez-en plus](vmware-physical-azure-support-matrix.md##replicated-machines) dans la matrice de prise en charge.

## <a name="how-often-can-i-replicate-to-azure"></a>À quelle fréquence puis-je répliquer vers Azure ?
La réplication est continue quand il s’agit de la réplication des machines virtuelles VMware vers Azure.

## <a name="can-i-extend-replication"></a>Puis-je étendre la réplication ?
La réplication étendue ou chaînée n’est pas prise en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Puis-je effectuer une réplication initiale hors connexion ?
Ceci n’est pas pris en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Puis-je exclure des disques ?
Oui, vous pouvez exclure des disques de la réplication. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Puis-je répliquer des machines virtuelles avec des disques dynamiques ?
Les disques dynamiques peuvent être répliqués. Le disque du système d’exploitation doit être un disque de base.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Puis-je ajouter une nouvelle machine virtuelle à un groupe de réplication existant ?
Oui.

## <a name="configuration-server"></a>Serveur de configuration

### <a name="what-does-the-configuration-server-do"></a>Quel fait le serveur de configuration ?
Le serveur de configuration exécute les composants Site Recovery en local, notamment : 
- Le serveur de configuration qui coordonne les communications entre les machines virtuelles en local et Azure, et gère la réplication des données.
- Le serveur de traitement qui fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers le stockage Azure. En outre, le serveur de traitement installe le service Mobilité sur les machines virtuelles que vous souhaitez répliquer et effectue une détection automatique des machines virtuelles VMware en local.
- Le serveur cible maître qui gère les données de réplication pendant la restauration automatique à partir d’Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Où configurer le serveur de configuration ?
Vous avez besoin d’une seule machine virtuelle VMware en local hautement disponible pour le serveur de configuration.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Quelle est la configuration requise pour le serveur de configuration ?

Examinez les [conditions préalables](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Puis-je configurer manuellement le serveur de configuration au lieu d’utiliser un modèle ?
Nous vous recommandons d’utiliser la version la plus récente du modèle OVF pour [créer la machine virtuelle du serveur de configuration](vmware-azure-deploy-configuration-server.md). Si ce n’est pas possible pour une raison quelconque, par exemple vous n’avez pas accès au serveur VMware, vous pouvez [télécharger le fichier d’installation unifiée](physical-azure-set-up-source.md) à partir du portail, puis l’exécuter sur une machine virtuelle. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Un serveur de configuration peut-il répliquer vers plusieurs régions ?
Non. Pour ce faire, vous devez configurer un serveur de configuration dans chaque région.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Puis-je héberger un serveur de configuration dans Azure ?
Bien que ce soit possible, la machine virtuelle Azure exécutant le serveur de configuration doit communiquer avec les machines virtuelles et votre infrastructure VMware en local. La surcharge n’est probablement pas viable.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Où puis-je obtenir la version la plus récente du modèle de serveur de configuration ?
Téléchargez la dernière version à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Comment mettre à jour le serveur de configuration ?
Vous installez des correctifs cumulatifs. Les informations de mise à jour les plus récentes sont disponibles dans la [page wiki relative aux mises à jour](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Service Mobilité

### <a name="where-can-i-find-the-mobility-service-installers"></a>Où puis-je trouver les programmes d’installation du service Mobilité ?
Les programmes d’installation sont conservés dans le dossier **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** sur le serveur de configuration.

## <a name="how-do-i-install-the-mobility-service"></a>Comment installer le service Mobilité ?
Vous l’installez sur chaque machine virtuelle que vous voulez répliquer avec une [installation Push](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), via une installation manuelle à partir de [l’interface utilisateur](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui) ou [avec PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Vous pouvez également effectuer le déploiement à l’aide d’un outil de déploiement comme [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md) ou [Azure Automation et DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Sécurité

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Quel est l’accès aux serveurs VMware dont Site Recovery a besoin ?
Site Recovery doit pouvoir accéder aux serveurs VMware pour :

- Configurer une machine virtuelle VMware exécutant le serveur de configuration et d’autres composants Site Recovery en local. [En savoir plus](vmware-azure-deploy-configuration-server.md)
- Détecter automatiquement les machines virtuelles pour la réplication. Découvrez comment préparer un compte pour la détection automatique. [En savoir plus](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Quel est l’accès aux machines virtuelles VMware dont Site Recovery a besoin ?

- Pour effectuer la réplication, le service Mobilité Site Recovery doit être installé et exécuté sur une machine virtuelle VMware. Vous pouvez déployer l’outil manuellement ou spécifier que Site Recovery doit effectuer une installation Push du service quand vous activez la réplication pour une machine virtuelle. Pour l’installation Push, Site Recovery a besoin d’un compte qu’il peut utiliser pour installer le composant de service. [Plus d’informations](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation) Le serveur de traitement (exécuté par défaut sur le serveur de configuration) effectue cette installation. [Découvrez-en plus](vmware-azure-install-mobility-service.md) sur l’installation du service Mobilité.
- Lors de la réplication, les machines virtuelles communiquent avec Site Recovery comme suit :
    - Elles communiquent avec le serveur de configuration sur le port HTTPS 443 pour la gestion de la réplication.
    - Elles envoient des données de réplication au serveur de traitement sur le port HTTPS 9443 (modification possible).
    - Si vous activez la cohérence multimachine virtuelle, les machines virtuelles communiquent entre elles sur le port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Est-ce que les données de réplication sont envoyées à Site Recovery ?
Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les éléments exécutés sur vos machines virtuelles. Les données de réplication sont échangées entre les hyperviseurs VMware et le stockage Azure. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  

Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Pouvons-nous conserver des métadonnées en local dans une région géographique ?
Oui. Quand vous créez un coffre dans une région, nous garantissons que toutes les métadonnées utilisées par Site Recovery restent dans les limites géographiques de cette région.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery chiffre-t-il la réplication ?
Oui, le chiffrement en transit et le [chiffrement dans Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) sont tous deux pris en charge.


## <a name="failover-and-failback"></a>Basculement et restauration automatique
### <a name="how-far-back-can-i-recover"></a>Jusqu’à quand peut remonter la récupération ?
Le point de récupération le plus ancien que vous pouvez utiliser pour VMware vers Azure est de 72 heures.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Comment accéder aux machines virtuelles Azure après un basculement ?
Après un basculement, vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée, via un réseau privé virtuel de site à site ou via Azure ExpressRoute. Vous devez préparer un certain nombre de choses afin de vous connecter. [En savoir plus](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Est-ce que les données ayant fait l’objet d’un basculement sont résilientes ?
Azure est conçu pour la résilience. Site Recovery est prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure. En cas de basculement, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?
Le [basculement](site-recovery-failover.md) n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail, ou vous pouvez utiliser [PowerShell](/powershell/module/azurerm.siterecovery) pour déclencher un basculement.

### <a name="can-i-fail-back-to-a-different-location"></a>Puis-je effectuer la restauration à un autre emplacement ?
Oui, si vous effectuez le basculement vers Azure, vous pouvez effectuer la restauration à un autre emplacement si celui d’origine n’est pas disponible. [Plus d’informations](concepts-types-of-failback.md#alternate-location-recovery-alr)

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Pourquoi ai-je besoin d’un VPN ou d’ExpressRoute pour la restauration ?

Quand vous effectuez la restauration à partir d’Azure, les données d’Azure sont recopiées vers votre machine virtuelle en local et un accès privé est requis. 



## <a name="automation-and-scripting"></a>Automatisation et scripts

### <a name="can-i-set-up-replication-with-scripting"></a>Puis-je configurer la réplication avec des scripts ?
Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du kit SDK Azure. [En savoir plus](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Performances et capacité
### <a name="can-i-throttle-replication-bandwidth"></a>Est-il possible de limiter la bande passante de réplication ?
Oui. [Plus d’informations](site-recovery-plan-capacity-vmware.md)


## <a name="next-steps"></a>Étapes suivantes
* [Examiner](vmware-physical-azure-support-matrix.md) les conditions de prise en charge.
* [Configurer](vmware-azure-tutorial.md) la réplication VMware vers Azure.
