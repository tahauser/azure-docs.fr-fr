---
title: "Définir des stratégies de sécurité dans Azure Security Center | Microsoft Docs"
description: "Cet article est conçu pour vous aider à configurer des stratégies de sécurité dans Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 4d1a30b046c0c398d934291a907af891e9ac7fdf
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="set-security-policies-in-azure-security-center"></a>Définir des stratégies de sécurité dans Azure Security Center
Cet article est conçu pour vous aider à configurer des stratégies de sécurité dans Security Center.

## <a name="how-security-policies-work"></a>Fonctionnement des stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Dans Security Center, vous pouvez modifier les stratégies et en surveiller la conformité.

> [!NOTE]
> Vous pouvez maintenant étendre des stratégies Security Center à l’aide d’Azure Policy, disponible en préversion limitée. Pour vous inscrire à la préversion, accédez à [S’inscrire à Azure Policy](https://aka.ms/getpolicy). Pour plus d’informations, consultez la page [Intégrer des stratégies de sécurité Security Center avec Azure Policy](security-center-azure-policy.md).

Les exigences de sécurité applicables aux ressources utilisées pour le développement ou le test peuvent être différentes de celles imposées aux ressources utilisées pour les applications de production. Les applications qui utilisent des données réglementées, telles que des informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans Azure Security Center déterminent les recommandations de sécurité et la surveillance qui vous aident à identifier les vulnérabilités potentielles et à éliminer les menaces. Pour plus d’informations sur le choix de l’option adaptée à votre situation, consultez le [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Modifier des stratégies de sécurité
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos abonnements Azure dans Security Center. Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire, de collaborateur ou d’administrateur de la sécurité de l’abonnement. Pour configurer des stratégies de sécurité dans Security Center, procédez comme suit :

1. Connectez-vous au portail Azure.

2. Dans le tableau de bord **Security Center**,sous **Général**, sélectionnez **Stratégie de sécurité**.

3. Sélectionnez l’abonnement pour lequel vous souhaitez activer une stratégie de sécurité.

4. Dans la section **Composants de la stratégie**, sélectionnez **Stratégie de sécurité**.  
    Il s’agit de la stratégie par défaut affectée par Security Center. Vous pouvez activer ou désactiver les recommandations de sécurité disponibles.

5. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

## <a name="available-security-policy-definitions"></a>Définitions de stratégie de sécurité disponibles

Référez-vous au tableau suivant afin de comprendre les définitions de stratégies qui sont disponibles dans la stratégie de sécurité par défaut :

| Stratégie | Rôle de la stratégie |
| --- | --- |
| Mises à jour système |Une liste quotidienne des mises à jour de sécurité et critiques disponibles est récupérée à partir de Windows Update ou de Windows Server Update Services. La liste récupérée dépend du service configuré pour vos machines virtuelles et recommande d’appliquer les mises à jour manquantes. Pour les systèmes Linux, la stratégie utilise le système de gestion des packages fournis par un distributeur afin de déterminer pour quels packages des mises à jour sont disponibles. Elle vérifie également la disponibilité des mises à jour de sécurité et critiques à partir des machines virtuelles des [Services cloud Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurations de sécurité |Analyse les configurations de système d’exploitation tous les jours afin d’identifier les problèmes susceptibles de rendre la machine virtuelle vulnérable aux attaques. Cette stratégie recommande également des modifications de configuration pour résoudre ces vulnérabilités. Consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations spécifiques surveillées. (À ce stade, Windows Server 2016 n'est pas entièrement pris en charge.) |
| Protection du point de terminaison |Recommande la configuration de la protection du point de terminaison pour toutes les machines virtuelles (VM) Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants. |
| Chiffrement de disque |Recommande d’activer le chiffrement de disque dans toutes les machines virtuelles pour améliorer la protection des données au repos. |
| Groupes de sécurité réseau |Recommande la configuration de [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour contrôler le trafic entrant et sortant vers les machines virtuelles dotées de points de terminaison publics. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. En plus de vérifier si un groupe de sécurité réseau a été configuré, cette stratégie évalue les règles de sécurité de trafic entrant afin d’identifier les règles autorisant le trafic entrant. |
| Pare-feu d’application web |Recommande la configuration d’un pare-feu d’applications web sur les machines virtuelles lorsque l’une des conditions suivantes est remplie : <ul><li>Une [adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) est utilisée et les règles de sécurité entrantes pour le groupe de sécurité réseau associé sont configurées pour autoriser l’accès au port 80/443.</li><li>Une adresse IP à équilibrage de charge est utilisée et les règles associées d’équilibrage de charge et NAT (Network Access Translation) de trafic entrant sont configurées pour autoriser l’accès au port 80 ou 443. Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](../load-balancer/load-balancer-arm.md).</li> |
| Pare-feu de nouvelle génération |Étend les protections du réseau au-delà des groupes de sécurité réseau intégrés à Azure. Security Center détecte les déploiements pour lesquels un pare-feu de nouvelle génération est recommandé et vous permet ensuite de configurer une appliance virtuelle. |
| Audit SQL et détection des menaces |Recommande l’activation de l’audit de l’accès à votre base de données SQL pour la conformité et la détection avancée des menaces à des fins d’examen. |
| Chiffrement SQL |Recommande l’activation du chiffrement au repos pour votre base de données SQL, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Même si vos données font l’objet d’une violation de sécurité, elles ne sont pas lisibles. |
| Évaluation des vulnérabilités |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| Chiffrement du stockage |Cette fonctionnalité est actuellement disponible pour les objets blob et Azure Files. Après l’activation du chiffrement de service de stockage, seules les nouvelles données sont chiffrées et tous les fichiers existants dans ce compte de stockage restent non chiffrés. |
| Accès réseau JIT |Lorsque la fonctionnalité d’accès réseau Juste à temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant doit être verrouillé. Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ Azure Security Center](security-center-faq.md) : obtenez des réponses aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
