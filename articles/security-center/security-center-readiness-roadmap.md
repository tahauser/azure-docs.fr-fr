---
title: Feuille de route de préparation de Azure Security Center | Microsoft Docs
description: Ce document vous fournit une feuille de route de préparation pour démarrer sur Azure Security Center.
services: security-center
documentationcenter: na
author: YuriDio
manager: ndicola
editor: ''
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 0de8dda6f88f31208c3fe7d560a461fea46a67e6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-security-center-readiness-roadmap"></a>Feuille de route de préparation de Azure Security Center
Ce document fournit une feuille de route de préparation qui vous aidera à prendre en main Azure Security Center.

## <a name="understanding-security-center"></a>Comprendre Security Center
Azure Security Center fournit une gestion unifiée de la sécurité et une protection avancée contre les menaces pour les charges de travail s’exécutant dans Azure, en local et dans d’autres clouds. 

Utilisez les ressources suivantes pour prendre en main Security Center.

Articles
* [Présentation d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* [Guide de démarrage rapide de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

vidéos
* [Vidéo de présentation rapide](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
* [Vue d’ensemble des fonctionnalités de prévention, de détection et de réponse de Security Center](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planification et fonctionnement
Pour tirer pleinement parti du Centre de sécurité, il est important de comprendre comment les différents utilisateurs ou équipes de votre entreprise utilisent ce service afin de répondre aux besoins de sécurisation des opérations, de surveillance, de gouvernance et de réponse aux incidents.

Utilisez les ressources suivantes pour vous aider lors des processus de planification et de fonctionnement.


Article
* [Guide des opérations et de planification d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

Vidéo
* [Protection de la charge de travail de cloud hybride avec Security Center](https://mva.microsoft.com/training-courses/hybrid-cloud-workload-protection-with-azure-security-center-18173?l=X4WqTA3jE_1106218965)

### <a name="onboarding-computers-to-security-center"></a>Intégration des ordinateurs à Security Center
Security Center détecte automatiquement les abonnements Azure ou les espaces de travail dans lesquels Security Center Standard n’est pas activé. Cela inclut les abonnements Azure utilisant Security Center Gratuit et les espaces de travail dans lesquels la solution de sécurité n’est pas activée.

Utilisez les ressources suivantes pour vous aider lors des processus d’intégration.

Article
* [Intégration d’Azure Security Center Standard pour une sécurité renforcée](https://docs.microsoft.com/azure/security-center/security-center-onboarding)

Vidéo
* [Vue d’ensemble de Azure Security Center hybride](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Atténuation des problèmes de sécurité à l’aide de Security Center
Le Centre de sécurité collecte, analyse et intègre automatiquement les données de journaux provenant de vos ressources Azure, du réseau et des solutions partenaires connectées, telles que les solutions de protection des points de terminaison et des pare-feu, pour détecter les menaces réelles et réduire le nombre de faux positifs.

Utilisez les ressources suivantes pour vous aider à gérer les alertes de sécurité et à protéger vos ressources.

Articles    
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
* [Protection de vos machines virtuelles dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-recommendations)
* [Protection de votre réseau dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
* [Protection de vos applications dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-application-recommendations)
* [Protection des données et du service SQL Azure dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


Vidéo   
* [Atténuation des problèmes de sécurité à l’aide de Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center pour la réponse aux incidents
Pour réduire les coûts et les dommages, il est important de disposer d’un plan de réponse aux incidents avant qu’une attaque ne survienne. Vous pouvez utiliser Azure Security Center à différentes étapes de la réponse à un incident.

Utilisez les ressources suivantes pour comprendre comment Security Center peut être intégré dans votre processus de réponse aux incidents.

vidéos  
* [Azure Security Center dans la réponse aux incidents](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Répondre rapidement aux menaces avec des opérations et des investigations de sécurité de nouvelle génération](https://youtu.be/e8iFCz5RM4g)

Articles    
* [Utilisation d’Azure Security Center pour la réponse aux incidents](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [Automatiser la réponse avec le playbook de sécurité](https://docs.microsoft.com/azure/security-center/security-center-playbooks)

## <a name="advanced-cloud-defense"></a>Défense de cloud avancée

Les machines virtuelles Azure peuvent profiter des fonctionnalités de défense de cloud avancée dans Security Center. Ces fonctionnalités incluent l’accès Juste à temps à la machine virtuelle ainsi que des contrôles d’application adaptatifs.

Utilisez les ressources suivantes pour savoir comment utiliser ces fonctionnalités dans Security Center.

Vidéo   
* [Azure Security Center – Accès Juste à temps à la machine virtuelle](https://youtu.be/UOQb2FcdQnU)

Articles    
* [Gérer l’accès Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Contrôles d’application adaptative dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>Travaux pratiques

* [Atelier pratique du Security Center](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook de recommandations du pare-feu d’applications web (WAF) dans Security Center](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Page de documentation de Security Center](https://docs.microsoft.com/azure/security-center/)
* [Page de documentation de l’API REST de Security Center](https://msdn.microsoft.com/library/mt704034.aspx)
* [FAQ de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Page de tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/)
* [Meilleures pratiques relatives à la sécurité d’identité](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)
* [Meilleures pratiques en matière de sécurité réseau](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)
* [Recommandations de PaaS](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [Conformité](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist)

## <a name="community-resources"></a>Ressources de la Communauté

* [UserVoice de Security Center](https://feedback.azure.com/forums/347535-azure-security-center)
* [Forums de la communauté de Security Center](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSecurityCenter)



