---
title: "Personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Découvrez la personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7394857f55493b072e6ea549c8eeec54a808f5e5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Personnalisation des revendications émises dans le jeton SAML pour les applications d’entreprise dans Azure Active Directory
Actuellement, Azure Active Directory prend en charge l’authentification unique avec la plupart des applications d’entreprise, y compris les deux applications pré-intégrées dans la galerie d’applications Azure AD, ainsi que les applications personnalisées. Quand un utilisateur s’authentifie auprès d’une application par l’intermédiaire d’Azure AD en utilisant le protocole SAML 2.0, Azure AD envoie un jeton à l’application (via HTTP POST). Après quoi, l’application valide et utilise ce jeton pour connecter l’utilisateur au lieu de lui demander un nom d’utilisateur et un mot de passe. Ces jetons SAML contiennent des informations sur l’utilisateur appelées « revendications ».

En jargon d’identité, une « revendication » concerne ce qu’un fournisseur d’identité déclare sur un utilisateur dans le jeton qu’il émet sur cet utilisateur. Dans un [jeton SAML](http://en.wikipedia.org/wiki/SAML_2.0), ces données sont généralement contenues dans l’instruction d’attribut SAML. L’ID unique de l’utilisateur est généralement présent dans le SAML Subject, également appelé « identificateur de nom ».

Par défaut, Azure Active Directory émet un jeton SAML à destination de votre application qui contient une revendication NameIdentifier, dont la valeur est le nom de l’utilisateur (ou nom d’utilisateur principal) dans Azure AD. Cette valeur peut identifier l’utilisateur de manière unique. Le jeton SAML contient également des revendications supplémentaires contenant l’adresse de messagerie de l’utilisateur, son prénom et son nom.

Pour afficher ou modifier les revendications émises dans le jeton SAML à destination de l’application, ouvrez l’application dans le portail Azure. Dans la section **Attributs utilisateur** de l’application, cochez la case **Afficher et modifier tous les autres attributs utilisateur**.

![Section Attributs utilisateur][1]

Vous pouvez être amené à modifier les revendications émises dans le jeton SAML pour les deux raisons suivantes :
* L’application a été écrite pour exiger un ensemble différent d’URI de revendication ou de valeurs de revendication.
* L’application a été déployée d’une manière qui nécessite que la revendication NameIdentifier soit différente du nom d’utilisateur (nom d’utilisateur principal) stocké dans Azure Active Directory.

Vous pouvez modifier n’importe quelle valeur de revendication par défaut. Sélectionnez la ligne de la revendication dans le tableau d’attributs de jetons SAML. Cette opération ouvre la section **Modifier l’attribut**, où vous pouvez modifier le nom de la revendication, ainsi que la valeur et l’espace de noms associés.

![Modifier un attribut utilisateur][2]

Vous pouvez également supprimer les revendications (autres que NameIdentifier) à l’aide du menu contextuel qui s’ouvre lorsque vous cliquez sur l’icône **...**.  Vous pouvez également ajouter de nouvelles revendications à l’aide du bouton **Ajouter un attribut**.

![Modifier un attribut utilisateur][3]

## <a name="editing-the-nameidentifier-claim"></a>Modification de la revendication NameIdentifier
Pour résoudre le problème dans lequel l’application a été déployée à l’aide d’un nom d’utilisateur différent, cliquez sur la liste déroulante **Identificateur d’utilisateur** dans la section **Attributs utilisateur**. Cette action donne accès à une boîte de dialogue qui contient différentes options :

![Modifier un attribut utilisateur][4]

Dans la liste déroulante, sélectionnez **user.mail** pour faire correspondre la revendication NameIdentifier à l’adresse e-mail de l’utilisateur contenue dans l’annuaire. Sinon, sélectionnez **user.onpremisessamaccountname** pour affecter le nom de compte SAM de l’utilisateur qui a été synchronisé à partir d’Azure AD en local.

Vous pouvez également utiliser la fonction spéciale **ExtractMailPrefix()** pour supprimer le suffixe de domaine de l’adresse e-mail, du nom de compte SAM ou du nom d’utilisateur principal. Ainsi, seule la première partie du nom d’utilisateur transmis est extraite (par exemple, « joe_smith » au lieu de joe_smith@contoso.com).

![Modifier un attribut utilisateur][5]

Nous avons également ajouté la fonction **join()** pour joindre le domaine vérifié à la valeur d’identificateur d’utilisateur. Lorsque vous sélectionnez la fonction join() dans **l’identificateur de l’utilisateur**, sélectionnez d’abord l’identificateur d’utilisateur comme nom d’utilisateur principal ou adresse e-mail, puis, dans la deuxième liste déroulante, sélectionnez votre domaine vérifié. Si vous sélectionnez l’adresse e-mail avec le domaine vérifié, Azure AD extrait le nom d’utilisateur de la première valeur joe_smith de joe_smith@contoso.com, puis l’ajoute à contoso.onmicrosoft.com. Voir l’exemple suivant :

![Modifier un attribut utilisateur][6]

## <a name="adding-claims"></a>Ajout de revendications
Au moment d’ajouter une revendication, vous pouvez spécifier le nom d’attribut (qui ne doit pas nécessairement correspondre de manière stricte à un modèle d’URI, conformément à la spécification SAML). Faites correspondre la valeur à n’importe quel attribut utilisateur stocké dans l’annuaire.

![Ajouter un attribut utilisateur][7]

Par exemple, vous devez envoyer le service auquel appartient l’utilisateur au sein de son entreprise en tant que revendication (par exemple, Ventes). Entrez le nom de la revendication qui est attendu par l’application, puis sélectionnez **user.department** comme valeur.

> [!NOTE]
> Si pour un utilisateur donné, aucune valeur n’est stockée pour un attribut sélectionné, la revendication n’est pas émise dans le jeton.

> [!TIP]
> **user.onpremisesecurityidentifier** et **user.onpremisesamaccountname** sont uniquement pris en charge lors de la synchronisation des données utilisateur issues de l’annuaire Active Directory local en utilisant [l’outil Azure AD Connect](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Revendications restreintes

Il existe certaines revendications restreintes dans SAML. Si vous ajoutez ces revendications, Azure AD ne les enverra pas. Voici ces revendications SAML restreintes :

    | Type de revendication (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Étapes suivantes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](../active-directory-apps-index.md)
* [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
