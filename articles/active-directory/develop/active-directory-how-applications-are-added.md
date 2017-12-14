---
title: "Comment les applications sont ajoutées à Azure AD"
description: "Cet article décrit la façon dont les applications sont ajoutées à une instance d'Azure Active Directory."
services: active-directory
documentationcenter: 
author: shoatman
manager: mtillman
editor: 
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2016
ms.author: shoatman
ms.custom: aaddev
ms.openlocfilehash: 51ef7e554b6fd3764893f0fd35464088e42e49f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Comment et pourquoi les applications sont ajoutées à Azure AD
L'une des choses surprenantes au premier abord lorsque vous affichez une liste des applications de votre instance d'Azure Active Directory consiste à comprendre d'où proviennent les applications et pourquoi elles sont là.  Cet article fournit une vue d'ensemble détaillée de la manière dont les applications sont représentées dans le répertoire et vous fournissent un contexte qui vous aidera à comprendre comment une application s'est retrouvée dans votre répertoire.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>Quels services Azure AD fournit-il aux applications ?
Les applications sont ajoutées à Azure AD pour exploiter un ou plusieurs des services fournis.  Ces services incluent :

* Authentification et autorisation de l'application
* Authentification et autorisation de l'utilisateur
* Authentification unique (SSO) à l'aide de la fédération ou du mot de passe
* Configuration et synchronisation de l'utilisateur
* Contrôle d'accès basé sur les rôles ; Utilisez le répertoire pour définir les rôles d'application afin d'effectuer des vérifications d'autorisation basées sur les rôles dans une application.
* Services d'autorisation oAuth (utilisées par Office 365 et d'autres applications Microsoft pour autoriser l'accès aux API/ressources).
* Publication et proxy d'applications ; Publier une application sur Internet à partir d'un réseau privé

## <a name="how-are-applications-represented-in-the-directory"></a>Comment les applications sont-elles représentées dans le répertoire ?
Les applications sont représentées dans Azure AD à l'aide de deux objets : un objet d'application et un objet de principal du service.  Il existe un objet d'application, enregistré dans un répertoire « accueil »/« propriétaire » ou « publication », et un ou plusieurs objets de principal du service qui représentent l'application dans chaque répertoire dans lequel il agit.  

L'objet d'application décrit l'application à Azure AD (service mutualisé) et peut inclure les éléments suivants : (*Remarque*: il ne s'agit pas d'une liste exhaustive.)

* Nom, logo et publication
* Secrets (clés symétriques et/ou asymétriques utilisées pour authentifier l'application)
* Dépendances d'API (oAuth)
* API/ressources/étendues publiés (oAuth)
* Rôles d'application (RBAC)
* Configuration et métadonnées de l'authentification unique (SSO)
* Configuration et métadonnées du déploiement de l'utilisateur
* Configuration et métadonnées du proxy

Le principal du service est un enregistrement de l'application dans chaque répertoire où l'application agit notamment son répertoire d'accueil.  Le principal du service :

* fait référence à un objet d'application via la propriété d'id de l'application ;
* enregistre les attributions de rôle de l'application de l'utilisateur local et du groupe ;
* enregistre les autorisations de l'utilisateur local et de l'administrateur accordées à l'application ;
  * Par exemple : l'autorisation de l'application pour accéder à un message électronique d'utilisateurs en particulier
* enregistre les stratégies locales, y compris la stratégie d'accès conditionnel ;
* enregistre les paramètres locaux alternatifs pour une application.
  * Revendication des règles de transformation
  * Mappages d'attributs (déploiement de l'utilisateur)
  * Rôles d'application spécifiques du client (si l'application prend en charge les rôles personnalisés)
  * Nom/logo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Diagramme des objets d'application et des principaux de service au sein des répertoires
![Diagramme illustrant la façon dont les objets d'application et les principaux du service existent dans les instances d'Azure AD.][apps_service_principals_directory]

Comme vous pouvez le voir dans le diagramme ci-dessus,  Microsoft gère deux répertoires en interne (à gauche), qu'il utilise pour publier des applications.

* Une pour Microsoft Apps (répertoire de services Microsoft)
* Une pour des applications tierces pré-intégrées (répertoire de la galerie d'applications)

Les serveurs de publication/fournisseurs d'applications qui s'intègrent à Azure AD doivent avoir un répertoire de publication  (certains répertoires SAAS).

Les applications que vous ajoutez vous-même comprennent :

* les applications que vous avez développées (intégrées avec AAD) ;
* les applications que vous avez connectées à l'authentification unique ;
* les applications que vous avez publiées à l'aide du proxy d'application Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Quelques remarques et exceptions
* Tous les principaux du service ne pointent pas vers les objets d'application.  C'est-à-dire que lorsqu'Azure AD a été créé, les services fournis aux applications étaient alors beaucoup plus limités et le principal du service était suffisant pour établir une identité d'application.  Le principal du service d'origine était plus proche en termes de forme du compte de service Windows Server Active Directory.  Pour cette raison, il est toujours possible de créer des principaux du service à l'aide d'Azure AD PowerShell sans d'abord créer un objet d'application.  L'API Graph requiert un objet d'application avant de pouvoir créer un principal du service.
* Actuellement, toutes les informations décrites ci-dessus sont exposées par programmation.  Les éléments suivants sont uniquement disponibles dans l'interface utilisateur :
  * Revendication des règles de transformation
  * Mappages d'attributs (déploiement de l'utilisateur)
