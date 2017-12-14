---
title: "Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ? | Microsoft Docs"
description: "Utilisez Azure Active Directory pour activer l’authentification unique pour toutes les applications web et SaaS dont vous avez besoin."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 75d1a3fd-b3c5-4495-a5c8-c4c24145ff00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: curtand
ms.reviewer: asmalser
ms.custom: it-pro
ms.openlocfilehash: b577a427590b058319f9315a6d54deb6ec1c7634
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-application-access-and-single-sign-on-with-azure-active-directory"></a>Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?
Avec l’authentification unique, vous pouvez accéder à toutes les applications et toutes les ressources dont vous avez besoin pour travailler, en vous connectant une seule fois avec un seul compte utilisateur. Une fois connecté, vous pouvez accéder à toutes les applications dont vous avez besoin sans devoir vous authentifier à nouveau (par exemple, taper un mot de passe).

De nombreuses entreprises s’appuient sur des applications SaaS comme Office 365, Box et Salesforce pour accroître la productivité des utilisateurs finaux. Historiquement, le personnel informatique doit créer et mettre à jour chaque compte d’utilisateur dans chaque application SaaS et les utilisateurs doivent mémoriser un mot de passe pour chaque application SaaS.

Azure Active Directory étend l’annuaire Active Directory local dans le cloud, ce qui permet aux utilisateurs d’utiliser leur compte professionnel principal non seulement pour se connecter à leurs appareils appartenant au domaine et aux ressources de l’entreprise, mais également à toutes les applications SaaS et web nécessaires à leur travail.

Donc, non seulement les utilisateurs n’ont plus besoin de gérer plusieurs noms d’utilisateur et mots de passe, mais l’accès aux applications peut être automatiquement approvisionné en fonction de leur appartenance aux groupes de l’organisation et de leur statut en tant qu’employés. Azure Active Directory ajoute des contrôles de sécurité et de gouvernance de l’accès qui vous permettent de gérer de manière centralisée l’accès des utilisateurs sur les différentes applications SaaS.

Azure AD permet une intégration simple de nombreuses applications SaaS actuelles. Il assure la gestion des identités et des accès et permet aux utilisateurs d’utiliser l’authentification unique pour se connecter aux applications ou de les découvrir et de les lancer à partir d’un portail comme Office 365 ou le panneau d’accès Azure AD.

L’architecture de l’intégration se compose des quatre composants principaux suivants :

* L’authentification unique permet aux utilisateurs d’accéder à leurs applications SaaS avec leur compte professionnel dans Azure AD. L’authentification unique est ce qui permet aux utilisateurs de s’authentifier auprès d’une application à l’aide de leur compte professionnel.
* L’approvisionnement de l’utilisateur et l’annulation de l’approvisionnement dans le SaaS cible en fonction des modifications apportées dans Windows Server Active Directory et/ou Azure AD. Un compte approvisionné est ce qui permet à un utilisateur d’être autorisé à utiliser une application, une fois qu’il est authentifié via l’authentification unique.
* La gestion centralisée de l’accès aux applications du portail de gestion Azure permet de regrouper l’accès aux applications SaaS et leur gestion, avec la possibilité de déléguer l’approbation des accès à toute personne membre de l’organisation.
* Rapports unifiés et surveillance de l’activité utilisateur dans Azure AD

## <a name="how-does-single-sign-on-with-azure-active-directory-work"></a>Fonctionnement de l’authentification unique avec Azure Active Directory
Lorsqu’un utilisateur « se connecte » à une application, il passe par un processus d’authentification qui lui permet de prouver qu’il est bien qui il prétend être. Sans l’authentification unique, ceci s’effectue habituellement en entrant un mot de passe stocké au niveau de l’application. Cela implique que l’utilisateur connaisse ce mot de passe.

Azure AD prend en charge trois types de connexion aux applications :

* **L’authentification unique fédérée** permet aux applications de se tourner vers Azure AD pour l’authentification des utilisateurs au lieu de leur demander un mot de passe spécifique. Ceci est pris en charge pour les applications compatibles avec les protocoles comme SAML 2.0, WS-Federation ou OpenID Connect. Il s’agit du mode d’authentification unique le plus riche.
* **L’authentification unique par mot de passe** permet de sécuriser le stockage et la lecture des mots de passe des applications à l’aide d’une extension de navigateur web ou d’une application mobile. Elle s’appuie sur le processus de connexion existant fourni par l’application, mais permet aux administrateurs de gérer les mots de passe et ne nécessite pas que l’utilisateur connaisse le mot de passe.
* **L’authentification unique existante** permet à Azure AD de tirer parti de toute authentification unique déjà configurée pour l’application, mais permet à ces applications d’être liées aux portails Office 365 ou Azure AD. Elle permet également de générer des rapports supplémentaires dans Azure AD lorsque les applications sont lancées.

