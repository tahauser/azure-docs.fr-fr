---
title: "Intégration des stratégies de sécurité Azure Security Center avec Azure Policy | Microsoft Docs"
description: "Ce document est conçu pour vous aider à configurer l’intégration de stratégies de sécurité Azure Security Center avec Azure Policy."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 5dedad4fa3695d1b210e1174d8f29966d2259889
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Intégrer des stratégies de sécurité Security Center avec Azure Policy
Cet article est conçu pour vous aider à configurer les stratégies de sécurité Azure Security Center offertes par Azure Policy.

## <a name="how-security-policies-work"></a>Fonctionnement des stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier les stratégies dans Security Center ou utiliser [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) pour effectuer les opérations suivantes :
* Créer de nouvelles définitions de stratégie.
* Affecter des stratégies pour des groupes d’administration, qui peuvent représenter une organisation entière ou une unité commerciale au sein de l’organisation.
* Surveiller la conformité à la stratégie.

> [!NOTE]
> La stratégie Azure est disponible en préversion limitée. Pour s’inscrire, accédez à [S’inscrire à Azure Policy](https://aka.ms/getpolicy). Pour plus d’informations sur Azure Policy, consultez [Créer et gérer des stratégies pour mettre en vigueur la conformité](http://docs.microsoft.com/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Modifier des stratégies de sécurité
Vous pouvez modifier la stratégie de sécurité par défaut pour chacun de vos abonnements Azure dans Security Center. Pour modifier une stratégie de sécurité, vous devez avoir le rôle d’administrateur de la sécurité, de propriétaire ou de collaborateur pour l’abonnement concerné ou le groupe de gestion dans laquelle elle se trouve. Pour afficher vos stratégies de sécurité dans Security Center, procédez comme suit :

1. Connectez-vous au portail Azure.

2. Dans le tableau de bord **Security Center**,sous **Général**, sélectionnez **Stratégie de sécurité**.

    ![Le volet Gestion des stratégies](./media/security-center-azure-policy/security-center-policies-fig10.png)

3. Sélectionnez l’abonnement pour lequel vous souhaitez activer une stratégie de sécurité.  

4. Dans la section **Composants de la stratégie**, sélectionnez **Stratégie de sécurité**.  
    La fenêtre **Notions de base** s’ouvre.

    ![Composants de la stratégie](./media/security-center-azure-policy/security-center-policies-fig12.png)

5. Pour supprimer une définition de stratégie, sous **Stratégies et paramètres**, sélectionnez **Supprimer** en regard de la définition que vous voulez supprimer.

6. Cliquez sur **Enregistrer**.  
    La fenêtre **Définitions disponibles** s’ouvre, affichant la stratégie par défaut qui est attribuée à Security Center via Azure Policy.

7. (Facultatif) Dans la fenêtre **Définitions disponibles**, effectuez l’une des opérations suivantes :

    * Pour ajouter une définition de stratégie, sélectionnez le signe plus (+) en regard de la définition.

    ![Définitions de stratégie disponibles](./media/security-center-azure-policy/security-center-policies-fig11.png)

    * Pour obtenir une explication détaillée d’une stratégie, sélectionnez-la.  
    Une fenêtre **Aperçu** de la définition s’ouvre. Elle affiche une description de la définition et un lien vers le code JSON de la structure de la [Définition de stratégie](../azure-policy/policy-definition.md).

    ![La fenêtre Aperçu de la définition](./media/security-center-azure-policy/security-center-policies-fig14.png)

7. Lorsque vous avez terminé, cliquez sur **Enregistrer**.

## <a name="available-security-policy-definitions"></a>Définitions de stratégie de sécurité disponibles

Référez-vous au tableau suivant afin de comprendre les définitions de stratégies qui sont disponibles dans la stratégie de sécurité par défaut :

| Stratégie | Ce que fait la stratégie activée |
| --- | --- |
| Mises à jour système |Une liste quotidienne des mises à jour de sécurité et critiques disponibles est récupérée à partir de Windows Update ou de Windows Server Update Services. La liste récupérée dépend du service configuré pour vos machines virtuelles et recommande d’appliquer les mises à jour manquantes. Pour les systèmes Linux, la stratégie utilise le système de gestion des packages fournis par un distributeur afin de déterminer pour quels packages des mises à jour sont disponibles. Elle vérifie également la disponibilité des mises à jour de sécurité et critiques à partir des machines virtuelles des [Services cloud Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Configurations de sécurité |Analyse les configurations de système d’exploitation tous les jours afin d’identifier les problèmes susceptibles de rendre la machine virtuelle vulnérable aux attaques. Cette stratégie recommande également des modifications de configuration pour résoudre ces vulnérabilités. Consultez la [liste des règles de base recommandées](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) pour plus d’informations sur les configurations spécifiques surveillées. (À ce stade, Windows Server 2016 n'est pas entièrement pris en charge.) |
| Protection du point de terminaison |Recommande la configuration de la protection du point de terminaison pour toutes les machines virtuelles (VM) Windows afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants. |
| Chiffrement de disque |Recommande d’activer le chiffrement de disque dans toutes les machines virtuelles pour améliorer la protection des données au repos. |
| Groupes de sécurité réseau |Recommande la configuration de [Groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour contrôler le trafic entrant et sortant vers les machines virtuelles dotées de points de terminaison publics. Les groupes de sécurité réseau configurés pour un sous-réseau sont hérités par toutes les interfaces réseau de machine virtuelle, sauf indication contraire. En plus de vérifier si un groupe de sécurité réseau a été configuré, cette stratégie évalue les règles de sécurité de trafic entrant afin d’identifier les règles autorisant le trafic entrant. |
| Pare-feu d’application web |Recommande la configuration d’un pare-feu d’applications web sur les machines virtuelles lorsque l’une des conditions suivantes est remplie : <ul><li>Une [adresse IP publique de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) est utilisée et les règles de sécurité entrantes pour le groupe de sécurité réseau associé sont configurées pour autoriser l’accès au port 80/443.</li><li>Une adresse IP à équilibrage de charge est utilisée et les règles associées d’équilibrage de charge et NAT (Network Access Translation) de trafic entrant sont configurées pour autoriser l’accès au port 80 ou 443. Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](../load-balancer/load-balancer-arm.md).</li> |
| Pare-feu de nouvelle génération |Étend les protections du réseau au-delà des groupes de sécurité réseau intégrés à Azure. Security Center détecte les déploiements pour lesquels un pare-feu de nouvelle génération est recommandé et vous permet ensuite de configurer une appliance virtuelle. |
| Audit SQL et détection des menaces |Recommande l’activation de l’audit de l’accès à Azure SQL Database à des fins de conformité et de détection avancée des menaces et d’examen. |
| Chiffrement SQL |Recommande l’activation du chiffrement au repos pour votre base de données Azure SQL Database, ainsi que pour les sauvegardes associées et les fichiers journaux de transaction. Même si vos données font l’objet d’une violation de sécurité, elles ne sont pas lisibles. |
| Évaluation des vulnérabilités |Recommande d’installer une solution d’évaluation des vulnérabilités sur votre machine virtuelle. |
| Chiffrement du stockage |Cette fonctionnalité est actuellement disponible pour le stockage d’objets blob et les fichiers Azure. Après l’activation du chiffrement de service de stockage, seules les nouvelles données sont chiffrées et tous les fichiers existants dans ce compte de stockage restent non chiffrés. |
| Accès réseau JIT |Lorsque la fonctionnalité d’accès réseau Juste à temps est activée, Security Center verrouille le trafic entrant vers vos machines virtuelles Azure en créant une règle de groupe de sécurité réseau. Vous sélectionnez les ports de la machine virtuelle pour lesquels le trafic entrant doit être verrouillé. Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time). |


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à configurer des stratégies de sécurité dans le Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Guide des opérations et de planification d’Azure Security Center](security-center-planning-and-operations-guide.md) : découvrez comment planifier l’adoption d’Azure Security Center et prenez connaissance des considérations relatives à la conception.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ Azure Security Center](security-center-faq.md) : obtenez des réponses aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.
