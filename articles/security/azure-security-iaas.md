---
title: "Meilleures pratiques de sécurité pour les charges de travail IaaS dans Azure | Microsoft Docs"
description: " La migration des charges de travail vers Azure IaaS nous offre l’occasion de réévaluer nos conceptions "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 376a3e47e5099aa4d74732e0b6ed14ed9af14091
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Meilleures pratiques de sécurité pour les charges de travail IaaS dans Azure

Depuis que vous avez commencé à réfléchir au déplacement des charges de travail vers Azure IaaS, vous vous êtes probablement rendu compte que certaines considérations vous sont familières. Vous disposez peut-être déjà d’une expérience de sécurisation d’environnements virtuels. Lorsque vous migrez vers Azure IaaS, vous pouvez appliquer votre expertise dans la sécurisation des environnements virtuels et utiliser de nouvelles options pour sécuriser vos ressources.

Commençons par mentionner qu’il ne faut pas s’attendre à transposer les ressources locales telles quelles dans Azure. Les nouveaux défis et les nouvelles options donnent l’occasion de réévaluer les conceptions, les outils et les processus existants.

Votre responsabilité en matière de sécurité dépend du type de service cloud. Le graphique suivant résume le partage de la responsabilité entre Microsoft et vous :


![Domaines de responsabilité](./media/azure-security-iaas/sec-cloudstack-new.png)


Nous aborderons certaines des options disponibles dans Azure qui peuvent vous aider à répondre aux exigences de sécurité de votre organisation. N’oubliez pas que les exigences de sécurité peuvent varier selon les types de charges de travail. Aucune de ces meilleures pratiques ne peut à elle seule sécuriser vos systèmes. Comme toujours en matière de sécurité, vous devez choisir les options appropriées et voir comment les solutions peuvent se compléter mutuellement en remplissant les espaces laissés vides.

## <a name="use-privileged-access-workstations"></a>Utiliser des stations de travail d’accès privilégié

Les entreprises sont souvent victimes de cyberattaques dues au fait que les administrateurs effectuent des actions en utilisant des comptes avec des droits élevés. Généralement, leur intention n’est pas de nuire mais, simplement, la configuration et les processus existants le permettent. La plupart de ces utilisateurs comprennent les risques de ces actions d’un point de vue conceptuel, mais choisissent quand même de les effectuer.

Des actions telles que consulter ses e-mails et naviguer sur Internet semblent innocentes. Mais ils peuvent exposer des comptes avec élévation de privilèges pour compromettre des acteurs malveillants. Une exploration des activités, des e-mails spécialement conçus ou d’autres techniques peuvent servir à accéder à votre entreprise. Nous recommandons chaudement l’utilisation de stations de travail de gestion sécurisées (SAW) pour effectuer toutes les tâches d’administration Azure. Les SAW constituent un moyen de réduction des risques de compromission accidentelle.

Les stations de travail d’accès privilégié (PAW) fournissent un système d’exploitation dédié aux tâches sensibles, et protégé contre les attaques Internet et les vecteurs de menaces. La séparation de ces tâches et comptes sensibles à partir de l’utilisation quotidienne des stations de travail et périphériques offre une protection renforcée. Cette séparation limite l’impact des attaques d’hameçonnage, les vulnérabilités des applications et des systèmes d’exploitation, les différentes attaques d’emprunt d’identité et les attaques pour le vol des informations d’identification. (journalisation des séquences de touches, Pass-the-Hash et Pass-the-Ticket)

L’approche PAW est une extension bien établie et recommandée de la pratique consistant à utiliser un compte d’administration affecté de manière individuelle. Le compte d’administration est distinct d’un compte d’utilisateur standard. Une approche PAW fournit une station de travail digne de confiance pour ces comptes sensibles.

