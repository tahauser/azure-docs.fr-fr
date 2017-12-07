---
title: "Autoriser des comptes de développeurs à l’aide d’Azure Active Directory dans Gestion des API Azure | Microsoft Docs"
description: Comment autoriser des utilisateurs avec Azure Active Directory dans Gestion des API.
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 3faa6c1867808436a66a2b33ea1a9d79ede2c8fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> L’intégration d’Azure Active Directory est disponible uniquement avec les niveaux [Développeur et Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Comment autoriser des comptes de développeurs avec Azure Active Directory dans Gestion des API Azure
## <a name="overview"></a>Vue d'ensemble
Ce guide vous explique comment activer l’accès au portail des développeurs pour les utilisateurs d’Azure Active Directory. Il vous montre également comment gérer des groupes d’utilisateurs Azure Active Directory en ajoutant des groupes externes qui contiennent les utilisateurs d’un annuaire Azure Active Directory.

> Pour effectuer les étapes de ce guide, vous devez disposer d’un annuaire Azure Active Directory dans lequel vous souhaitez créer une application.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Comment autoriser des comptes de développeurs avec Azure Active Directory
Pour commencer, cliquez sur **Portail des éditeurs** dans le portail Azure de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

> Si vous n’avez pas encore créé une instance de service Gestion des API, consultez la page de [création d’une instance de service Gestion des API][Create an API Management service instance] dans le didacticiel de [prise en main de Gestion des API Azure][Get started with Azure API Management].
> 
> 

Cliquez sur **Sécurité** dans le menu **Gestion des API** à gauche, puis sur **Identités externes**.

![Identités externes][api-management-security-external-identities]

Cliquez sur **Azure Active Directory**. Notez l’ **URL de redirection** et revenez à votre annuaire Azure Active Directory dans le portail Azure Classic.

![Identités externes][api-management-security-aad-new]

Cliquez sur le bouton **Ajouter** pour créer une application Azure Active Directory, puis choisissez **Ajouter une application développée par mon organisation**.

![Ajouter de nouvelles applications Azure Active Directory][api-management-new-aad-application-menu]

Entrez le nom de l’application, sélectionnez **Application web et/ou API web**, puis cliquez sur le bouton Suivant.

![Nouvelle application Azure Active Directory][api-management-new-aad-application-1]

Pour **URL de connexion**, entrez l’URL de connexion de votre portail des développeurs. Dans cet exemple, la valeur de **URL de connexion** est `https://aad03.portal.current.int-azure-api.net/signin`. 

Dans **URL d’ID de l’application**, entrez le domaine par défaut ou un domaine personnalisé pour Azure Active Directory et ajoutez une chaîne unique. Dans cet exemple, le domaine par défaut de **https://contoso5api.onmicrosoft.com** est utilisé avec le suffixe **/api** spécifié.

![Propriétés de la nouvelle application Azure Active Directory][api-management-new-aad-application-2]

Cliquez sur la coche pour enregistrer et créer l’application, puis revenez à l’onglet **Configurer** pour configurer la nouvelle application.

![Nouvelle application Azure Active Directory créée][api-management-new-aad-app-created]

Si plusieurs annuaires Azure Active Directory vont être utilisés pour cette application, cliquez sur **Oui** pour **Application mutualisée**. La valeur par défaut est **Non**.

![Application mutualisée][api-management-aad-app-multi-tenant]

Copiez **l’URL de redirection** dans la section **Azure Active Directory** de l’onglet **Identités externes** du portail des éditeurs et collez-la dans la zone de texte **URL de réponse**. 

![URL de réponse][api-management-aad-reply-url]

Allez en bas de l’onglet Configurer, sélectionnez la liste déroulante **Autorisations de l’application** et activez l’option **Lire les données de l’annuaire**.

![Autorisations de l’application][api-management-aad-app-permissions]

Sélectionnez la liste déroulante **Déléguer les autorisations** et activez l’option **Activer l’authentification et lire les profils utilisateur**.

![Autorisations déléguées][api-management-aad-delegated-permissions]

> Pour plus d’informations sur l’application et les autorisations déléguées, consultez la page [Accès à l’API Graph][Accessing the Graph API].
> 
> 

Copiez l’ **ID de client** dans le Presse-papiers.

![ID de client][api-management-aad-app-client-id]

Revenez au portail des éditeurs et collez l’ **ID de client** copié dans la configuration de l’application Azure Active Directory.

![ID de client][api-management-client-id]

Revenez à la configuration d’Azure Active Directory, puis cliquez sur la liste déroulante **Sélectionner une durée** dans la section **Clés** et spécifiez un intervalle. Dans cet exemple, la valeur **1 an** est utilisée.

![Clé][api-management-aad-key-before-save]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. Copiez la clé dans le Presse-papiers.

> Notez sa valeur. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne peut plus être affichée.
> 
> 

![Clé][api-management-aad-key-after-save]

Revenez au portail des éditeurs et collez la clé dans la zone de texte **Clé secrète client** .

![Clé secrète client][api-management-client-secret]

**Locataires autorisés** spécifie les répertoires qui ont accès aux API de l’instance de service Gestion des API. Spécifiez les domaines des instances Azure Active Directory auxquelles vous souhaitez accorder l’accès. Vous pouvez séparer plusieurs domaines par des sauts de ligne, des espaces ou des virgules.

![Locataires autorisés][api-management-client-allowed-tenants]


Une fois la configuration souhaitée spécifiée, cliquez sur **Enregistrer**.

![Enregistrer][api-management-client-allowed-tenants-save]

Après avoir enregistré les modifications, les utilisateurs de l’annuaire Azure Active Directory spécifié peuvent se connecter au portail des développeurs en suivant les étapes de la section [Connexion au portail des développeurs avec un compte Azure Active Directory][Log in to the Developer portal using an Azure Active Directory account].

Plusieurs domaines peuvent être spécifiés dans la section **Locataires autorisés** . Avant qu’un utilisateur puisse se connecter à partir d’un autre domaine que le domaine d’origine dans lequel l’application a été enregistrée, l’administrateur général de l’autre domaine doit accorder à l’application l’autorisation d’accéder aux données de l’annuaire. Pour accorder l’autorisation, l’administrateur général doit accéder à `https://<URL of your developer portal>/aadadminconsent` (par exemple, https://contoso.portal.azure-api.net/aadadminconsent), entrer le nom de domaine du client Active Directory auquel il souhaite accorder l’accès, puis cliquer sur Envoyer. Dans l’exemple suivant, un administrateur général de `miaoaad.onmicrosoft.com` tente d’accorder l’autorisation à ce portail développeur spécifique. 

![Autorisations][api-management-aad-consent]

Dans l’écran suivant, l’administrateur général sera invité à confirmer l’octroi de l’autorisation. 

![Autorisations][api-management-permissions-form]

> Si un administrateur autre que l’administrateur global tente de se connecter avant que les autorisations ne soient accordées par un administrateur général, la tentative de connexion échoue et un écran d’erreur s’affiche.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Ajout d’un groupe Azure Active Directory externe
Après avoir activé l’accès pour les utilisateurs dans Azure Active Directory, vous pouvez ajouter des groupes Azure Active Directory à Gestion des API pour gérer plus facilement l’association des développeurs du groupe avec les produits souhaités.

> Pour pouvoir configurer un groupe Azure Active Directory externe, Azure Active Directory doit d’abord être configuré dans l’onglet Identités, selon la procédure décrite dans la section précédente. 
> 
> 

Les groupes Azure Active Directory externes sont ajoutés à partir de l’onglet **Visibilité** du produit auquel vous souhaitez accorder l’accès au groupe. Cliquez sur **Produits**, puis sur le nom du produit souhaité.

![Configure product][api-management-configure-product]

Passez à l’onglet **Visibilité**, puis cliquez sur **Ajouter des groupes depuis Azure Active Directory**.

![Ajouter des groupes][api-management-add-groups]

Sélectionnez le **locataire Azure Active Directory** dans la liste déroulante, puis tapez le nom du groupe de votre choix dans la zone de texte des **groupes** à ajouter.

![Sélectionner un groupe][api-management-select-group]

Ce nom de groupe se trouve dans la liste **Groupes** de votre annuaire Azure Active Directory, comme illustré dans l’exemple suivant.

![Liste des groupes Azure Active Directory][api-management-aad-groups-list]

Cliquez sur **Ajouter** pour valider le nom du groupe et ajouter le groupe. Dans cet exemple, le groupe externe **Contoso 5 Developers** est ajouté. 

![Group added][api-management-aad-group-added]

Cliquez sur **Enregistrer** pour enregistrer la nouvelle sélection de groupe.

Une fois le groupe Azure Active Directory configuré à partir d’un produit, il est consultable dans l’onglet **Visibilité** des autres produits dans l’instance de service Gestion des API.

Pour vérifier et configurer les propriétés des groupes externes une fois qu’ils ont été ajoutés, cliquez sur le nom du groupe dans l’onglet **Groupes**.

![Gérer les groupes][api-management-groups]

À partir de là, vous pouvez modifier le **nom** et la **description** du groupe.

![Modifier un groupe][api-management-edit-group]

Les utilisateurs de l’annuaire Azure Active Directory configuré peuvent se connecter au portail des développeurs et consulter des groupes. Ils peuvent s’abonner aux groupes qui leur sont accessibles selon les instructions de la section suivante.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Connexion au portail des développeurs avec un compte Azure Active Directory
Pour vous connecter au portail des développeurs à l’aide d’un compte Azure Active Directory configuré dans les sections précédentes, ouvrez une nouvelle fenêtre de navigateur avec **l’URL de connexion** dans la configuration de l’application Active Directory, puis cliquez sur **Azure Active Directory**.

![Portail des développeurs][api-management-dev-portal-signin]

Entrez les informations d’identification d’un des utilisateurs dans votre annuaire Azure Active Directory, puis cliquez sur **Se connecter**.

![Se connecter][api-management-aad-signin]

Un formulaire d’inscription peut vous être présenté si certaines informations supplémentaires sont requises. Renseignez le formulaire d’inscription, puis cliquez sur **S’inscrire**.

![Inscription][api-management-complete-registration]

Votre utilisateur est maintenant connecté au portail des développeurs pour votre instance de service Gestion des API.

![Inscription terminée][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

