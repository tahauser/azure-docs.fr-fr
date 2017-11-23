---
title: "Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article explique comment protéger les déploiements d’applications SAP NetWeaver à l’aide d’Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar
ms.openlocfilehash: 5a47acab598e113ef7ed968dd3a6429ac3bc1ec3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2017
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide d’Azure Site Recovery

La plupart des déploiements SAP, de moyenne et grande taille, disposent d’une forme de solution de récupération d’urgence.  Avoir des solutions de récupération d’urgence fiables et testables est de plus en plus important, d’autant que de plus en plus de processus métier essentiels sont déplacés vers des applications telles que SAP.  Testé et intégré aux applications SAP, le service Azure Site Recovery dépasse les capacités de la plupart des solutions de récupération d’urgence locales, à un coût total de possession (TCO) inférieur aux solutions concurrentes.
Avec Azure Site Recovery, vous pouvez :
* Activer la protection des applications de production SAP NetWeaver et non-NetWeaver exécutées localement, par la réplication des composants sur Azure.
* Activez la protection des applications de production SAP NetWeaver et non-NetWeaver exécutant Azure, par la réplication des composants sur un autre centre de données Azure.
* Simplifiez la migration vers le cloud en utilisant Site Recovery pour migrer votre déploiement SAP vers Azure.
* Simplifier les mises à niveau, les tests et la création de prototypes du projet SAP, par la création d’un clone de production à la demande pour tester les applications SAP.

Cet article indique comment protéger les déploiements d’applications SAP NetWeaver à l’aide du service [Azure Site Recovery](site-recovery-overview.md). Cet article traite des bonnes pratiques de protection d’un déploiement SAP NetWeaver à trois niveaux dans Azure, par la réplication sur un autre centre de données Azure, au moyen d’Azure Site Recovery ; il aborde aussi les configurations et les scénarios pris en charge ainsi que la procédure de basculement, qu’il s’agisse d’un basculement test (exercices de récupération d’urgence) ou d’un basculement réel.


## <a name="prerequisites"></a>Prérequis
Avant de commencer, veillez à bien comprendre ce qui suit :

