---
title: "Considérations relatives à la conception d'identités hybrides Azure Active Directory - Suite | Microsoft Docs"
description: "Résumé et étapes suivantes après lecture du guide des considérations relatives à la conception d'identités hybrides"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 02d48768-ea9e-4bfe-ae54-b54c4bd0a789
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 29c045af81134847321d9ef69943f0154e9ee5a0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations--next-steps"></a>Considérations relatives à la conception d'identités hybrides Azure Active Directory - Suite
Maintenant que vous avez défini vos exigences et examiné toutes les options pour votre solution de gestion des périphériques mobiles, vous êtes prêt à passer aux étapes suivantes et à déployer l'infrastructure sous-jacente qui vous est adaptée ainsi qu'à votre organisation.

## <a name="hybrid-identity-solutions"></a>Solutions d'identité hybride
Utiliser des scénarios de solution précis qui répondent à vos besoins est un excellent moyen d'étudier et de planifier les détails du déploiement d'une infrastructure de gestion des périphériques mobiles. Les solutions suivantes décrivent plusieurs scénarios de gestion des périphériques mobiles parmi les plus courants :

* La [solution de gestion des périphériques mobiles et ordinateurs dans des environnements d'entreprise](https://technet.microsoft.com/library/dn582037.aspx) vous permet de gérer les périphériques mobiles en étendant votre infrastructure locale System Center 2012 Configuration Manager dans le cloud avec Microsoft Intune. Cette infrastructure hybride permet aux professionnels de l'informatique des environnements de taille moyenne ou grande d'activer le BYOD et l'accès à distance tout en réduisant la complexité d'administration.
* La [solution de gestion des périphériques mobiles pour Configuration Manager 2007](https://technet.microsoft.com/library/dn508400.aspx) vous permet de gérer les périphériques mobiles lorsque votre infrastructure se trouve sur un serveur System Center Configuration Manager 2007. Cette solution montre comment configurer un serveur unique sous System Center 2012 Configuration Manager pour pouvoir ensuite exécuter Microsoft Intune et tirer parti de ses capacités de GPM.
* La [solution de gestion des périphériques mobiles dans de petits environnements](https://technet.microsoft.com/library/dn715906.aspx) est destinée aux petites entreprises qui doivent prendre en charge la GPM. Elle explique comment utiliser Microsoft Intune pour étendre votre infrastructure actuelle afin qu'elle prenne en charge la gestion des périphériques mobiles et le BYOD. Cette solution décrit le plus simple des scénarios pris en charge d'utilisation de Microsoft Intune dans une configuration cloud uniquement et autonome sans serveurs locaux.

## <a name="hybrid-identity-documentation"></a>Documentation des identités hybrides
Les contenus relatifs à la planification conceptuelle et procédurale, au déploiement et à l'administration sont utiles lors de l'implémentation de votre solution de gestion des périphériques mobiles :

* [Microsoft System Center](https://technet.microsoft.com/library/cc507089.aspx) peuvent vous aider à recueillir et à agréger des informations sur votre infrastructure, les stratégies, les processus et les meilleures pratiques afin que votre personnel informatique puisse créer des systèmes gérables et automatiser les opérations.
* [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx) est un service cloud de gestion des périphériques qui vous aide à gérer vos ordinateurs et périphériques mobiles et à sécuriser les informations de votre société.
* [GPM pour Office 365](https://technet.microsoft.com/library/ms.o365.cc.devicepolicy.aspx) vous permet de gérer et de sécuriser les périphériques mobiles lorsqu'ils sont connectés à votre organisation Office 365. Vous pouvez utiliser la GPM pour Office 365 afin de définir des règles d'accès et des stratégies de sécurité des périphériques et de réinitialiser les périphériques mobiles s'ils sont perdus ou volés.

## <a name="hybrid-identity-resources"></a>Ressources sur les identités hybrides
L'analyse des ressources suivantes fournit souvent les dernières actualités et mises à jour des solutions de gestion des périphériques mobiles :

* [Blog Microsoft Enterprise Mobility](http://blogs.technet.com/b/enterprisemobility/)
* [Blog Microsoft In The Cloud](http://blogs.technet.com/b/in_the_cloud/)
* [Blog Microsoft Intune](http://blogs.technet.com/b/microsoftintune/)
* [Blog Microsoft System Center Configuration Manager](http://blogs.technet.com/b/configurationmgr/)
* [Blog de l’équipe Microsoft System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/)

## <a name="see-also"></a>Voir aussi
[Présentation des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

