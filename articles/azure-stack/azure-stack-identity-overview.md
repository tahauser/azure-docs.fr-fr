---
title: Vue d’ensemble d’une identité pour Azure Stack | Microsoft Docs
description: En savoir plus sur les systèmes d’identité que vous pouvez utiliser avec Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: deebe5d8ff4c35c4507d2daf5c15025a1810d865
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Vue d’ensemble d’une identité pour Azure Stack

Azure Stack nécessite Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS), ainsi qu’Active Directory en tant que fournisseur d’identité. Le choix d’un fournisseur est une décision unique que vous prenez lorsque vous déployez Azure Stack pour la première fois. Les concepts et les détails d’autorisation de cet article peuvent vous aider à choisir un fournisseur d’identité. 

Votre choix entre Azure AD et AD FS peut être limité par le mode dans lequel vous déployez Azure Stack : 
- Lorsque vous le déployez en mode connecté, vous pouvez utiliser Azure AD ou AD FS. 
- Lorsque vous le déployez en mode déconnecté sans connexion internet, seul AD FS est pris en charge.

Pour plus d’informations sur ces options, qui dépendent de votre environnement Azure Stack, consultez les articles suivants :
- Kit de déploiement d’Azure Stack : [Considérations sur l’identité](azure-stack-datacenter-integration.md#identity-considerations).
- Systèmes intégrés Azure Stack : [Décisions relatives à la planification du déploiement pour les systèmes intégrés Azure Stack](azure-stack-deployment-decisions.md).

 
## <a name="common-concepts-for-identity"></a>Concepts courants pour l’identité
Les sections suivantes décrivent les concepts communs relatifs aux fournisseurs d’identité et à leur utilisation dans Azure Stack.

![Terminologie pour les fournisseurs d’identité](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Les locataires d’annuaire et les organisations
Un répertoire est un conteneur qui conserve des informations sur les *utilisateurs*, les *applications*, les *groupes*, et les *principaux de service*.
 
Un locataire d’annuaire est une *organisation*, comme Microsoft ou votre propre entreprise. 
- Azure AD prend en charge plusieurs locataires et peut prendre en charge plusieurs organisations, chacune dans son propre répertoire. Si vous utilisez Azure AD et que vous disposez de plusieurs locataires, vous pouvez accorder un accès à d’autres locataires du même répertoire pour des applications et des utilisateurs d’un autre locataire.
- AD FS ne prend en charge qu’un seul locataire, et donc une seule organisation. 

### <a name="users-and-groups"></a>Utilisateurs et groupes
Les comptes d’utilisateur (identités) sont des comptes standard qui authentifient des individus à l’aide d’un ID utilisateur et d’un mot de passe. Les groupes peuvent inclure des utilisateurs ou d’autres groupes. 

La façon de créer et de gérer les utilisateurs et les groupes dépend de la solution d’identité que vous utilisez. 

Dans Azure Stack, des comptes d’utilisateur : 
- Sont créés au format *username@domain*. Bien qu’AD FS mappe les comptes d’utilisateur à une instance Active Directory, AD FS ne prend pas en charge le format *\<domaine >\<alias >*. 
- Peuvent être configurés pour utiliser l’authentification multifacteur. 
- Sont limités au répertoire où ils se sont inscrits en premier lieu, qui est le répertoire de leur organisation.
- Peuvent être importés à partir de vos répertoires locaux. Pour obtenir plus d’informations, consultez [Intégrer vos répertoires locaux avec Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect). 

Lorsque vous vous connectez au portail du locataire de votre organisation, vous utilisez l’URL *https://portal.local.azurestack.external*. 

### <a name="guest-users"></a>Utilisateurs invités
Les utilisateurs invités sont des comptes d’utilisateur pour des locataires d’annuaire ayant reçu un accès aux ressources de votre répertoire. Pour prendre en charge les utilisateurs invités, vous devez utiliser Azure AD et activer la prise en charge d’architecture mutualisée. Lorsqu’elle est prise en charge, vous pouvez inviter un utilisateur invité à accéder aux ressources de votre locataire d’annuaire, ce qui permet la collaboration avec des organisations externes. 

Pour inviter des utilisateurs invités, les opérateurs cloud et les utilisateurs peuvent utiliser la [collaboration interentreprise Azure AD](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Les utilisateurs invités ont accès aux documents, aux ressources et aux applications à partir de votre répertoire et vous gardez le contrôle de vos propres ressources et données. 

En tant qu’utilisateur invité, vous pouvez vous connecter à un locataire d’annuaire d’autres organisations. Pour ce faire, vous devez ajouter le nom de répertoire de ces organisations à l’URL du portail. Par exemple, si vous appartenez à l’organisation Contoso et que vous souhaitez vous connecter à l’annuaire de Fabrikam, utilisez https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

### <a name="applications"></a>APPLICATIONS
Vous pouvez inscrire des applications à Azure AD ou AD FS et les offrir à des utilisateurs de votre organisation. 

Les applications incluent :
- **Application web** : le portail Azure et Azure Resource Manager sont des exemples. Elles prennent en charge les appels d’API web. 
- **Client natif** : Azure PowerShell, Visual Studio et Azure CLI sont des exemples.

Les applications peuvent prendre en charge deux types de location : 
- **Locataire unique** : ces applications prennent seulement en charge les utilisateurs et les services du répertoire où l’application est inscrite. 

  > [!NOTE]    
  > Étant donné qu’AD FS prend seulement en charge un répertoire unique, les applications que vous créez dans une topologie AD FS sont, par défaut, des applications à locataire unique.

- **Applications mutualisées** : peuvent être utilisées par les utilisateurs et les services du répertoire où elles sont enregistrées, mais également depuis d’autres répertoires de locataire. Avec les applications mutualisées, les utilisateurs d’un autre répertoire (un autre locataire Azure AD) peuvent se connecter à votre application. 

  Pour plus d’informations sur la mutualisation, consultez [Activer la mutualisation](azure-stack-enable-multitenancy.md). 

  Pour plus d’informations sur le développement d’une application multilocataire, consultez [Applications multilocataires](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Lorsque vous inscrivez une application, vous créez deux objets :

- **Objet d’application** : représentation globale de l’application sur tous les locataires. Cette relation est de type un à un avec l’application logicielle et existe uniquement dans le premier répertoire d’inscription de l’application.

- **Objet principal de service** : informations d’identification créées pour une application dans le premier répertoire d’inscription de l’application. Un principal de service est également créé dans le répertoire de chaque locataire supplémentaire où cette application est utilisée. Cette relation peut être du type un à plusieurs avec l’application. 

Pour en savoir plus sur les objets d’application et principal de service, consultez [Objets application et principal de service dans Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects). 

### <a name="service-principals"></a>Principaux de service 
Un principal de service est un ensemble d’*informations d’identification* pour une application ou un service qui accorde l’accès aux ressources dans Azure Stack. L’utilisation d’un principal de service sépare les autorisations de l’application et celles de l’utilisateur de l’application.

Un principal de service est créé dans chaque locataire où l’application est utilisée. Le principal de service établit une identité pour se connecter et accéder aux ressources (comme les utilisateurs) sécurisées par ce locataire. 

- Une application à locataire unique dispose d’un seul principal de service, dans le répertoire où elle a été créée. Ce principal de service est créé et donne son consentement à être utilisé lors de l’inscription de l’application. 
- Une application web ou une API multilocataire dispose également d’un principal de service créé dans chaque locataire où un utilisateur de ce locataire consent à utiliser l’application. 

Les informations d’identification pour les principaux de service peuvent être un certificat ou une clé générée via le portail Azure. L’utilisation d’un certificat est adaptée à l’automatisation, car les certificats sont considérés comme plus sûrs que des clés. 


> [!NOTE]    
> Lorsque vous utilisez AD FS avec Azure Stack, seul l’administrateur peut créer des principaux de service. Avec AD FS, les principaux de service nécessitent des certificats et sont créés via le point de terminaison privilégié (PEP). Pour plus d’informations, consultez [Fournir l’accès des applications à Azure Stack](azure-stack-create-service-principals.md).

Pour en savoir plus sur les principaux de service pour Azure Stack, consultez [Créer des principaux de service](azure-stack-create-service-principals.md).


### <a name="services"></a>Services
Dans Azure Stack, les services qui interagissent avec le fournisseur d’identité sont enregistrés comme des applications avec le fournisseur d’identité. Comme les applications, l’inscription permet l’authentification par un service avec le système d’identité. 

Tous les services Azure utilisent les protocoles [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) et [JSON Web Tokens](/azure/active-directory/develop/active-directory-token-and-claims) pour établir leur identité. Étant donné qu’Azure AD et AD FS utilisent les protocoles d’une façon cohérente, vous pouvez utiliser la [bibliothèque d’authentification Active Directory Azure](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) pour l’authentification locale ou sur Azure (dans un scénario connecté). La bibliothèque ADAL vous permet également d’utiliser des outils comme Azure PowerShell et Azure CLI pour la gestion des ressources locales et entre clouds. 


### <a name="identities-and-your-identity-system"></a>Les identités et votre système d’identité 
Les identités pour Azure Stack comprennent les comptes d’utilisateurs, les groupes et les principaux de service. 

Lorsque vous installez Azure Stack, plusieurs services et applications intégrés s’inscrivent automatiquement avec votre fournisseur d’identité dans le locataire d’annuaire. Certains services inscrits sont utilisés pour l’administration. D’autres services sont disponibles pour les utilisateurs. Les inscriptions par défaut affichent les identités des services de base qui peuvent interagir entre eux et avec des identités ajoutées plus tard.

Si vous configurez Azure AD avec une mutualisation, certaines applications se propagent vers les nouveaux répertoires. 

## <a name="authentication-and-authorization"></a>Authentification et autorisation
 

### <a name="authentication-by-applications-and-users"></a>Authentification par les applications et les utilisateurs
  
![Identité entre les couches de Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Pour les applications et les utilisateurs, l’architecture de Azure Stack est décrite par quatre couches. Les interactions entre chacune de ces couches peuvent utiliser différents types d’authentification.


|Couche    |Authentification entre les couches  |
|---------|---------|
|Outils et clients, comme le portail d’administration     | Pour accéder ou modifier une ressource dans Azure Stack, les outils et les clients utilisent un [JSON Web Token](/azure/active-directory/develop/active-directory-token-and-claims) pour passer un appel à Azure Resource Manager. <br>Azure Resource Manager valide le JSON Web Token et lit furtivement les *revendications* dans le jeton émis pour estimer le niveau d’autorisation de l’utilisateur ou du principal de service dans Azure Stack. |
|Azure Resource Manager et ses services de base     |Azure Resource Manager communique avec les fournisseurs de ressources pour transférer les communications des utilisateurs. <br> Les transferts utilisent des appels *impératifs directs* ou des appels *déclaratifs* via des [modèles Azure Resource Manager](/azure/azure-stack/user/azure-stack-arm-templates.md).|
|Fournisseurs de ressources     |Les appels passés à des fournisseurs de ressources sont sécurisés avec l’authentification par certificat. <br>Azure Resource Manager et le fournisseur de ressources restent ensuite en communication via une API. Pour chaque appel reçu depuis Azure Resource Manager, le fournisseur de ressources valide l’appel avec ce certificat.|
|Infrastructure et logique métier     |Les fournisseurs de ressources communiquent avec une logique métier et une infrastructure à l’aide du mode d’authentification de leur choix. Les fournisseurs de ressources par défaut fournis avec Azure Stack utilisent l’authentification Windows pour sécuriser cette communication.|

![Informations requises pour l’authentification](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>S’authentifier à Azure Resource Manager
Pour s’authentifier auprès du fournisseur d’identité et recevoir un JSON Web Token, vous devez disposer des informations suivantes : 
1.  **URL pour le système d’identité (autorité)** : l’URL à laquelle votre fournisseur d’identité peut être contacté. Par exemple : *https://login.windows.net*. 
2.  **URI ID d’application pour Azure Resource Manager** : l’identificateur unique pour Azure Resource Manager enregistré avec votre fournisseur d’identité. Il est également unique pour chaque installation Azure Stack.
3.  **Informations d’identification** : informations d’identification utilisées pour l’authentification avec le fournisseur d’identité. 
4.  **URL pour Azure Resource Manager** : l’URL est l’emplacement du service Azure Resource Manager. Par exemple *https://management.azure.com* ou *https://management.local.azurestack.external*.

Lorsqu’un principal (un client, une application ou un utilisateur) effectue une demande d’authentification pour accéder à une ressource, cette demande doit inclure :
- Les informations d’identification du principal.
- L’URI de l’ID d’application de la ressource à laquelle le principal demande l’accès. 

Les informations d’identification sont validées par le fournisseur d’identité. Le fournisseur d’identité valide également que l’URI ID d’application concerne une application inscrite, et que le principal possède les privilèges corrects pour obtenir un jeton pour cette ressource. Si la demande est valide, un JWT est accordé. 

Le jeton doit passer dans l’en-tête d’une demande à Azure Resource Manager. Azure Resource Manager effectue les actions suivantes sans ordre spécifique :
- Valide la revendication *issuer* (iss) pour confirmer que le jeton vient du fournisseur d’identité correct. 
- Valides la revendication *audience* (aud) pour confirmer que le jeton a été émis pour Azure Resource Manager. 
- Vérifie que le JSON Web Token est signé avec un certificat configuré via OpenID connu d’Azure Resource Manager. 
- Examine les revendications *issued at* (iat) et *expiration* (exp) pour confirmer que le jeton est actif et peut être accepté. 

Une fois toutes les validations terminées, Azure Resource Manager utilise les revendications *objected* (oid) et *groups* pour dresser la liste des ressources auxquelles le principal peut accéder. 

![Diagramme du protocole d’échange de jeton](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>Utiliser le contrôle d’accès en fonction du rôle  
Le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack est cohérent avec la mise en œuvre dans Microsoft Azure. Vous pouvez gérer l’accès aux ressources en assignant le rôle RBAC approprié aux utilisateurs, groupes et applications. Pour plus d’informations sur l’utilisation de RBAC avec Azure Stack, consultez les articles suivants :
- [Découvrir le contrôle d’accès en fonction du rôle dans le portail Azure](/azure/active-directory/role-based-access-control-what-is).
- [Utiliser le contrôle d’accès en fonction du rôle pour gérer l’accès aux ressources d’un abonnement Azure](/azure/active-directory/role-based-access-control-configure).
- [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](/azure/active-directory/role-based-access-control-custom-roles).
- [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md) dans Azure Stack.


### <a name="authenticate-with-azure-powershell"></a>S’authentifier avec Azure PowerShell  
Vous trouverez des détails sur l’utilisation de Azure PowerShell pour s’authentifier auprès de Azure Stack dans l’article [Configurer l’environnement PowerShell de l’utilisateur de Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>S’authentifier avec Azure CLI
Pour en savoir plus sur l’utilisation d’Azure PowerShell pour s’authentifier auprès d’Azure Stack, consultez l’article [Installer et configurer Azure CLI pour l’utiliser avec Azure Stack](/azure/azure-stack/user/azure-stack-connect-cli.md).

## <a name="next-steps"></a>Étapes suivantes
- [Architecture d’identités](azure-stack-identity-architecture.md)   
- [Intégration au centre de données - Identité](azure-stack-integrate-identity.md)




