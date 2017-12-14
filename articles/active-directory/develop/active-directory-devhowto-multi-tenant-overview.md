---
title: "Guide pratique pour créer une application capable de connecter n’importe quel utilisateur Azure AD"
description: "Procédure étape par étape pour créer une application capable de connecter un utilisateur à partir de n’importe quel client Azure Active Directory, également appelée application mutualisée."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: f6d8d2c07c2860059c4e9deb75d0bc4a876e057b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>Comment connecter un utilisateur Azure Active Directory (AD) à l’aide du modèle d’application mutualisée
Si vous proposez une application SaaS (Software as a Service) à de nombreuses organisations, vous pouvez configurer votre application pour accepter les connexions à partir de n’importe quel client Azure AD.  Dans Azure AD, cette configuration permet de rendre une application multi-locataire.  Les utilisateurs de n’importe quel client Azure AD pourront se connecter à votre application après votre consentement afin d’utiliser leur compte avec votre application.  

Si vous avez une application existante qui possède son propre système de compte, ou prend en charge d’autres types de connexion auprès d’autres fournisseurs cloud, l’ajout de la connexion Azure AD à partir de n’importe quel client est simple. Inscrivez simplement votre application, ajoutez le code de connexion via OAuth2, OpenID Connect ou SAML, et placez un bouton « Se connecter avec Microsoft » dans votre application. Cliquez sur le bouton suivant pour en savoir plus sur la personnalisation de votre application.

[![Bouton Se connecter][AAD-Sign-In]][AAD-App-Branding]

Cet article suppose que vous êtes déjà familiarisé avec la création d’une application à client unique pour Azure AD.  Si ce n’est pas le cas, retournez à la [page d’accueil du guide du développeur][AAD-Dev-Guide] et testez l’un de nos démarrages rapides !

Il existe quatre étapes simples pour convertir votre application en une application Azure AD mutualisée :

1. Conversion de votre application d’inscription en une application mutualisée
2. Mise à jour de votre code pour envoyer des demandes au point de terminaison /common 
3. Mise à jour de votre code pour gérer plusieurs valeurs issuer
4. Comprendre le consentement de l’utilisateur et de l’administrateur et application des modifications de code appropriées

