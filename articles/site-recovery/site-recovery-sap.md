---
title: "Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Cet article indique comment protéger les déploiements d’applications SAP NetWeaver à l’aide du service Azure Site Recovery."
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
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Protéger un déploiement d’applications SAP NetWeaver multiniveau à l’aide de Site Recovery

La plupart des déploiements SAP, de moyenne et grande taille, utilisent une forme de solution de récupération d’urgence. Disposer de solutions de récupération d’urgence fiables et testables est de plus en plus important, d’autant qu’un nombre croissant de processus métier essentiels sont déplacés vers des applications telles que SAP. Azure Site Recovery a été testé et intégré aux applications SAP. Site Recovery dépasse les capacités de la plupart des solutions de récupération d’urgence locales, et à un coût total de possession (TCO) moins élevé que les solutions concurrentes.

Avec Site Recovery, vous pouvez effectuer les actions décrites ici.
* **Activer la protection des applications de production SAP NetWeaver et non-NetWeaver qui s’exécutent localement** par la réplication des composants vers Azure.
* **Activer la protection des applications de production SAP NetWeaver et non-NetWeaver qui s’exécutent sur Azure** par la réplication des composants sur un autre centre de données Azure.
* **Simplifier la migration vers le cloud** en utilisant Site Recovery pour migrer votre déploiement SAP vers Azure.
* **Simplifier les mises à niveau, les tests et la création de prototypes de projet SAP** par la création d’un clone de production à la demande pour tester les applications SAP.

Cet article explique comment protéger les déploiements d’applications SAP NetWeaver à l’aide d’[Azure Site Recovery](site-recovery-overview.md). L’article traite des bonnes pratiques permettant de protéger un déploiement SAP NetWeaver à trois niveaux sur Azure en répliquant vers un autre centre de données Azure à l’aide de Site Recovery. Il décrit les configurations et scénarios pris en charge ainsi que la façon de réaliser des tests de basculement (tests de récupération d’urgence) et des basculements réels.

## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous que vous savez accomplir les tâches suivantes :

