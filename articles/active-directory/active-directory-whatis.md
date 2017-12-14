---
title: "Qu’est-ce qu’Azure Active Directory ?"
description: "Azure Active Directory permet d'étendre vos identités locales existantes dans le cloud ou de développer des applications intégrées Azure AD."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.openlocfilehash: e5eafd4d25d2638ab5d9f904a7e0c00b36501d68
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-azure-active-directory"></a>Qu’est-ce qu’Azure Active Directory ?
Azure Active Directory (Azure AD) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, mutualisé. Azure AD associe des services d’annuaires principaux, une gouvernance avancée des identités et une gestion des accès aux applications. Azure AD offre également une riche plateforme reposant sur des standards, qui permet aux développeurs de contrôler l’accès à leurs applications, grâce à des stratégies et des règles centralisées. 

Azure AD fournit une solution économique pour donner aux employés et aux partenaires commerciaux un accès par authentification unique à [des milliers d’Applications SaaS de cloud](active-directory-saas-tutorial-list.md) , comme Office 365, Salesforce.com, DropBox et Concur.

Azure AD permet également aux développeurs d’applications de se concentrer sur le développement de leur application en simplifiant et en accélérant son intégration dans une solution de gestion des identités de classe mondiale utilisée par des millions d’organisations dans le monde.

Azure AD inclut également une suite complète de fonctionnalités de gestion d’identité, comme l’authentification multifacteur, l’inscription d’appareil, la gestion de mot de passe libre-service, la gestion de groupes libre-service, la gestion des comptes privilégiés, le contrôle d’accès en fonction du rôle, la surveillance de l’utilisation de l’application, ainsi que la création d’audits complets, la surveillance de la sécurité et la création d’alertes. Ces fonctions permettent d’améliorer la sécurité des applications basées sur le cloud, de simplifier les processus informatiques, de réduire les coûts et de garantir le respect des objectifs de conformité aux normes du secteur.

De plus, en [quatre clics](./connect/active-directory-aadconnect-get-started-express.md), Azure AD peut être intégré à une instance existante de Windows Server Active Directory, ce qui offre aux entreprises la possibilité d’exploiter leurs investissements d’identité locale pour gérer l’accès aux applications SaaS basées sur le cloud.

Si vous êtes un client Office 365, Azure ou Dynamics CRM Online, il est possible que vous ne réalisiez pas que vous utilisez déjà Azure AD. Chaque abonné Office 365, Azure et Dynamics CRM est déjà un abonné Azure AD. Chaque fois que vous le souhaitez, vous pouvez utiliser ce client pour gérer l’accès aux milliers d’autres applications de cloud avec lesquelles s’intègre Azure AD !

