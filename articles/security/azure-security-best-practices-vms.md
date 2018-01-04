---
title: "Meilleures pratiques de sécurité pour les machines virtuelles Azure | Microsoft Docs"
description: "Cet article propose plusieurs meilleures pratiques de sécurité à utiliser dans les machines virtuelles situées dans Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: db8b0cc58738308116da84f2a45d6507c87f3cde
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Meilleures pratiques pour la sécurité des machines virtuelles Azure

Dans la plupart des scénarios IaaS (Infrastructure en tant que service), les [machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/) représentent la principale charge de travail pour les organisations qui utilisent l’informatique cloud. Cela est particulièrement vrai dans les [scénarios hybrides](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) où les organisations souhaitent migrer lentement les charges de travail vers le cloud. Dans ces cas, suivez les [considérations générales de sécurité pour IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) et appliquez les meilleures pratiques de sécurité à toutes vos machines virtuelles.

Cet article décrit différentes meilleures pratiques de sécurité qui sont le résultat de nos expériences ou de celles de nos clients avec les machines virtuelles.

Ces meilleures pratiques font l’objet d’un consensus et sont compatibles avec les capacités et fonctionnalités actuelles de la plateforme Azure. Les opinions et technologies évoluant au fil du temps, nous prévoyons de mettre cet article régulièrement à jour en fonction de ces changements.

Pour chaque meilleure pratique, cet article détaille les points suivants :

* la nature de la meilleure pratique ;
* l’intérêt de la mettre en œuvre ;
* comment la mettre en œuvre ;
* les éventuelles conséquences en cas de non-mise en œuvre ;
* ses alternatives possibles.

Cet article examine les meilleures pratiques suivantes pour les machines virtuelles :

* Authentification des machines virtuelles et contrôle d’accès à celles-ci
* Disponibilité des machines virtuelles et accès au réseau
* Protéger les données au repos sur les machines virtuelles Azure à l’aide du chiffrement
* Gérer les sauvegardes des machines virtuelles
* Gérer l’état de sécurité des machines virtuelles
* Analyser les performances des machines virtuelles

## <a name="vm-authentication-and-access-control"></a>Authentification des machines virtuelles et contrôle d’accès à celles-ci

La première étape dans la protection de votre machine virtuelle consiste à vous assurer que seuls les utilisateurs autorisés sont en mesure de créer des machines virtuelles. Vous pouvez utiliser les [stratégies Azure](../azure-policy/azure-policy-introduction.md) pour définir les conventions des ressources de votre organisation, créer des stratégies personnalisées et les appliquer aux ressources, comme un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).

Les machines virtuelles qui appartiennent à un groupe de ressources héritent naturellement ses stratégies. Si nous recommandons cette approche pour la gestion des machines virtuelles, vous pouvez également contrôler l’accès à chaque stratégie de machine virtuelle à l’aide du [contrôle d’accès basé sur les rôles (RBAC)](../active-directory/role-based-access-control-configure.md).

Lorsque vous activez des stratégies Resource Manager et le contrôle RBAC pour contrôler l’accès aux machines virtuelles, vous renforcez la sécurité globale des machines virtuelles. Nous vous recommandons de consolider les machines virtuelles ayant le même cycle de vie, dans le même groupe de ressources. Les groupes de ressources vous aident à déployer et surveiller vos ressources, tout en compilant leur coût. Pour permettre aux utilisateurs de créer des machines virtuelles et d’y accéder, utilisez l’[approche du moindre privilège](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). Et lorsque vous attribuez des privilèges aux utilisateurs, privilégiez les rôles Azure intégrés suivants :

