---
title: Migrer des applications locales AD FS vers Azure. | Microsoft Docs
description: Cet article a pour but d’aider les organisations à comprendre comment migrer des applications locales vers Azure AD, et se concentre sur les applications SaaS fédérées.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: billmath
ms.openlocfilehash: 5eb562901d73974765878024b1107e3b75e9abb5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrer des applications locales AD FS vers Azure 

Cet article vous aidera à comprendre comment migrer des applications locales vers Azure Active Directory (Azure AD). Il se concentre sur les applications SaaS fédérées. 

Il ne constitue pas un guide pas à pas. Il fournit des conseils conceptuels pour vous aider à réussir la migration, en comprenant comment se traduisent les configurations locales dans Azure AD. Il couvre également les scénarios courants.

## <a name="introduction"></a>Introduction

Si vous disposez d’un répertoire local contenant des comptes utilisateur, il est probable que vous disposiez d’au moins un ou deux applications. Ces dernières sont configurées pour que les utilisateurs y accèdent en s’authentifiant avec ces identités.

Et si votre organisation est similaire aux autres, vous êtes probablement sur le point d’adopter des applications et des identités cloud. Vous maîtrisez peut-être Office 365 et Azure AD Connect. Vous avez peut-être configuré des applications SaaS basées sur le cloud pour quelques charges de travail clé.  

De nombreuses organisations disposent d’applications SaaS ou métiers personnalisées (LOB), fédérées directement dans un service d’authentification local tel qu’Active Directory Federation Services (AD FS), et les applications Office 365 et Azure AD. Ce guide de migration décrit pourquoi et comment migrer des applications locales vers Azure AD.

>[!NOTE]
>Il fournit des informations détaillées sur la configuration et la migration d’applications SaaS, dont des informations de niveau supérieur sur les applications métiers personnalisées. Nous prévoyons d’ajouter davantage de conseils détaillés sur les applications métiers personnalisées.

![Applications directement connectées localement](media/migrate-adfs-apps-to-azure/migrate1.png)

