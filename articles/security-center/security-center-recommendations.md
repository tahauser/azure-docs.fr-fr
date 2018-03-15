---
title: "Gestion des recommandations de sécurité dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 2cb4a1c944d6893ca7913eef4e93620059f2a839
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestion des recommandations de sécurité dans le Centre de sécurité Azure
Ce document explique comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n'est pas un guide pas à pas.
>
>

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?
Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

## <a name="implementing-security-recommendations"></a>Mise en œuvre des recommandations de sécurité
### <a name="set-recommendations"></a>Définition de recommandations
Dans la section [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md), vous apprendrez à :

* configurer des stratégies de sécurité ;
* activer la collecte des données ;
* choisir les recommandations à afficher dans le cadre de votre stratégie de sécurité.

Les recommandations de stratégie actuelles se concentrent sur les mises à jour système, les règles de base, les logiciels anti-programme malveillant, les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour les sous-réseaux et les interfaces réseau, l’audit des bases de données SQL, le Transparent Data Encryption de bases de données SQL et les pare-feu d’applications web.  [Définition de stratégies de sécurité](security-center-policies.md) fournit une description de chacune des recommandations.

### <a name="monitor-recommendations"></a>Suivi des recommandations
Après la définition d’une stratégie de sécurité, le Centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La vignette **Recommandations** sous **Vue d’ensemble** vous permet de connaître le nombre total de recommandations fournies par Security Center.

![Panneau Recommandations][1]

Pour afficher les détails de chaque recommandation, sélectionnez la vignette **Recommandations** sous **Vue d’ensemble**. **Recommandations** s’ouvre.

![Filtrer les recommandations][2]

Les recommandations sont affichées dans un tableau où chaque ligne correspond à une recommandation. Les colonnes du tableau sont les suivantes :

* **DESCRIPTION**: explication de la recommandation et de la procédure à suivre pour résoudre le problème.
* **RESSOURCE**: ressources auxquelles s’appliquent les recommandations.
* **ÉTAT**: décrit l’état actuel de la recommandation :
  * **Ouverte**: la recommandation n’a pas encore été prise en compte.
  * **En cours**: la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
  * **Résolue**: la recommandation a déjà été appliquée (dans ce cas, la ligne est grisée).
* **GRAVITÉ**: donne le niveau de gravité de chaque recommandation :
  * **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle ou groupe de sécurité réseau). Le problème doit être analysé.
  * **Moyenne**: il existe une vulnérabilité ; des étapes supplémentaires ou non critiques sont requises pour l’éliminer ou pour terminer un processus.
  * **Faible**: existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

Utilisez le tableau ci-dessous pour mieux comprendre les recommandations et leurs effets.

> [!NOTE]
> Vous devez apprendre à différencier les [modèles de déploiement de type classique et Resource Manager](../azure-classic-rm.md) utilisés pour les ressources Azure.
>
>