Pour obtenir plus d’informations et des instructions d’implémentation, consultez [Stations de travail d’accès privilégié](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Utiliser l’authentification multifacteur

Dans le passé, votre périmètre réseau était utilisé pour contrôler l’accès aux données d’entreprise. Dans un monde ou le cloud et la mobilité sont la priorité, l’identification est le plan de contrôle : elle permet de contrôler l’accès aux services IaaS à partir de n’importe quel appareil. Vous également l’utiliser pour obtenir de la visibilité et des informations sur les lieux et modes d’utilisation de vos données. La protection de l’identité numérique de vos utilisateurs Azure est la pierre angulaire de la protection de vos abonnements contre l’usurpation d’identité et d’autres cybercrimes.

L’une des mesures les plus intéressantes que vous pouvez prendre pour sécuriser un compte consiste à activer l’authentification à deux facteurs. L’authentification à deux facteurs est une méthode d’authentification qui est complémentaire au mot de passe. Cela permet d’atténuer le risque d’accès par une personne qui serait parvenue à obtenir le mot de passe de quelqu’un d’autre.

[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une authentification forte via diverses options de vérification simples : appel téléphonique, SMS, notification sur l’application mobile. Les utilisateurs choisissent la méthode qu’ils préfèrent.

Le moyen le plus simple d’utiliser Multi-Factor Authentication est l’application mobile Microsoft Authenticator, utilisable sur des appareils mobiles sous Windows, iOS et Android. Avec la dernière version de Windows 10 et l’intégration de votre instance Active Directory locale à Azure Active Directory (Azure AD), [Windows Hello for Business](../active-directory/active-directory-azureadjoin-passport-deployment.md) peut être utilisé pour une authentification unique transparente auprès des ressources Azure. Dans ce cas, l’appareil Windows 10 sert de second facteur d’authentification.

Pour les comptes qui gèrent votre abonnement Azure et pour les comptes qui peuvent se connecter aux machines virtuelles, l’utilisation de Multi-Factor Authentication vous donne un niveau de sécurité bien supérieur à celui apporté par l’utilisation d’un mot de passe seul. D’autres formes d’authentification à deux facteurs peuvent fonctionner tout aussi bien, mais elles risquent d’être plus complexes à déployer si elles ne sont pas déjà en production.

La capture d’écran suivante montre quelques-unes des options disponibles pour Azure Multi-Factor Authentication :

![Options de Multi-Factor Authentication](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limiter et contraindre l’accès administrateur

Il est extrêmement important de sécuriser les comptes qui peuvent gérer votre abonnement Azure. La compromission de ces comptes réduit à néant toutes les autres mesures prises pour garantir la confidentialité et l’intégrité de vos données. Comme l’a récemment illustré [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), les attaques internes représentent une menace énorme pour la sécurité globale d’une organisation.

Évaluez les personnes à qui octroyer des droits administratifs par les critères suivants :

- Effectue-t-elle des tâches nécessitant des privilèges administratifs ?
- À quelle fréquence ces tâches sont-elles réalisées ?
- Y a-t-il une raison spécifique pour laquelle ces tâches ne peuvent pas être effectuées par un autre administrateur en son nom ?

Documentez toutes les autres approches connues d’octroi du privilège et les raisons pour lesquelles elles ne sont pas acceptables.

L’utilisation de l’administration juste-à-temps permet d’éviter l’existence inutile de comptes avec des droits élevés pendant les périodes où ces droits ne sont pas nécessaires. Les comptes disposent de plus de droits pendant une période limitée afin que les administrateurs puissent effectuer leurs tâches. Ces droits sont supprimés à la fin d’un service, ou lorsqu’une tâche est terminée.

Vous pouvez utiliser [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) pour gérer, analyser et contrôler l’accès dans votre organisation. Cela vous permet de tenir compte des actions effectuées par les membres de votre organisation. Il permet également une administration très réactive pour Azure AD en introduisant le concept des administrateurs éligibles. Il s’agit des personnes disposant de comptes auxquels des droits d’administrateur peuvent être accordés. Ces types d’utilisateurs peuvent suivre un processus d’activation et se voir accorder des droits d’administrateur pour une durée limitée.


## <a name="use-devtest-labs"></a>Utiliser DevTest Labs

Utiliser Azure pour les environnements de développement et les labos permet aux organisations de gagner en agilité de test et de développement en éliminant les retards dus à l’approvisionnement en matériel. Malheureusement, l’administrateur peut être trop permissif en ce qui concerne l’affectation des droits en raison d’un manque de connaissance d’Azure ou de la volonté d’accélérer son adoption. Cela peut exposer involontairement l’organisation à des attaques internes. Certains utilisateurs peuvent alors disposer de droits d’accès bien supérieurs à ce qu’ils devraient avoir.

Le service [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) utilise le [contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-what-is.md). RBAC vous permet de séparer les responsabilités au sein de votre équipe au sein de rôles qui accordent uniquement le niveau d’accès nécessaire pour permettre aux utilisateurs d’effectuer leurs travaux. RBAC est fourni avec des rôles prédéfinis (propriétaire, utilisateur de labo et collaborateur). Vous pouvez même utiliser ces rôles pour affecter des droits à des partenaires externes et simplifier considérablement la collaboration.

Étant donné que DevTest Labs utilise RBAC, il est possible de créer d’autres [rôles personnalisés](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs ne simplifie pas seulement la gestion des autorisations, il simplifie le processus de configuration des environnements. Cela vous permet également de traiter les autres défis types des équipes qui travaillent sur des environnements de développement et de test. Une certaine préparation s’impose mais, à long terme, cela rendra les choses plus faciles pour votre équipe.

Voici certaines des fonctionnalités d’Azure DevTest Labs :

- Contrôle administratif sur les options accessibles aux utilisateurs L’administrateur peut gérer de manière centralisée des éléments tels que les tailles de machines virtuelles autorisées, le nombre maximal de machines virtuelles, et le démarrage et l’arrêt des machines virtuelles.
- Automatisation de la création de l’environnement de laboratoire
- Suivi des coûts
- Distribution simplifiée des machines virtuelles pour un travail collaboratif temporaire
- Libre-service permettant aux utilisateurs d’approvisionner leurs labos à l’aide de modèles
- Gestion et limitation de la consommation

![Création d’un labo à l’aide de DevTest Labs](./media/azure-security-iaas/devtestlabs.png)

Aucun coût supplémentaire n’est associé à l’utilisation de DevTest Labs. La création de laboratoires, de stratégies, de modèles et d’artefacts est gratuite. Vous payez uniquement pour les ressources Azure utilisées dans vos laboratoires, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels.



## <a name="control-and-limit-endpoint-access"></a>Contrôler et limiter l’accès aux points de terminaison

Pour que vous puissiez héberger des labos ou des systèmes de production dans Azure, vos systèmes doivent être accessibles sur Internet. Pour une nouvelle machine virtuelle Windows, le port RDP est par défaut accessible à partir d’Internet ; pour une machine virtuelle Linux, le port SSH est ouvert. Il est nécessaire de prendre des mesures pour « limiter les points de terminaison exposés » afin de réduire le risque d’accès non autorisé.

Les technologies d’Azure peuvent vous aider à limiter l’accès à ces points de terminaison d’administration. Vous pouvez notamment utiliser les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG) dans Azure. Lorsque vous utilisez Azure Resource Manager pour le déploiement, les NSG limitent l’accès provenant de tous les réseaux aux seuls points de terminaison de gestion (RDP ou SSH). Lorsque vous pensez NSG, pensez listes de contrôle d'accès (ACL) du routeur. Vous pouvez les utiliser pour contrôler étroitement les communications réseau entre les différents segments de vos réseaux Azure. Le processus est similaire à la création de réseaux dans des réseaux de périmètre ou d’autres réseaux isolés. Ils n’inspectent pas le trafic, mais facilitent la segmentation du réseau.


Dans Azure, vous pouvez configurer un [VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) à partir de votre réseau local. Un VPN de site à site étend votre réseau local dans le cloud. Cela vous donne une autre possibilité d’utiliser des NSG, car vous pouvez également les modifier afin de ne pas autoriser l’accès à partir de tout point hors du réseau local. Vous pouvez imposer une connexion au réseau Azure via VPN pour l’administration.

L’option de VPN de site à site peut être très intéressante dans les cas où vous hébergez des systèmes de production étroitement intégrés à vos ressources locales dans Azure.

Il existe également l’option [de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) pour les situations où vous souhaitez gérer des systèmes qui n’ont pas besoin d’accéder aux ressources locales. Ces systèmes peuvent être isolés dans leur propre réseau virtuel Azure. Les administrateurs peuvent se connecter via un VPN à l’environnement hébergé par Azure à partir de leur station de travail d’administration.

>[!NOTE]
>Vous pouvez utiliser l’une des deux options de VPN pour reconfigurer les ACL sur les NSG de façon à interdire l’accès aux points de terminaison de gestion à partir d’Internet.

Une autre option intéressante consiste à envisager un déploiement de type [Passerelle des services Bureau à distance](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). Vous pouvez utiliser ce déploiement pour vous connecter de manière sécurisée aux serveurs Bureau à distance via HTTPS, tout en appliquant des contrôles plus granulaires à ces connexions.

Voici quelques-unes des fonctionnalités auxquelles vous auriez accès :

- Options d’administration pour limiter les connexions aux demandes provenant de systèmes spécifiques
- Authentification de carte à puce ou Azure Multi-Factor Authentication
- Contrôle des systèmes auxquels chacun peut se connecter via la passerelle
- Contrôle de la redirection des appareils et des disques

## <a name="use-a-key-management-solution"></a>Utiliser une solution de gestion des clés

Une gestion sécurisée des clés est primordiale pour la protection des données du cloud. Avec [Azure Key Vault](../key-vault/key-vault-whatis.md), vous pouvez stocker en toute sécurité des clés de chiffrement et des secrets (tels que les mots de passe) dans des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM.

Microsoft traite vos clés dans des modules de sécurité matériels validés selon la norme « FIPS 140-2 Level 2 » (matériel et microprogramme). Surveillez et auditez l’utilisation des clés avec la journalisation Azure : envoyez les journaux dans Azure ou votre système SIEM (Security Information and Event Management) pour bénéficier d’une analyse et d’une détection des menaces supplémentaires.

Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’administrateur responsable de la gestion des services Azure dans une organisation.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Chiffrer les disques virtuels et le stockage des disques

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) élimine le risque de vol de données ou d’exposition à des accès non autorisés par le biais du déplacement d’un disque. Le disque peut être attaché à un autre système dans le but de contourner d’autres contrôles de sécurité. Disk Encryption utilise [BitLocker](https://technet.microsoft.com/library/hh831713) sous Windows et DM-Crypt sous Linux pour chiffrer le système d’exploitation et les lecteurs de données. Azure Disk Encryption est intégrée à Key Vault pour contrôler et gérer les clés de chiffrement. Il est disponible pour des machines virtuelles standard et des machines virtuelles avec stockage premium.

Pour plus d’informations, voir [Azure Disk Encryption pour machines virtuelles Windows et Linux IaaS](azure-security-disk-encryption.md).

Le [chiffrement des services de stockage Azure](../storage/common/storage-service-encryption.md) vous aide à protéger vos données au repos. Il est activé au niveau du compte de stockage. Il chiffre les données au fur et à mesure de leur écriture dans nos centres de données ; elles sont déchiffrées automatiquement lorsque vous y accédez. Cette méthode prend en charge les scénarios suivants :

- Chiffrement des objets blob de blocs, des objets blob d’ajout et des objets blob de pages
- Chiffrement des modèles et des disques durs virtuels archivés dans Azure depuis un emplacement local
- Chiffrement du système d’exploitation et des disques de données sous-jacents pour les machines virtuelles IaaS créées à l’aide de vos disques durs virtuels

Avant de passer au chiffrement du Stockage Azure, tenez compte de deux limitations :

- Il n’est pas disponible sur les comptes de stockage Classic.
- Il ne crypte les données écrites qu’une fois le chiffrement activé.

## <a name="use-a-centralized-security-management-system"></a>Utiliser un système de gestion centralisée de la sécurité

Les serveurs doivent être surveillés lors des mises à jour correctives, de la configuration, des événements et des activités qui peuvent être considérées comme des problèmes de sécurité. Pour résoudre ces problèmes, vous pouvez utiliser [Security Center](https://azure.microsoft.com/services/security-center/) et [Sécurité et conformité Operations Management Suite](https://azure.microsoft.com/services/security-center/). Ces deux options vont au-delà de la configuration au sein du système d’exploitation. Elles fournissent également une analyse de la configuration de l’infrastructure sous-jacente, comme la configuration du réseau et l’utilisation des appliances virtuelles.

## <a name="manage-operating-systems"></a>Gérer les systèmes d’exploitation

Dans un déploiement IaaS, vous êtes toujours responsable de la gestion des systèmes que vous déployez, comme tout autre serveur ou station de travail de votre environnement. Mise à jour corrective, sécurisation renforcée, affectation des droits et les autres activités relatives à la maintenance de votre système restent sous votre responsabilité. Pour les systèmes étroitement intégrés avec vos ressources locales, il peut être intéressant d’utiliser les mêmes outils et procédures qu’en local pour les antivirus, les logiciels anti-programme malveillant, les mises à jour correctives et les sauvegardes notamment.

### <a name="harden-systems"></a>Sécurisation renforcée
Toutes les machines virtuelles dans Azure IaaS doivent faire l’objet d’une sécurisation renforcée afin qu’elles n’exposent que les points de terminaison de service requis pour les applications installées. Pour les machines virtuelles Windows, suivez les recommandations publiées par Microsoft sous forme de lignes de base pour la solution de [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx).

Security Compliance Manager est un outil gratuit. Vous pouvez l’utiliser pour rapidement configurer et gérer vos postes de travail, votre centre de données traditionnel et vos clouds privé et public, à l’aide de la stratégie de groupe et de System Center Configuration Manager.

Security Compliance Manager fournit des stratégies prêtes à l’emploi et des packs de configuration de gestion de la configuration souhaitée testés. Ces lignes de base sont basées sur les recommandations [Instructions de sécurité Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx) et les meilleures pratiques du secteur. Ils vous aident à gérer les dérives de configuration, à respecter les exigences de conformité et à réduire les menaces de sécurité.

Vous pouvez utiliser Security Compliance Manager pour importer la configuration actuelle de vos ordinateurs à l’aide de deux méthodes différentes. Tout d’abord, vous pouvez importer des stratégies de groupe Active Directory. Ensuite, vous pouvez importer la configuration d’un ordinateur de référence « golden master » à l’aide de l’[outil LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) pour sauvegarder la stratégie de groupe locale. Vous pouvez ensuite importer la stratégie de groupe locale dans Security Compliance Manager.

Comparez vos standards aux meilleures pratiques du secteur, personnalisez-les et créez de nouvelles stratégies et de nouveaux packs de configuration de gestion de la configuration souhaitée. Les lignes de base ont été publiées pour tous les systèmes d’exploitation pris en charge, y compris la Mise à jour anniversaire Windows 10 et Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Installer et gérer des logiciels anti-programme malveillant

Pour les environnements hébergés séparément de votre environnement de production, vous pouvez utiliser une extension anti-programme malveillant pour protéger vos machines virtuelles et services cloud. Elle s’intègre avec [Azure Security Center](../security-center/security-center-intro.md).


Le [logiciel anti-programme malveillant Microsoft](azure-security-antimalware.md) inclut des fonctionnalités telles que la protection en temps réel, l’analyse planifiée, la correction des logiciels malveillants, la mise à jour des signatures, la mise à jour des moteurs, les rapports d’exemples, la collecte d’événements d’exclusion et la [prise en charge de PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Logiciel anti-programme malveillant Azure](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Installer les dernières mises à jour de sécurité
Parmi les premières charges de travail que nos clients déplacent vers Azure figurent les labos et les systèmes accessibles de l’extérieur. Si vos machines virtuelles hébergées par Azure hébergent des applications ou des services qui doivent être accessibles par Internet, soyez vigilant sur les mises à jour correctives. Installez les correctifs au-delà du système d’exploitation. Des vulnérabilités non corrigées sur des applications tierces peuvent également provoquer des problèmes pouvant être facilement évités si une gestion efficace des correctifs est en place.

### <a name="deploy-and-test-a-backup-solution"></a>Déployer et tester une solution de sauvegarde

Tout comme les mises à jour de sécurité, la sauvegarde doit être gérée de la même façon que les autres opérations. Cela s’applique aux systèmes qui font partie de votre environnement de production étendu au cloud. Les systèmes de développement et de test doivent suivre des stratégies de sauvegarde capables de fournir des capacités de restauration similaires à ce à quoi les utilisateurs se sont habitués au cours de leur expérience avec des environnements sur site.

Les charges de travail migrées vers Azure doivent s’intégrer avec les solutions de sauvegarde existantes lorsque cela est possible. Vous pouvez également utiliser [Azure Backup](../backup/backup-azure-arm-vms.md) pour répondre à vos exigences de sauvegarde.


## <a name="monitor"></a>Surveiller

[Security Center](../security-center/security-center-intro.md) évalue en continu l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires.

Voici quelques exemples :

- Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
- Configuration de groupes de sécurité réseau et de règles pour contrôler le trafic vers les machines virtuelles
- Approvisionnement de pare-feu d’applications web pour protéger vos applications web contre les attaques ciblées
- Déploiement de mises à jour système manquantes
- Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

L’illustration suivante montre certaines des options que vous pouvez activer dans Security Center.

![Stratégies Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud. Operations Management Suite étant implémenté sous la forme d’un service cloud, elle peut être déployée rapidement et avec un investissement minimal en ressources d’infrastructure.

Les nouvelles fonctionnalités sont fournies automatiquement, ce qui vous permet d’économiser sur les coûts de mise à niveau et de maintenance. Operations Management Suite s’intègre également avec System Center Operations Manager. Il comprend différents composants qui vous aident à mieux gérer vos charges de travail Azure, notamment un module [Sécurité et conformité](../operations-management-suite/oms-security-getting-started.md).

Vous pouvez utiliser les fonctionnalités de sécurité et de conformité dans Operations Management Suite pour afficher des informations concernant vos ressources. Les informations de cette série sont organisées en quatre catégories majeures :

- **Domaines de sécurité** : explorez davantage les enregistrements de sécurité au fil du temps. Accédez aux évaluations des logiciels malveillants, mises à jour des évaluations, informations de sécurité réseau, informations d’identité et d’accès, et ordinateurs avec des événements de sécurité. Tirez parti d’un accès rapide au tableau Azure Security Center.
- **Problèmes importants** : identifiez rapidement le nombre de problèmes actifs et leur gravité.
- **Détections (version préliminaire)** : identifiez les modèles d’attaque en visualisant les alertes de sécurité au fur et à mesure qu’elles affectent vos ressources.
- **Informations sur les menaces** : identifiez les modèles d’attaques en visualisant le nombre total de serveurs présentant un trafic IP sortant malveillant, le type de menace malveillante et une carte indiquant l’origine de ces adresses IP.
- **Requêtes de sécurité courantes** : consultez une liste des requêtes de sécurité les plus courantes que vous pouvez utiliser pour surveiller votre environnement. Cliquez sur l’une de ces requêtes pour ouvrir le panneau **Recherche** affichant les résultats de cette requête.

La capture d’écran suivante montre un exemple des informations que Operations Management Suite peut afficher.

![Instructions de sécurité de base pour Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>Étapes suivantes

* [Blog de l’équipe de sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)
* [Meilleures pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md)
