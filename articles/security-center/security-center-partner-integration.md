---
title: "Intégrer des solutions de sécurité dans Azure Security Center | Microsoft Docs"
description: "Découvrez comment Azure Security Center s’intègre avec les partenaires pour améliorer la sécurité globale de vos ressources Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: yurid
ms.openlocfilehash: 48648c2e84d2a2e4de01f04495fb08df603c6017
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Intégrer des solutions de sécurité dans Azure Security Center
Ce document vous aide à gérer les solutions de sécurité déjà connectées à Azure Security Center et à en ajouter de nouvelles.

## <a name="integrated-azure-security-solutions"></a>Solutions de sécurité Azure intégrées
Security Center simplifie l’activation des solutions de sécurité intégrées dans Azure. Voici les avantages :

- **Déploiement simplifié** : Security Center permet un approvisionnement rationalisé des solutions de partenaire intégrées. Pour des solutions comme les logiciels anti-programme malveillant et l’évaluation des vulnérabilités, Security Center peut approvisionner l’agent nécessaire sur vos machines virtuelles. En outre, pour les appliances de pare-feu, Security Center peut se charger d’une grande partie de la configuration réseau nécessaire.
- **Détections intégrées** : les événements de sécurité des solutions de partenaire sont automatiquement collectés, agrégés et affichés dans le cadre des alertes et des incidents de Security Center. Ces événements sont également fusionnés avec les détections d’autres sources pour fournir des fonctions de détection de menaces avancées.
- **Surveillance et gestion unifiées du fonctionnement** : les clients peuvent utiliser les événements d’analyse intégrés pour surveiller toutes les solutions de partenaire en un coup d’œil. La gestion de base offre un accès facile à la configuration avancée avec la solution de partenaire.

Actuellement, les solutions de sécurité intégrées comprennent :

- Protection de point de terminaison ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Windows Defender et Protocole d’inscription de certificats simple (SCEP))
- Pare-feu d’applications web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Pare-feu de nouvelle génération ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) et [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Évaluation des vulnérabilités ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

L’expérience d’intégration protection de point de terminaison peut varier en fonction de la solution. Le tableau suivant contient plus de détails sur l’expérience de chaque solution :

| Protection du point de terminaison               | Plateformes                             | Installation du centre de sécurité | Détection du centre de sécurité |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (logiciel anti-programme malveillant de Microsoft)                  | Windows Server 2016                   | Non, intégré au système d’exploitation           | OUI                       |
| System Center Endpoint Protection (logiciel anti-programme malveillant de Microsoft) | Windows Server 2012 R2, 2012, 2008 R2 | Via l’extension                | OUI                       |
| Trend Micro : toutes les versions         | Gamme Windows Server                 | Via l’extension                | OUI                       |
| Symantec v12.1.1100+                     | Gamme Windows Server                 | Non                            | OUI                        |
| MacAfee                           | Gamme Windows Server                 | Non                            | Non                         |
| Kaspersky                         | Gamme Windows Server                 | Non                            | Non                         |
| Sophos                            | Gamme Windows Server                 | Non                            | Non                         |



## <a name="how-security-solutions-are-integrated"></a>Comment sont intégrées les solutions de sécurité
Les solutions de sécurité Azure déployées à partir de Security Center sont automatiquement connectées. Vous pouvez également connecter d’autres sources de données de sécurité, notamment :

- Azure AD Identity Protection
- Ordinateurs exécutés en local ou dans d’autres clouds
- Solution de sécurité qui prend en charge le format CEF
- Microsoft Advanced Threat Analytics

![Intégration de solutions de partenaire](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gérer des solutions de sécurité Azure intégrées et d’autres sources de données

1. Connectez-vous au [Portail Azure](https://azure.microsoft.com/features/azure-portal/).

2. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**. La fenêtre **Security Center - Vue d’ensemble** s’ouvre.

  ![Vue d’ensemble de Security Center](./media/security-center-partner-integration/overview.png)

3. Sous **Vue d’ensemble**, sélectionnez **Solutions de sécurité**.

Sous **Solutions de sécurité**, vous pouvez consulter des informations sur le fonctionnement des solutions de sécurité Azure intégrées et effectuer des tâches de gestion de base. Vous pouvez également connecter d’autres types de sources de données de sécurité, telles que des journaux de pare-feu et des alertes Azure AD Identity Protection au format CEF.

### <a name="connected-solutions"></a>Solutions connectées

La section **Solutions connectées** inclut des solutions de sécurité qui sont actuellement connectées à Security Center et des informations sur l’état de fonctionnement de chaque solution.  

![Solutions connectées](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Consultez [gestion des solutions partenaires connectées](security-center-partner-solutions.md) pour en savoir plus.

### <a name="discovered-solutions"></a>Solutions découvertes

Security Center découvre automatiquement les solutions exécutées dans Azure mais qui ne sont pas connectées à Security Center. Elles sont ensuite affichées dans la section **Solutions découvertes**. Cela inclut les solutions Azure, telles que [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), ainsi que les solutions partenaires.

> [!NOTE]
> La fonctionnalité des solutions découvertes est disponible pour le niveau Standard de Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>
>

Sélectionne **CONNECTER** sous une solution pour l’intégrer à Security Center et être notifié sur les alertes de sécurité.

![Solutions découvertes](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center découvre aussi les solutions déployées dans l’abonnement, capables d’envoyer des journaux CEF (Common Event Format). Découvrez comment [Connecter une solution de sécurité](quick-security-solutions.md) qui utilise des journaux CEF dans Security Center.

### <a name="add-data-sources"></a>Ajouter des sources de données

La section **Ajouter des sources de données** comprend d’autres sources de données disponibles qui peuvent être connectées. Pour obtenir des instructions sur l’ajout de données à partir d’une de ces sources, cliquez sur **AJOUTER**.

![Sources de données](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à intégrer des solutions de partenaires dans Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Connexion de Microsoft Advanced Threat Analytics à Azure Security Center](security-center-ata-integration.md)
* [Connecting Azure Active Directory Identity Protection to Azure Security Center](security-center-aadip-integration.md) (Connexion d’Azure Active Directory Identity Protection à Azure Security Center)
* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Surveiller les solutions de partenaire avec Security Center](security-center-partner-solutions.md). découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [Questions fréquentes : Azure Security Center](security-center-faq.md). Obtenez des réponses aux questions fréquentes concernant l’utilisation de Security Center.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.