| Recommandation | Description |
| --- | --- |
| [Activer la collecte des données pour des abonnements](security-center-enable-data-collection.md) |Recommande l’activation de la collecte des données dans la stratégie de sécurité pour chacun de vos abonnements et pour l’ensemble des machines virtuelles Azure et des ordinateurs non Azure. |
| [Corriger les configurations de sécurité](security-center-remediate-os-vulnerabilities.md) |Recommande d’aligner les configurations de votre système d’exploitation sur les règles de configuration de sécurité recommandées, comme le fait de ne pas permettre l’enregistrement des mots de passe. |
| [Appliquer des mises à jour système](security-center-apply-system-updates.md) |Recommande le déploiement des mises à jour de sécurité du système et des mises à jour critiques manquantes sur vos machines virtuelles et ordinateurs Windows et Linux. |
| [Appliquer un contrôle d’accès réseau Juste à temps](security-center-just-in-time.md) | Recommande d’appliquer un accès Juste à temps à la machine virtuelle. La fonctionnalité Juste à temps est disponible en préversion pour le niveau Standard de Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center. |
| [Redémarrage après des mises à jour système](security-center-apply-system-updates.md#reboot-after-system-updates) |Recommande de redémarrer une machine virtuelle pour terminer le processus de mise à jour du système. |
| [Add a web application firewall](security-center-add-web-application-firewall.md) |Recommande le déploiement d’un pare-feu d’applications web (WAF) pour les points de terminaison web. Une recommandation WAF est indiquée pour n’importe quelle IP publique (adresse IP de niveau d’instance ou adresse IP à équilibrage de charge) ayant un groupe de sécurité réseau associé avec des ports web entrants ouverts (80, 443). </br>Security Center recommande d’approvisionner un WAF pour vous défendre contre les attaques ciblant vos applications web sur les machines virtuelles et sur l’environnement App Service (ASE). Un environnement App Service (ASE) est une option de plan de service [Premium](https://azure.microsoft.com/pricing/details/app-service/) d'Azure App Service qui fournit un environnement totalement isolé et dédié pour l'exécution sécurisée de vos applications Azure App Service. Pour en savoir plus sur ASE, voir [Documentation sur l’environnement App Service](../app-service/environment/intro.md).</br>Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants. |
| [Finaliser la protection des applications](security-center-add-web-application-firewall.md#finalize-application-protection) |Pour terminer la configuration d’un pare-feu d’applications web, le trafic doit être redirigé vers l’appliance de pare-feu d’applications web. L’application de cette recommandation permet d’apporter les modifications nécessaires à la configuration. |
| [Ajouter un pare-feu de nouvelle génération](security-center-add-next-generation-firewall.md) |Recommande l’ajout d’un pare-feu de nouvelle génération d’un partenaire Microsoft afin de renforcer vos protections de sécurité. |
| [Acheminer le trafic uniquement via un pare-feu de nouvelle génération](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recommande la configuration de règles de groupe de sécurité réseau qui forcent le trafic entrant de votre machine virtuelle via votre pare-feu de nouvelle génération. |
| [Installer Endpoint Protection](security-center-install-endpoint-protection.md) |Recommande l’approvisionnement de logiciels anti-programme malveillant sur les machines virtuelles (Windows uniquement). |
| [Activer des groupes de sécurité réseau sur les sous-réseaux et ou les machines virtuelles](security-center-enable-network-security-groups.md) |Recommande l’activation des groupes de sécurité réseau sur les sous-réseaux ou les machines virtuelles. |
| [Restreindre l’accès via un point de terminaison accessible sur Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Recommande la configuration de règles de trafic entrant pour les groupes de sécurité réseau. |
| [Activer l’audit et la détection des menaces dans les serveurs SQL](security-center-enable-auditing-on-sql-servers.md) |Recommande l’activation de l’audit et de la détection des menaces pour les serveurs Azure SQL. (Service Azure SQL uniquement. N’inclut pas SQL en cours d’exécution sur vos machines virtuelles.) |
| [Activer l’audit et la détection des menaces dans les bases de données SQL](security-center-enable-auditing-on-sql-databases.md) |Recommande l’activation de l’audit et de la détection des menaces pour les bases de données Azure SQL. (Service Azure SQL uniquement. N’inclut pas SQL en cours d’exécution sur vos machines virtuelles.) |
| [Activer le chiffrement transparent des données des bases de données SQL](security-center-enable-transparent-data-encryption.md) |Recommande l’activation du chiffrement pour les bases de données SQL. (Service Azure SQL uniquement.) |
| [Activer l’agent de machine virtuelle](security-center-enable-vm-agent.md) |Vous permet de connaître les machines virtuelles qui nécessitent l’agent de machine virtuelle. L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle. |
| [Apply disk encryption (Appliquer le chiffrement de disque Azure Disk Encryption)](security-center-apply-disk-encryption.md) |Recommande le chiffrement des disques des machines virtuelles à l’aide d’Azure Disk Encryption (Windows et Linux). Le chiffrement est recommandé pour les systèmes d’exploitation et les volumes de données de votre machine virtuelle. |
| [Fournir des informations de contact de sécurité](security-center-provide-security-contact-details.md) |Vous recommande de fournir des informations de contact de sécurité pour chacun de vos abonnements. Les informations de contact correspondent à une adresse électronique et à un numéro de téléphone. Elles sont utilisées pour vous contacter si notre équipe de sécurité détecte que vos ressources sont compromises. |
| [Mettre à jour la version du système d’exploitation](security-center-update-os-version.md) |Recommande de mettre à jour la version du système d’exploitation de votre service Cloud vers la version la plus récente disponible pour votre famille de systèmes d’exploitation.  Pour en savoir plus sur Cloud Services, consultez [Vue d’ensemble de Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation des vulnérabilités non installée](security-center-vulnerability-assessment-recommendations.md) |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| [Corriger des vulnérabilités](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Vous permet de voir les vulnérabilités du système et des applications détectées par la solution d’évaluation des vulnérabilités installée sur votre machine virtuelle. |
| [Activer le chiffrement pour le compte de stockage Azure](security-center-enable-encryption-for-storage-account.md) | Recommande d’activer Azure Storage Service Encryption pour les données au repos. SSE (Storage Service Encryption) chiffre les données lorsqu’elles sont écrites dans le stockage Azure et les déchiffre avant récupération. SSE est actuellement disponible uniquement pour le service BLOB Azure et peut être utilisé pour les objets blob de blocs, les objets blob de pages les objets blob Append. Pour en savoir plus, consultez [Azure Storage Service Encryption pour les données au repos](../storage/common/storage-service-encryption.md).</br>SSE est uniquement pris en charge sur les comptes de stockage Resource Manager. |

Vous pouvez filtrer et ignorer les recommandations.

1. Sélectionnez **Filtrer** dans le panneau **Recommandations**. Le panneau **Filtrer** s’ouvre et vous permet d’afficher uniquement certains états ou niveaux de gravité.

2. Si vous déterminez qu’une recommandation n’est pas applicable, vous pouvez l’ignorer pour la faire disparaître. Il existe deux façons d’ignorer une recommandation. L’une consiste à cliquer avec le bouton droit de la souris sur un élément, puis à sélectionner **Ignorer**. L’autre consiste à pointer sur un élément, à cliquer sur les trois points qui apparaissent à droite, puis à sélectionner **Ignorer**. Vous pouvez afficher les recommandations ignorées en cliquant sur **Filtrer**, puis en sélectionnant **Rejeté**.

    ![Ignorer une recommandation][3]

### <a name="apply-recommendations"></a>Appliquer les recommandations
Après avoir examiné toutes les recommandations, vous pouvez décider d’en appliquer une en priorité. Les recommandations à appliquer en priorité sont celles dont le niveau de gravité est le plus élevé.

Dans le tableau de recommandations ci-dessus, sélectionnez une recommandation et suivez les instructions pour savoir comment appliquer une recommandation.

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
