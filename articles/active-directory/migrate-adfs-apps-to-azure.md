---
title: "Migrer des applications locales AD FS vers Azure. | Microsoft Docs"
description: "Ce document a pour but d’aider les organisations à comprendre comment migrer des applications locales vers Azure AD, et se concentre sur les applications SaaS fédérées."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/29/2018
ms.author: billmath
ms.openlocfilehash: ec0731534da2543d48bedc575bf882b790fa136b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrer des applications locales AD FS vers Azure 

Ce document a pour but d’aider les organisations à comprendre comment migrer des applications locales vers Azure AD.  Il se concentre sur les applications SaaS fédérées.  Il ne constitue pas un guide pas à pas.  Il fournit des conseils conceptuels pour vous aider à réussir la migration, en comprenant comment se traduisent les configurations locales dans Azure AD. Il couvre aussi ce que nécessitent la plupart des scénarios.

## <a name="introduction"></a>Introduction

Si vous disposez d’un répertoire local contenant des comptes utilisateur, il est probable que vous disposiez d’au moins un ou deux applications.  Ces dernières sont configurées pour que les utilisateurs y accèdent en s’authentifiant avec ces identités.

Et si votre organisation est similaire aux autres, vous êtes probablement sur le point d’adopter des applications et des identités cloud.  Vous maîtrisez peut-être Office 365 et Azure AD Connect.  Vous avez peut-être configuré des applications SaaS basées sur le cloud pour quelques charges de travail clé.  

De nombreuses organisations disposent d’applications SaaS ou métiers personnalisées (LoB), fédérées directement dans un service d’authentification local tel qu’Active Directory Federation Service (AD FS), et les applications Office 365 et Azure AD.  Ce guide de migration décrit pourquoi et comment migrer des applications locales vers Azure AD.

>[!NOTE]
>Il fournit des informations détaillées sur la configuration et la migration d’applications SaaS, dont des informations de niveau supérieur sur les applications métiers personnalisées.  Nous prévoyons d’ajouter davantage de conseils détaillés sur les applications métiers personnalisées.

Figure 1 : Applications directement connectées localement ![locales](media/migrate-adfs-apps-to-azure/migrate1.png)

Figure 2 : Applications fédérées via Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>Pourquoi migrer des applications vers Azure AD ?

Pour une organisation qui utilise déjà AD FS, Ping ou un autre fournisseur d’authentification locale, la migration d’applications vers Azure AD permet les avantages suivants :