- [Collaborateur de machine virtuelle](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) : peut gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées.
- [Collaborateur de machine virtuelle classique](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) : peut gérer les machines virtuelles créées avec le modèle de déploiement classique, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées.
- [Gestionnaire de sécurité](../active-directory/role-based-access-built-in-roles.md#security-manager) : peut gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles.
- [Utilisateur de DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user) : peut tout afficher et connecter, démarrer, redémarrer et arrêter les machines virtuelles.

Ne partagez pas les comptes ou les mots de passe entre plusieurs administrateurs, et ne réutilisez pas les mots de passe dans plusieurs comptes d’utilisateur ou services, notamment pour les médias sociaux ou d’autres activités non administratives. Dans l’idéal, utilisez les modèles [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour créer vos machines virtuelles en toute sécurité. Avec cette approche, vous renforcez vos choix de déploiement et appliquez les paramètres de sécurité tout au long du déploiement.

Les organisations qui n’appliquent aucun contrôle d’accès aux données grâce aux fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. L’accès de certains utilisateurs à certaines données peut compromettre directement ces dernières.

## <a name="vm-availability-and-network-access"></a>Disponibilité des machines virtuelles et accès au réseau

Si votre machine virtuelle exécute des applications critiques qui requièrent une haute disponibilité, il est vivement recommandé d’utiliser plusieurs machines virtuelles. Pour une meilleure disponibilité, créez au moins deux machines virtuelles dans le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) requiert également que les machines virtuelles dont la charge est équilibrée appartiennent au même groupe à haute disponibilité. Si ces machines virtuelles doivent être accessibles à partir d’Internet, vous devez configurer un [équilibrage de charge sur Internet](../load-balancer/load-balancer-internet-overview.md).

Lorsque les machines virtuelles sont exposées à Internet, vous devez vérifier que vous [contrôlez le trafic réseau à l’aide de groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Comme les groupes de sécurité réseau s’appliquent aux sous-réseaux, vous pouvez réduire leur nombre en regroupant vos ressources par sous-réseau et en les appliquant aux sous-réseaux. L’objectif est de créer une couche d’isolement réseau, en configurant correctement les fonctionnalités de [sécurité réseau](../best-practices-network-security.md) dans Azure.

Vous pouvez également utiliser la fonctionnalité d’accès juste à temps d’Azure Security Center pour contrôler qui accède à distance à une machine virtuelle et pendant combien de temps.

Les organisations qui ne mettent pas en œuvre de restrictions d’accès au réseau pour les machines virtuelles connectées à Internet s’exposent à des risques de sécurité, comme des attaques par force brute RDP (Remote Desktop Protocol).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Protéger les données au repos sur les machines virtuelles Azure à l’aide du chiffrement

Le [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire vers la confidentialité, la conformité et la souveraineté des données. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permet aux administrateurs informatiques de chiffrer les disques des machines virtuelles IaaS Windows et Linux. Il utilise la fonctionnalité standard BitLocker de Windows et la fonctionnalité dm-crypt de Linux pour chiffrer les volumes des disques du système d’exploitation et des données.

Azure Disk Encryption vous permet de protéger vos données selon les critères de sécurité et de conformité de votre organisation. Celle-ci doit envisager le chiffrement pour réduire les risques d’accès non autorisé aux données. Nous vous recommandons également de chiffrer vos lecteurs avant d’écrire des données sensibles dessus.

Veillez à chiffrer les volumes de données de vos machines virtuelles afin de les protéger au repos dans votre compte de stockage Azure. Protégez les clés de chiffrement et les secrets avec [Azure Key Vault](https://azure.microsoft.com/documentation/articles/key-vault-whatis/).

Les organisations qui ne chiffrent pas leurs données sont plus exposées aux problèmes d’intégrité. Par exemple, les utilisateurs non autorisés ou non fiables peuvent voler des données dans des comptes compromis ou accéder indûment à des données codées en ClearFormat. Outre ces risques, pour se conformer aux réglementations en vigueur, les entreprises doivent prouver leur diligence et la mise en place de contrôles appropriés visant à améliorer la sécurité des données.

Pour en savoir plus sur Disk Encryption, consultez [Azure Disk Encryption pour machines virtuelles IaaS Windows et Linux](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Gérer les sauvegardes des machines virtuelles

Étant donné que les machines virtuelles Azure, comme toutes les machines virtuelles locales, sont destinées à être gérées par l’utilisateur, Azure ne leur envoie pas les mises à jour de Windows. Cependant, vous êtes invité à maintenir la configuration automatique de Windows Update activée. L’autre option consiste à déployer [WSUS (Windows Server Update Services)](https://technet.microsoft.com/windowsserver/bb332157.aspx) ou une autre solution de gestion des mises à jour sur une autre machine virtuelle Azure ou locale. WSUS et Windows Update maintiennent les machines virtuelles à jour. Nous vous recommandons également d’utiliser un logiciel d’analyse pour vérifier que toutes vos machines virtuelles IaaS sont à jour.

Les images fournies par Azure intègrent régulièrement les mises à jour de Windows les plus récentes. Toutefois, il n’existe aucune garantie que les images seront à jour lors du déploiement. Un léger décalage (de quelques semaines au maximum) après la diffusion des mises à jour est possible. Chaque déploiement doit commencer par la recherche et l’installation de toutes les mises à jour de Windows. Cette phase est particulièrement importante lorsque vous déployez les images que vous avez créées ou issues de votre propre bibliothèque. Les images fournies dans le cadre d’Azure Marketplace sont automatiquement mises à jour par défaut.

Les organisations qui n’appliquent pas de stratégies de mise à jour logicielle s’exposent davantage aux menaces qui exploitent les vulnérabilités connues, déjà corrigées. Outre ces risques, pour se conformer aux réglementations en vigueur, les entreprises doivent prouver leur diligence et la mise en place de contrôles appropriés visant à améliorer la sécurité de leur charge de travail dans le cloud.

Il faut souligner que les meilleures pratiques des centres de données traditionnels et IaaS Azure en matière de mise à jour logicielle présentent beaucoup de similitudes. Nous vous recommandons donc d’évaluer vos stratégies actuelles de mise à jour logicielle afin d’y inclure les machines virtuelles.

## <a name="manage-your-vm-security-posture"></a>Gérer l’état de sécurité des machines virtuelles

Les cybermenaces évoluent et la protection de vos machines virtuelles requiert des fonctionnalités plus complètes, capables de détecter les menaces, d’empêcher les accès non autorisés à vos ressources, de déclencher des alertes et réduire les faux positifs. L’état de sécurité d’une telle charge de travail englobe tous les aspects de sécurité de la machine virtuelle, depuis la gestion des mises à jour jusqu’à l’accès sécurisé au réseau.

Pour surveiller l’état de la sécurité de vos [machines virtuelles Windows](../security-center/security-center-virtual-machine.md) et [Linux](../security-center/security-center-linux-virtual-machine.md), utilisez [Azure Security Center](../security-center/security-center-intro.md). Dans Azure Security Center, protégez vos machines virtuelles grâce aux fonctionnalités suivantes :

* Appliquer les paramètres de sécurité du système d’exploitation avec les règles de configuration recommandées
* Rechercher et télécharger les mises à jour critiques et les mises à jour de sécurité qui peuvent être manquantes
* Mettre en œuvre les recommandations de protection contre les logiciels malveillants
* Valider le chiffrement des disques
* Évaluer et corriger les vulnérabilités
* Détecter les menaces

Security Center peut surveiller activement les menaces qui sont identifiées sous **Alertes de sécurité**. Les menaces corrélées sont regroupées dans la vue **Incident de sécurité**.

Pour comprendre comment Security Center peut vous aider à identifier les menaces potentielles dans vos machines virtuelles Azure, regardez la vidéo ci-dessous :

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Les organisations qui n’appliquent pas une sécurité renforcée à leurs machines virtuelles ne détectent pas les actions des utilisateurs non autorisés visant à contourner les contrôles de sécurité mis en place.

## <a name="monitor-vm-performance"></a>Analyser les performances des machines virtuelles

Parfois, une machine virtuelle consomme trop de ressources, ce qui peut poser problème. Une machine virtuelle insuffisamment performante peut entraîner une interruption de service, réduisant ainsi la disponibilité. C’est pourquoi il est impératif de surveiller l’accès aux machines virtuelles de façon réactive (lorsqu’un problème survient) et de façon proactive (par rapport à des performances de référence mesurées pendant un fonctionnement normal).

En analysant les [fichiers journaux de diagnostic Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), vous pouvez surveiller les ressources de vos machines virtuelles et identifier les problèmes potentiels susceptibles de nuire à la disponibilité et aux performances. L’extension Diagnostics Azure fournit des fonctionnalités d’analyse et de diagnostic pour les machines virtuelles Azure Windows. Vous pouvez activer ces fonctionnalités en intégrant l’extension dans le [modèle Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Vous pouvez également utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) pour obtenir plus de visibilité sur l’intégrité de votre ressource.

Les organisations qui ne surveillent pas les performances des machines virtuelles ne peuvent pas déterminer si certaines variations des performances sont normales ou pas. Si la machine virtuelle consomme davantage de ressources que la normale, cela peut être le signe d’une attaque potentielle par une ressource externe ou un processus interne compromis.
