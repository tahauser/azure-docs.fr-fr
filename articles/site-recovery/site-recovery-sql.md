---
title: "Répliquer des applications avec SQL Server et Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment répliquer SQL Server à l’aide d’Azure Site Recovery pour les fonctionnalités de récupération d’urgence de SQL Server."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: pratshar
ms.openlocfilehash: 7981173b419632683a40a54bc07f51f0fccab531
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Protéger SQL Server à l’aide de la récupération d’urgence SQL Server et d’Azure Site Recovery

Cet article décrit comment protéger le serveur SQL Server principal d’une application en combinant les technologies de continuité d’activité et de récupération d’urgence (BCDR) de SQL Server et [Azure Site Recovery](site-recovery-overview.md).

Avant de commencer, assurez-vous de bien comprendre les fonctionnalités de récupération d’urgence de SQL Server, notamment le clustering de basculement, les groupes de disponibilité AlwaysOn, la mise en miroir de bases de données et la copie des journaux de transaction.


## <a name="sql-server-deployments"></a>Déploiements SQL Server

De nombreuses charges de travail utilisent SQL Server comme base, qui peut être intégré avec des applications telles que SharePoint, Dynamics et SAP pour mettre en œuvre des services de données.  SQL Server peut être déployé de plusieurs façons :

* **SQL Server autonome**: le serveur SQL et toutes les bases de données sont hébergés sur un seul ordinateur (physique ou une machine virtuelle). Quand le serveur est virtualisé, le cluster hôte est utilisé pour la haute disponibilité locale. La haute disponibilité pour le niveau invité n’est pas mise en œuvre.
* **Instances de clustering de basculement SQL Server (Always On FCI)** : au moins deux nœuds exécutant des instances SQL Server avec des disques partagés sont configurés dans un cluster de basculement Windows. Si un nœud est inactif, le cluster peut basculer SQL Server vers une autre instance. Cette configuration est généralement utilisée pour mettre en œuvre la haute disponibilité sur un site principal. Ce type de déploiement ne protège pas contre la défaillance ou une panne dans la couche de stockage partagé. Un disque partagé peut être mis en œuvre avec iSCSI, Fibre Channel ou VHDx partagé.
* **Groupes de disponibilité SQL Always On** : au moins deux nœuds sont configurés dans un cluster sans partage avec des bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique.

 Cet article s’appuie sur les technologies de récupération d’urgence SQL natives suivantes pour mettre en œuvre la récupération des bases de données vers un site distant :

* Groupes de disponibilité SQL AlwaysOn, pour la récupération d’urgence pour SQL Server éditions 2012 ou 2014 Enterprise.
* Mise en miroir de base de données SQL en mode haute sécurité pour SQL Server édition Standard (toute version) ou SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Prise en charge de Site Recovery

### <a name="supported-scenarios"></a>Scénarios pris en charge
Site Recovery peut protéger SQL Server comme décrit dans le tableau ci-dessous.

**Scénario** | **Vers un site secondaire** | **Vers Azure**
--- | --- | ---
**Hyper-V** | OUI | OUI
**VMware** | OUI | OUI
**Serveur physique** | OUI | OUI
**Microsoft Azure**|N/D| OUI

### <a name="supported-sql-server-versions"></a>Versions de SQL Server prises en charge
Les scénarios de cet article prennent en charge les versions de SQL Server suivantes :

* SQL Server 2016 Entreprise et Standard
* SQL Server 2014 Entreprise et Standard
* SQL Server 2012 Entreprise et Standard
* SQL Server 2008 R2 Entreprise et Standard

### <a name="supported-sql-server-integration"></a>Intégration de SQL Server prise en charge

Azure Site Recovery peut être intégré aux technologies BCDR SQL Server natives répertoriées dans le tableau pour fournir une solution de récupération d’urgence.

