---
title: "Fonctionnalités de sécurité Azure utilisées avec les machines virtuelles Azure | Microsoft Docs"
description: " Vue d’ensemble des principales fonctionnalités de sécurité Azure utilisables avec les machines virtuelles Azure. Les machines virtuelles Azure vous apportent la souplesse de la virtualisation sans vous obliger à acheter le matériel qui exécute la machine virtuelle ni à en assurer la maintenance. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9d10b9fde99867a218c4368f0be6bee00bc44dc1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Vue d’ensemble de la sécurité des machines virtuelles Azure
Azure Virtual Machines vous permet de déployer un large éventail de solutions de calcul de façon agile. Avec la prise en charge de Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP et Azure BizTalk Services, vous pouvez déployer des charges de travail et des langages variés sur la plupart des systèmes d’exploitation.

Une machine virtuelle Azure vous donne la flexibilité de la virtualisation sans devoir acheter le matériel physique qui exécute la machine virtuelle ni en assurer la maintenance.  Vous pouvez créer et déployer vos applications en ayant la certitude que vos données sont protégées au sein de nos centres de données hautement sécurisés.

Avec Azure, vous pouvez créer des solutions sécurisées et conformes qui :

* protègent vos machines virtuelles contre les virus et logiciels malveillants
* chiffrent vos données sensibles
* sécurisent le trafic réseau
* identifient et détectent les menaces
* respectent les exigences en matière de conformité

Cet article vise à fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure utilisables sur les machines virtuelles. Il contient également des liens vers des articles fournissant des informations complémentaires sur chaque fonctionnalité.  

Les principales fonctionnalités de sécurité des machines virtuelles Azure sont abordées dans cet article :

* Logiciel anti-programme malveillant
* Module de sécurité matériel
* Chiffrement du disque de machine virtuelle
* Sauvegarde de machine virtuelle
* Azure Site Recovery
* Réseau virtuel
* Gestion des stratégies de sécurité et création de rapports
* Conformité

## <a name="antimalware"></a>Logiciel anti-programme malveillant
Azure met à votre disposition des logiciels anti-programmes malveillants provenant de fournisseurs de sécurité tels que Microsoft, Symantec, Trend Micro et Kaspersky. Ceux-ci permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces. Consultez la section En savoir plus ci-dessous pour rechercher des articles sur les solutions partenaires.

Microsoft Antimalware pour Azure Cloud Services et Virtual Machines offre une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, logiciels espions et autres logiciels malveillants.  Microsoft Antimalware fournit des alertes configurables lorsqu’un logiciel malveillant ou indésirable connu tente de s’installer ou de s’exécuter sur vos systèmes Azure.

Microsoft Antimalware est une solution d’agent unique pour les applications et les environnements client, conçue pour s’exécuter en arrière-plan sans intervention humaine. Vous pouvez déployer la protection en fonction des besoins de vos charges de travail d’application, avec une configuration de base sécurisée par défaut ou une configuration personnalisée avancée, y compris pour la surveillance anti-programmes malveillants.

Lorsque vous déployez et activez Microsoft Antimalware, les fonctionnalités essentielles suivantes sont disponibles :

* Protection en temps réel : surveille l’activité dans Cloud Services et sur les machines virtuelles pour détecter et bloquer l’exécution de logiciels malveillants.
* Analyse planifiée : effectue périodiquement une analyse ciblée pour détecter les logiciels malveillants, y compris les programmes en cours d’exécution.
* Correction de logiciels malveillants : prend automatiquement des mesures sur les programmes malveillants détectés, notamment la suppression ou la mise en quarantaine des fichiers malveillants et le nettoyage des entrées de registre malveillantes.
* Mises à jour de signatures : installe automatiquement les dernières signatures de protection (définitions de virus) pour garantir la mise à jour de la protection selon une fréquence prédéfinie.
* Mises à jour du moteur Antimalware : met automatiquement à jour le moteur Microsoft Antimalware.
* Mises à jour de la plateforme Antimalware : met automatiquement à jour la plateforme Microsoft Antimalware.
* Protection active : signale à Azure les métadonnées de télémétrie relatives aux menaces détectées et aux ressources suspectes afin d’assurer une réaction rapide, et active la remise de signatures synchrones en temps réel avec le système MAPS (système de protection active Microsoft).
* Exemples de création de rapport : crée et fournit des exemples de rapports au service Microsoft Antimalware pour contribuer à améliorer le service et permettre la résolution des problèmes.
* Exclusions : permet aux administrateurs d’applications et de services de configurer certains fichiers, processus et lecteurs pour les exclure de la protection et de l’analyse, entre autres pour des raisons de performances.
* Collecte d’événements Antimalware : enregistre l’intégrité du service Antimalware, les activités suspectes et les mesures de correction prises dans le journal des événements du système d’exploitation et les rassemble dans le compte de stockage Azure du client.

