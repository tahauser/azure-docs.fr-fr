---
title: "Comprendre les identité Azure | Microsoft Docs"
description: "Acquérez une compréhension élémentaire des conditions, concepts et recommandations liés aux identités Microsoft Azure afin de pouvoir prendre une décision optimale en matière de gouvernance des identités pour votre organisation."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 4438917db93c37ddbba3e7ee692b2e3c065d2beb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Comprendre les solutions d’identité Azure
Microsoft Azure Active Directory (Azure AD) est une solution cloud de gestion des identités et des accès qui associe des services d’annuaire, une gouvernance des identités et la gestion de l’accès aux applications. Azure AD [active rapidement l’authentification unique (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) sur des milliers d’applications commerciales et personnalisées dans la [galerie d’applications Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Vous utilisez probablement déjà bon nombre de ces applications : Office 365, Salesforce.com, Box, ServiceNow et Workday.

Un annuaire Azure AD est automatiquement associé à un abonnement Azure lors de la création de celui-ci. En tant que service d’identité dans Azure, Azure AD fournit ensuite toutes les fonctions de gestion des identités et de contrôle d’accès pour les ressources de cloud. Ces ressources peuvent inclure des utilisateurs, des applications et des groupes pour un client individuel (organisation), comme indiqué dans le diagramme suivant :

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

Microsoft Azure offre plusieurs façons de tirer parti de la gestion des identités et des accès en tant que service (IDaaS) avec divers niveaux de complexité pour répondre aux besoins de votre organisation. Le reste de cet article explique la terminologie et les concepts relatifs aux identités Azure, et énonce diverses recommandations pour vous aider à choisir au mieux parmi les options disponibles.

## <a name="terms-to-know"></a>Termes à connaître

Avant de prendre une décision concernant la solution de gestion des identités Azure appropriée pour votre organisation, vous devez avoir une compréhension élémentaire des termes couramment utilisés en relation avec les services d’identité Azure.

|Terme à connaître| Description|
|-----|-----|
|Abonnement Azure |Les abonnements sont utilisés pour payer les services cloud Azure et sont généralement liés à une carte de crédit. Vous pouvez avoir plusieurs abonnements, mais il peut être difficile de partager des ressources entre ceux-ci.|
|Client Azure | Un client Azure AD représente une organisation. Il s’agit d’une instance dédiée et approuvée d’Azure AD automatiquement créée quand une organisation souscrit un abonnement à un service Microsoft Cloud tel qu’Azure, Microsoft Intune ou Office 365. Les clients peuvent avoir accès aux services dans un environnement dédié (client unique) ou dans un environnement partagé avec d’autres organisations (plusieurs clients).|
|Annuaire Azure AD | Chaque client Azure dispose d’un annuaire Azure AD dédié et approuvé contenant ses utilisateurs, groupes et applications. Celui-ci est utilisé pour exécuter des fonctions de gestion des identités et des accès en relation avec les ressources du client. Étant donné qu’un annuaire Azure AD est automatiquement configuré pour représenter votre organisation lorsque vous souscrivez un service Microsoft Cloud tel qu’Azure, Microsoft Intune ou Office 365, les termes *Client*, *Azure AD* et *Azure Active Directory* son parfois utilisés de façon interchangeable. |
|Domaine personnalisé | Lorsque vous souscrivez pour la première fois un abonnement à un service Microsoft Cloud, votre client (ou organisation) utilise un nom de domaine *.onmicrosoft.com*. Toutefois, la plupart des organisations disposent d’un ou plusieurs noms de domaine utilisés pour les relations commerciales, dont les utilisateurs finaux peuvent se servir pour accéder aux ressources de l’entreprise. Vous pouvez ajouter votre nom de domaine personnalisé à Azure AD afin que vos utilisateurs puissent s’y habituer, par exemple, en spécifiant *alice@contoso.com* au lieu de *alice@contoso.onmicrosoft.com*. |
|Compte Azure AD | Il s’agit d’identités créées à l’aide d’Azure AD ou d’un autre service Microsoft Cloud tel qu’Office 365. Elles sont stockées dans Azure AD, et accessibles à tout abonnement au service cloud de l’organisation. |
|Administrateur d’abonnement Azure| L’administrateur de compte est la personne qui s’est inscrite ou a acheté l’abonnement Azure. Il peut utiliser le [Centre des comptes](https://account.azure.com/Subscriptions) pour effectuer diverses tâches de gestion comme créer des abonnements, annuler des abonnements, modifier la facturation d’un abonnement et modifier l’administrateur de services fédérés. |
|Administrateur général Azure AD | Les administrateurs généraux Azure AD disposent d’un accès illimité à toutes les fonctions d’administration d’Azure AD. La personne qui souscrit un abonnement au service Microsoft Cloud devient automatiquement un administrateur général par défaut. Vous pouvez avoir plusieurs administrateurs généraux, mais seuls ceux-ci peuvent affecter les [autres rôles Administrateur](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) à des utilisateurs. |
|Compte Microsoft | Les comptes Microsoft (créés par vous pour votre usage personnel) donnent accès à l’ensemble des produits et services cloud Microsoft orientés utilisateur, comme Outlook (Hotmail), OneDrive, Xbox LIVE ou Office 365. Ces identités sont créées et stockées dans le système de comptes d’identité des consommateurs de Microsoft.|
|Comptes professionnels ou scolaires | Les comptes professionnels ou scolaires (créés par un administrateur pour une utilisation professionnelle ou scolaire) donnent accès à des services cloud Microsoft de niveau professionnel, tels qu’Azure, Intune ou Office 365.|


## <a name="concepts-to-understand"></a>Concepts à comprendre

Maintenant que vous connaissez les termes de base sur les identités Azure, il est temps d’en apprendre plus sur les concepts associés pour vous aider à prendre une décision informée quant au service d’identité Azure qui vous convient.

|Concepts à comprendre |Description|
|-----|-----|
|[Association des abonnements Azure avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Chaque abonnement Azure a une relation d’approbation avec un annuaire Azure AD pour authentifier les utilisateurs, les services et les appareils. *Plusieurs abonnements peuvent approuver le même annuaire Azure AD, mais un abonnement n’approuve qu’un seul annuaire Azure AD*. Cette relation de confiance diffère de la relation qu’un abonnement a avec d’autres ressources Azure (sites web, bases de données, etc.), qui se rapprochent davantage des ressources enfants d’un abonnement. Quand un abonnement expire, l’accès aux ressources autres qu’Azure AD qui y sont associées cesse également. Toutefois, l’annuaire Azure AD restant dans Azure, vous pouvez associer un autre abonnement à cet annuaire et continuer à gérer les ressources client.|
|[Comment fonctionne la gestion des licences Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Lorsque vous achetez ou activez Enterprise Mobility Suite, Azure AD Premium ou Azure AD Standard, votre annuaire est mis à jour avec l’abonnement, y compris sa période de validité et les licences prépayées. Une fois l’abonnement actif, le service peut être géré par des administrateurs généraux Azure et utilisé par des utilisateurs sous licence. Les informations relatives à votre abonnement, dont le nombre de licences attribuées ou disponibles, sont accessibles via le portail Azure, dans le panneau **Azure Active Directory** > **Licences**. Cette vignette constitue également l’emplacement idéal pour gérer vos attributions de licence.|
|[Contrôle d’accès en fonction du rôle dans le portail Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|La fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) Azure permet de gérer avec précision l’accès aux ressources Azure. Un trop grand nombre d’autorisations peut exposer un compte à des attaques potentielles. Si le nombre d’autorisations est trop faible, les employés ne peuvent pas effectuer leur travail efficacement. La fonctionnalité RBAC vous permet d’octroyer précisément aux employés les autorisations dont ils ont besoin en fonction de trois rôles de base qui s’appliquent à tous les groupes de ressources : propriétaire, contributeur et lecteur. Vous pouvez également créer jusqu’à 2 000 [rôles RBAC personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) pour répondre à vos besoins spécifiques. |
|[Identité hybride](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Vous obtenez une identité hybride en intégrant votre Windows Server Active Directory (AD DS) local avec Azure AD à l’aide d’[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Cela vous permet de fournir une identité commune à vos utilisateurs pour les applications Office 365, Azure, ainsi que les applications locales ou SaaS intégrées avec Azure AD. Avec une identité hybride, vous étendez efficacement votre environnement local au cloud pour l’identité et l’accès.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>Différence entre Windows Server AD DS et Azure AD
Azure Active Directory (Azure AD) et Active Directory local (Active Directory Domain Services ou AD DS) sont tous deux des systèmes qui stockent des données d’annuaire et gèrent les communications entre les utilisateurs et les ressources, notamment les processus d’ouverture de session, d’authentification des utilisateurs et de recherche dans l’annuaire.

Si vous connaissez Windows Server Active Directory domaine Services (AD DS), introduit avec Windows 2000 Server, vous comprenez probablement le concept de base d’un service d’identité. Cependant, il est également important de comprendre qu’Azure AD n’est pas seulement un contrôleur de domaine dans le cloud. Il s’agit d’une façon entièrement nouvelle de fournir l’identité en tant que service (IDaaS) dans Azure, ce qui nécessite une toute autre manière de penser pour appréhender pleinement les fonctionnalités basées sur le cloud et protéger votre organisation contre les menaces modernes. 

AD DS est un rôle serveur sur Windows Server, ce qui signifie qu’il peut être déployé sur des ordinateurs physiques ou des machines virtuelles. Il a une structure hiérarchique basée sur X.500. Il utilise le système DNS pour la localisation d’objets, peut être contacté par le biais du protocole LDAP et utilise principalement Kerberos pour l’authentification. Active Directory admet les unités d’organisation (UO) et les objets de stratégie de groupe (GPO) en plus de joindre des ordinateurs au domaine ; les approbations sont créées entre domaines.

Traditionnellement, les informaticiens protègent leur périmètre de sécurité à l’aide d’AD DS. Toutefois, les entreprises modernes, sans périmètre, qui prennent en charge les besoins d’identité de leurs employés, clients et partenaires nécessitent un nouveau plan de contrôle. Azure AD est ce plan de contrôle des identités. La sécurité s’est déplacée au-delà du pare-feu d’entreprise vers le nuage où Azure AD protège les ressources de l’entreprise et l’accès en fournissant une identité commune unique pour les utilisateurs (localement ou dans le cloud). Ainsi, vos utilisateurs ont la possibilité d’accéder en toute sécurité aux applications dont ils ont besoin pour effectuer leur travail à partir de presque n’importe quel appareil. Des contrôles de protection des données transparents et basés sur les risques, accompagnés par des fonctionnalités d’apprentissage automatique et des rapports approfondis, permettent au département informatique de sécuriser les données de l’entreprise.

Azure AD est un service d’annuaire public multiclient, ce qui signifie que vous pouvez créer un locataire pour vos applications comme Office 365 et vos serveurs cloud dans Azure AD. Les utilisateurs et les groupes sont créés selon une structure plate sans unités d’organisation ou objets de stratégie de groupe. L’authentification s’effectue suivant des protocoles tels que SAML, WS-Federation et OAuth. Il est possible d’interroger Azure AD mais, au lieu d’utiliser LDAP, vous devez utiliser une API REST nommée API Graph AD. Toutes fonctionnent sur HTTP et HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Étendre les fonctionnalités de gestion et de sécurité d’Office 365
Vous utilisez déjà Office 365 ? Vous pouvez accélérer votre transformation numérique en étendant les fonctionnalités intégrées d’Office 365 avec Azure AD afin de sécuriser l’ensemble de vos ressources, ce qui vous permet également de renforcer et de sécuriser la productivité de vos effectifs. En utilisant Azure AD, en plus des fonctionnalités Office 365, vous pouvez sécuriser votre portefeuille d’applications tout entier en utilisant une seule identité permettant l’authentification unique sur toutes les applications. Vous pouvez étendre vos capacités d’accès conditionnel en fonction, non seulement de l’état de l’appareil, mais aussi de l’utilisateur, de l’emplacement, de l’application et du risque. Les fonctionnalités d’authentification multifacteur (Multi-Factor Authentication, MFA) offrent davantage de protection lorsque vous en avez besoin. Vous allez bénéficier d’un meilleur contrôle des privilèges d’utilisateur et de la possibilité de fournir un accès administratif juste-à-temps à la demande. Vos utilisateurs seront plus productifs et créeront moins de tickets de support technique grâce aux fonctionnalités en libre-service d’Azure AD, telles que la réinitialisation des mots de passe oubliés, les demandes d’accès aux applications, ainsi que la création et la gestion des groupes.

> [!TIP]
> Vous souhaitez en savoir plus sur la gestion des identités Azure AD avec Office 365 ? [Téléchargez le livre électronique](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Solutions Microsoft Azure pour le contrôle des identités

Microsoft Azure offre plusieurs façons de gérer les identités des utilisateurs : uniquement localement, uniquement dans le cloud, ou entre les deux. Ces options sont les suivantes : AD DS à configurer en libre service dans Azure, Azure Active Directory (Azure AD), identité hybride et Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>AD DS à configurer en libre service
Les organisations qui privilégient un faible encombrement dans le cloud peuvent opter pour la solution d’**AD DS à configurer en libre service** dans Azure. Cette option prend en charge de nombreux scénarios de Windows Server AD DS bien adaptés au déploiement de machines virtuelles sur Azure. Par exemple, vous pouvez créer une machine virtuelle Azure faisant office de contrôleur de domaine opérant au sein d’un centre de données lointain connecté au réseau à distance. À partir de là, la machine virtuelle peut prendre en charge les demandes d’authentification d’utilisateurs distants et améliorer les performances de l’authentification. Cette option convient également comme alternative relativement bon marché aux sites de récupération d’urgence coûteux en hébergeant un petit nombre de contrôleurs de domaine et un seul réseau virtuel sur Azure. Enfin, vous pourriez avoir besoin de déployer sur Azure une application telle que SharePoint, qui nécessite Windows Server AD DS, mais ne dépend pas du réseau local ou du Windows Server Active Directory de l’entreprise. Dans ce cas, vous pouvez déployer une forêt isolée sur Azure pour répondre aux besoins de la batterie de serveurs SharePoint. Il est également possible de déployer des applications réseau nécessitant une connectivité au réseau et à l’instance Active Directory locaux.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Une **instance Azure AD autonome** est solution de gestion des accès et des identités en tant que service (IDaaS) entièrement basée sur le cloud. Azure AD offre un ensemble puissant de fonctionnalités pour gérer les utilisateurs et les groupes. Il permet de sécuriser l’accès aux applications locales et cloud, dont des services web Microsoft, tels qu’Office 365 et de nombreuses applications SaaS (Software as a service) tierces. Azure AD est proposé en 3 versions : Gratuite, De base et Premium. Azure AD améliore l’efficacité organisationnelle et étend la sécurité, au-delà du pare-feu de périmètre, à un nouveau plan de contrôle protégé par Azure Machine Learning et d’autres fonctionnalités de sécurité avancées.

### <a name="hybrid-identity"></a>Identité hybride
Au lieu d’opter pour des solutions de gestion des identités locales ou basées sur le cloud, bon nombre de responsables de services informatiques et d’entreprises visionnaires qui ont commencé à anticiper les tendances à long terme de leur organisation étendent leurs annuaires locaux à des solutions d’**identité hybride**. Une identité hybride vous permet de bénéficier d’une solution réellement globale de gestion des identités et des accès, offrant un accès sécurisé et productif aux applications dont les utilisateurs ont besoin pour accomplir leurs tâches.

> [!TIP]
> Pour en savoir plus sur la manière dont les responsables de services informatiques ont fait d’Azure Active Directory un élément central de leurs stratégies, téléchargez le [Guide Azure Active Directory pour les responsables de services informatiques](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Services de domaine Azure AD
La solution **Azure AD Domain Services** offre une option basée sur le cloud permettant d’utiliser AD DS pour un contrôle léger de la configuration des machines virtuelles Azure afin de répondre aux besoins en matière de développement et de test d’applications réseau. La solution Azure AD Domain Services n’a pas pour vocation de porter votre infrastructure AD DS locale sur des machines virtuelles Azure gérées par Azure AD Domain Services. Au lieu de cela, il convient d’utiliser des machines virtuelles Azure dans des domaines gérés pour prendre en charge le développement, le test et le déplacement d’applications locales nécessitant des méthodes d’authentification AD DS vers le cloud.

## <a name="common-scenarios-and-recommendations"></a>Scénarios et recommandations courants

Voici quelques scénarios courants de gestion des identités et des accès avec des recommandations concernant l’option d’identité Azure la plus appropriée dans chaque cas.

|Scénario d’identité| Recommandation|
|-----|-----|
|Mon organisation a investi massivement dans Windows Server Active Directory en local, mais nous voulons étendre la gestion des identités au cloud.| La solution de gestion des identités Azure la plus utilisée est celle de l’[identité hybride](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Si vous avez déjà investi dans une solution AD DS locale, vous pouvez facilement étendre l’identité au cloud à l’aide d’Azure AD Connect.|
|Notre activité a vu le jour dans le cloud et nous n’avons rien investi dans des solutions locales de gestion des identités.| [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) est le meilleur choix pour les entreprises opérant uniquement dans le cloud sans investissement local.|
|J’ai besoin d’une solution de configuration et de gestion de machine virtuelle Azure légère afin de répondre aux besoins de gestion des identités pour le développement et le test d’applications.|La solution [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) est un bon choix si vous devez utiliser AD DS pour effectuer un contrôle léger de la configuration de machines virtuelles Azure, ou si vous cherchez à développer ou à migrer vers le cloud des applications locales héritées prenant en charge les annuaires.|  
|Je dois prendre en charge quelques machines virtuelles dans Azure, mais mon organisation a considérablement investi dans des solutions Active Directory (AD DS) locales.|Pour utiliser des machines virtuelles Azure lorsque vous devez prendre en charge quelques machines virtuelles et avez consenti des investissements importants dans des solutions AD DS locales, optez pour [AD DS à configurer en libre service](https://msdn.microsoft.com/library/azure/jj156090.aspx). |

## <a name="where-can-i-learn-more"></a>Où en savoir plus ?
Nous pouvons vous proposer une multitude de ressources en ligne très utiles pour vous aider à tout savoir sur Azure AD. Voici une liste d’articles complets pour vous aider à démarrer :

* [Activation de votre annuaire pour la gestion hybride avec Azure AD Connect](active-directory-aadconnect.md)
* [Sécurité supplémentaire pour un monde toujours connecté](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Prise en main de la création de rapports Azure AD](active-directory-reporting-getting-started.md)
* [Gestion de vos mots de passe en tout lieu](active-directory-passwords-update-your-own-password.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md)
* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Qu’est-ce que la gestion des licences Microsoft Azure Active Directory ?](active-directory-licensing-whatis-azure-portal.md)
* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Étapes suivantes

À présent que vous comprenez les concepts liés à la gestion des identités Azure et les options disponibles, vous pouvez utiliser les ressources suivantes pour commencer à implémenter l’option de votre choix :

[En savoir plus sur les solutions d’identité hybride Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[En apprendre davantage dans un environnement de preuve de concept Azure](https://aka.ms/aad-poc)

[Déployer Azure AD en production](https://aka.ms/aad-onboard)