**Fonctionnalité** | **Détails** | **SQL Server** |
--- | --- | ---
**Groupe de disponibilité AlwaysOn** | Plusieurs instances autonomes de SQL Server s’exécutent chacune dans un cluster de basculement qui comporte plusieurs nœuds.<br/><br/>Les bases de données peuvent être regroupées dans des groupes de basculement qui peuvent être copiés (mis en miroir) sur des instances de SQL Server, pour éviter tout stockage partagé.<br/><br/>Cette fonction assure une récupération d'urgence entre un site primaire et un ou plusieurs sites secondaires. Il est possible de configurer deux nœuds dans un cluster sans partage avec les bases de données SQL Server configurées dans un groupe de disponibilité avec réplication synchrone et basculement automatique. | SQL Server 2016, SQL Server 2014 et SQL Server 2012 édition Entreprise
**Clustering de basculement (instance de cluster de basculement AlwaysOn)** | SQL Server tire parti de la fonction de cluster de basculement Windows pour assurer la haute disponibilité des charges de travail SQL Server locales.<br/><br/>Les nœuds qui exécutent des instances de SQL Server avec des disques partagés sont configurés dans un cluster de basculement. Si une instance est arrêtée, le cluster bascule vers un autre.<br/><br/>Le cluster ne protège pas contre les défaillances ou les pannes en stockage partagé. Le disque partagé peut être implémenté avec iSCSI, Fibre Channel ou VHDX partagé. | Éditions SQL Server Enterprise<br/><br/>Éditions SQL Server Standard (limitée à deux nœuds)
**Mise en miroir de base de données (mode haute sécurité)** | Protège une base de données sur une seule copie secondaire. Disponible dans les modes de réplication haute sécurité (synchrone) et hautes performances (asynchrone). Cluster de basculement non requis. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise (toutes les éditions)
**Serveur SQL autonome** | SQL Server et la base de données sont hébergés sur un seul serveur (physique ou virtuel). Le cluster hôte est utilisé pour la haute disponibilité, si le serveur est virtuel. Aucune haute disponibilité pour le niveau invité. | Édition Enterprise ou Standard

## <a name="deployment-recommendations"></a>Recommandations concernant le déploiement

Ce tableau récapitule nos recommandations pour intégrer les technologies BCDR de SQL Server à Site Recovery.

| **Version** | **Édition** | **Déploiement** | **Local à local** | **Local vers Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 ou 2012 |Entreprise |Instance de cluster de basculement |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn |
|| Entreprise |Groupes de disponibilité AlwaysOn pour la haute disponibilité |Groupes de disponibilité AlwaysOn |Groupes de disponibilité AlwaysOn | |
|| standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local | |
|| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server 2008 R2 ou 2008 |Enterprise ou Standard |Instance de cluster de basculement (FCI) |Réplication Site Recovery avec miroir local |Réplication Site Recovery avec miroir local |
|| Enterprise ou Standard |Standalone |Réplication de la récupération de sites |Réplication de la récupération de sites | |
| SQL Server (toute version) |Enterprise ou Standard |Instance de cluster de basculement - application DTC |Réplication de la récupération de sites |Non pris en charge |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