En savoir plus : pour en savoir plus sur les logiciels anti-programme malveillant pour protéger vos machines virtuelles, consultez :

* [Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](azure-security-antimalware.md)
* [Déploiement de solutions anti-programmes malveillants sur des machines virtuelles Azure (en anglais)](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows](../virtual-machines/windows/classic/install-trend.md)
* [Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Solutions de sécurité dans Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Module de sécurité matériel
Les protections par chiffrement et authentification peuvent être améliorées grâce à une meilleure clé de sécurité. Vous pouvez simplifier la gestion et la sécurité de vos clés et secrets critiques en les stockant dans Azure Key Vault. Key Vault permet de stocker les clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Vos clés de chiffrement SQL Server pour la sauvegarde ou le [chiffrement transparent des données](https://msdn.microsoft.com/library/bb934049.aspx) peuvent toutes être stockées dans Key Vault avec les clés ou secrets de vos applications. Les autorisations et l’accès à ces éléments protégés sont gérés via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

En savoir plus :

* [Qu’est-ce qu’Azure Key Vault ?](../key-vault/key-vault-whatis.md)
* [Bien démarrer avec Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Blog Azure Key Vault](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Chiffrement du disque de machine virtuelle
Azure Disk Encryption est une nouvelle fonctionnalité qui vous permet de chiffrer vos disques de machine virtuelle Windows et Linux Azure. Azure Disk Encryption utilise la fonctionnalité standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows et la fonctionnalité [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume du système d’exploitation et des disques de données.

La solution est intégrée à Azure Key Vault pour vous aider à contrôler et à gérer les clés de chiffrement de disque et les secrets de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques virtuels sont chiffrées au repos dans le stockage Azure.

En savoir plus :

* [Azure Disk Encryption pour des machines virtuelles Windows et Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Azure Disk Encryption pour les machines virtuelles Windows et Linux](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Chiffrement d’une machine virtuelle](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Sauvegarde de machine virtuelle
Sauvegarde Azure est une solution scalable qui protège les données de vos applications, sans aucun investissement en capital et avec des frais de fonctionnement minimaux. Les erreurs rencontrées par les applications peuvent endommager vos données et les erreurs humaines peuvent introduire des bogues dans vos applications. Avec Sauvegarde Azure, vos machines virtuelles exécutant Windows et Linux sont protégées.

En savoir plus :

* [Qu’est-ce que Sauvegarde Azure ?](../backup/backup-introduction-to-azure-backup.md)
* [Parcours d’apprentissage Sauvegarde Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Service Sauvegarde Azure – Forum aux questions](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Une partie importante de la stratégie de continuité des activités et de récupération d’urgence de votre organisation consiste à savoir comment maintenir les charges de travail et les applications d’entreprise opérationnelles lorsque des interruptions planifiées et non planifiées se produisent. Azure Site Recovery aide à coordonner la réplication, le basculement et la récupération des charges de travail et des applications afin qu’elles soient disponibles à partir d’un site secondaire si votre site principal tombe en panne.

Site Recovery :

* **Simplifie votre stratégie de continuité des activités et de récupération d’urgence** : Site Recovery facilite la gestion de la réplication, du basculement et de la récupération de plusieurs charges de travail et applications métier à partir d’un site unique. Site Recovery orchestre la réplication et le basculement, sans intercepter les données de vos applications ni posséder des informations à leur sujet.
* **Fournit une réplication flexible** : à l’aide de Site Recovery, vous pouvez répliquer des charges de travail s’exécutant sur des machines virtuelles Hyper-V et VMware, et des serveurs physiques Windows/Linux.
* **Prend en charge le basculement et la récupération** : Site Recovery fournit des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence sans affecter les environnements de production. Vous pouvez également exécuter des basculements planifiés avec une perte de données zéro pour les interruptions attendues, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Après le basculement, vous pouvez procéder à une restauration automatique sur vos sites principaux. Site Recovery fournit des plans de récupération qui peuvent inclure des scripts et des classeurs Azure Automation afin que vous puissiez personnaliser le basculement et la récupération d’applications multiniveaux.
* **Élimine le centre de données secondaire** : vous pouvez procéder à une réplication vers un site secondaire local ou vers Azure. L’utilisation d’Azure comme destination de la récupération d’urgence élimine le coût et la complexité associés à la gestion d’un site secondaire. Les données répliquées sont stockées dans Azure Storage.
* **S’intègre aux technologies existantes de continuité des activités et de récupération d’urgence** : Site Recovery collabore avec les fonctionnalités de continuité des activités et de récupération d’urgence d’autres applications. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server des charges de travail d’entreprise. Cela inclut la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.

En savoir plus :

* [Qu’est-ce que le service Azure Site Recovery ?](../site-recovery/site-recovery-overview.md)
* [Comment Azure Site Recovery fonctionne-t-il ?](../site-recovery/site-recovery-components.md)
* [Quelles sont les charges de travail protégées par Azure Site Recovery ?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Réseau virtuel
Les machines virtuelles nécessitent une connectivité réseau. Pour cela, les machines virtuelles doivent être connectées à un réseau virtuel Azure. Un réseau virtuel Azure est une construction logique basée sur le réseau physique Azure. Chaque réseau virtuel logique Azure est isolé des autres réseaux virtuels Azure. Cet isolement permet de s’assurer que le trafic réseau dans votre déploiement n’est pas accessible aux autres clients Microsoft Azure.

En savoir plus :

* [Vue d’ensemble de la sécurité du réseau Azure](security-network-overview.md)
* [Présentation du réseau virtuel.](../virtual-network/virtual-networks-overview.md)
* [Fonctionnalités de mise en réseau et partenariats pour les scénarios d’entreprise](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestion des stratégies de sécurité et création de rapports
Azure Security Center vous aide à prévenir, détecter et résoudre les menaces, en vous apportant une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Le Centre de sécurité Azure vous permet d’optimiser et de surveiller la sécurité des machines virtuelles grâce aux opérations suivantes :

* Proposition de [recommandations de sécurité](../security-center/security-center-recommendations.md) sur la machine virtuelle, telles que l’application des mises à jour système, la configuration des points de terminaison ACL, l’activation des logiciels anti-programme malveillant, l’activation des groupes de sécurité réseau et l’application du chiffrement de disque.
* Surveillance de l’état de vos machines virtuelles

En savoir plus :

* [Présentation du Centre de sécurité Azure](../security-center/security-center-intro.md)
* [FAQ du Centre de sécurité Azure](../security-center/security-center-faq.md)
* [Opérations et planification du Centre de sécurité Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformité
Azure Virtual Machines bénéficie des certifications FISMA, FedRAMP, HIPAA, PCI DSS Level 1 et de celles d’autres programmes majeurs de conformité. Cette certification facilite le respect des exigences de conformité pour vos propres applications Azure, et donne à votre entreprise la possibilité de se conformer à un large éventail d’exigences réglementaires internationales et locales.

En savoir plus :

* [Centre de gestion de la confidentialité Microsoft - Conformité](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Cloud de confiance : sécurité, confidentialité et conformité dans Microsoft Azure](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