Une fois que l’utilisateur s’est authentifié auprès d’une application, il doit également disposer d’un enregistrement de compte approvisionné au niveau de l’application et qui indique à l’application où se trouvent son niveau d’accès et ses autorisations. L’approvisionnement de cet enregistrement de compte peut se produire automatiquement ou il peut être effectué manuellement par un administrateur avant que l’utilisateur n’ait accès à l’authentification unique.

 Plus d’informations sur ces modes d’authentification unique et sur l’approvisionnement sont disponibles ci-dessous.

### <a name="federated-single-sign-on"></a>Authentification unique fédérée
L’authentification unique fédérée permet aux utilisateurs de votre organisation d’être automatiquement connectés aux applications SaaS tierces via Azure AD grâce aux informations de compte d’utilisateur d’Azure AD.

Dans ce scénario, lorsque vous avez déjà été connecté à Azure AD et que vous souhaitez accéder aux ressources qui sont contrôlées par une application SaaS tierce, la fédération élimine le besoin pour un utilisateur de s’authentifier à nouveau.

Azure AD peut prendre en charge l’authentification unique fédérée avec les applications qui prennent en charge les protocoles de connexion SAML 2.0, WS-Federation ou OpenID.

Voir aussi : [Gestion des certificats pour l’authentification unique fédérée](active-directory-sso-certs.md)

### <a name="password-based-single-sign-on"></a>L’authentification unique par mot de passe
La configuration de l’authentification unique par mot de passe permet aux utilisateurs de votre organisation de se connecter automatiquement aux applications SaaS tierces via Azure AD, avec leurs informations de compte d’utilisateur de l’application SaaS tierce. Lorsque vous activez cette fonctionnalité, Azure AD recueille et stocke en toute sécurité les informations du compte d’utilisateur et le mot de passe qui y est associé.

Azure AD prend en charge l’authentification unique par mot de passe pour toutes les applications cloud qui possèdent une page de connexion HTML. Avec un plug-in de navigateur personnalisé, Azure AD automatise le processus de connexion de l’utilisateur en récupérant en toute sécurité dans l’annuaire les informations d’identification de l’application, par exemple le nom d’utilisateur et le mot de passe. Ces informations d’identification sont ensuite insérées dans la page de connexion de l’application au nom de l’utilisateur. Il existe deux cas d’utilisation :

1. **Un administrateur gère les informations d’identification** : les administrateurs peuvent créer et gérer les informations d’identification de l’application et les affecter aux utilisateurs ou groupes qui doivent accéder à l’application. Dans ce cas, l’utilisateur final n’a pas besoin de connaître les informations d’identification, mais bénéficie toujours d’une authentification unique pour l’accès à l’application en cliquant dessus dans son panneau d’accès ou via un lien fourni. Cela permet à la fois à l’administrateur de gérer le cycle de vie des informations d’identification et à l’utilisateur de ne pas avoir à mémoriser ou gérer les mots de passe spécifiques de l’application. Les informations d’identification sont masquées pour l’utilisateur final lors de la connexion automatique. Elles sont cependant techniquement détectables par l’utilisateur à l’aide des outils de débogage web. Les utilisateurs et les administrateurs doivent de ce fait suivre les mêmes règles de sécurité que si les informations d’identification étaient présentées directement par l’utilisateur. Les informations d’identification fournies par l’administrateur sont très utiles lorsqu’un compte d’accès est partagé entre plusieurs utilisateurs, par exemple pour les applications de médias sociaux ou de partage de documents.
2. **L’utilisateur gère les informations d’identification** : les administrateurs peuvent affecter des applications à des utilisateurs ou à des groupes et autoriser les utilisateurs à entrer leurs propres informations d’identification directement lors du premier accès à l’application dans leur panneau d’accès. Ceci simplifie les choses pour les utilisateurs finaux, car ils n’ont pas besoin d’entrer le mot de passe propre à l’application à chaque fois qu’ils y accèdent. Ce cas de figure peut également servir de point de départ pour la gestion administrative des informations d’identification dans laquelle l’administrateur peut définir de nouvelles informations d’identification pour l’application sans modifier l’expérience de l’accès à l’application pour l’utilisateur final.

