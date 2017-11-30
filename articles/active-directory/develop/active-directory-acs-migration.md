---
title: "Migration à partir d’Azure Access Control Service (ACS)"
description: "Options pour la migration d’applications et de services à partir d’Azure Access Control Service | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migration à partir d’Azure Access Control Service (ACS)

Microsoft Azure Active Directory Access Control (également appelé Access Control Service ou ACS) sera retiré en novembre 2018.  Les applications et services qui utilisent actuellement ACS doivent migrer entièrement vers un autre mécanisme d’authentification avant cette date. Ce document fournit des recommandations pour les clients actuels dans le cadre de la planification de l’arrêt du service ACS. Si vous n’utilisez pas ACS actuellement, vous n’avez rien à faire.


## <a name="brief-acs-overview"></a>Brève vue d’ensemble d’ACS

ACS est un service d’authentification cloud qui offre une façon simple d’authentifier et d’autoriser les utilisateurs à accéder à vos applications et services web tout en permettant de ne pas prendre en compte les fonctionnalités d’authentification et d’autorisation dans votre code. Il est utilisé principalement par les développeurs et les architectes de clients .NET, les applications web ASP.NET et les services web WCF.

Les cas d’usage d’ACS peuvent être divisés en trois catégories principales :

- Authentification auprès de certains services cloud de Microsoft, notamment Azure Service Bus et Dynamics CRM. Les applications clientes peuvent obtenir des jetons auprès d’ACS, qui peuvent servir à s’authentifier auprès de ces services pour effectuer diverses actions.
- Ajout de l’authentification à des applications web, à la fois personnalisée et prédéfinie (par exemple Sharepoint). À l’aide de l’authentification ACS « passive », les applications web peuvent prendre en charge la connexion avec des comptes à partir de Google, Facebook, Yahoo, Compte Microsoft (Live ID), Azure Active Directory et les services AD FS.
- Sécurisation des services web personnalisés avec des jetons émis par ACS. À l’aide de l’authentification « active », les services web peuvent s’assurer qu’ils accordent l’accès uniquement à partir de clients connus qui se sont authentifiés auprès d’ACS.

Chacun de ces cas d’usage et leurs stratégies de migration recommandées sont décrits dans les sections suivantes. Dans la grande majorité des cas, des modifications de code significatives sont nécessaires pour migrer les applications et services existants vers les nouvelles technologies. Nous vous recommandons de commencer à planifier et à exécuter votre migration immédiatement, afin d’éviter tout risque d’arrêt ou d’interruption.

> [!WARNING]
> Dans la plupart des cas, des modifications de code significatives sont nécessaires pour migrer les applications et services existants vers les nouvelles technologies. Nous vous recommandons de commencer à planifier et à exécuter votre migration immédiatement, afin d’éviter tout risque d’arrêt ou d’interruption.

Du point de vue architectural, ACS est entièrement constitué des composants suivants :

- Un service STS (Secure Token Service), qui reçoit les demandes d’authentification et émet des jetons de sécurité en retour.
- Le portail Azure Classic, qui sert à créer, à supprimer et à activer/désactiver des espaces de noms ACS.
- Un portail de gestion ACS distinct, qui sert à personnaliser et à configurer le comportement d’un espace de noms ACS.
- Un service de gestion, qui peut servir à automatiser les fonctions des portails.
- Un moteur de règles de transformation de jetons, qui peut servir à définir une logique complexe pour la manipulation du format de sortie des jetons émis par ACS.

Pour utiliser ces composants, vous devez créer un ou plusieurs **espaces de noms** ACS. Un espace de noms est une instance dédiée d’ACS avec laquelle vos services et applications communiquent. Il est isolé de tous les autres clients d’ACS, qui utilisent leurs propres espaces de noms. Un espace de noms ACS a une URL dédiée, telle que :

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Toutes les communications avec le service STS et les opérations de gestion sont effectuées à cette URL, avec différents chemins correspondant à différentes fonctions. Pour déterminer si vos applications ou services utilisent ACS, vérifiez s’il y a du trafic vers `https://{namespace}.accesscontrol.windows.net`.  Tout trafic vers cette URL est le trafic géré par ACS. Il doit être supprimé.  La seule exception concerne le trafic vers `https://accounts.accesscontrol.windows.net`. Le trafic vers cette URL est déjà géré par un autre service et n’est pas affecté par la dépréciation d’ACS.  Veillez également à vous connecter au portail Azure Classic et à vérifier s’il existe des espaces de noms ACS dans les abonnements dont vous êtes propriétaire.  Les espaces de noms ACS sont répertoriés dans le service **Active Directory**, sous l’onglet **Espaces de noms Access Control**.