1. [Réplication d’une machine virtuelle dans Azure](azure-to-azure-walkthrough-enable-replication.md)
2. [Conception d’un réseau de récupération](site-recovery-azure-to-azure-networking-guidance.md)
3. [Réalisation d’un test de basculement vers Azure](azure-to-azure-walkthrough-test-failover.md)
4. [Exécution d’un basculement vers Azure](site-recovery-failover.md)
5. [Réplication d’un contrôleur de domaine](site-recovery-active-directory.md)
6. [Réplication de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scénarios pris en charge
Avec Azure Site Recovery, vous pouvez implémenter une solution de récupération d’urgence pour les scénarios suivants :
* Systèmes SAP s’exécutant dans un centre de données Azure et par réplication sur un autre centre de données Azure (Azure vers la récupération d’urgence Azure), tel qu’élaboré [ici](https://aka.ms/asr-a2a-architecture).
* Systèmes SAP s’exécutant sur des serveurs VMWare (ou physiques) locaux et par réplication sur un site de récupération d’urgence dans un centre de données Azure (VMware vers la récupération d’urgence Azure), ce qui nécessite des composants supplémentaires, tels que mis en œuvre [ici](https://aka.ms/asr-v2a-architecture).
* Systèmes SAP s’exécutant sur Hyper-V local et par réplication sur un site de récupération d’urgence dans un centre de données Azure (Hyper-V vers la récupération d’urgence Azure), ce qui nécessite des composants supplémentaires, tels que mis en œuvre [ici](https://aka.ms/asr-h2a-architecture).

Ce document utilise le premier cas, Azure vers la récupération d’urgence Azure, pour illustrer les possibilités de récupération d’urgence SAP d’Azure Site Recovery. Comme la réplication d’Azure Site Recovery est indépendante des applications, le processus décrit est censé s’appliquer également à d’autres scénarios.

### <a name="required-foundation-services"></a>Services de base nécessaires
Ce scénario de documentation a été déployé avec les services de base suivants :
* ExpressRoute ou réseau privé virtuel (VPN) site à site
* Au moins un contrôleur de domaine Active Directory et un serveur DNS s’exécutant dans Azure

Il est recommandé de mettre en place l’infrastructure ci-dessus avant de déployer Azure Site Recovery.


## <a name="typical-sap-application-deployment"></a>Déploiement classique des applications SAP
Les gros clients SAP déploient généralement entre 6 et 20 applications SAP individuelles.  La plupart de ces applications sont basées sur les moteurs SAP NetWeaver ABAP ou Java.  La prise en charge de ces applications NetWeaver vitales est assurée par de nombreux autres moteurs autonomes SAP plus petits, non-NetWeaver et spécifiques, et en général par quelques applications non-SAP.  

Il est indispensable de procéder à l’inventaire de toutes les applications SAP en cours d’exécution dans un environnement, et de déterminer les conditions exigées par rapport au mode de déploiement (à 2 ou 3 niveaux), aux versions, correctifs, tailles, taux de variation et à la persistance de disque.

![Modèle de déploiement](./media/site-recovery-sap/sap-typical-deployment.png)

La couche de persistance de la base de données SAP doit être protégée par le biais des outils SGBD (système de gestion de base de données) natifs, tels que SQL Server AlwaysOn, Oracle DataGuard ou le système de réplication HANA. La couche client n’est pas non plus protégée par Azure Site Recovery, mais il est important d’étudier les rubriques ayant une incidence sur cette couche, telles que le délai de propagation DNS, la sécurité et l’accès à distance du centre de données de récupération d’urgence.

Azure Site Recovery est la solution recommandée pour la couche d’application, y compris (A)SCS. D’autres applications, telles que les applications SAP non-NetWeaver et les applications non-SAP, font partie de l’environnement de déploiement SAP global et doivent aussi être protégées avec Azure Site Recovery.

## <a name="replicate-virtual-machines"></a>Répliquer des machines virtuelles
Suivez [ce guide](azure-to-azure-walkthrough-enable-replication.md) pour commencer la réplication de toutes les machines virtuelles d’applications SAP sur le centre de données de la récupération d’urgence Azure.

Si vous utilisez une adresse IP statique, vous pouvez spécifier l’adresse IP que vous souhaitez pour la machine virtuelle dans la section Carte d’interface réseau des paramètres Calcul et réseau.

![IP cible](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Création d’un plan de récupération
Un plan de récupération permet de séquencer le basculement de différents niveaux d’une application multiniveau, ce qui contribue au maintien de la cohérence de l’application. Suivez les étapes décrites [ici](site-recovery-create-recovery-plans.md) lorsque vous créez un plan de récupération pour une application web multiniveau.

### <a name="adding-scripts-to-the-recovery-plan"></a>Ajout de scripts au plan de récupération
Vous devrez peut-être effectuer certaines opérations lors du post-basculement/test de basculement des machines virtuelles Azure pour vous assurer du bon fonctionnement de vos applications. Vous pouvez automatiser l’opération de post-basculement, comme la mise à jour des entrées DNS ou la modification des liaisons et connexions, en ajoutant les scripts correspondants dans le plan de récupération, comme indiqué dans [cet article](site-recovery-create-recovery-plans.md#add-scripts).

### <a name="dns-update"></a>Mise à jour DNS
Si le service DNS est configuré pour la mise à jour DNS dynamique, les machines virtuelles le mettent généralement à jour avec la nouvelle adresse IP dès leur démarrage. Pour ajouter une étape explicite pour mettre à jour DNS avec les nouvelles adresses IP des machines virtuelles, ajoutez ce [script pour mettre à jour les adresses IP dans DNS](https://aka.ms/asr-dns-update) en tant qu’action postérieure dans les groupes du plan de récupération.  

## <a name="example-azure-to-azure-deployment"></a>Exemple de déploiement Azure vers Azure
Dans le schéma ci-dessous, le scénario de récupération d’urgence Azure vers Azure d’Azure Site Recovery est représenté :
* Le centre de données principal est à Singapour (Azure en Asie du Sud-Est) et le centre de données de récupération d’urgence est à Hong-Kong (Azure en Asie-Pacifique).  Dans ce scénario, une haute disponibilité locale est fournie par la présence de deux machines virtuelles exécutant SQL Server AlwaysOn en mode synchrone à Singapour.
* L’ASCS de partage de fichiers peut servir à fournir la haute disponibilité aux points de défaillance uniques SAP. L’ASCS de partage de fichiers ne nécessite pas de disque partagé en cluster, et les applications telles que SIOS ne sont pas utiles.
* La protection de la récupération d’urgence pour la couche SGBD est obtenue au moyen de la réplication asynchrone.
* Ce scénario présente une « récupération d’urgence symétrique », une expression utilisée pour désigner une solution de récupération d’urgence constituée par un réplica exact de la production ; ainsi, la solution SQL Server de récupération d’urgence dispose d’une haute disponibilité locale. L’utilisation de la récupération d’urgence symétrique n’est pas obligatoire pour la couche de base de données, et de nombreux clients tirent profit de la flexibilité des déploiements cloud pour créer un nœud local à haute disponibilité, rapidement après un événement de récupération d’urgence.
* Le schéma illustre l’ASCS SAP NetWeaver et la couche serveur d’application dont la réplication est assurée par Azure Site Recovery.

![Scénario de réplication](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>Exécution d’un test de basculement
Suivez [ce guide](azure-to-azure-walkthrough-test-failover.md) pour effectuer un test de basculement.

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
2.  Cliquez sur le plan de récupération créé pour la ou les applications SAP.
3.  Cliquez sur Test de basculement.
4.  Sélectionnez un point de récupération et un réseau virtuel Azure pour démarrer le test de basculement.
5.  Lorsque l’environnement secondaire est opérationnel, vous pouvez effectuer vos validations.
6.  Une fois les validations terminées, cliquez sur « Nettoyer le test de basculement » pour nettoyer l’environnement de basculement.

## <a name="doing-a-failover"></a>Exécution d’un basculement
Suivez [ce guide](site-recovery-failover.md) lorsque vous effectuez un basculement.

1.  Accédez au portail Azure et sélectionnez votre coffre Recovery Services.
2.  Cliquez sur le plan de récupération créé pour la ou les applications SAP.
3.  Cliquez sur « Basculement ».
4.  Sélectionnez un point de récupération pour démarrer le processus de basculement.

## <a name="next-steps"></a>Étapes suivantes
Découvrez en détail la création d’une solution de récupération d’urgence pour les déploiements SAP NetWeaver à l’aide d’Azure Site Recovery dans [ce livre blanc](http://aka.ms/asr-sap). Ce livre blanc présente également les recommandations émises pour les différentes architectures SAP, il répertorie les applications et les types de machines virtuelles pris en charge pour SAP sur Azure, et décrit les plans de tests possibles pour votre solution de récupération d’urgence.

Découvrez en détail la [réplication d’autres charges de travail](site-recovery-workload.md) à l’aide de Site Recovery.