![Applications fédérées via Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-migrating-apps-to-azure-ad"></a>Pourquoi migrer des applications vers Azure AD

Pour une organisation qui utilise déjà AD FS, Ping ou un autre fournisseur d’authentification locale, la migration d’applications vers Azure AD présente les avantages suivants :

**Accès plus sécurisé**
- Configurez des contrôles d’accès granulaires par application, incluant l’authentification multifacteur Azure, avec l’[Accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Les stratégies peuvent être appliquées à des applications SaaS et personnalisées de la même façon que sur Office 365, si vous l’utilisez.
- Pour détecter des menaces et protéger l’authentification basée sur le Machine Learning et les heuristiques qui identifient le trafic à risque, profitez de la fonction [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection).

**Azure AD B2B Collaboration**
- Une fois que l’authentification aux applications SaaS est basée sur Azure AD, vous pouvez donner l’accès à vos partenaires aux ressources cloud avec [Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Expérience administrateur plus facile et capacités supplémentaires d’Azure AD**
- En tant que fournisseur d’identité pour les applications SaaS, Azure AD prend en charge des fonctionnalités supplémentaires :
  - Certificats de signature de jeton par application.
  - [Date d’expiration des certificats configurable](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).
  - [Approvisionnement automatisé](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) des comptes utilisateur (dans les applications de la Place de marché Microsoft Azure principales) basé sur les identités Azure AD.

**Avantages d’un fournisseur d’identité local conservés**
- Tout en profitant des avantages d’Azure AD, vous pouvez continuer à utiliser votre solution locale pour l’authentification. Vous bénéficiez ainsi toujours des solutions locales d’authentification multifacteur, de journalisation et d’audit. 

**Assistance à la mise hors service du fournisseur d’identité local**
- Pour les organisations qui souhaitent mettre hors service le produit d’authentification local, la migration d’applications vers Azure AD permet une transition plus facile en prenant en charge une partie des opérations. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mappage de types d’applications locales à des types d’applications dans Azure AD
La plupart des applications entrent dans l’une des quelques catégories en fonction du type d’authentification qu’elles utilisent. Ces catégories déterminent comment l’application est représentée dans Azure AD.

En bref, les applications SAML 2.0 peuvent être intégrées à Azure AD soit via la galerie d’applications Azure AD de la place de marché, soit comme des applications ne figurant pas dans la place de marché. Les applications qui utilisent OAuth 2.0 ou OpenID Connect peuvent être intégrées à Azure AD de façon similaire en tant qu’*inscriptions d’application*. Lisez la suite pour plus de détails.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Applications SaaS fédérées et applications métiers personnalisées
Les applications fédérées comprennent les applications incluses dans les catégories suivantes :

- Applications SaaS 
    - Si vos utilisateurs s’authentifient dans des applications telles que Salesforce, ServiceNow ou Workday, et que vous effectuez une intégration à un fournisseur d’identité local tel qu’AD FS ou Ping, vous utilisez une authentification fédérée pour des applications SaaS.
    - Les applications utilisent en général le protocole SAML 2.0 pour une authentification fédérée.
    - Les applications comprises dans cette catégorie peuvent être intégrées à Azure AD comme des applications d’entreprise, depuis la place de marché ou en tant qu’applications ne figurant pas dans la place de marché.
- Applications métiers personnalisées
    - Cette catégorie regroupe les applications non SaaS, développées en interne par votre organisation ou disponibles en tant que produit groupé standard installé dans votre centre de données. Sont incluses les applications SharePoint et les applications créées dans Windows Identity Foundation.
    - Ces applications peuvent utiliser le protocole SAML 2.0, WS-Federation, OAuth ou OpenID Connect pour une authentification fédérée.
    - Les applications personnalisées qui utilisent OAuth 2.0, OpenID Connect ou WS-Federation peuvent être intégrées à Azure AD en tant qu’inscriptions d’application. Les applications personnalisées qui utilisent SAML 2.0 ou WS-Federation peuvent être intégrées en tant qu’applications ne figurant pas dans la place de marché au sein des applications d’entreprise.

### <a name="non-federated-apps"></a>Applications non fédérées
Vous pouvez intégrer des applications non fédérées à Azure AD à l’aide du Proxy d’application Azure Active Directory et des fonctions associées. Applications non fédérées :
- Applications utilisant l’Authentification intégrée à Windows directement dans Active Directory. Vous pouvez intégrer ces applications à Azure AD via le [Proxy d’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).
- Applications s’intégrant à votre fournisseur d’authentification unique via un agent et utilisant des en-têtes pour l’autorisation. Les applications locales qui utilisent un agent installé pour l’authentification et une autorisation basée sur l’en-tête peuvent être configurées pour une authentification basée sur Azure AD via le Proxy d’application Azure Active Directory avec [Ping Access pour Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Traduire des applications fédérées locales dans Azure AD 
AD FS et Azure AD fonctionnent de la même manière. La configuration de la confiance, des URL et des identificateurs d’authentification et de déconnexion s’appliquent pour les deux. Toutefois, il est important de connaître les petites différences qui existent pendant votre transition.

Pour vous aider à traduire, nous avons mappé dans les tableaux suivants plusieurs idées clés partagées entre les applications AD FS, Azure AD et SaaS. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Représentation de l’application dans Azure AD ou AD FS
La migration commence par l’évaluation de la configuration locale de l’application, et par le mappage de cette configuration dans Azure AD. Le tableau suivant mappe des éléments de configuration d’une partie de confiance AD FS aux éléments correspondants dans Azure AD.  
- Terme AD FS : partie de confiance ou approbation de partie de confiance.
- Terme Azure AD : application d’entreprise ou inscription d’application (selon le type d’application).

|Élément de configuration d’application|Description|Emplacement dans la configuration d’AD FS|Emplacement correspondant dans la configuration d’Azure AD|Élément de jeton SAML|
|-----|-----|-----|-----|-----|
|URL d’authentification à une application|URL de la page de connexion de cette application. C’est ici que l’utilisateur entame sa connexion à un flux SAML initié du fournisseur de services.|N/A|Dans Azure AD, l’URL d’authentification est configurée au sein du portail Azure dans les propriétés d’**authentification unique** de l’application en tant qu’URL d’authentification.</br></br>(Il vous faudra peut-être cliquer sur **Afficher les paramètres d’URL avancés** pour voir l’URL d’authentification.)|N/A|
|URL de réponse de l’application|URL de l’application du point de vue du fournisseur d’identité. C’est ici que l’utilisateur et le jeton sont envoyés lorsque l’utilisateur s’est authentifié auprès du fournisseur d’identité.</br></br> Parfois appelé « point de terminaison consommateur d’assertion SAML ».|Se trouve dans l’approbation de partie de confiance AD FS pour l’application. Cliquez avec le bouton droit sur la partie de confiance, choisissez **Propriétés**, puis sélectionnez l’onglet **Points de terminaison**.|Dans Azure AD, l’URL de réponse est configurée au sein du portail Azure dans les propriétés d’**authentification unique** de l’application en tant qu’URL de réponse.</br></br>(Il vous faudra peut-être cliquer sur **Afficher les paramètres d’URL avancés** pour voir l’URL de réponse.)|Cartes vers l’élément **Destination** dans le jeton SAML.</br></br> Exemple de valeur : https://contoso.my.salesforce.com|
|URL de déconnexion de l’application|L’URL à laquelle sont envoyées les requêtes de « nettoyage de déconnexion » lorsqu’un utilisateur se déconnecte d’une application, afin de déconnecter toutes les applications dans lesquelles l’utilisateur avait été authentifié par le fournisseur d’identité.|Se trouve dans la gestion AD FS sous **Approbations de partie de confiance**. Cliquez avec le bouton droit sur la partie de confiance, choisissez **Propriétés**, puis sélectionnez l’onglet **Points de terminaison**.|N/A. Azure AD ne prend pas en charge la fonction « Single Logout », ce qui veut dire déconnexion de toutes les applications. Il déconnecte simplement l’utilisateur d’Azure AD.|N/A|
|Identificateur de l’application|Identificateur de l’application du point de vue du fournisseur d’identité. La valeur de l’URL d’authentification est souvent utilisée pour l’identificateur (mais pas toujours).</br></br> L’application l’appelle parfois l’« ID d’entité ».|Dans AD FS, il s’agit de l’ID de la partie de confiance. Cliquez avec le bouton droit sur l’approbation de partie de confiance, choisissez **Propriétés**, puis sélectionnez l’onglet **Identificateurs**.|Dans Azure AD, l’identificateur est configuré au sein du portail Azure dans les propriétés d’**authentification unique** de l’application en tant qu’identificateur sous **Domaine et URL**. (Il vous faudra peut-être cliquer sur **Afficher les paramètres d’URL avancés**.)|Correspond à l’élément **Audience** du jeton SAML.|
|Métadonnées de fédération de l’application|Emplacement des métadonnées de fédération de l’application. Les fournisseurs d’identité l’utilisent pour mettre à jour automatiquement des paramètres de configuration spécifiques tels que les points de terminaison ou les certificats de chiffrement.|L’URL des métadonnées de fédération de l’application se trouve dans l’approbation de partie de confiance AD FS de l’application. Cliquez avec le bouton droit sur l’approbation et sélectionnez **Propriétés** puis cliquez sur l’onglet **Analyse**.|N/A. Azure AD ne prend pas en charge la consommation directe de métadonnées de fédération de l’application.|N/A|
|Identificateur de l’utilisateur/**NameID**|Attribut utilisé pour indiquer uniquement l’utilisateur d’Azure AD ou AD FS vers votre application.</br></br> Il s’agit en général de l’UPN ou de l’adresse de messagerie de l’utilisateur.|Dans AD FS, il se trouve sur la partie de confiance en tant que règle de revendication. Dans la plupart des cas, la règle de revendication génère une revendication dont le type se termine par « nameidentifier ».|Dans Azure AD, l’identificateur d’utilisateur se trouve au sein du portail Azure dans les propriétés d’**authentification unique** de l’application, sous l’en-tête **Attributs utilisateur**.</br></br>Par défaut, c’est le nom d’utilisateur principal qui est utilisé.|Il est communiqué à l’application par le fournisseur d’identité comme **NameID** dans le jeton SAML.|
|Autres revendications envoyées à l’application|En plus de l’identificateur d’utilisateur/**NameID**, d’autres informations de revendication sont couramment envoyées à l’application à partir du fournisseur d’identité. Par exemple, le prénom, le nom, l’adresse de messagerie et les groupes dont l’utilisateur est membre.|Dans AD FS, elles se trouvent sur la partie de confiance en tant qu’autres règles de revendication.|Dans Azure AD, elles se trouvent au sein du portail Azure dans les propriétés d’**authentification unique** de l’application, sous l’en-tête **Attributs utilisateur**. Sélectionnez **Afficher** et modifier tous les autres attributs utilisateur.|N/A|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Représentation d’Azure AD en tant que fournisseur d’identité dans une application SaaS
Dans le cadre d’une migration, vous devez configurer l’application pour qu’elle pointe vers Azure AD (et non vers le fournisseur d’identité local). Cette section se concentre sur les applications SaaS qui utilisent le protocole SAML, pas sur les applications métiers personnalisées. Toutefois, les concepts qui y sont abordés peuvent s’appliquer à ces dernières. 

À un niveau général, plusieurs éléments clés pointent une application SaaS vers Azure AD :
- Émetteur de fournisseur d’identité : https&#58;//sts.windows.net/{tenant-id}/
- URL de connexion du fournisseur d’identité : https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- URL de déconnexion du fournisseur d’identité : https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Emplacement des métadonnées de fédération : https&#58;//login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id} 

Remplacez {tenant-id} par votre ID de locataire qui se trouve dans le portail Azure sous **Azure Active Directory** > **Propriétés**, **ID de répertoire**. Remplacez {application-id} par votre ID d’application, qui se trouve dans les propriétés de l’application sous le nom **ID de l’application**.

Le tableau suivant décrit les éléments clés à la configuration des paramètres d’authentification unique dans l’application, ainsi que leurs valeurs et emplacements au sein d’AD FS et Azure AD. Le cadre de référence de ce tableau correspond à l’application SaaS, qui doit savoir où envoyer les requêtes d’authentification et comment valider les jetons ainsi reçus.

|Élément de configuration|Description|AD FS|Azure AD|
|---|---|---|---|
|Fournisseur d’identité (IdP) </br>URL de connexion </br>URL|URL de connexion du fournisseur d’identité depuis l’application (où l’utilisateur est redirigé pour s’identifier).|L’URL de connexion AD FS correspond au nom de service de fédération AD FS, suivi par « /adfs/ls/ ». Par exemple : https&#58;//fs.contoso.com/adfs/ls/|La valeur correspondante pour Azure AD suit le modèle dans lequel {tenant-id} est remplacé par votre ID de locataire. Il se trouve dans le portail Azure sous **Azure Active Directory** > **Propriétés**, **ID de répertoire**.</br></br>Pour les applications qui utilisent le protocole SAML-P : https&#58;//login.microsoftonline.com/{tenant-id}/saml2 </br></br>Pour les applications qui utilisent le protocole WS-Federation : https&#58;//login.microsoftonline.com/{tenant-id}/wsfed|
|Fournisseur d’identité (IdP) </br>déconnexion </br>URL|URL de déconnexion du fournisseur d’identité depuis l’application (où l’utilisateur est redirigé lorsqu’il choisit de se déconnecter de l’application).|Pour AD FS, l’URL de déconnexion correspond soit à l’URL de connexion, soit à l’URL de connexion suivie de « wa=wsignout1.0 ». Par exemple : https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|La valeur correspondante pour Azure AD dépend de la prise en charge d’une déconnexion SAML 2.0 par l’application.</br></br>Si l’application prend en charge la déconnexion SAML, la valeur suit le modèle dans lequel la valeur {tenant-id} est remplacée par l’ID de locataire. Il se trouve dans le portail Azure sous **Azure Active Directory** > **Propriétés**, **ID de répertoire** : https&#58;//login.microsoftonline.com/{tenant-id}/saml2</br></br>Si l’application ne prend pas en charge la déconnexion SAML : https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|par jeton </br>signature </br>certificat|Certificat dont la clé privée qu’utilise le fournisseur d’identité pour se connecter a émis des jetons. Il vérifie que le jeton provient du fournisseur d’identité auquel l’application fait confiance.|Le certificat de signature de jetons AD FS se situe dans Gestion AD FS sous **Certificats**.|Dans Azure AD, le certificat de signature de jetons se situe au sein du portail Azure dans les propriétés d’**authentification unique** de l’application, sous l’en-tête **Certificat de signature SAML**. Vous pouvez y télécharger le certificat pour le charger dans l’application.</br></br> Si l’application possède plus d’un certificat, vous pouvez tous les trouver dans le fichier XML des métadonnées de fédération.|
|Identificateur/</br>« émetteur »|Identificateur du fournisseur d’identité depuis l’application (parfois appelé « ID émetteur »).</br></br>Dans le jeton SAML, la valeur apparaît comme l’élément **Émetteur**.|L’identificateur pour AD FS correspond généralement à l’identificateur du service FS dans Gestion AD FS sous **Service** > **Modifier propriétés du service FS**. Par exemple : http&#58;//fs.contoso.com/adfs/services/trust|La valeur correspondante pour Azure AD suit le modèle dans lequel la valeur {tenant-id} est remplacée par l’ID de locataire. Il se trouve dans le portail Azure sous **Azure Active Directory** > **Propriétés**, **ID de répertoire** : https&#58;//sts.windows.net/{tenant-id}/|
|Fournisseur d’identité (IdP) </br>fédération </br>metadata|Emplacement des métadonnées de fédération du fournisseur d’identité disponibles au public. (Certaines applications utilisent les métadonnées de fédération comme une alternative à l’administrateur pour configurer individuellement les URL, l’identificateur et le certificat de signature de jetons.)|L’URL des métadonnées de fédération AD FS se trouve dans Gestion AD FS sous **Service** > **Points de terminaison** > **Métadonnées** > **Type : métadonnées de fédération**. Par exemple : https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|La valeur correspondante pour Azure AD suit le modèle https&#58;//login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml. La valeur {TenantDomainName} est remplacée par votre nom de locataire au format « contoso.onmicrosoft.com ». </br></br>Pour plus d’informations, consultez la section [Métadonnées de fédération](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata).

## <a name="migrating-saas-apps"></a>Migrer les applications SaaS
La migration d’application SaaS depuis AD FS ou un autre fournisseur d’identité vers Azure AD est aujourd’hui un processus manuel. Pour des conseils spécifiques à une application, consultez [la liste des didacticiels sur l’intégration des applications SaaS situées dans la place de marché](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Les didacticiels sur l’intégration partent du principe que vous faites une intégration en mode champs vert. Il existe quelques principes fondamentaux à connaître concernant la migration si vous planifiez, évaluez, configurez et basculez vos applications :  
- Certaines applications peuvent facilement être migrées. D’autres plus complexes telles que les revendications personnalisées nécessitent une configuration supplémentaire dans Azure AD et/ou Azure AD Connect.
- Dans les scénarios les plus courants, seule la revendication **NameID** et d’autres revendications d’identificateur utilisateur courantes sont requises pour une application. Pour déterminer si une revendication supplémentaire est nécessaire, examinez les revendications émises à partir d’AD FS ou de votre fournisseur d’identité tiers.
- Après avoir déterminé des revendications supplémentaires requises, assurez-vous qu’elles sont disponibles dans Azure AD. Vérifiez la configuration de synchronisation d’Azure AD Connect afin de garantir qu’un attribut requis (par exemple **samAccountName**) est bien synchronisé avec Azure AD.
- Une fois que les attributs sont disponibles dans Azure AD, vous pouvez ajouter des règles d’émission de revendications dans Azure AD pour inclure ces attributs en tant que revendications dans des jetons émis. Vous pouvez ajouter ces règles au sein des propriétés d’**authentification unique** de l’application dans Azure AD.

### <a name="assess-what-can-be-migrated"></a>Déterminer ce qui est peut être migré
Les applications SAML 2.0 peuvent être intégrées à Azure AD soit via la galerie d’applications Azure AD de la place de marché, soit comme des applications ne figurant pas dans la place de marché.  

Des étapes supplémentaires sont nécessaires pour certaines configurations dans Azure AD, dont certaines ne sont actuellement pas prises en charge. Pour déterminer ce que vous pouvez déplacer, consultez la configuration actuelle de chaque application. En particulier :
- Les règles de revendication configurées (règles de transformation d’émission).
- Format et attribut de **NameID** SAML.
- Versions des jetons SAML émis.
- Autres configurations telles que les règles d’autorisation d’émission, les stratégies de contrôle d’accès ou les règles d’authentification multifacteur (authentification supplémentaire).

#### <a name="what-can-be-migrated-today"></a>Ce qui est peut être migré actuellement
Actuellement, les applications que vous pouvez migrer facilement sont les applications SAML 2.0 qui utilisent l’ensemble standard d’éléments de configuration et de revendications. Il peut s’agir des éléments suivants :
- Nom d’utilisateur principal.
- Adresse de messagerie.
- Prénom.
- Nom.
- D’un autre attribut comme **NameID** de SAML, y compris l’attribut de messagerie Azure AD, le préfixe de messagerie, l’attribut employeeid, les attributs d’extension 1-15, ou l’attribut local **SamAccountName**. Pour plus d’informations, consultez la section [Modification de la revendication NameIdentifier](./develop/active-directory-saml-claims-customization.md).
- Revendications personnalisées. Pour plus d’informations sur les mappages de revendications pris en charge, consultez les pages [Mappage des revendications dans Azure Active Directory](active-directory-claims-mapping.md) et [Personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory](./develop/active-directory-saml-claims-customization.md).

En plus des revendications personnalisées et des éléments **NameID**, les configurations qui nécessitent des étapes supplémentaires dans Azure AD dans le cadre de la migration sont :
- Règles d’autorisation personnalisées ou règles d’authentification multifacteur dans AD FS. Vous pouvez les configurer à l’aide de la fonction [Accès conditionnel Azure AD](active-directory-conditional-access-azure-portal.md).
- Applications avec plusieurs points de terminaison SAML. Vous pouvez les configurer dans Azure AD à l’aide de PowerShell. (Cette fonctionnalité n’est pas disponible dans le portail.)
- Applications WS-Federation telles que les applications SharePoint qui nécessitent des jetons SAML version 1.1. Vous devez les configurer manuellement à l’aide de PowerShell.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Applications et configurations non prises en charge dans Azure AD actuellement
Les applications qui nécessitent les fonctionnalités suivantes ne peuvent actuellement pas être migrées. Si vous disposez d’applications qui nécessitent ces fonctionnalités, faites-nous-en part dans la section des commentaires.
- Fonctionnalités de protocole :
    - Prise en charge de la déconnexion unique (Single Logout) SAML pour toutes les applications connectées.
    - Prise en charge du modèle WS-Trust ActAs.
    - Résolution d’artefacts SAML.
    - Vérification de signature des requêtes SAML signées. Notez que les requêtes signées sont acceptées, mais la signature n’est pas vérifiée.
- Fonctionnalités des jetons : 
    - Chiffrement des jetons SAML. 
    - Jetons de version SAML 1.1 au sein des réponses du protocole SAML. 
- Revendications dans les fonctionnalités des jetons :
    - Émission des noms de groupe locaux en tant que revendications.
    - Revendications depuis des magasins autres qu’Azure AD.
    - Règles de transformation des émissions de revendications complexes. Pour plus d’informations sur les mappages de revendications pris en charge, consultez les pages [Mappage des revendications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) et [Personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
    - Émission d’extensions de répertoire en tant que revendications.
    - Spécification personnalisée de format **NameID**.
    - Émission d’attributs à valeur multiple.

>[!NOTE]
>Azure AD évolue constamment afin d’ajouter des fonctionnalités dans ce domaine. Nous mettons à jour cet article régulièrement. 

### <a name="configure-azure-ad"></a>Configurer Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Configurer les paramètres d’authentification unique pour l’application SaaS

Dans Azure AD, la configuration de l’authentification SAML (requise par votre application) est effectuée au sein des propriétés d’**authentification unique** de l’application sous **Attributs utilisateur**.

![Propriétés d’authentification unique avec la section « Attributs utilisateur »](media/migrate-adfs-apps-to-azure/migrate3.png)

Cliquez sur **Afficher et modifier tous les autres attributs utilisateur** pour voir les attributs à envoyer en tant que revendications dans le jeton de sécurité.

![Liste d’attributs](media/migrate-adfs-apps-to-azure/migrate4.png)

Sélectionnez une ligne d’attribut spécifique pour le modifier ou **Ajouter un attribut** pour ajouter un nouvel attribut.

![Volet « Modifier l’attribut »](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Assigner des utilisateurs à l’application
Pour que les utilisateurs Azure AD puissent se connecter à une application SaaS, vous devez leur accorder l’accès.

Pour assigner des utilisateurs dans le portail Azure AD, naviguez jusqu’à la page de l’application SaaS, puis cliquez sur **Utilisateurs et groupes** dans la barre latérale. Pour ajouter un utilisateur ou un groupe, cliquez sur **Ajouter utilisateur** en haut du volet. 

![Bouton « Ajouter utilisateur » dans « Utilisateurs et groupes »](media/migrate-adfs-apps-to-azure/migrate6.png) 

![Volet « Ajouter une attribution »](media/migrate-adfs-apps-to-azure/migrate7.png)

Pour vérifier l’accès, les utilisateurs doivent voir l’application SaaS dans leur [panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quand ils se connectent. Le panneau d’accès se trouve à l’adresse http://myapps.microsoft.com. Dans cet exemple, un utilisateur a obtenu l’accès à Salesforce et ServiceNow.

![Exemple de panneau d’accès avec les applications Salesforce et ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Configurer l’application SaaS
Le processus de basculement depuis la fédération locale vers Azure AD ne peut se faire que si l’application SaaS sur laquelle vous travaillez prend en charge plusieurs fournisseurs d’identité. Voici certaines questions fréquentes sur la prise en charge de fournisseurs d’identité multiples :

   **Q : Qu’est-ce que la prise en charge de fournisseurs d’identité multiples pour une application ?**
    
   R : Les applications SaaS qui prennent en charge plusieurs fournisseurs d’identité vous permet d’entrer toutes les informations du nouveau fournisseur (ici, Azure AD) avant de vous engager à changer l’expérience d’authentification. Une fois la configuration terminée, vous pouvez changer la configuration d’authentification de l’application pour qu’elle pointe vers Azure AD.

   **Q : En quoi la prise en charge de plusieurs fournisseurs d’identité est importante ?**

   R : Si l’application ne prend pas en charge plusieurs fournisseurs d’application, l’administrateur doit prévoir un temps de service ou de maintenance pour configurer Azure AD comme nouveau fournisseur d’identité de l’application. Pendant cette maintenance, les utilisateurs sont informés qu’ils ne pourront pas se connecter à leur compte.

   Si une application ne prend pas en charge les fournisseurs d’identité multiples, les fournisseurs d’identité supplémentaires peuvent être configurés à l’avance. L’administrateur peut ensuite changer de fournisseur lors du basculement Azure.

   Si l’application prend en charge plusieurs fournisseurs d’identité, et que vous faites en sorte que plusieurs fournisseurs gèrent l’authentification en même temps, l’utilisateur peut choisir son fournisseur pour s’authentifier sur la page de connexion.

#### <a name="example-support-for-multiple-idps"></a>Exemple : prise en charge de plusieurs fournisseurs d’identité
Par exemple, dans Salesforce, la configuration du fournisseur d’identité se situe sous **Paramètres** > **Paramètres de la société** > **Mon domaine** > **Configuration de l’authentification**.

![Section « Configuration de l’authentification » de l’application Salesforce](media/migrate-adfs-apps-to-azure/migrate9.png)

En raison de la configuration créée plus tôt sous **Identité** > **Paramètres de l’authentification unique**, vous devez être en mesure de changer de fournisseur d’identité pour la configuration d’authentification. Par exemple, vous pouvez passer d’AD FS à Azure AD. 

![Sélectionner Azure AD en tant que service d’authentification](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Facultatif : configurer l’approvisionnement utilisateur dans Azure AD
Si vous souhaitez qu’Azure AD gère l’approvisionnement utilisateur pour une application SaaS, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-app-provisioning).

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Gérer l’accès aux applications](active-directory-managing-access-to-apps.md)
- [Fédération avec Azure AD Connect](active-directory-aadconnectfed-whatis.md)
