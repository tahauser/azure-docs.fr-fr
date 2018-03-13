---
title: "Qu’est-ce que l’Azure Security Center ? | Microsoft Docs"
description: "Découvrez le Centre de sécurité Azure, ses fonctionnalités principales et son fonctionnement."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>Qu’est-ce que le Centre de sécurité Azure ?
Azure Security Center fournit des fonctionnalités unifiées de gestion de la sécurité et de protection avancée contre les menaces sur l’ensemble des charges de travail cloud hybrides. Avec Security Center, vous pouvez appliquer des stratégies de sécurité sur l’ensemble de vos charges de travail, limiter votre exposition aux menaces, détecter et répondre aux attaques.

Pourquoi utiliser Security Center ?

- **Gestion de stratégie centralisée** : assurez la conformité aux exigences de sécurité obligatoires ou de votre société en gérant de façon centralisée les stratégies de sécurité dans toutes vos charges de travail cloud hybride.
- **Évaluation continue de la sécurité** : contrôlez la sécurité des machines, réseaux, services de stockage et de données, et des applications pour découvrir d’éventuels problèmes de sécurité.
- **Recommandations exploitables** : corrigez les failles de sécurité avant qu’elles puissent être exploitées par des attaquants avec des recommandations de sécurité exploitables et classées par ordre de priorité.
- **Défenses cloud avancées** : minimisez les menaces avec un accès juste-à-temps aux ports de gestion et la mise sur liste verte pour contrôler les applications exécutées sur vos machines virtuelles.
- **Alertes et incidents classés par ordre de priorité** : concentrez-vous en premier lieu sur les menaces les plus importantes avec les incidents et alertes de sécurité classés par ordre de priorité.
- **Solutions de sécurité intégrées** : collectez, recherchez et analysez les données de sécurité d’un large éventail de sources, notamment les solutions partenaires connectées.

La **vue d’ensemble de Security Center** fournit un aperçu rapide de l’état de sécurité de vos charges de travail Azure et autres qu’Azure, ce qui vous permet de détecter et d’évaluer la sécurité de vos charges de travail, ainsi que d’identifier et d’atténuer les risques. Le tableau de bord intégré offre des insights instantanés sur les alertes de sécurité et les vulnérabilités nécessitant votre attention.