**Accès plus sécurisé**
- Configurez des contrôles d’accès granulaires par application, incluant l’authentification multifacteur (MFA), avec l’[Accès conditionnel Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).  Les stratégies peuvent être appliquées à des applications SaaS et personnalisées de la même façon que sur Office 365, si vous l’utilisez
- Pour détecter des menaces et protéger l’authentification basée sur le Machine Learning et les heuristiques qui identifient le trafic à risque, profitez des capacités intégrées et en constante évolution de Azure AD avec [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

**Azure AD B2B Collaboration**
- Une fois que l’authentification aux applications SaaS est basée sur Azure AD, vous pouvez donner l’accès à vos partenaires aux ressources cloud avec [Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Expérience administrateur plus facile et capacités supplémentaires d’Azure AD**
- En tant que fournisseur d’identité pour applications SaaS, Azure AD prend en charge des capacités supplémentaires telles que les certificats de signature de jetons par application, les [dates d’expiration de certificat configurable](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs), et l’[approvisionnement automatique](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) de comptes utilisateur (dans des applications de galerie de clé) basés sur des identités Azure AD

**Conservez les avantages d’un fournisseur d’identité local**
- Tout en profitant des avantages Azure AD, vous pouvez continuer à utiliser votre solution locale pour l’authentification. Vous conservez ainsi des avantages tels que les solutions d’authentification multifacteur (MFA), la journalisation ou l’audit 

**Découvrir rapidement comment mettre hors service les fournisseurs d’identité locaux**
- Pour les organisations qui souhaitent mettre hors service le produit d’authentification local, la migration d’applications vers Azure AD permet une transition plus facile en prenant en charge une partie des opérations 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mappage de types d’applications locales à des types d’applications dans Azure AD
La plupart des applications entrent dans l’une des quelques catégories en fonction du type d’authentification qu’elles utilisent.  Ces catégories déterminent comment l’application est représentée dans Azure AD.

En bref, les applications SAML 2.0 peuvent être intégrées à Azure AD soit via la galerie d’applications Azure AD, soit comme des applications ne figurant pas dans la galerie.  Les applications qui utilisent OAuth 2.0 ou OpenID Connect peuvent être intégrées à Azure AD de façon similaire en tant qu’« inscriptions d’application ».  Lisez la suite pour plus de détails.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Applications SaaS fédérées et applications métiers personnalisées
Les applications fédérées comprennent les applications incluses dans les catégories listées.

- Applications SaaS 
    - Si vos utilisateurs s’authentifient dans des applications telles que Salesforce, ServiceNow ou Workday, et que vous effectuez une intégration à un fournisseur d’identité local tel qu’AD FS ou Ping, vous utilisez une authentification fédérée pour des applications SaaS.
    - Les applications utilisent en général le protocole SAML 2.0 pour une authentification fédérée.
    - Les applications comprises dans cette catégorie peuvent être intégrées à Azure AD comme des applications d’entreprise, depuis la galerie ou en tant qu’applications ne figurant pas dans la galerie.
- Applications métiers personnalisées
    - Cette catégorie regroupe les applications non SaaS, développées en interne par votre organisation ou disponibles en tant que produit groupé standard installé dans votre centre de données.  Sont incluses les applications SharePoint et les applications créées dans Windows Identity Foundation (WIF).
    - Ces applications peuvent utiliser le protocole SAML 2.0, WS-Federation, OAuth ou OpenID Connect pour une authentification fédérée
    - Les applications personnalisées qui utilisent OAuth 2.0, OpenID Connect ou WS-Federation peuvent être intégrées à Azure AD en tant qu’« inscriptions des applications », et celles qui utilisent le protocole SAML 2.0 ou WS-Federation peuvent être intégrées comme des applications ne figurant pas dans la galerie au sein d’applications d’entreprise

### <a name="non-federated-apps"></a>Applications non fédérées
En outre, les applications non fédérées peuvent être intégrées à Azure AD à l’aide du proxy d’application Azure AD et des capacités associées.  Pour en savoir plus sur les capacités, suivez ces liens :
- Applications utilisant l’Authentification intégrée à Windows (WIA) directement dans Active Directory
    - Ces applications peuvent être intégrées à Azure AD via le [proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)
- Applications s’intégrant à votre fournisseur d’authentification unique via un agent et utilisant des en-têtes pour l’autorisation
    - Les applications locales qui utilisent un agent installé pour l’authentification et une autorisation basée sur l’en-tête peuvent être configurées pour une authentification basée sur Azure AD à l’aide du proxy d’application Azure AD avec [Ping Access pour Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Traduire des applications fédérées locales dans Azure AD 
Heureusement, AD FS et Azure AD fonctionnent de la même manière. La configuration de la confiance, des URL et des identificateurs d’authentification et de déconnexion s’appliquent pour les deux.  Cependant, il y a des différences qu’il faut connaître au moment de la transition.

Pour vous aider à traduire, nous avons mappé dans le tableau plusieurs idées clé partagées entre les applications AD FS, Azure AD et SaaS. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Représentation de l’application dans Azure AD ou AD FS
La migration commence par l’évaluation de la configuration locale de l’application, et par le mappage de cette configuration dans Azure AD.  Ci-après, des éléments de configuration d’une partie de confiance AD FS mappés aux éléments correspondants dans Azure AD.  
- Terme AD FS : partie de confiance ou approbation de partie de confiance
- Terme Azure AD : application d’entreprise ou inscription d’application (selon le type d’application)

|Élément de configuration de l’application|Description|Configuration au sein d’AD FS|Emplacement correspondant dans la configuration d’Azure AD|Élément de jeton SAML|
|-----|-----|-----|-----|-----|
|URL d’authentification à une application|URL de la page de connexion de cette application. C’est ici que l’utilisateur entame sa connexion à un flux SAML initié du fournisseur de services.|N/A|Dans Azure AD, l’URL d’authentification est configurée au sein du portail Azure dans les propriétés d’authentification unique de l’application en tant qu’URL d’authentification.</br></br>(Il vous faudra peut-être cliquer sur Afficher paramètres URL avancés pour voir l’URL d’authentification)||
|URL de réponse de l’application|URL de l’application du point de vue du fournisseur d’identité.  C’est ici que l’utilisateur et le jeton sont envoyés lorsque l’utilisateur s’est authentifié auprès du fournisseur d’identité.</br></br>  Parfois appelé point de terminaison consommateur d’assertion SAML.|À trouver dans l’approbation de partie de confiance AD FS pour l’application.  Cliquez avec le bouton droit de la souris sur la partie de confiance et choisissez « Propriétés » puis l’onglet « Points de terminaison ».|Dans Azure AD, l’URL de réponse est configurée au sein du portail Azure dans les propriétés d’authentification unique de l’application en tant qu’URL de réponse.</br></br>(Il vous faudra peut-être cliquer sur Afficher paramètres URL avancés pour voir l’URL de réponse)|Cartes vers l’élément de destination dans le jeton SAML.</br></br>  Valeur d’exemple :
https://contoso.my.salesforce.com|
|URL de déconnexion de l’application|L’URL à laquelle sont envoyées les requêtes de « nettoyage de déconnexion » lorsqu’un utilisateur se déconnecte d’une application, afin de déconnecter toutes les applications dans lesquelles l’utilisateur avait été authentifié par le fournisseur d’identité.|À trouver dans la gestion AD FS sous : Approbations de partie de confiance.  Cliquez avec le bouton droit de la souris sur la partie de confiance et choisissez « Propriétés » puis l’onglet « Points de terminaison »|N/D Azure AD ne prend pas en charge la fonction « Single Logout », ce qui veut dire déconnexion de toutes les applications.  Il déconnecte simplement l’utilisateur d’Azure AD.|N/D|
|Identificateur de l’application|L’identificateur de l’application du point de vue du fournisseur d’identité. La valeur de l’URL d’authentification est souvent utilisée pour l’identificateur (mais pas toujours)</br></br>  L’application l’appelle parfois l’« ID d’entité ».|Dans AD FS, il s’agit de l’ID de la partie de confiance : Cliquez avec le bouton droit de la souris sur l’approbation de partie de confiance et sélectionnez « Propriétés » puis l’onglet «Identificateurs »|Dans Azure AD, l’identificateur est configuré au sein du portail Azure dans les propriétés d’authentification unique de l’application en tant qu’identificateur sous Domaine et URL (il vous faudra peut-être cliquer sur Afficher paramètres URL avancés)|Correspond à l’élément Audience du jeton SAML|
|Métadonnées de fédération de l’application|Emplacement des métadonnées de fédération de l’application.  Utilisé par les fournisseurs d’identité pour mettre à jour automatiquement des paramètres de configuration spécifiques tels que les points de terminaison ou les certificats de chiffrement.|L’URL des métadonnées de fédération de l’application se trouve dans l’approbation de partie de confiance AD FS de l’application.  Cliquez avec le bouton droit sur l’approbation et sélectionnez Propriétés puis cliquez sur l’onglet Analyse.|N/D Azure AD ne prend pas en charge la consommation directe de métadonnées de fédération de l’application|N/D|
|Identificateur d’utilisateur / NameID|Attribut utilisé pour indiquer uniquement l’utilisateur d’Azure AD ou AD FS vers votre application.</br></br>  Il s’agit en général de l’UPN ou de l’adresse de messagerie de l’utilisateur.|Dans AD FS, il se trouve sur la partie de confiance en tant que règle de revendication.  Dans la plupart des cas, il s’agit d’une règle de revendication qui génère une revendication dont le type se termine par « nameidentifier »|Dans Azure AD, l’identificateur d’utilisateur peut être trouvé au sein du portail Azure dans les propriétés d’authentification unique de l’application, sous l’en-tête Attributs utilisateur.</br></br>Par défaut, c’est le nom d’utilisateur principal qui est utilisé.|Il est communiqué à l’application par le fournisseur d’identité comme « NameID » dans le jeton SAML.|
|Autres revendications envoyées à l’application|En plus de l’identificateur utilisateur / NameID, d’autres informations de revendication sont généralement envoyées depuis le fournisseur d’identité vers l’application. Par exemple : prénom, nom de famille, adresse et groupes dont l’utilisateur fait partie|Dans AD FS, les autres règles de revendications se trouvent dans la partie de confiance.|Dans Azure AD, elles se trouvent au sein du portail Azure dans les propriétés d’authentification unique sous l’en-tête Attributs utilisateur. Cliquez sur Afficher et modifiez tous les autres attributs utilisateur.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Représentation d’Azure AD en tant que fournisseur d’identité (IdP) dans une application SaaS
Dans le cadre d’une migration, l’application doit être configurée pour pointer vers Azure AD (contre le fournisseur d’identité local).  Cette section se concentre principalement sur les applications SaaS qui utilisent le protocole SAML, pas sur les applications métier personnalisées. Toutefois, les concepts qui y sont abordés peuvent s’applique à ces dernières. 

À un niveau élevé, il y a quelques points importants à savoir pour pointer une application SaaS vers Azure AD
- Émetteur de fournisseur d’identité :  https&#58;//sts.windows.net/{tenant-id}/
- URL de connexion du fournisseur d’identité : https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- URL de déconnexion du fournisseur d’identité : https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- Emplacement des métadonnées de fédération :  https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

où {tenant-id} est remplacé par votre ID d’abonné, située dans le portail Azure sous Azure Active Directory -> Propriétés -> « ID Répertoire ». {application-id} est remplacé par l’ID de votre application, située dans les Propriétés de l’application, « ID Application »

Le tableau décrit plus en détail les éléments clé à la configuration des paramètres d’authentification unique dans l’application, ainsi que leurs valeurs et emplacements au sein d’AD FS et Azure AD.  Le cadre de référence de ce tableau correspond à l’application SaaS, qui doit savoir où envoyer les requêtes d’authentification et comment valider les jetons ainsi reçus.

|Élément de configuration|Description|AD FS|Azure AD|
|---|---|---|---|
|Fournisseur d’identité (IdP) </br>URL de connexion </br>URL|URL de connexion du fournisseur d’identité depuis l’application (où l’utilisateur est redirigé pour s’identifier).|L’URL de connexion AD FS correspond au nom de service de fédération AD FS, suivi par « /adfs/ls/ ». Par exemple, https&#58;//fs.contoso.com/adfs/ls/|La valeur correspondante pour Azure AD suit le même schéma, où {tenant-id} est remplacé par votre ID d’abonné, située dans le portail Azure sous Azure Active Directory -> Propriétés -> « ID Répertoire ».</br></br>Pour les applications qui utilisent le protocole SAML-P : https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>Pour les applications qui utilisent le protocole WS-Federation : https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|Fournisseur d’identité (IdP) </br>URL de déconnexion </br>URL|URL de déconnexion du fournisseur d’identité depuis l’application (où l’utilisateur est redirigé lorsqu’il choisit de se déconnecter de l’application).|Pour AD FS, l’URL de déconnexion correspond soit à l’URL de connexion, soit à l’URL de connexion suivie de « wa=wsignout1.0 ». Par exemple, https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0|La valeur correspondante pour Azure AD dépend de la prise en charge d’une déconnexion SAML 2.0 par l’application.</br></br>Si l’application prend en charge la déconnexion SAML, la valeur correspond au schéma où la valeur de {tenant-id} est remplacée par l’ID d’abonné, située dans le portail Azure sous Azure Active Directory -> Propriétés -> « ID Répertoire ». https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>Si l’application ne prend pas en charge la déconnexion SAML : https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|par jeton </br>Signature </br>Certificat|Certificat dont la clé privée qu’utilise le fournisseur d’identité pour se connecter a émis des jetons.  Il vérifie que le jeton provient du fournisseur d’identité auquel l’application fait confiance.|Le certificat de signature de jetons AD FS se situe dans Gestion AD FS sous Certificats.|Dans Azure AD, le certificat de signature de jetons se situe au sein du portail Azure dans les Propriétés d’authentification unique de l’application, sous l’en-tête Certificat de signature SAML. Vous pouvez y télécharger le certificat pour le charger dans l’application.</br></br>  Si l’application possède plus d’un certificat, tous peuvent alors être trouvés dans le fichier XML des métadonnées de fédération.|
|Identificateur / </br>« Émetteur »|Identificateur du fournisseur d’identité depuis l’application (parfois appelé « émetteur » ou « ID émetteur »)</br></br>Dans le jeton SAML, la valeur apparaît comme l’élément « émetteur »|L’identificateur pour AD FS correspond généralement à l’identificateur du service FS dans Gestion AD FS sous Service -> Modifier propriétés du service FS.  Par exemple : http&#58;//fs.contoso.com/adfs/services/trust|La valeur correspondante pour Azure AD suit le même schéma, où la valeur {tenant-id} est remplacée par votre ID d’abonné, située dans le portail Azure sous Azure Active Directory -> Propriétés -> « ID Répertoire ».  https&#58;//sts.windows.net/{tenant-id}/|
|Fournisseur d’identité (IdP) </br>Fédération </br>Métadonnées|Emplacement des métadonnées de fédération du fournisseur d’identité disponibles au public.  (Les métadonnées de fédération sont utilisées par quelques applications comme une alternative à l’administrateur pour configurer individuellement les URL, l’identificateur et le certificat de signature de jetons)|Trouvez l’URL de métadonnées de fédération AD FS dans Gestion AD FS sous Service -> Points de terminaison -> Métadonnées -> Type : Métadonnées de fédération. Par exemple : https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|La valeur correspondante pour Azure suit le modèle https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml où la valeur {TenantDomainName} est remplacée par votre nom d’abonné au format « contoso.onmicrosoft.com » </br></br>[En savoir plus](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) sur les métadonnées de fédération dans Azure AD.

## <a name="migrating-saas-apps"></a>Migrer les applications SaaS
La migration d’application SaaS depuis AD FS ou un autre fournisseur d’identité vers Azure AD est aujourd’hui un processus manuel. Pour des conseils spécifiques à une application, [consulter la liste des didacticiels sur l’intégration des applications SaaS situées dans la Galerie](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Les didacticiels sur l’intégration partent du principe que vous faites une intégration en mode champs vert.  Il existe quelques principes fondamentaux à savoir concernant la migration si vous planifiez, évaluez, configurez et basculez vos applications.  
- Tandis que certaines applications migrent facilement, d’autres plus complexes telles que les revendications personnalisées nécessitent une configuration supplémentaire dans Azure AD et/ou Azure AD Connect
- Le plus couramment, seule la revendication NameId et d’autres revendications communes d’identificateur d’utilisateur sont nécessaires à une application. Pour déterminer s’il faut des revendications supplémentaires, examinez celles que vous émettez depuis AD FS ou depuis votre fournisseur d’identité tiers
- Une fois les revendications supplémentaires nécessaires déterminées, vous devez vous assurer de leur disponibilité dans Azure AD.  Vous devez vérifier la configuration de synchronisation d’Azure AD Connect afin de garantir qu’un attribut requis (par exemple samAccountName) est bien synchronisé avec Azure AD
- Une fois que les attributs sont disponibles dans Azure AD, ajoutez des règles d’émission de revendications dans Azure AD pour inclure ces attributs en tant que revendications dans des jetons émis.  Effectué au sein des propriétés d’authentification unique de l’application dans Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Évaluer ce qui peut être migré
Les applications SAML 2.0 peuvent être intégrées à Azure AD soit via la galerie d’applications Azure AD, soit comme des applications ne figurant pas dans la galerie.  

Des étapes supplémentaires sont nécessaires pour certaines configurations dans Azure AD, dont certaines ne sont actuellement pas prises en charge.  Pour déterminer ce qui peut être déplacé, consultez la configuration actuelle de chaque application, en particulier :
- Les règles de revendication configurées (règles de transformation d’émission)
- Format et attribut de NameID SAML
- Versions des jetons SAML émis
- Autres configurations telles que les règles d’autorisation d’émission, les stratégies de contrôle d’accès ou les règles d’authentification multifacteur (authentification supplémentaire)

#### <a name="what-can-be-migrated-today"></a>Ce qui est peut être migré actuellement
Actuellement, les applications pouvant être migrées facilement sont les applications SAML 2.0 qui utilisent l’ensemble standard d’éléments de configuration et de revendications.  Il peut s’agir
- du nom d’utilisateur principal
- de l’adresse de messagerie
- Prénom
- Surname
- d’un autre attribut comme NameID de SAML, y compris l’attribut de messagerie Azure AD, le préfixe de messagerie, l’attribut employeeid, les attributs d’extension 1 - 15, ou l’attribut local SamAccountName (voir [Modifier la revendication NameIdentifier](./develop/active-directory-saml-claims-customization.md))
- des revendications personnalisées (voir les documents [ici](active-directory-claims-mapping.md) et [là](./develop/active-directory-saml-claims-customization.md) pour en savoir plus sur les mappages de revendications pris en charge)

En plus des revendications personnalisées et des éléments nameID, les configurations qui nécessitent des étapes supplémentaires dans Azure AD dans le cadre de la migration sont :
- Les règles d’autorisation personnalisée ou les règles d’authentification multifacteur dans AD FS (configurées avec la fonctionnalité d’[accès conditionnel Azure AD](active-directory-conditional-access-azure-portal.md))
- Les applications avec des points de terminaison SAML multiples peuvent être configurées dans Azure AD avec PowerShell (cette fonctionnalité n’est pas disponible dans le portail)
- Les applications WS-Federation comme les applications SharePoint qui nécessitent des jetons de version SAML 1.1 doivent être configurées manuellement avec PowerShell

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Applications/configuration non prises en charge dans Azure AD actuellement
Les applications qui nécessitent les fonctionnalités suivantes ne peuvent actuellement pas être migrées.  Si vous disposez d’applications qui nécessitent ces fonctionnalités, faites-nous en part dans la section des commentaires.
- Fonctionnalités de protocole
    - Prise en charge de la déconnexion unique (Single Logout) SAML pour toutes les applications connectées
    - Prise en charge du modèle WS-Trust ActAs
    - Résolution d’artefacts SAML 
    - Vérification de signature des requêtes SAML signées (Note : les requêtes signées sont acceptées mais la signature n’est pas vérifiée)
 - Fonctionnalités des jetons 
     - Chiffrement des jetons SAML 
     - Jetons de version SAML 1.1 au sein des réponses du protocole SAML 
- Revendications dans les fonctionnalités des jetons
    - Émission des noms de groupe locaux en tant que revendications
    - Revendications depuis des magasins autres que Azure AD
    - Règles de transformation d’émission de revendications complexes (voir ce document et celui-ci pour en savoir plus sur les mappages de revendications pris en charge)
    - Émission d’extensions de répertoire en tant que revendications
    - Spécification personnalisée de format NameID
    - Émission d’attributs à valeur multiple

>[!NOTE]
>Azure AD évolue constamment afin d’ajouter de nouvelles fonctionnalités dans ce domaine. Nous mettons régulièrement ce document à jour. 

### <a name="configuring-azure-ad"></a>Configuration d’Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Configurer les paramètres d’authentification unique pour l’application SaaS

Dans Azure AD, la configuration de l’authentification SAML requise par votre application est effectuée au sein des propriétés d’authentification unique de l’application sous Attributs utilisateur, comme indiqué ci-dessous :

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Cliquez sur « Afficher et modifier autre attribut » pour voir les attributs à envoyer en tant que revendications dans le jeton de sécurité

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Cliquez sur une ligne d’attribut spécifique pour modifier ou sur « Ajouter attribut » pour ajouter un nouvel attribut. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Assigner des utilisateurs à l’application
Pour que vos utilisateurs au sein d’Azure AD soient en mesure de se connecter à une application donnée, il faut leur donner l’accès depuis Azure AD.

Pour assigner des utilisateurs dans le portail Azure AD, naviguez jusqu’à l’écran de l’application SaaS au sein du portail, puis cliquez sur « Utilisateurs et groupes » dans la barre latérale. Pour ajouter un utilisateur ou un groupe, cliquez sur « Ajouter utilisateur » en haut de l’écran. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png)Pour vérifier l’accès, l’utilisateur doit voir l’application SaaS en question dans son [panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) lorsqu’il se connecte, qui se trouve à l’adresse http://myapps.microsoft.com. Par exemple, l’utilisateur a obtenu l’accès à Salesforce et ServiceNow.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Configuration de l’application SaaS
Le processus de basculement depuis la fédération locale vers Azure AD ne peut se faire que si l’application SaaS sur laquelle vous travaillez prend en charge plusieurs fournisseurs d’identité.  Voici des questions fréquentes concernant la prise en charge de fournisseurs d’identité multiples :

   **Q : Qu’est-ce que la prise en charge de fournisseurs d’identité multiples pour une application ?**
    
   R : Les applications SaaS qui prennent en charge plusieurs fournisseurs d’identité vous permet d’entrer toutes les informations du nouveau fournisseur (ici, Azure AD) avant de s’engager à changer l’expérience d’authentification.  Une fois la configuration terminée, vous pouvez changer la configuration d’authentification de l’application pour qu’elle pointe vers Azure AD.

   Q : En quoi la prise en charge de plusieurs fournisseurs d’identité est importante ?

   R : Si l’application ne prend pas en charge plusieurs fournisseurs d’application, l’administrateur devra prévoir un temps de service ou de maintenance pour configurer Azure AD comme nouveau fournisseur d’identité de l’application. Pendant cette maintenance, les utilisateurs sont informés qu’ils ne pourront pas se connecter à leur compte.

   Si une application ne prend pas en charge plusieurs fournisseurs d’identité, la configuration de fournisseurs supplémentaires peut être faite en avance, de sorte que l’administrateur puisse changer simplement de fournisseur lors du basculement Azure.

   De plus, si une application prend en charge plusieurs fournisseurs d’identité, et que vous faites en sorte que plusieurs fournisseurs gèrent l’authentification en même temps, l’utilisateur peut choisir son fournisseur pour s’authentifier sur la page de connexion.

#### <a name="example-multiple-idp-support"></a>Exemple : prise en charge de fournisseurs d’identité multiples
Par exemple, dans Salesforce, la configuration du fournisseur d’identité se situe sous Paramètres -> Paramètres de la société -> Mon domaine -> Configuration de l’authentification.

![](media/migrate-adfs-apps-to-azure/migrate9.png)

En raison de la configuration créée plus tôt sous Identité -> Paramètres de l’authentification unique, vous devez être en mesure de changer de fournisseur d’identité pour la configuration d’authentification (AD FS par Azure AD, par exemple). 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Facultatif : configurer l’approvisionnement utilisateur dans Azure AD
Éventuellement, si vous préfériez qu’Azure AD gère directement l’approvisionnement utilisateur pour une application SaaS donnée, consultez ce document sur la gestion de l’approvisionnement de compte utilisateur pour des applications d’entreprise dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Gérer l’accès aux applications](active-directory-managing-access-to-apps.md)
- [Azure AD Connect Federation](active-directory-aadconnectfed-whatis.md)