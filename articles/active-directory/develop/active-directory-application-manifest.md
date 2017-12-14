---
title: "Connaître le manifeste d’application Azure Active Directory | Microsoft Docs"
description: "Présentation détaillée de l’utilisation du manifeste d’application Azure Active Directory, qui représente la configuration d’identité d’une application dans un locataire Azure AD et permet de faciliter l’autorisation OAuth, le consentement et bien plus encore."
services: active-directory
documentationcenter: 
author: sureshja
manager: mtillman
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: c92631323040f9be015d3824b9803cdde95d874b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Connaître le manifeste d’application Azure Active Directory
Les applications qui s’intègrent à Azure Active Directory (AD) doivent être inscrites auprès d’Azure AD, fournissant une configuration permanente d’identité pour l’application. Cette configuration est consultée lors de l’exécution, activant des scénarios qui permettent à une application d’externaliser et d’assurer le courtage de l’authentification/autorisation via Azure AD. Pour plus d’informations sur le modèle d’application Azure AD, consultez l’article [Ajout, mise à jour et suppression d’une application][ADD-UPD-RMV-APP].

## <a name="updating-an-applications-identity-configuration"></a>Mise à jour d’une configuration de l’identité d’une application
Il existe en fait plusieurs options pour mettre à jour les propriétés de configuration de l’identité d’une application dont les capacités et le degré de difficulté varient, notamment les suivantes :

* **[L’interface utilisateur web][AZURE-PORTAL] du portail Azure** vous permet de mettre à jour les propriétés les plus courantes d’une application. Il s’agit de la méthode la plus rapide et la plus susceptible de mettre à jour les propriétés de votre application, mais elle ne pas vous donne un accès complet à toutes les propriétés, au même titre que les deux méthodes suivantes.
* Pour des scénarios plus avancés nécessitant la mise à jour de propriétés qui ne sont pas exposées dans le portail Azure Classic, vous pouvez modifier le **manifeste d’application**. C’est le sujet qui est abordé dans cet article et est décrit en détail à partir de la section suivante.
* Il est également possible **d’écrire une application qui utilise [l’API Graph][GRAPH-API]** pour mettre à jour votre application, ce qui demande le plus d’effort. Cette option peut être malgré tout intéressante, si vous écrivez des logiciels de gestion ou si vous devez régulièrement mettre à jour les propriétés de l’application de manière automatique.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Utilisation du manifeste d’application pour la mise à jour de la configuration de l’identité d’une application
Via le [portail Azure][AZURE-PORTAL], vous pouvez gérer la configuration de l’identité de votre application en mettant à jour le manifeste d’application à l’aide de l’éditeur de manifeste en ligne. Vous pouvez également télécharger et charger le manifeste d’application en tant que fichier JSON. Aucun fichier réel n’est stocké dans le répertoire. Le manifeste d’application est simplement une opération HTTP GET sur l’entité d’application API Azure AD Graph et le téléchargement est une opération HTTP PATCH sur l’entité de l’application.

Par conséquent, pour comprendre le format et les propriétés du manifeste d’application, vous devez référencer la documentation associée à [l’entité d’Application][APPLICATION-ENTITY] API Graph. Les exemples de mises à jour pouvant être effectuées via le manifeste d’application sont les suivants :