![Vue d'ensemble][1]

## <a name="centralized-policy-management"></a>Gestion de stratégie centralisée
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Security Center, vous définissez les stratégies et vous les adaptez à votre type de charge de travail ou à la sensibilité de vos données.

Les stratégies Security Center comprennent les composants suivant :

- **Collecte de données** : permet de déterminer le provisionnement d’agent et les paramètres de la [collecte de données](security-center-enable-data-collection.md) de sécurité.
- **Stratégie de sécurité** : permet de déterminer quels contrôles Security Center surveille et recommande par le biais de la modification de la [stratégie de sécurité](security-center-policies.md).
- **Notifications par e-mail** : permettent de déterminer les contacts de sécurité et les paramètres des [notifications par e-mail](security-center-provide-security-contact-details.md).
- **Niveau tarifaire** : permet de définir la [sélection d’un tarif](security-center-pricing.md) gratuit ou standard. Le niveau que vous choisissez détermine les fonctionnalités de Security Center qui sont disponibles pour les ressources visées.

![Stratégie de sécurité][2]

Pour plus d’informations, consultez [Vue d’ensemble des stratégies de sécurité](security-center-policies-overview.md).

## <a name="continuous-security-assessment"></a>Évaluation continue de la sécurité
Security Center analyse l’état de sécurité de vos ressources de calcul, réseaux virtuels, services de stockage et de données, et applications. L’évaluation continue vous aide à découvrir d’éventuels problèmes de sécurité, comme les systèmes avec des mises à jour de sécurité manquantes ou des ports réseau exposés. Sélectionnez une vignette dans la section Prévention pour afficher plus d’informations, notamment une liste des ressources et les vulnérabilités identifiées.

![Monitoring de l’intégrité de la sécurité][3]

Pour plus d’informations, consultez [Monitoring de l’intégrité de la sécurité](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Recommandations exploitables
Security Center analyse l’état de sécurité de vos ressources Azure et autres qu’Azure pour identifier les vulnérabilités de sécurité potentielles. Une liste de recommandations de sécurité par ordre de priorité vous guide tout au long du processus de gestion des problèmes de sécurité.

![Recommandations][4]

Pour plus d’informations, consultez [Recommandations de gestion de la sécurité](security-center-recommendations.md).

## <a name="just-in-time-vm-access"></a>Accès Juste à temps à la machine virtuelle
Réduisez la surface exposée aux attaques du réseau avec l’accès contrôlé juste à temps aux ports de gestion sur les machines virtuelles Azure, ce qui diminue de façon significative l’exposition aux attaques par force brute et autres attaques réseau.

![Accès Juste à temps à la machine virtuelle][5]

Définissez des règles sur la façon dont les utilisateurs peuvent se connecter aux machines virtuelles. Si nécessaire, l’accès peut être demandé à partir de Security Center ou via PowerShell. Tant que la requête est conforme aux règles, l’accès est accordé automatiquement pendant la durée demandée.

Pour plus d’informations, consultez [Gérer l’accès Juste à temps à la machine virtuelle](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Contrôles d’application adaptative
Bloquez les programmes malveillants et les autres applications indésirables en appliquant des recommandations de mise en liste verte adaptées à vos charges de travail Azure spécifiques et alimentées par l’apprentissage machine.

![Contrôles d’application adaptative][6]

Passez en revue et cliquez pour appliquer les règles recommandées de mise sur liste verte d’applications générées par Security Center ou modifiez les règles déjà configurées.

Pour plus d’informations, consultez [Contrôles d’application adaptative](security-center-adaptive-application.md).

## <a name="prioritized-alerts-and-incidents"></a>Alertes et incidents classés par ordre de priorité
Security Center utilise l’analytique avancée et une fonctionnalité de Threat Intelligence globale pour détecter les attaques entrantes et toute activité suivant la violation. Les alertes sont classées par ordre de priorité et regroupées en incidents, ce qui vous permet de vous concentrer tout d’abord sur les menaces les plus critiques. Vous pouvez aussi créer vos propres alertes de sécurité personnalisées.

![Alertes et incidents classés par ordre de priorité][7]

Vous pouvez évaluer rapidement l’étendue et l’impact d’une attaque à l’aide d’une expérience d’enquête visuelle et interactive. Vous pouvez utiliser des requêtes prédéfinies ou ad hoc pour une exploration plus approfondie des données de sécurité.

Pour plus d’informations, consultez [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md).

## <a name="integrate-your-security-solutions"></a>Intégrer vos solutions de sécurité
Dans Security Center, vous pouvez collecter, rechercher et analyser les données de sécurité d’un large éventail de sources, notamment les solutions partenaires connectées comme les pare-feu réseau et d’autres services Microsoft.

![Intégrer des solutions de sécurité][8]

Pour plus d’informations, consultez [Intégrer des solutions de sécurité](security-center-partner-integration.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour utiliser le Centre de sécurité, vous devez disposer d’un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free/).
- Le niveau tarifaire Gratuit de Security Center est activé avec votre abonnement Azure. Pour tirer parti des fonctionnalités avancées de gestion de la sécurité et de détection des menaces, vous devez mettre à niveau vers le niveau tarifaire Standard. Le niveau Standard est gratuit les 60 premiers jours. Pour plus d’informations, consultez la [page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).
- Si vous êtes prêt à activer Security Center Standard dès maintenant, le [démarrage rapide : Intégrer votre abonnement Azure à Security Center Standard](security-center-get-started.md) vous guidera.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