* [Répliquer une machine virtuelle vers Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Constituer un réseau de récupération](site-recovery-azure-to-azure-networking-guidance.md)
* [Effectuer un test de basculement vers Azure](azure-to-azure-walkthrough-test-failover.md)
* [Procéder à un basculement vers Azure](site-recovery-failover.md)
* [Répliquer un contrôleur de domaine](site-recovery-active-directory.md)
* [Réplication de SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Scénarios pris en charge
Vous pouvez utiliser Site Recovery pour implémenter une solution de récupération d’urgence dans les scénarios décrits ici.
* Systèmes SAP s’exécutant dans un centre de données Azure et qui répliquent dans un autre centre de données Azure (Azure vers la récupération d’urgence Azure). Pour plus d’informations, consultez [Architecture de réplication Azure vers Azure](https://aka.ms/asr-a2a-architecture).
* Systèmes SAP s’exécutant sur des serveurs VMware (ou physiques) locaux et qui répliquent sur un site de récupération d’urgence dans un centre de données Azure (récupération d’urgence VMware vers Azure). Ce scénario nécessite quelques composants supplémentaires. Pour plus d’informations, consultez [Architecture de réplication VMware vers Azure](https://aka.ms/asr-v2a-architecture).
* Systèmes SAP s’exécutant sur Hyper-V localement et qui répliquent sur un site de récupération d’urgence dans un centre de données Azure (récupération d’urgence Hyper-V vers Azure). Ce scénario nécessite quelques composants supplémentaires. Pour plus d’informations, consultez [Architecture de réplication Hyper-V vers Azure](https://aka.ms/asr-h2a-architecture).

Dans cet article, nous utilisons un scénario de récupération d’urgence Azure vers Azure pour illustrer les fonctionnalités de récupération d’urgence SAP de Site Recovery. La réplication Site Recovery n’étant pas propre à l’application, le processus qui est décrit est censé s’appliquer également à d’autres scénarios.

### <a name="required-foundation-services"></a>Services de base nécessaires
Dans le scénario que cet article développe, les services de base suivants sont déployés :
* Azure ExpressRoute ou Passerelle VPN Azure
* Au moins un contrôleur de domaine Active Directory et un serveur DNS, s’exécutant dans Azure

Nous vous recommandons de mettre en place cette infrastructure avant de déployer Site Recovery.

## <a name="typical-sap-application-deployment"></a>Déploiement classique des applications SAP
Les gros clients SAP déploient généralement entre 6 et 20 applications SAP individuelles. La plupart de ces applications sont basées sur les moteurs SAP NetWeaver ABAP ou Java. De nombreux autres moteurs autonomes SAP plus petits, spécifiques et non-NetWeaver, et généralement quelques applications non-SAP, prennent en charge ces applications NetWeaver fondamentales.  

Il est indispensable de procéder à l’inventaire de toutes les applications SAP qui s’exécutent dans votre environnement. Déterminez ensuite les conditions exigées par rapport au mode de déploiement (à deux ou trois niveaux), aux versions, correctifs, tailles, taux de variation et à la persistance de disque.

![Schéma d’un modèle de déploiement SAP classique](./media/site-recovery-sap/sap-typical-deployment.png)

Protégez la couche de persistance de base de données SAP au moyen des outils SGBD (système de gestion de base de données) natifs, tels que SQL Server AlwaysOn, Oracle DataGuard ou le système de réplication SAP HANA. Comme la couche de base de données SAP, la couche client n’est pas protégée par Site Recovery. Il est important de tenir compte des facteurs qui ont une incidence sur cette couche. Ces facteurs comprennent le délai de propagation DNS, la sécurité et l’accès à distance du centre de données de récupération d’urgence.

Azure Site Recovery est la solution recommandée pour la couche d’application, y compris pour SAP SCS et ASCS. D’autres applications, telles que les applications SAP non-NetWeaver et les applications non-SAP, font partie de l’environnement de déploiement SAP global. Vous devez les protéger avec Site Recovery.

## <a name="replicate-virtual-machines"></a>Répliquer des machines virtuelles
Pour commencer la réplication de toutes les machines virtuelles d’application SAP vers le centre de données de récupération d’urgence Azure, suivez les instructions dans [Répliquer une machine virtuelle vers Azure](azure-to-azure-walkthrough-enable-replication.md).

Si vous utilisez une adresse IP statique, vous pouvez spécifier l’adresse IP que vous souhaitez attribuer à la machine virtuelle. Pour définir l’adresse IP, accédez à **Paramètres Calcul et réseau** > **Carte d’interface réseau**.

![Capture d’écran qui illustre la définition d’une adresse IP privée dans le volet de carte d’interface réseau de Site Recovery](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération
Lors d’un basculement, un plan de récupération prend en charge le séquencement des différents niveaux d’une application multiniveau. La mise en séquence permet d’assurer la cohérence de l’application. Lorsque vous créez un plan de récupération pour une application web multiniveau, suivez les étapes décrites dans [Créer un plan de récupération à l’aide de Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Ajouter des scripts au plan de récupération
Afin de vous assurer du bon fonctionnement de vos applications, vous pouvez être amené à effectuer certaines opérations sur les machines virtuelles Azure après le basculement ou pendant un test de basculement. Vous pouvez automatiser certaines opérations après le basculement. Par exemple, mettre à jour l’entrée DNS et modifier les liaisons et les connexions en ajoutant au plan de récupération les scripts correspondants.

### <a name="dns-update"></a>Mise à jour DNS
Si le système DNS est configuré pour la mise à jour DNS dynamique, les machines virtuelles le mettent généralement à jour avec la nouvelle adresse IP dès leur démarrage. Si vous voulez ajouter une étape explicite afin de mettre à jour le DNS avec les nouvelles adresses IP des machines virtuelles, ajoutez un [script pour mettre à jour les adresses IP dans DNS](https://aka.ms/asr-dns-update) en tant qu’action post-basculement dans les groupes de plan de récupération.  

## <a name="example-azure-to-azure-deployment"></a>Exemple de déploiement Azure vers Azure
Le schéma suivant illustre le scénario de récupération d’urgence Azure vers Azure de Site Recovery :

![Schéma d’un scénario de réplication Azure vers Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* Le centre de données principal est à Singapour (Azure en Asie du Sud-Est). Le centre de données de récupération d’urgence est à Hong Kong (Azure en Asie-Pacifique). Dans ce scénario, une haute disponibilité locale est fournie par la présence de deux machines virtuelles qui exécutent SQL Server AlwaysOn en mode synchrone à Singapour.
* Le partage de fichiers ASCS SAP fournit une haute disponibilité pour les points de défaillance uniques SAP. Le partage de fichiers ASCS ne nécessite pas de disque partagé en cluster. Les applications telles que SIOS ne sont pas nécessaires.
* La protection de la récupération d’urgence pour la couche SGBD est obtenue au moyen de la réplication asynchrone.
* Ce scénario montre une « récupération d’urgence symétrique ». Ce terme désigne une solution de récupération d’urgence qui est un réplica exact de production. La solution SQL Server de récupération d’urgence présente une haute disponibilité locale. La récupération d’urgence symétrique n’est pas obligatoire pour la couche de base de données. Beaucoup de clients tirent parti de la flexibilité des déploiements cloud pour créer rapidement un nœud local à haute disponibilité après un événement de récupération d’urgence.
* Le schéma illustre l’ASCS SAP NetWeaver et la couche de serveur d’application dont la réplication est assurée par Site Recovery.

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

1.  Dans le portail Azure, sélectionnez votre coffre Recovery Services.
2.  Sélectionnez le plan de récupération que vous avez créé pour les applications SAP.
3.  Sélectionnez **Test de basculement**.
4.  Pour démarrer le test de basculement, sélectionnez le point de récupération et le réseau virtuel Azure.
5.  Lorsque l’environnement secondaire est opérationnel, procédez aux validations.
6.  Lorsque les validations sont terminées, pour nettoyer l’environnement de basculement, sélectionnez **Nettoyer le test de basculement**.

Pour plus d’informations, consultez [Tester le basculement vers Azure dans Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Exécuter un basculement

1.  Dans le portail Azure, sélectionnez votre coffre Recovery Services.
2.  Sélectionnez le plan de récupération que vous avez créé pour les applications SAP.
3.  Sélectionnez **Basculement**.
4.  Pour démarrer le processus de basculement, sélectionnez le point de récupération.

Pour plus d’informations, consultez [Basculement dans Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création d’une solution de récupération d’urgence pour les déploiements SAP NetWeaver à l’aide de Site Recovery, consultez le livre blanc téléchargeable [SAP NetWeaver : Création d’une solution de récupération d’urgence avec Azure Site Recovery](http://aka.ms/asr-sap). Ce livre blanc présente les recommandations émises pour les diverses architectures SAP, il répertorie les applications et les types de machines virtuelles pris en charge pour SAP sur Azure, et décrit les options de plan de test pour votre solution de récupération d’urgence.
* Approfondissez vos connaissances sur la [réplication d’autres charges de travail](site-recovery-workload.md) à l’aide de Site Recovery.