![Pile Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Quelle est la fiabilité d’Azure AD ?
La conception d’Azure AD pour l’architecture mutualisée géolocalisée à haute disponibilité signifie que vous pouvez vous reposer sur cette solution pour répondre à vos besoins les plus critiques. L’exécution de 28 centres de données dans le monde entier avec basculement automatisé vous offre le confort de savoir qu’Azure AD est hautement disponible et que, même si un centre de données tombe en panne, des copies de vos données d’annuaire sont disponibles dans des centres de données situés dans au moins deux régions, pour un accès instantané.

Pour plus d’informations, consultez la rubrique [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Choisir une édition
Tous les services d'entreprise Microsoft Online reposent sur Azure Active Directory (Azure AD) pour l'authentification et autres besoins d’identification. Si vous vous abonnez à un service d’entreprise Microsoft Online quelconque (par exemple, Office 365 ou Microsoft Azure), vous obtenez Azure AD avec un accès à toutes les fonctionnalités gratuites. L’édition gratuite d’Azure Active Directory vous permet de gérer les utilisateurs et les groupes, d’effectuer une synchronisation avec les annuaires locaux, d’obtenir l’authentification unique entre Azure, Office 365 et des milliers d’applications SaaS populaires telles que Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox, etc. 

Pour enrichir votre expérience Azure Active Directory, vous pouvez ajouter des fonctionnalités payées à l'aide des éditions Azure Active Directory de base, Premium P1 et Premium P2. Les versions payées Azure Active Directory se superposent à votre annuaire gratuit existant et vous offrent de nombreuses fonctionnalités de classe entreprise : libre-service, surveillance améliorée, rapports de sécurité, authentification multifacteur MFA (Multi-Factor Authentication) et accès sécurisé pour votre personnel mobile.

> [!NOTE]
> Pour les options de tarification de ces éditions, consultez la [tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Les éditions Azure Active Directory, Premium P1, Premium P2 et Azure Active Directory de base ne sont actuellement pas prises en charge en Chine. Pour plus d’informations, contactez-nous via le Forum Azure Active Directory.
>

* **Azure Active Directory - version de base** : Conçue pour les employés avec des besoins principalement centrés sur le cloud, cette édition offre un accès aux applications avec des besoins principalement centrés sur le cloud ainsi que des solutions de gestion des identités en libre-service. Avec l’édition de base d’Azure Active Directory, vous disposez de fonctionnalités qui améliorent votre productivité et réduisent vos coûts comme la gestion d’accès basée sur les groupes, la réinitialisation libre-service du mot de passe pour les applications cloud et un proxy d’application Azure Active Directory (pour publier des applications web locales à l’aide d’Azure Active Directory). Le tout, avec un contrat SLA pour professionnel qui fournit 99,9 % de disponibilité.
* **Azure Active Directory - version Premium P1** : Conçue pour permettre aux entreprises avec des besoins d’identification et de gestion des accès plus élevés, la version Premium d’Azure Active Directory ajoute des fonctionnalités de gestion des identités de niveau professionnel et permet aux utilisateurs hybrides d'accéder en toute transparence aux fonctionnalités locaux et disponibles sur le cloud. Cette édition inclut tout ce dont vous avez besoin pour les travailleurs de l'information et les administrateurs d'identités dans les environnements hybrides grâce à un accès inter-applications, à une gestion des identités et de l’accès en libre-service (IAM), à la protection des identités et à la sécurité dans le cloud. Elle prend en charge des ressources d'administration et de délégation avancées telles que des groupes dynamiques et la gestion de groupes en libre-service. Elle inclut Microsoft Identity Manager (une suite de gestion locale des identités et des accès) et fournit des fonctionnalités d'écriture différée sur le cloud offrant aux utilisateurs locaux des solutions comme la réinitialisation en libre-service du mot de passe du service.
* **Azure Active Directory Premium P2** - conçu avec la protection avancée pour tous vos utilisateurs et administrateurs, cette nouvelle offre inclut toutes les fonctionnalités d’Azure AD Premium P1, ainsi que nos nouvelles solutions Identity Protection et Privileged Identity Management. Active Directory Azure Identity Protection exploite des milliards de signaux pour fournir un accès conditionnel basé sur les risques à vos applications et données d’entreprise critiques. Nous vous aidons également ç gérer et protéger les comptes privilégiés avec Azure Active Directory Privileged Identity Management afin que vous puissiez découvrir, restreindre et surveiller les administrateurs et leur accès aux ressources, et fournir un accès juste-à-temps quand cela est nécessaire.  

> [!NOTE]
> Plusieurs fonctionnalités Azure Active Directory sont disponibles via les éditions de « paiement à l’utilisation » :
>
> * Active Directory B2C constitue la solution de gestion des identités et des accès pour vos applications grand public. Pour plus d’informations, consultez [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * L'authentification multifacteur MFA (Multi-Factor Authentication) Azure peut être effectuée par utilisateur ou par le biais de fournisseurs d'authentification. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Multi-Factor Authentication ?](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Comment faire pour démarrer ?

**Si vous êtes un administrateur informatique :**

* [Faites un essai.](https://azure.microsoft.com/trial/get-started-active-directory/) Vous pouvez vous inscrire pour une période d’essai gratuit de 30 jours, puis déployer votre première solution cloud en moins de 5 minutes à l’aide de ce lien

* Lisez la page [Prise en main d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium) pour accélérer l’installation et la configuration d’un client Azure AD.

**Si vous êtes un développeur :**
 
* Consultez notre [Guide pour les développeurs](active-directory-developers-guide.md) pour Azure Active Directory

* [Démarrer une version d’évaluation](https://azure.microsoft.com/trial/get-started-active-directory/) : abonnez-vous à un essai gratuit pendant 30 jours dès aujourd’hui et commencez à intégrer vos applications à Azure AD

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les principes fondamentaux de la gestion des identités et des accès Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