* Pour plus d'informations détaillées sur le principal du service et les objets d'application, consultez la documentation de référence API REST d'Azure AD Graph.  *Conseil*: la documentation API REST d'Azure AD Graph est ce qui se rapproche le plus d'une référence de schéma pour Azure AD qui soit actuellement disponible.  
  * [Application](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Principal du service](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Comment les applications sont-elles ajoutées à mon instance Azure AD ?
Il existe de nombreuses façons dont une application peut être ajoutée à Azure AD :

* Ajout d'une application à partir de la [galerie d'applications Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Connexion à une application tierce intégrée à Azure Active Directory (par exemple : [Smartsheet](https://app.smartsheet.com/b/home) ou [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
  * Lors de la connexion, les utilisateurs sont invités à autoriser l'application à accéder à leur profil et à effectuer d'autres actions.  La première personne à donner son consentement a pour effet qu'un principal du service qui représente l'application est ajouté au répertoire.
* Connexion aux services en ligne de Microsoft comme [Office 365](http://products.office.com/)
  * Lorsque vous vous abonnez à Office 365 ou commencez une version d'évaluation, un ou plusieurs principaux du service sont créés dans le répertoire représentant les différents services qui sont utilisés pour transmettre toutes les fonctionnalités associées à Office 365.
  * Certains services d'Office 365 tels que SharePoint créent des principaux du service sur une base continue afin de sécuriser les communications entre les composants, y compris les flux de travail.
* Ajout d’une application que vous développez au portail de gestion Azure, consultez : https://msdn.microsoft.com/library/azure/dn132599.aspx
* Ajout d'une application que vous développez à l'aide de Visual Studio :
  * [Méthode d'authentification ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
  * [Services connectés](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Ajout d'une application pour utiliser le [Proxy d'application d'Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Mise en relation avec une application pour l'authentification unique à l'aide de SAML ou Password SSO ;
* Beaucoup d'autres, notamment les différentes expériences de développement dans Azure et/dans les expériences d'explorateur d'API dans tous les centres de développement.

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Qui a l'autorisation d'ajouter des applications à mon instance Azure AD ?
Seuls les administrateurs généraux peuvent :

* ajouter des applications à partir de la galerie d'applications d'Azure AD (applications tierces pré-intégrées),
* publier une application à l'aide du proxy d'application d'Azure AD.

Tous les utilisateurs de votre répertoire disposent de droits pour ajouter des applications qu'ils développent et de la discrétion concernant les applications qu'ils partagent ou pour lesquelles ils donnent accès à leurs données d'organisation.  *N'oubliez pas qu'un utilisateur qui se connecte à une application et accorde des autorisations peut engendrer la création d'un principal du service.*

Ceci peut paraître inquiétant, mais gardez ce qui suit à l'esprit :

* Les applications ont été capables de tirer parti de Windows Server Active Directory pour l'authentification utilisateur depuis de nombreuses années sans nécessiter le fait que l'application soit inscrite/enregistrée dans le répertoire.  Désormais, l'organisation disposera d'une visibilité améliorée sur le nombre précis d'applications qui utilisent le répertoire, et pour quelles raisons.
* Pas besoin d'un processus de publication/inscription d'application piloté par l'administrateur.  Avec Active Directory Federation Services, il était probable que l'administrateur doive ajouter une application comme une partie de confiance pour le compte des développeurs.  Les développeurs sont désormais libres.
* La connexion des utilisateurs à des applications à l'aide de leurs comptes d'organisation à des fins commerciales est un point positif.  Par la suite, s'ils quittent l'organisation, ils perdront l'accès au compte qu'ils utilisaient pour cette application.
* Il est bon de disposer d'un enregistrement permettant de savoir avec quelle application les données ont été partagées.  Les données sont plus que jamais transportables, et il est utile de disposer d'un enregistrement précisant qui a partagé quelles données, et à l'aide de quelles applications.
* Les applications qui utilisent Azure AD pour oAuth décident en détail des autorisations que les utilisateurs sont en mesure d'accorder aux applications et des autorisations nécessitant un administrateur pour les confirmer.  Il va sans dire que seuls les administrateurs peuvent donner leur consentement à de plus grandes étendues et des autorisations plus importantes.
* Les utilisateurs qui ajoutent et autorisent des applications pour accéder à leurs données sont des événements audités afin de pouvoir afficher les rapports d'audit dans le portail de gestion d'Azure, dans le but de déterminer de quelle façon une application a été ajoutée au répertoire.

**Remarque :***Microsoft a lui-même fonctionné pendant plusieurs mois avec la configuration par défaut.*

Cela étant, il est possible d'empêcher les utilisateurs de votre répertoire d'ajouter des applications et d'exercer une discrétion sur les informations qu'ils partagent avec les applications en modifiant la configuration de répertoire dans le portail de gestion Azure.  La configuration suivante est accessible dans le portail de gestion Azure, dans l'onglet « Configuration » de votre répertoire.

![Capture d'écran de l'interface utilisateur pour la configuration des paramètres de l'application intégrée][app_settings]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les procédures d'ajout d'applications à Azure AD et de configuration des services pour les applications.

* Développeurs : [Apprendre à intégrer une application avec AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Développeurs : [Consulter des exemples de code pour les applications intégrées à Azure Active Directory sur GitHub](https://github.com/AzureADSamples)
* Développeurs et professionnels de l'informatique : [Lire la documentation de l'API REST pour l'API Graph d'Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Professionnels de l'informatique : [Apprendre à utiliser les applications pré-intégrée d'Azure Active Directory à partir de la galerie d'applications](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Professionnels de l'informatique : [Rechercher des didacticiels pour la configuration des applications spécifiques pré-intégrées](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Professionnels de l'informatique : [Apprendre à publier une application à l'aide du Proxy d'application d'Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Voir aussi
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:../media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