Dans les deux cas, les informations d’identification sont stockées sous forme chiffrée dans l’annuaire et sont transmises uniquement via HTTPS au cours du processus de connexion automatique. Avec l’authentification unique par mot de passe, Azure AD offre une solution de gestion d’accès pratique pour les applications qui ne peuvent pas prendre en charge les protocoles de fédération.

L’authentification unique avec mot de passe s’appuie sur une extension de navigateur pour récupérer de façon sécurisée les informations sur l’application et sur l’utilisateur à partir d’Azure AD et les appliquer au service. La plupart des applications SaaS tierces prises en charge par Azure AD prennent en charge cette fonctionnalité.

Pour l’authentification unique par mot de passe, les navigateurs de l’utilisateur final peuvent être :
* Internet Explorer 8, 9, 10, 11 -- sur Windows 7 ou version ultérieure
* Edge sur Windows 10 Édition anniversaire ou version ultérieure 
* Chrome -- sur Windows 7 ou ultérieur, et sur Mac OS X ou ultérieur
* Firefox 26.0 ou ultérieur -- sur Windows XP SP2 ou ultérieur, et sur Mac OS X 10.6 ou ultérieur

### <a name="existing-single-sign-on"></a>Authentification unique existante
Lors de la configuration de l’authentification unique pour une application, le portail de gestion Azure propose une troisième option, l’authentification unique existante. Cette option permet simplement à l’administrateur de créer un lien vers une application et de le placer dans le panneau d’accès pour les utilisateurs sélectionnés.

Par exemple, s’il existe une application qui est configurée pour authentifier les utilisateurs avec Active Directory Federation Services 2.0, l’administrateur peut utiliser l’option d’authentification unique existante pour créer un lien vers cette application dans le panneau d’accès. Lorsque les utilisateurs accèdent au lien, ils sont authentifiés via Active Directory Federation Services 2.0 ou toute autre solution d’authentification unique fournie par l’application.

### <a name="user-provisioning"></a>Approvisionnement de l’utilisateur
Pour certaines applications, Azure AD permet l’approvisionnement automatisé de l’utilisateur et l’annulation de l’approvisionnement des comptes dans les applications SaaS tierces depuis le portail de gestion Azure, à l’aide de vos informations d’identité Windows Server Active Directory ou Azure AD. Lorsqu’un utilisateur reçoit l’autorisation dans Azure AD d’accéder à l’une de ces applications, un compte peut être automatiquement créé (approvisionné) dans l’application SaaS cible.

Lorsque l’utilisateur est supprimé ou que ses informations sont modifiées dans Azure AD, ces modifications sont également appliquées dans l’application SaaS. Ce qui veut dire que la configuration de la gestion du cycle de vie automatique des identités permet aux administrateurs de contrôler de façon automatisée l’approvisionnement et l’annulation de l’approvisionnement des applications SaaS. Dans Azure AD, cette automatisation de la gestion du cycle de vie des identités se fait via l’approvisionnement de l’utilisateur.

Pour plus d'informations, consultez [Automatisation de l'approvisionnement et de l'annulation de l'approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)

## <a name="get-started-with-the-azure-ad-application-gallery"></a>Prise en main de la galerie d’applications Azure AD
Vous êtes prêt à commencer ? Pour déployer l’authentification unique entre Azure AD et les applications SaaS utilisées par votre organisation, suivez ces instructions.

### <a name="using-the-azure-ad-application-gallery"></a>Utilisation de la galerie d’applications Azure AD
La [Galerie d’applications Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) contient une liste d’applications qui prennent en charge une forme d’authentification unique avec Azure Active Directory.

![][1]

Voici quelques conseils pour trouver des applications en fonction des fonctionnalités qu’elles prennent en charge :

* Azure AD prend en charge l'approvisionnement et l'annulation de l'approvisionnement automatique pour toutes les applications de la sélection de la [Galerie d'applications Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).
* Une liste d'applications fédérées qui prennent en charge l'authentification unique fédérée à l'aide d'un protocole comme SAML, WS-Federation ou OpenID Connect est disponible [ici](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx).

Une fois que vous avez trouvé votre application, vous pouvez commencer par suivre les instructions détaillées présentées dans la galerie d’applications et dans le portail de gestion Azure pour activer l’authentification unique.