Pour plus d’informations sur ACS, consultez [cette documentation archivée sur MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Planification de la mise hors service

À la date de rédaction de cet article (novembre 2017), tous les composants d’ACS sont entièrement pris en charge et opérationnels. La seule restriction est que [vous ne pouvez pas créer de nouveaux espaces de noms ACS par le biais du portail Azure Classic](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Le planning de dépréciation de ces composants est le suivant :

- **Novembre 2017** : l’expérience d’administration d’Azure AD dans le portail Azure Classic sera [mise hors service](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). À ce stade, la gestion des espaces de noms pour ACS sera disponible à cette nouvelle URL dédiée : `http://manage.windowsazure.com?restoreClassic=true`. Vous pourrez ainsi visualiser vos espaces de noms existants, les activer/désactiver, et les supprimer entièrement si vous le souhaitez.
- **Avril 2018** : la gestion des espaces de noms ACS ne sera plus disponible à cette URL dédiée. À ce stade, vous ne pourrez plus activer/désactiver, supprimer ou énumérer vos espaces de noms ACS. Le portail de gestion ACS, toutefois, sera entièrement fonctionnel et accessible à l’adresse `https://{namespace}.accesscontrol.windows.net`. Tous les autres composants ACS continueront à fonctionner normalement.
- **Novembre 2018** : tous les composants ACS seront définitivement arrêtés. Cela inclut le portail de gestion ACS, le service de gestion, le service STS et le moteur de règles de transformation de jetons. À ce stade, toutes les demandes envoyées à ACS (situé à `{namespace}.accesscontrol.windows.net`) échoueront. Vous devez avoir migré toutes les applications et services existants vers d’autres technologies bien avant cette période.


## <a name="migration-strategies"></a>Stratégies de migration

Les sections suivantes fournissent des recommandations générales pour la migration à partir d’ACS vers d’autres technologies Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Clients de services cloud Microsoft

Chacun des services cloud Microsoft qui accepte des jetons émis par ACS prend maintenant en charge au moins une autre forme d’authentification. Le mécanisme d’authentification correct varie en fonction de chaque service. Nous vous recommandons de consulter la documentation propre à chaque service pour obtenir des instructions officielles. Pour plus de commodité, chaque ensemble de documentation est mentionné ici :

| Service | Instructions |
| ------- | -------- |
| Azure Service Bus | [Migrer vers des signatures d’accès partagé](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [Migrer vers des signatures d’accès partagé](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache Azure | [Migrer vers le Cache Redis Azure](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data Market | [Migrer vers les API Cognitive Services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrer vers Azure Logic Apps](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrer vers l’authentification Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Sauvegarde Azure | [Mettre à jour l’agent Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Applications web utilisant l’authentification passive

Pour les applications web utilisant ACS pour l’authentification utilisateur, ACS fournissait les fonctionnalités suivantes aux développeurs et aux architectes d’applications web :

- Intégration étroite avec Windows Identity Foundation (WIF).
- Fédération avec Google, Facebook, Yahoo, Compte Microsoft (Live ID), Azure Active Directory et les services AD FS.
- Prise en charge des protocoles d’authentification suivants : OAuth 2.0 draft 13, Ws-Trust et Ws-Federation.
- Prise en charge des formats de jeton suivants : jeton web JSON (JWT), SAML 1.1, SAML 2.0 et Simple web token (SWT).
- Une expérience de découverte de domaine d’accueil, intégrée à WIF, permettant aux utilisateurs de choisir le type de compte qu’ils utilisent pour se connecter. Cette expérience était hébergée par l’application web et entièrement personnalisable.
- Transformation des jetons autorisant une personnalisation enrichie des revendications reçues par l’application web en provenance d’ACS, notamment :
    - Passage de revendications à partir de fournisseurs d’identité
    - Ajout de revendications personnalisées supplémentaires
    - Logique if-then simple pour émettre des revendications sous certaines conditions

Malheureusement, il n’existe pas de service qui fournit l’ensemble de ces fonctionnalités.  Vous devez évaluer les fonctionnalités ACS dont vous avez besoin, puis choisir entre utiliser [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/) ou un autre service d’authentification cloud.

#### <a name="migrating-to-azure-active-directory"></a>Migration vers Azure Active Directory

L’une des solutions envisageables consiste à intégrer vos applications et services directement avec Azure Active Directory. Azure AD est le fournisseur d’identité basé sur le cloud pour les comptes professionnels et scolaires Microsoft. Il s’agit du fournisseur d’identité pour Office 365, Azure et bien plus encore. Il fournit des fonctions d’authentification fédérée semblables à ACS, mais ne prend pas en charge toutes les fonctionnalités d’ACS. Le principal exemple concerne la fédération avec des fournisseurs d’identité sociale, tels que Facebook, Google et Yahoo. Si vos utilisateurs se connectent avec ces types d’informations d’identification, Azure AD n’est pas pour vous. De plus, il ne prend pas nécessairement en charge les mêmes protocoles d’authentification qu’ACS. Ainsi, bien qu’ACS et Azure AD prennent tous deux en charge OAuth, il existe des différences subtiles entre chaque implémentation qui imposent de modifier le code dans le cadre de la migration.

En revanche, Azure AD offre plusieurs avantages potentiels aux clients d’ACS. Il prend nativement en charge les comptes professionnels et scolaires Microsoft hébergés dans le cloud, qui sont couramment utilisés par les clients d’ACS.  Un locataire Azure AD peut également être fédéré à une ou plusieurs instances locales d’Active Directory par le biais d’AD FS, ce qui permet à votre application d’authentifier à la fois les utilisateurs basés sur le cloud et ceux qui sont hébergés localement.  Il prend également en charge le protocole Ws-Federation, ce qui permet d’intégrer relativement facilement une application web à l’aide de Windows Identity Foundation (WIF).

Le tableau suivant compare les fonctionnalités d’ACS pertinentes aux applications web avec celles qui sont disponibles dans Azure AD. De manière générale, **Azure Active Directory est probablement le choix approprié pour votre migration si vous laissez uniquement les utilisateurs se connecter à l’aide de leur compte professionnel ou scolaire Microsoft**.

| Fonctionnalité | Prise en charge par ACS | Prise en charge par Azure AD |
| ---------- | ----------- | ---------------- |
| **Types de comptes** | | |
| Comptes professionnels et scolaires Microsoft | Pris en charge | Pris en charge |
| Comptes Windows Server Active Directory et AD FS | Pris en charge par le biais de la fédération avec un locataire Azure AD <br /> Pris en charge par le biais de la fédération directe avec AD FS | Pris en charge uniquement par le biais de la fédération avec un locataire Azure AD | 
| Comptes d’autres systèmes de gestion d’identité d’entreprise | Possible par le biais de la fédération avec un locataire Azure AD <br />Pris en charge par le biais de la fédération directe | Possible par le biais de la fédération avec un locataire Azure AD |
| Comptes Microsoft pour une utilisation personnelle (Windows Live ID) | Pris en charge | Pris en charge par le biais du protocole OAuth v2.0 d’Azure AD, mais pas sur d’autres protocoles. | 
| Comptes Facebook, Google, Yahoo | Pris en charge | Aucune prise en charge. |
| **Protocoles et compatibilité des SDK** | | |
| Windows Identity Foundation (WIF) | Pris en charge | Pris en charge, mais les instructions disponibles sont limitées. |
| Ws-Federation | Pris en charge | Pris en charge |
| OAuth 2.0 | Pris en charge pour Draft 13 | Pris en charge pour RFC 6749, la spécification la plus récente. |
| Ws-Trust | Pris en charge | Non pris en charge |
| **Formats de jetons** | | |
| Jetons web JSON (JWT) | Pris en charge dans la version bêta | Pris en charge |
| Jetons SAML 1.1 | Pris en charge | Pris en charge |
| Jetons SAML 2.0 | Pris en charge | Pris en charge |
| Jetons SWT (Simple Web Tokens) | Pris en charge | Non pris en charge |
| **Personnalisations** | | |
| Interface utilisateur de sélection de compte/découverte de domaine d’accueil personnalisable | Code téléchargeable pouvant être incorporé dans des applications | Non pris en charge |
| Chargement de certificats de signature de jeton personnalisés | Pris en charge | Pris en charge |
| Personnalisation des revendications dans les jetons | Transfert direct des revendications d’entrée à partir des fournisseurs d’identité<br />Obtention de jeton d’accès à partir du fournisseur d’identité en tant que revendication<br />Émission de revendications de sortie basées sur les valeurs des revendications d’entrée<br />Émission de revendications de sortie avec des valeurs constantes | Impossible de transférer directement des revendications à partir de fournisseurs d’identité fédérés<br />Impossible d’obtenir un jeton d’accès à partir du fournisseur d’identité en tant que revendication<br />Impossible d’émettre des revendications de sortie basées sur les valeurs des revendications d’entrée<br />Possibilité d’émettre des revendications de sortie avec des valeurs constantes<br />Possibilité d’émettre des revendications de sortie basées sur des propriétés d’utilisateurs synchronisées avec Azure AD |
| **Automation** | | |
| Automatisation des tâches de gestion/configuration | Prise en charge par le service de gestion ACS | Prise en charge par le biais de l’API Azure AD Graph et Microsoft Graph. |

Si vous décidez qu’Azure AD convient à vos applications et services, vous devez connaître deux manières d’intégrer votre application avec Azure AD.

Pour utiliser Ws-Federation/WIF pour l’intégration avec Azure AD, nous vous recommandons de suivre [l’approche décrite dans cet article](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Cet article fait référence à la configuration d’Azure AD pour l’authentification unique basée sur SAML, mais il concerne également la configuration de Ws-Federation. Cette approche nécessite une licence Azure AD Premium, mais elle présente deux avantages :

- Vous bénéficiez de toute la flexibilité offerte par la personnalisation des jetons Azure AD. Cela vous permet de personnaliser les revendications émises par Azure AD pour qu’elles correspondent à celles émises par ACS, notamment d’inclure la revendication ID ou Identificateur de nom de l’utilisateur. Vous devez vérifier que les ID émis par Azure AD correspondent à ceux émis par ACS, afin de continuer à recevoir des identificateurs d’utilisateurs cohérents pour vos utilisateurs même après avoir changé de technologie.
- Vous pouvez configurer un certificat de signature de jeton propre à votre application, dont vous contrôlez la durée de vie.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Adopter cette approche nécessite une licence Azure AD Premium. Si vous êtes un client ACS et que vous avez besoin d’une licence Premium pour configurer l’authentification unique pour une application, contactez-nous. Nous serons heureux de vous fournir des licences de développeur.

Une autre approche consiste à suivre [cet exemple de code](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), qui fournit des instructions légèrement différentes quant à la configuration de Ws-Federation. Cet exemple de code n’utilise pas WIF mais plutôt l’intergiciel (middleware) OWIN ASP.NET 4.5. Toutefois, les instructions d’inscription d’application sont valides pour les applications qui utilisent WIF, et elles ne nécessitent pas de licence Azure AD Premium.  Si vous choisissez cette approche, vous devez comprendre [la substitution de clé de signature dans Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Cette approche utilise la clé de signature globale Azure AD pour émettre des jetons. Par défaut, WIF n’actualise pas automatiquement les clés de signature. Quand Azure AD permute ses clés de signature globale, votre implémentation de WIF doit être prête à accepter les changements.

Si vous pouvez effectuer une intégration à Azure AD par le biais du protocole OpenID Connect ou OAuth, nous vous recommandons de le faire.  Vous trouverez une documentation complète et des instructions détaillées expliquant comment intégrer Azure AD dans votre application web dans notre [Guide du développeur Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migration vers Azure AD B2C

L’autre chemin de migration à prendre en compte est Azure AD B2C.  B2C est un service d’authentification cloud qui, un peu comme ACS, permet aux développeurs d’externaliser leur logique de gestion d’identité et d’authentification à un service cloud.  Il s’agit d’un service payant (avec des niveaux gratuits et premium) conçu pour les applications exposées aux consommateurs et pouvant avoir plusieurs millions d’utilisateurs actifs.

Comme ACS, l’une des fonctionnalités les plus intéressantes de B2C est sa prise en charge de nombreux types de comptes. Avec B2C, vous pouvez connecter les utilisateurs avec leurs comptes Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo et bien plus encore. B2C prend également en charge les « comptes locaux », ou les noms d’utilisateurs et mots de passe que les utilisateurs créent spécifiquement pour votre application. Azure AD B2C fournit également une extensibilité étendue qui vous permet de personnaliser votre flux de connexion. En revanche, il ne prend pas en charge la large gamme de protocoles d’authentification et de formats de jetons dont les clients d’ACS peuvent avoir besoin. Vous ne pouvez pas non plus l’utiliser pour obtenir des jetons et interroger des informations supplémentaires sur l’utilisateur à partir du fournisseur d’identité, Microsoft ou autre.

Le tableau suivant compare les fonctionnalités d’ACS pertinentes aux applications web avec celles qui sont disponibles dans Azure AD B2C. De manière générale, **Azure AD B2C est probablement le bon choix pour votre migration si votre application est exposée aux consommateurs ou si elle prend en charge de nombreux types de comptes.**

| Fonctionnalité | Prise en charge par ACS | Prise en charge par Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Types de comptes** | | |
| Comptes professionnels et scolaires Microsoft | Pris en charge | Pris en charge par le biais des stratégies personnalisées  |
| Comptes Windows Server Active Directory et AD FS | Pris en charge par le biais de la fédération directe avec AD FS | Pris en charge par le biais de la fédération SAML à l’aide de stratégies personnalisées |
| Comptes d’autres systèmes de gestion d’identité d’entreprise | Pris en charge par le biais de la fédération directe sur Ws-Federation | Pris en charge par le biais de la fédération SAML à l’aide de stratégies personnalisées |
| Comptes Microsoft pour une utilisation personnelle (Windows Live ID) | Pris en charge | Pris en charge | 
| Comptes Facebook, Google, Yahoo | Pris en charge | Facebook et Google sont pris en charge en mode natif, Yahoo est pris en charge par le biais de la fédération OpenID Connect à l’aide de stratégies personnalisées |
| **Protocoles et compatibilité des SDK** | | |
| Windows Identity Foundation (WIF) | Pris en charge | Non pris en charge. |
| Ws-Federation | Pris en charge | Non pris en charge. |
| OAuth 2.0 | Pris en charge pour Draft 13 | Pris en charge pour RFC 6749, la spécification la plus récente. |
| Ws-Trust | Pris en charge | Non pris en charge. |
| **Formats de jetons** | | |
| Jetons web JSON (JWT) | Pris en charge dans la version bêta | Pris en charge |
| Jetons SAML 1.1 | Pris en charge | Non pris en charge |
| Jetons SAML 2.0 | Pris en charge | Non pris en charge |
| Jetons SWT (Simple Web Tokens) | Pris en charge | Non pris en charge |
| **Personnalisations** | | |
| Interface utilisateur de sélection de compte/découverte de domaine d’accueil personnalisable | Code téléchargeable pouvant être incorporé dans des applications | Interface utilisateur entièrement personnalisable par le biais de feuilles de style CSS personnalisées. |
| Chargement de certificats de signature de jeton personnalisés | Pris en charge | Clés de signature (et non certificats) personnalisées prises en charge par le biais des stratégies personnalisées. |
| Personnalisation des revendications dans les jetons | Transfert direct des revendications d’entrée à partir des fournisseurs d’identité<br />Obtention de jeton d’accès à partir du fournisseur d’identité en tant que revendication<br />Émission de revendications de sortie basées sur les valeurs des revendications d’entrée<br />Émission de revendications de sortie avec des valeurs constantes | Possibilité de transférer des revendications à partir de fournisseurs d’identité. Stratégies personnalisées obligatoires pour certaines revendications.<br />Impossible d’obtenir un jeton d’accès à partir du fournisseur d’identité en tant que revendication<br />Possibilité d’émettre des revendications de sortie basées sur les valeurs des revendications d’entrée par le biais des stratégies personnalisées<br />Possibilité d’émettre des revendications avec des valeurs constantes par le biais des stratégies personnalisées |
| **Automation** | | |
| Automatisation des tâches de gestion/configuration | Prise en charge par le service de gestion ACS | Création d’utilisateurs autorisée par le biais de l’API Graph Azure AD. Impossible de créer des locataires, des applications ou des stratégies B2C par programmation. |

Si vous décidez qu’Azure AD B2C convient à vos applications et services, vous devez commencer par les ressources suivantes :

- [Documentation d’Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Stratégies personnalisées Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Tarification Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Autres options de migration

Si ni Azure AD ni Azure AD B2C ne répond aux besoins de votre application web, n’hésitez pas à nous contacter afin que nous puissions vous aider à identifier la meilleure marche à suivre.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Services web utilisant l’authentification active

Pour les services web sécurisés avec des jetons émis par ACS, ACS offrait les fonctionnalités suivantes :

- Capacité à inscrire une ou plusieurs **identités de service** dans votre espace de noms ACS, pouvant être utilisées pour demander des jetons.
- Prise en charge des protocoles OAuth WRAP et OAuth 2.0 Draft 13 pour les demandes de jetons, avec les types d’informations d’identification suivants :
    - Un mot de passe simple créé pour l’identité du service
    - Un jeton SWT signé utilisant une clé symétrique ou un certificat X509
    - Un jeton SAML émis par un fournisseur d’identité approuvé (en général, une instance AD FS)
- Prise en charge des formats de jeton suivants : jeton web JSON (JWT), SAML 1.1, SAML 2.0 et Simple web token (SWT).
- Règles de transformation de jetons simples

Les identités de service dans ACS sont généralement utilisées pour l’implémentation d’une authentification de type « serveur à serveur » (S2S).  

#### <a name="migrating-to-azure-active-directory"></a>Migration vers Azure Active Directory

Notre recommandation pour ce type de flux d’authentification consiste à migrer vers [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). Azure AD est le fournisseur d’identité basé sur le cloud pour les comptes professionnels et scolaires Microsoft. Il s’agit du fournisseur d’identité pour Office 365, Azure et bien plus encore. Mais vous pouvez aussi l’utiliser pour l’authentification de serveur à serveur, grâce à l’implémentation de l’octroi des informations d’identification du client OAuth d’Azure AD.  Le tableau suivant compare les fonctionnalités d’authentification de serveur à serveur offertes par ACS avec celles qui sont disponibles dans Azure AD.

| Fonctionnalité | Prise en charge par ACS | Prise en charge par Azure AD |
| ---------- | ----------- | ---------------- |
| Inscription de service web | Créer une partie de confiance dans le portail de gestion ACS. | Créer une application web Azure AD dans le portail Azure. |
| Inscription de client | Créer une identité de service dans le portail de gestion ACS. | Créer une autre application web Azure AD dans le portail Azure. |
| Protocole utilisé | Protocole OAuth WRAP<br />Octroi des informations d’identification du client OAuth 2.0 Draft 13 | Octroi des informations d’identification du client OAuth 2.0 |
| Méthodes d’authentification du client | Mot de passe simple<br />Jeton SWT signé<br />Jeton SAML de fournisseur d’identité fédérée | Mot de passe simple<br />Jeton SWT signé |
| Formats de jetons | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | JWT uniquement |
| Transformation de jeton | Ajouter des revendications personnalisées<br />Logique d’émission de revendications if-then simple | Ajouter des revendications personnalisées | 
| Automatisation des tâches de gestion/configuration | Prise en charge par le service de gestion ACS | Prise en charge par le biais de l’API Azure AD Graph et Microsoft Graph. |

Pour obtenir des conseils sur l’implémentation des scénarios de serveur à serveur, consultez les ressources suivantes :

- [Section « service à service » du guide du développeur Azure AD](https://aka.ms/aaddev).
- [Exemple de code de démon utilisant des informations d’identification de clients avec mots de passe simples](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Exemple de code de démon utilisant des informations d’identification de clients avec certificats](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Autres options de migration

Si Azure AD ne répond pas aux besoins de votre service web, laissez un commentaire et nous vous aiderons à identifier le meilleur plan pour votre cas particulier.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Questions, problèmes et commentaires

Nous sommes conscients que de nombreux clients ACS n’auront pas identifié de chemin de migration clair après avoir lu cet article et pourront avoir besoin d’une assistance ou de conseils afin d’identifier le plan approprié. Si vous souhaitez discuter de vos scénarios de migration et nous faire part de vos questions, laissez un commentaire au bas de cette page.