* Un déploiement local de SQL Server exécutant une version prise en charge de SQL Server. En général, il est également nécessaire de configurer Active Directory pour votre serveur SQL.
* La configuration requise pour le scénario que vous souhaitez déployer. En savoir plus sur les conditions de prise en charge pour la [réplication vers Azure](site-recovery-support-matrix-to-azure.md) et [en local](site-recovery-support-matrix.md), ainsi que sur les [conditions préalables au déploiement](site-recovery-prereq.md).
* Pour configurer la récupération dans Azure, exécutez l’outil [Azure Virtual Machine Readiness Assessment](http://www.microsoft.com/download/details.aspx?id=40898) sur vos machines virtuelles SQL Server pour vous assurer qu’elles sont compatibles avec Azure et Site Recovery.

## <a name="set-up-active-directory"></a>Configurer Active Directory

Configurez Active Directory sur le site de récupération secondaire afin que SQL Server fonctionne correctement.

* **Petite entreprise** : si vous avez un petit nombre d’applications et un seul contrôleur de domaine pour le site local et que vous souhaitez basculer l’ensemble du site, nous vous recommandons d’utiliser Site Recovery pour répliquer le contrôleur de domaine sur le centre de données secondaire ou sur Azure.
* **Moyenne ou grande entreprise** : si vous avez un grand nombre d’applications et une forêt Active Directory et que vous souhaitez effectuer un basculement par application ou charge de travail, nous vous recommandons de configurer un contrôleur de domaine supplémentaire dans le centre de données secondaire ou dans Azure. Si vous utilisez des groupes de disponibilité AlwaysOn pour effectuer une récupération sur un site distant, nous vous recommandons de configurer un contrôleur de domaine supplémentaire sur le site secondaire ou dans Azure, qui sera utilisé pour l’instance SQL Server récupérée.

Les instructions fournies dans cet article supposent qu’un contrôleur de domaine est disponible sur le site secondaire. [ici](site-recovery-active-directory.md) .


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Intégration avec SQL Server AlwaysOn pour la réplication vers Azure

Voici ce que vous devez faire :

1. Importez les scripts sur votre compte Azure Automation. Ce dernier contient les scripts pour le basculement du groupe de disponibilité SQL dans une [machine virtuelle Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) et une [machine virtuelle classique](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Déploiement sur Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Ajoutez ASR-SQL-FailoverAG comme une action préalable du premier groupe dans le plan de récupération.

1. Suivez les instructions fournies dans le script pour créer une variable d’automatisation pour fournir le nom des groupes de disponibilité.

### <a name="steps-to-do-a-test-failover"></a>Procédure de test de basculement

SQL AlwaysOn ne prend pas en charge le test de basculement de manière native. Par conséquent, nous vous recommandons de :

1. Configurer la [Sauvegarde Azure](../backup/backup-azure-arm-vms.md) sur la machine virtuelle qui héberge le réplica du groupe de disponibilité dans Azure.

1. Avant de déclencher le test de basculement du plan de récupération, récupérez la machine virtuelle à partir de la sauvegarde effectuée à l’étape précédente.

    ![Restauration à partir de Sauvegarde Azure ](./media/site-recovery-sql/restore-from-backup.png)

1. [Forcez un quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) dans la machine virtuelle restaurée à partir d’une sauvegarde.

1. Mettez à jour l’adresse IP de l’écouteur avec une adresse IP disponible dans le réseau de test de basculement.

    ![Mettre à jour l’adresse IP de l’écouteur](./media/site-recovery-sql/update-listener-ip.png)

1. Mettez l’écouteur en ligne.

    ![Mettre l’écouteur en ligne](./media/site-recovery-sql/bring-listener-online.png)

1. Créez un équilibrage de charge avec une adresse IP créée sous le pool d’adresses IP de serveur frontal correspondant à chaque écouteur du groupe de disponibilité et avec la machine virtuelle SQL ajoutée dans le pool principal.

     ![Créer un équilibrage de charge – pool d’adresses IP frontal ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Créer un équilibrage de charge – pool principal ](./media/site-recovery-sql/create-load-balancer2.png)

1. Effectuer un test de basculement du plan de récupération.

### <a name="steps-to-do-a-failover"></a>Procédure de basculement

Après avoir ajouté le script dans le plan de récupération et validé ce plan grâce à un test de basculement, vous pouvez procéder au basculement du plan de récupération.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Intégration avec SQL Server AlwaysOn pour la réplication vers un site local secondaire

Si le serveur SQL Server utilise des groupes de disponibilité pour la haute disponibilité (ou une instance de cluster de basculement), nous vous recommandons d’utiliser également les groupes de disponibilité sur le site de récupération. Notez que ceci vaut pour les applications qui n’utilisent pas les transactions distribuées.

1. [Configurez les bases de données](https://msdn.microsoft.com/library/hh213078.aspx) dans des groupes de disponibilité.
1. Créez un réseau virtuel sur le site secondaire.
1. Configurez une connexion VPN de site à site entre le réseau virtuel et le site principal.
1. Créez une machine virtuelle sur le site de récupération et installez-y SQL Server.
1. Étendez les groupes de disponibilité AlwaysOn existants à la nouvelle machine virtuelle SQL Server. Configurez cette instance SQL Server comme une copie de réplica asynchrone.
1. Créez un écouteur de groupe de disponibilité ou modifiez l'écouteur existant pour inclure l’ordinateur virtuel de réplica asynchrone.
1. Vérifiez que la batterie de serveurs d’application est configurée pour utiliser l’écouteur. Si elle est configurée pour utiliser le nom du serveur de base de données, mettez-la à jour pour qu’elle utilise l’écouteur, afin de ne pas avoir à la reconfigurer après le basculement.

Pour les applications qui utilisent des transactions distribuées, nous vous recommandons de déployer Site Recovery avec la [réplication de site à site avec serveur physique / VMware](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Considérations concernant le plan de récupération
1. Ajoutez cet exemple de script à la bibliothèque VMM sur les sites principal et secondaire.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Lorsque vous créez un plan de récupération pour l’application, ajoutez une action préalable à l’étape de script du groupe 1, qui exécute le script de basculement des groupes de disponibilité.

## <a name="protect-a-standalone-sql-server"></a>Protéger un serveur SQL Server autonome

Dans ce scénario, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger la machine SQL Server. La procédure varie selon que SQL Server est configuré comme une machine virtuelle ou un serveur physique, et que vous souhaitez répliquer sur Azure ou un site local secondaire. En savoir plus sur les [scénarios Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Protéger un cluster SQL Server (Standard Edition/Windows Server 2008 R2)

Pour un cluster exécutant SQL Server Standard Edition ou SQL Server 2008 R2, nous vous recommandons d’utiliser la réplication Site Recovery pour protéger SQL Server.

### <a name="on-premises-to-on-premises"></a>Local vers local

* Si l’application utilise des transactions distribuées, nous vous recommandons de déployer [Site Recovery avec la réplication SAN](site-recovery-vmm-san.md) pour un environnement Hyper-V ou [serveur physique/VMware vers VMware](site-recovery-vmware-to-vmware.md) pour un environnement VMware.
* Pour les applications non-DTC, utilisez l’approche ci-dessus pour récupérer le cluster comme un serveur autonome en exploitant une mise en miroir de base de données locale haute sécurité.

### <a name="on-premises-to-azure"></a>Local vers Azure

Site Recovery ne prend pas en charge le cluster invité lors de la réplication vers Azure. De même, SQL Server n’inclut aucune solution de récupération d'urgence à faible coût dans l’édition Standard. Dans ce scénario, nous vous recommandons de protéger le cluster SQL Server local sur un serveur SQL Server autonome et de le récupérer dans Azure.

1. Configurez une instance SQL Server autonome supplémentaire sur le site local.
1. Configurez l’instance comme une copie miroir des bases de données que vous souhaitez protéger. Configurez la mise en miroir en mode haute sécurité.
1. Configurez Site Recovery sur le site local pour (serveurs physiques / de machines virtuelles [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware)](site-recovery-vmware-to-azure-classic.md).
1. Utilisez la réplication Site Recovery pour répliquer la nouvelle instance SQL Server sur Azure. Si cette copie miroir de haute sécurité est synchronisée avec le cluster principal, elle est répliquée sur Azure à l’aide de la réplication Site Recovery.


![Cluster standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considérations en matière de restauration automatique

Pour les clusters SQL Server Standard, la restauration automatique après un basculement non planifié nécessite une sauvegarde et une restauration SQL Server à partir de l’instance miroir sur le cluster d’origine, puis le rétablissement de la copie miroir.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-components.md) sur l’architecture de Site Recovery.