### <a name="application-not-in-the-gallery"></a>L’application ne se trouve pas dans la galerie ?
Si l’application est introuvable dans la galerie d’applications Azure AD, vous avez plusieurs possibilités :

* **Ajouter une application non répertoriée que vous utilisez** : utilisez la catégorie Personnalisée dans la galerie d'applications du portail de gestion Azure pour connecter une application non répertoriée utilisée par votre organisation. Vous pouvez ajouter n’importe quelle application qui prend en charge SAML 2.0 comme application fédérée, ou bien toute application qui possède une page de connexion HTML comme étape d’authentification unique avec mot de passe. Pour plus d’informations, consultez cet article sur l’ [ajout de votre propre application](application-config-sso-how-to-configure-federated-sso-non-gallery.md).
* **Ajouter une application que vous développez** : si vous avez développé l'application vous-même, suivez les instructions de la documentation développeur d'Azure AD pour implémenter l'authentification unique fédérée ou l'approvisionnement à l'aide de l'API Graph d'Azure AD. Pour plus d’informations, consultez ces ressources :
  
  * [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)
* **Demander l’intégration d’une application** : demandez à ce que l’application dont vous avez besoin soit prise en charge via le [forum de commentaires Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

### <a name="using-the-azure-management-portal"></a>À l'aide du portail de gestion Azure
Vous pouvez utiliser l’extension Active Directory dans le portail de gestion Azure pour configurer l’authentification unique de l’application. Dans un premier temps, vous devez sélectionner un annuaire dans la section Active Directory du portail :

![][2]

Pour gérer vos applications SaaS tierces, vous pouvez passer dans l’onglet Applications de l’annuaire sélectionné. Cette vue permet aux administrateurs d’effectuer les opérations suivantes :

* Ajouter de nouvelles applications à partir de la galerie Azure AD, ainsi que les applications que vous développez
* Supprimer les applications intégrées
* Gérer les applications que vous avez déjà intégrées

Les tâches administratives standard pour une application SaaS tierce sont :

* L’activation de l’authentification unique avec Azure AD avec authentification unique par mot de passe, ou, si elle est disponible pour l’application SaaS cible, l’authentification unique fédérée.
* Le cas échéant, l’activation de l’approvisionnement pour l’approvisionnement et l’annulation de l’approvisionnement de l’utilisateur (gestion du cycle de vie des identités).
* Pour les applications où l’approvisionnement de l’utilisateur est activé, sélectionnez les utilisateurs ayant accès à cette application.

Pour les applications de la galerie qui prennent en charge l’authentification unique fédérée, la configuration vous oblige généralement à fournir des paramètres de configuration supplémentaires, comme les certificats et les métadonnées pour créer une approbation fédérée entre l’application tierce et Azure AD. L’Assistant de configuration vous guide et vous permet d’accéder facilement aux données propres aux applications SaaS ainsi qu’aux instructions spécifiques.

Pour les applications de la galerie qui prennent en charge l’approvisionnement automatique de l’utilisateur, vous devez accorder des autorisations Azure AD pour gérer vos comptes dans l’application SaaS. Au minimum, vous devez fournir les informations d’identification qu’Azure AD doit utiliser lors de l’authentification sur l’application cible. En fonction des besoins de l’application, vous devrez peut-être fournir d’autres paramètres de configuration.

## <a name="deploying-azure-ad-integrated-applications-to-users"></a>Déploiement d’applications Azure AD intégrées pour les utilisateurs
Azure AD offre plusieurs moyens personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation :

* Panneau d’accès Azure AD
* Lanceur d’applications Office 365
* Authentification directe pour les applications fédérées
* Liens ciblés vers des applications fédérées, avec mot de passe ou des applications existantes

Les méthodes que vous choisissez de déployer dans votre organisation sont à votre entière discrétion.

### <a name="azure-ad-access-panel"></a>Panneau d’accès Azure AD
Le panneau d'accès à l'adresse https://myapps.microsoft.com est un portail web qui permet à un utilisateur final disposant d'un compte d'organisation dans Azure Active Directory de voir et de lancer les applications cloud pour laquelle il a reçu des autorisations d'accès de l'administrateur Azure AD. Si vous êtes un utilisateur final avec [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez également utiliser les fonctionnalités de gestion de groupes en libre-service via le panneau d’accès.

![][3]

Le panneau d’accès est séparé du portail de gestion Azure. Pour y accéder, les utilisateurs n’ont pas besoin d’un abonnement Azure ou Office 365.

Pour plus d'informations sur le panneau d'accès Azure AD, consultez la [Présentation du panneau d'accès](active-directory-saas-access-panel-introduction.md).

### <a name="office-365-application-launcher"></a>Lanceur d’applications Office 365
Pour les organisations ayant déployé Office 365, les applications affectées aux utilisateurs via Azure AD sont également affichées dans le portail Office 365 à la page https://portal.office.com/myapps. Pour les utilisateurs d’une organisation, il est donc simple de lancer les applications, sans avoir à utiliser un deuxième portail. Il s’agit de la solution de lancement d’application recommandée pour les organisations qui utilisent Office 365.

![][4]

Pour plus d’informations sur le lanceur d’applications Office 365, consultez [Faire apparaître votre application dans le lanceur d’applications Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

### <a name="direct-sign-on-to-federated-apps"></a>Authentification directe pour les applications fédérées
La plupart des applications fédérées qui prennent en charge la connexion SAML 2.0, WS-Federation ou OpenID prennent également en charge la capacité des utilisateurs à démarrer l’application, puis à obtenir la connexion via Azure AD, soit par une redirection automatique, soit en cliquant sur un lien. Cette capacité est appelée « authentification initiée par un fournisseur de service », et la plupart des applications fédérées de la galerie d’applications Azure AD la prennent en charge (consultez la documentation mentionnée dans l’Assistant de configuration de l’authentification unique de l’application dans le portail de gestion Azure pour plus d’informations).

![][5]

### <a name="direct-sign-on-links-for-federated-password-based-or-existing-apps"></a>Liens d’authentification directs pour les applications fédérées, par mot de passe ou des applications existantes
Azure AD prend également en charge les liens d’authentification unique directs vers les applications qui prennent en charge l’authentification unique par mot de passe, l’authentification unique existante et l’authentification unique fédérée.

Ces liens sont des URL spécifiquement conçues qui font passer l’utilisateur par le processus d’authentification Azure AD pour une application spécifique sans que l’utilisateur n’ait à la lancer dans le panneau d’accès Azure AD ou Office 365. Ces URL d’authentification unique sont accessibles sous l’onglet Tableau de bord de n’importe quelle application pré-intégrée dans la section Active Directory du portail de gestion Azure, comme illustré dans la capture d’écran ci-dessous.

![][6]

Ces liens peuvent être copiés et collés partout où vous souhaitez fournir un lien de connexion à l’application sélectionnée. Cela peut être dans un message électronique ou dans n’importe quel portail web personnalisé que vous avez configuré pour l’accès. Voici un exemple d’URL d’authentification unique Azure AD pour Twitter :

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Comme pour les URL propres à l’entreprise pour le panneau d’accès, vous pouvez personnaliser cette URL en ajoutant un des domaines actifs ou vérifiés de votre annuaire après le domaine myapps.microsoft.com. Cela garantit que le logo est immédiatement chargé sur la page de connexion sans que l’utilisateur n’ait à entrer d’abord son ID utilisateur :

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Lorsqu’un utilisateur autorisé clique sur un de ces liens spécifiques de l’application, il voit tout d’abord la page de connexion de son organisation (en supposant qu’il n’est pas déjà connecté). Après la connexion, il est redirigé vers l’application sans s’arrêter au panneau d’accès. Si l’utilisateur ne dispose pas des éléments requis pour accéder à l’application, par exemple l’extension de navigateur d’authentification unique basée sur mot de passe, le lien l’invite à installer l’extension manquante. L’URL du lien reste constante en cas de modification de la configuration de l’authentification unique de l’application.

Ces liens utilisent les mêmes mécanismes de contrôle d’accès que le panneau d’accès et qu’Office 365 et seuls les utilisateurs ou les groupes qui ont été affectés à l’application dans le portail de gestion Azure sont en mesure de s’authentifier. Toutefois, tout utilisateur qui n’est pas autorisé voit un message qui explique qu’il n’a pas reçu l’accès. Lui est présenté un lien permettant de charger le panneau d’accès pour afficher les applications disponibles auxquelles il a accès.

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Détection des applications cloud non approuvées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
* [Introduction à la gestion de l'accès aux applications](active-directory-managing-access-to-apps.md)
* [Comparaison des capacités de gestion des identités externes dans Azure AD](active-directory-b2b-compare-external-identities.md)

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png