* **Déclaration des étendues d’autorisation (oauth2Permissions)** exposées par votre API web. Consultez la rubrique « Exposition d’API web vers d’autres applications » dans [Intégration d’applications dans Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] pour plus d’informations sur l’implémentation de l’emprunt d’identité utilisateur à l’aide de l’étendue d’autorisation déléguée oauth2Permissions. Comme mentionné précédemment, les propriétés d’entité d’application sont documentées dans l’article de référence [Entité et type complexe][APPLICATION-ENTITY] sur l’API Graph, notamment la propriété oauth2Permissions, qui est une collection de type [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Déclaration des rôles d’application (appRoles) exposées par votre application**. La propriété appRole de l’entité d’applications est une collection de type [AppRole][APPLICATION-ENTITY-APP-ROLE]. Consultez le [contrôle d’accès basé sur les rôles dans les applications de cloud avec Azure AD][RBAC-CLOUD-APPS-AZUREAD] pour obtenir un exemple d’implémentation.
* **Déclaration des applications clientes connues (knownClientApplications)**, ce qui vous permet de lier logiquement le consentement des applications clientes spécifiées à l’API web/la ressource.
* **Demander à Azure AD d’émettre une revendication d’appartenance au groupe** pour l’utilisateur connecté (groupMembershipClaims).  Elle peut aussi être configurée pour émettre des revendications au sujet des candidatures de rôle de répertoire Active Directory. Consultez l’article [Autorisation dans les applications de Cloud à l’aide de groupes Active Directory][AAD-GROUPS-FOR-AUTHORIZATION] pour obtenir un exemple d’implémentation.
* **Permettre à votre application de prendre en charge des flux d’octroi implicites OAuth 2.0** (oauth2AllowImplicitFlow). Ce type de flux d’octroi est utilisé avec les pages web JavaScript incorporées ou les applications SPA (Single Page Applications). Pour plus d’informations sur l’octroi d’autorisation implicite, consultez [Comprendre le flux d’octroi implicite OAuth2 dans Azure Active Directory (AD)][IMPLICIT-GRANT].
* **Activer l’utilisation de certificats X509 en tant que clé secrète** (keyCredentials). Consultez l’article [Créer des applications de démon et de service dans Office 365][O365-SERVICE-DAEMON-APPS] et le [Guide des développeurs sur Auth avec l’API d’Azure Resource Manager][DEV-GUIDE-TO-AUTH-WITH-ARM] pour obtenir des exemples d’implémentation.
* **Ajouter un nouvel URI d’ID d’application** pour votre application (identifierURIs[]). Les URI d’ID d’application sont utilisés pour identifier de manière unique une application dans son locataire Azure AD (ou sur plusieurs locataires Azure AD, pour les scénarios multi-locataires qualifiés via un domaine personnalisé vérifié). Ils sont utilisés lors de la demande d’autorisations d’accès à une application de ressources ou de l’acquisition d’un jeton d’accès pour une application de ressources. Lorsque vous mettez à jour cet élément, la même mise à jour est effectuée vers la collection servicePrincipalNames[] du principal de service correspondant, qui se trouve dans le client de base de l’application.

Le manifeste d’application constitue également un bon moyen de suivre l’état de l’enregistrement de l’application. Le fichier étant disponible au format JSON, la représentation du fichier peut être intégrée à votre contrôle de code source, avec le code source de votre application.

## <a name="step-by-step-example"></a>Exemple étape par étape
Maintenant, examinons les opérations requises pour mettre à jour la configuration de l’identité de votre application dans le manifeste d’application. Nous allons mettre en avant un des exemples précédents en vous montrant comment déclarer une nouvelle étendue d’autorisation sur une application de ressource :

1. Connectez-vous au [portail Azure][AZURE-PORTAL].
2. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
3. Sélectionnez l’extension **Azure Active Directory** dans le volet de navigation de gauche, puis cliquez sur **Inscriptions des applications**.
4. Recherchez l’application que vous souhaitez mettre à jour dans la liste, puis cliquez dessus.
5. Dans la page de l’application, cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste en ligne. 
6. Vous pouvez modifier directement le manifeste à l’aide de cet éditeur. Le manifeste suit le schéma de [l’entité d’application][APPLICATION-ENTITY] comme indiqué précédemment. Par exemple, partons du principe que nous voulons implémenter/exposer une nouvelle autorisation appelée « Employees.Read.All » pour notre application de ressource (API). Dans ce cas, vous devez simplement ajouter un deuxième ou nouvel élément à la collection oauth2Permissions, par exemple :
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    L’entrée doit être unique. Vous devez donc générer un nouveau GUID pour la propriété `"id"`. Dans ce cas, étant donné que nous avons spécifié `"type": "User"`, cette autorisation peut être consentie par n’importe quel compte authentifié par le locataire Azure AD dans lequel l’application d’API/de ressource est inscrite. Cela accorde à l’application cliente l’autorisation d’accéder au nom du compte. Les chaînes de nom d’affichage et de description sont utilisées durant le consentement et pour l’affichage dans le portail Azure.
6. Lorsque vous avez terminé de mettre à jour le manifeste, cliquez sur **Enregistrer** pour enregistrer le manifeste.  
   
Maintenant que le manifeste est enregistré, vous pouvez donner aux applications clientes enregistrées l’accès à la nouvelle autorisation que nous avons ajoutée précédemment. Cette fois, vous pouvez utiliser l’interface utilisateur web du portail Azure au lieu de modifier le manifeste de l’application cliente :  

1. Tout d’abord, accédez au panneau **Paramètres** de l’application cliente à laquelle vous souhaitez ajouter l’accès à la nouvelle API. Cliquez sur **Autorisations requises** et choisissez **Sélectionner une API**.
2. La liste des applications de ressource (API) inscrites s’affiche dans le locataire. Cliquez sur l’application de ressource pour la sélectionner, ou tapez le nom de l’application dans la zone de recherche. Lorsque vous avez trouvé l’application, cliquez sur **Sélectionner**.  
3. Cela vous dirigera vers la page **Sélectionner des autorisations** qui affiche la liste des autorisations d’application et des autorisations déléguées disponibles pour l’application de ressource. Sélectionnez la nouvelle autorisation pour l’ajouter à la liste d’autorisations demandée par le client. Cette nouvelle autorisation est stockée dans la configuration d’identité de l’application cliente, dans la propriété de collection requiredResourceAccess.


Vous avez terminé. Vos applications s’exécutent désormais à l’aide de leur nouvelle configuration d’identité.

## <a name="next-steps"></a>Étapes suivantes
* Consultez la rubrique [Objets principal du service et application][AAD-APP-OBJECTS] pour plus d’informations sur la relation existant entre les objets principal du service et application de l’application.
* Consultez le [glossaire du développeur Azure AD][AAD-DEVELOPER-GLOSSARY] pour connaître les définitions de certains des principaux concepts de développement Azure Active Directory (AD).

Utilisez la section de commentaires ci-dessous pour fournir des commentaires et nous aider à affiner et à mettre en forme notre contenu.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