Examinons chaque étape en détail. Vous pouvez également accéder directement à [cette liste d’exemples d’architectures mutualisées][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Conversion d’une inscription en inscription mutualisée
Par défaut, les inscriptions web app/API dans Azure AD sont de type client unique.  Vous pouvez rendre votre inscription multi-locataire en activant (avec l’option « Oui ») le commutateur « Mutualisé » dans la page de propriétés de l’inscription de votre application dans le [portail Azure][AZURE-portal].

Notez également que, avant qu’une application ne soit mutualisée, l’URI d'ID d’application doit être globalement unique Azure AD. L’URI ID d’application est l’une des méthodes d’identification d'une application dans les messages de protocole.  Pour une application à client unique, il suffit que l’URI ID d’application soit unique au sein de ce client.  Pour une application mutualisée, l’URI doit être globalement unique afin qu’Azure AD puisse trouver l’application sur tous les clients.  L’unicité globale est appliquée en obligeant l’URI ID d’application à avoir un nom d’hôte correspondant à un domaine vérifié du client Azure AD.  Par exemple, si le nom de votre client était contoso.onmicrosoft.com, un URI ID d’application valide serait `https://contoso.onmicrosoft.com/myapp`.  Si votre client possède le domaine vérifié `contoso.com`, un URI ID d’application valide serait alors `https://contoso.com/myapp`.  Si l’URI ID d’application ne suit pas ce modèle, une application ne peut pas être définie comme multi-locataire.

Les inscriptions clientes natives sont mutualisées par défaut.  Aucune action n’est nécessaire de votre part pour rendre mutualisée une connexion d’application cliente native.

## <a name="update-your-code-to-send-requests-to-common"></a>Mise à jour de votre code pour envoyer des demandes à /common
Dans une application à client unique, les demandes de connexion sont envoyées au point de terminaison de connexion du client. Par exemple, pour contoso.onmicrosoft.com, le point de terminaison serait :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

Les demandes envoyées au point de terminaison d’un client permettent aux utilisateurs (ou invités) de ce client de se connecter aux applications de ce client.  Avec une application mutualisée, l’application ne sait pas à l’avance de quel client provient l’utilisateur et vous ne pouvez donc pas envoyer des demandes au point de terminaison d’un client.  Au lieu de cela, les demandes sont envoyées à un point de terminaison qui est multiplexé entre tous les clients Azure AD :

    https://login.microsoftonline.com/common

Quand Azure AD reçoit une demande sur le point de terminaison /common, elle connecte l’utilisateur et par conséquent détecte de quel client il provient.  Le point de terminaison /common fonctionne avec tous les protocoles d’authentification pris en charge par Azure AD : OpenID Connect OAuth 2.0, SAML 2.0 et WS-Federation.

La réponse de connexion envoyée à l’application contient un jeton représentant l’utilisateur.  La valeur issuer du jeton indique à une application de quel client provient l’utilisateur.  Quand une réponse revient du point de terminaison /common, la valeur issuer du jeton correspond au locataire de l’utilisateur.  Il est important de noter que point de terminaison /common n’est pas un client et n’est pas un émetteur : il s’agit simplement d’un multiplexeur.  Lorsque vous utilisez /common, la logique de votre application permettant de valider les jetons doit être mise à jour en conséquence. 

Comme mentionné précédemment, les applications mutualisées doivent également fournir une expérience de connexion cohérente aux utilisateurs en suivant les directives de personnalisation des applications Azure Active Directory. Cliquez sur le bouton suivant pour en savoir plus sur la personnalisation de votre application.

[![Bouton Se connecter][AAD-Sign-In]][AAD-App-Branding]

Intéressons-nous désormais de plus près à l’utilisation du point de terminaison /common et à l’implémentation de votre code.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Mise à jour de votre code pour gérer plusieurs valeurs issuer
Les applications web et les API reçoivent et valident les jetons d’Azure AD.  

> [!NOTE]
> Bien que les applications clientes natives demandent et recevoir les jetons d’Azure AD, elles ne les envoient pas API, où ils sont validés.  Les applications natives ne valident pas les jetons et doivent les traiter comme des valeurs opaques.
> 
> 

Examinons la manière dont une application valide les jetons qu’elle reçoit d’Azure AD.  Une application à locataire unique a généralement une valeur de point de terminaison de type :

    https://login.microsoftonline.com/contoso.onmicrosoft.com

et l’utilise pour construire une URL de métadonnées (dans ce cas, OpenID Connect) comme :

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

pour télécharger deux informations essentielles utilisées pour valider les jetons : les clés de connexion du client et la valeur issuer.  Chaque client Azure AD possède une valeur issuer unique de type :

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

où la valeur GUID est la version « rename-safe » de l’ID du client.  Si vous cliquez sur le lien de métadonnées précédent pour `contoso.onmicrosoft.com`, vous pouvez afficher cette valeur issuer dans le document.

Lorsqu’une application client unique valide un jeton, elle vérifie la signature du jeton par rapport aux clés de signature à partir du document de métadonnées. Ce test permet de vous assurer que la valeur de l’émetteur du jeton correspond à celui qui a été trouvé dans le document de métadonnées.

Comme le point de terminaison /common ne correspond pas à un client et n’a pas la valeur issuer, lorsque vous examinez la valeur issuer dans les métadonnées pour /common, elle comporte une URL basée sur un modèle au lieu d’une valeur réelle :

    https://sts.windows.net/{tenantid}/

Par conséquent, une application mutualisée ne peut pas valider les jetons simplement en faisant correspondre la valeur issuer dans les métadonnées avec la valeur `issuer` dans le jeton.  Une application mutualisée a besoin d’une logique pour déterminer les valeurs issuer valides et celles qui ne le sont pas, en fonction de la partie ID client de la valeur issuer.  

Par exemple, si une application mutualisée permet uniquement la connexion à partir de clients spécifiques qui se sont inscrits à leur service, elle doit vérifier la valeur issuer ou la valeur de revendication `tid` dans le jeton pour s’assurer que ce client figure dans sa liste d’abonnés.  Si une application mutualisée ne gère que des personnes et ne prend aucune décision concernant l’accès en fonction des clients, elle peut donc totalement ignorer la valeur issuer.

Dans les exemples d’architectures mutualisées figurant dans la section [Contenu associé](#related-content) à la fin de cet article, la validation de la valeur issuer est désactivée pour permettre à n’importe quel client Azure AD de se connecter.

Examinons à présent l’expérience des utilisateurs qui se connectent à des applications mutualisées.

## <a name="understanding-user-and-admin-consent"></a>Comprendre le consentement de l’utilisateur et de l’administrateur
Pour qu’un utilisateur puisse se connecter à une application dans Azure AD, cette application doit être représentée dans le client de l’utilisateur.  Cela permet à l’organisation d’effectuer différentes tâches, par exemple appliquer des stratégies uniques lorsque les utilisateurs de leurs clients se connectent à l’application.  Pour une application à locataire unique, l’inscription est simple ; il s’agit de l’inscription de l’application dans le [portail Azure][AZURE-portal].

Pour une application mutualisée, l’inscription initiale de l’application s’effectue dans le client Azure AD utilisé par le développeur.  Lorsqu’un utilisateur d’un autre client se connecte à l’application pour la première fois, Azure AD l’invite à donner son consentement pour les autorisations demandées par l’application.  S’il donne son consentement, une représentation de l’application appelée *principal du service* est créée dans le client de l’utilisateur, et la connexion peut alors continuer. Une délégation est également créée dans le répertoire qui enregistre le consentement de l’utilisateur pour l’application. Consultez la rubrique [Objets principal du service et application][AAD-App-SP-Objects] pour plus d’informations sur les objets ServicePrincipal et Application de l’application et les liens qui les unissent.

![Consentement pour une application à niveau unique][Consent-Single-Tier] 

Ce processus de consentement dépend des autorisations demandées par l’application.  Azure AD prend en charge deux types d’autorisations, application seule et application déléguée :

* une autorisation déléguée accorde à une application la possibilité d’agir comme un utilisateur connecté pour un sous-ensemble d’actions que l’utilisateur peut effectuer.  Par exemple, vous pouvez accorder à une application l’autorisation déléguée pour lire le calendrier de l’utilisateur connecté.
* Une autorisation d’application seule est directement accordée à l’identité de l’application.  Par exemple, vous pouvez accorder à une application une autorisation application seule pour lire la liste des utilisateurs d’un client, quels que soient les clients connectés à l’application.

Certaines autorisations peuvent être accordées par un utilisateur standard, tandis que d’autres nécessitent le consentement de l’administrateur d’un client. 

### <a name="admin-consent"></a>Consentement de l’administrateur
Les autorisations application seule nécessitent toujours le consentement de l’administrateur d’un client.  Si votre application demande une autorisation application seule et qu’un utilisateur tente de se connecter à l’application, un message d’erreur indiquant que l’utilisateur n’est pas en mesure de donner son consentement s’affiche.

Certaines autorisations déléguées nécessitent également le consentement de l’administrateur d’un client.  Par exemple, la possibilité de réécrire dans Azure AD en tant que l’utilisateur connecté requiert le consentement de l’administrateur d’un client.  Comme pour les autorisations application seule, si un utilisateur standard tente de se connecter à une application qui demande une autorisation déléguée nécessitant le consentement de l’administrateur, votre application reçoit une erreur.  Le fait qu’une autorisation nécessite ou non le consentement d’un administrateur est déterminé par le développeur qui a publié la ressource, et ces informations sont disponibles dans la documentation de cette ressource.  Des liens vers les rubriques qui décrivent les autorisations disponibles pour l’API Graph Azure AD et les API Graph Microsoft se trouvent dans la section [Contenu connexe](#related-content) de cet article.

Si votre application utilise des autorisations qui nécessitent le consentement de l’administrateur, vous devez y intégrer une option comme un bouton ou un lien afin que l’administrateur puisse initier l’action.  La requête que votre application envoie pour cette action est une demande d’autorisation OAuth2/OpenID Connect standard, mais qui inclut également le paramètre de chaîne de requête `prompt=admin_consent` .  Une fois que l’administrateur a donné son consentement et que le principal de service est créé dans le client, les connexions ultérieures n’ont plus besoin du paramètre `prompt=admin_consent`. Comme l’administrateur a décidé que les autorisations demandées sont acceptables, les autres utilisateurs n’ont plus à donner leur consentement par la suite.

Le paramètre `prompt=admin_consent` peut également être utilisé par les applications qui demandent des autorisations qui ne nécessitent pas d’autorisation de l’administrateur. Cela a lieu quand l’application requiert une expérience où l’administrateur du locataire « s’inscrit » une seule fois et qu’aucun utilisateur n’a à donner de consentement à compter de ce moment.

Si une application requiert le consentement de l’administrateur, et qu’un administrateur se connecte alors que le paramètre `prompt=admin_consent` n’est pas envoyé, l’administrateur consent l’accès à l’application **uniquement pour son compte utilisateur**.  Les utilisateurs standard ne peuvent toujours pas se connecter et donner leur consentement à l’application.  Cette fonctionnalité s’avère utile si vous souhaitez donner à l’administrateur du locataire la possibilité d’explorer votre application avant d’autoriser l’accès à d’autres utilisateurs.

L’administrateur d’un client peut empêcher les utilisateurs standard de donner son consentement aux applications.  Si cette fonctionnalité est désactivée, le consentement de l’administrateur est toujours requis pour que l’application soit configurée dans le client.  Si vous souhaitez tester votre application en désactivant le consentement de l’utilisateur standard, vous pouvez utiliser le commutateur de configuration dans la section sur la configuration du locataire Azure AD du [portail Azure][AZURE-portal].

> [!NOTE]
> Dans certaines applications, les utilisateurs standard peuvent donner leur consentement initialement, et l’application peut par la suite impliquer l’administrateur et imposer des autorisations nécessitant le consentement de l’administrateur.  Il n’existe actuellement aucun moyen d’effectuer cette opération avec une inscription unique dans Azure AD.  Avec le nouveau point de terminaison du modèle de déploiement Azure AD Resource Manager, les applications peuvent demander des autorisations au moment de l’exécution, au lieu de le faire à l’inscription, comme ce que permet ce scénario.  Pour plus d’informations, consultez le [Guide du développeur du modèle de déploiement Resource Manager d’Azure AD App][AAD-V2-Dev-Guide].
> 
> 

### <a name="consent-and-multi-tier-applications"></a>Consentement et applications multiniveau
Votre application peut comporter plusieurs niveaux, chacun représenté par sa propre inscription dans Azure AD.  Par exemple, une application native qui appelle une API web ou une application web qui appelle une autre API web.  Dans ces deux cas, le client (application native ou application web) demande des autorisations pour appeler la ressource (API web).  Pour que le client puisse donner son consentement pour un client, toutes les ressources nécessitant des autorisations doivent déjà exister dans ce client.  Si cette condition n’est pas remplie, Azure AD renvoie une erreur indiquant que la ressource doit d’abord être ajoutée.

**Plusieurs niveaux dans un seul client**

Cela peut poser problème si votre application logique implique deux ou plusieurs inscriptions d’application, par exemple un client et une ressource distincts.  Comment ajouter d’abord la ressource au client ?  Azure AD traite ce cas en permettant au client et aux ressources d’être consentis en une seule étape. L’utilisateur voit l’ensemble des autorisations demandées par le client et les ressources sur la page de consentement.  Pour activer ce comportement, l’inscription de l’application de la ressource doit inclure l’ID Application du client en tant qu’élément `knownClientApplications` dans son manifeste d’application.  Par exemple :

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Cette propriété peut être mise à jour via la ressource [Manifeste de l’application][AAD-App-Manifest]. Cela est illustré par un client natif multiniveau appelant un exemple d’API web dans la section [Contenu connexe](#related-content) à la fin de cet article. Le diagramme ci-dessous décrit le processus de consentement pour une application multiniveau enregistrée dans un seul locataire :

![Consentement pour une application cliente multiniveau connue][Consent-Multi-Tier-Known-Client] 

**Plusieurs niveaux dans plusieurs clients**

Un cas similaire se produit si les différents niveaux d’une application sont enregistrés dans différents clients.  Prenons par exemple le cas de la création d’une application cliente native qui appelle l’API Office 365 Exchange Online.  Pour développer l’application native, et pour que l’application native s’exécute ensuite sur un client, le principal du service Exchange Online doit être présent.  Dans ce cas, le développeur et l’utilisateur doivent acheter Exchange Online afin de créer le principal du service sur leurs clients.  

Dans le cas d’une API générée par une organisation autre que Microsoft, le développeur de l’API doit fournir un moyen à ses clients de donner leur consentement à l’application sur leurs clients. La conception recommandée est que le développeur tiers génère l’API de sorte à pouvoir également fonctionner comme un client web pour implémenter l’abonnement :

1. Suivez les sections précédentes pour vous assurer que l’API implémente les exigences de code/d’inscription d’application mutualisée
2. Outre l’exposition des rôles/étendues de l’API, vérifiez que l’inscription inclut l’autorisation Azure AD « Se connecter et lire le profil utilisateur » (fournie par défaut)
3. Implémentez une page de connexion/inscription dans le client web, en suivant le guide [Consentement de l’administrateur](#admin-consent) abordé précédemment 
4. Une fois que l’utilisateur donne son consentement à l’application, les liens du principal de service et de la délégation de consentement sont créés dans son client, et l’application native peut obtenir des jetons pour l’API

Le diagramme suivant décrit le processus de consentement pour une application multiniveau enregistrée dans différents clients :

![Consentement pour une application multiniveau avec différentes tierces parties][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Révocation d’un consentement
Les utilisateurs et les administrateurs peuvent à tout moment révoquer leur consentement pour votre application :

* Les utilisateurs révoquent l’accès à des applications individuelles en les supprimant de leur liste [Applications du panneau d’accès][AAD-Access-Panel].
* Les administrateurs révoquent l’accès à des applications en les supprimant d’Azure AD à l’aide de la section sur la gestion d’Azure AD du [portail Azure][AZURE-portal].

Si un administrateur donne son consentement à une application pour tous les utilisateurs d’un client, ces utilisateurs ne peuvent pas révoquer l’accès individuellement.  Seul l’administrateur peut révoquer l’accès et uniquement pour l’application entière.

### <a name="consent-and-protocol-support"></a>Consentement et prise en charge des protocoles
Le consentement est pris en charge dans Azure AD via les protocoles OAuth, OpenID Connect, WS-Federation et SAML.  Les protocoles SAML et WS-Federation ne prenant pas en charge le paramètre `prompt=admin_consent` , le consentement de l’administrateur est uniquement possible via OAuth et OpenID Connect.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Applications mutualisées et mise en cache des jetons d’accès
Les applications mutualisées peuvent également obtenir des jetons d’accès pour appeler des API protégées par Azure AD.  Une erreur courante lors de l’utilisation de la bibliothèque d’authentification Active Directory (ADAL) avec une application mutualisée consiste à demander initialement un jeton pour un utilisateur à l’aide du paramètre /common, à recevoir une réponse, puis à demander un nouveau jeton pour ce même utilisateur également à l’aide de /common.  Comme la réponse d’Azure AD provient d’un client et non de /common, ADAL met en cache le jeton comme provenant du client. L’appel suivant à /common pour obtenir un jeton d’accès pour l’utilisateur manque l’entrée du cache, et l’utilisateur est invité à se reconnecter.  Pour éviter de manquer le cache, assurez-vous que les appels suivants pour un utilisateur déjà connecté sont effectués vers le point de terminaison du client.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment créer une application pouvant connecter un utilisateur à partir de n’importe quel client Azure Active Directory. Après l’activation de l’authentification unique sur entre votre application et Azure Active Directory, vous pouvez également mettre à jour votre application pour accéder aux API exposées par les ressources de Microsoft, comme Office 365. Par conséquent, vous pouvez proposer une expérience personnalisée dans votre application, par exemple en affichant des informations contextuelles aux utilisateurs, comme leur photo de profil ou leur prochain rendez-vous de calendrier. Pour en savoir plus sur les appels d’API aux services Azure Active Directory et Office 365 comme Exchange, SharePoint, OneDrive, OneNote, Planner, Excel et bien plus encore, consultez : [API Microsoft Graph][MSFT-Graph-overview].


## <a name="related-content"></a>Contenu connexe
* [Exemples d’applications mutualisées][AAD-Samples-MT]
* [Directives de personnalisation des applications][AAD-App-Branding]
* [Guide du développeur Azure AD][AAD-Dev-Guide]
* [Objets principal du service et application][AAD-App-SP-Objects]
* [Intégration d’applications dans Azure Active Directory][AAD-Integrating-Apps]
* [Vue d’ensemble de l’infrastructure de consentement][AAD-Consent-Overview]
* [Étendues des autorisations de l’API Graph Microsoft][MSFT-Graph-permision-scopes]
* [Étendues des autorisations de l’API Graph Azure AD][AAD-Graph-Perm-Scopes]

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














