---
title: "Délégation de l'inscription des utilisateurs et des abonnements aux produits"
description: "Découvrez comment déléguer à un tiers l'enregistrement de l'utilisateur et la souscription à des produits dans la gestion des API Azure."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: fc8c5774eb616c33c00ecebeacd31e2a07b36e0c
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Délégation de l'inscription des utilisateurs et des abonnements aux produits
La délégation vous permet d'utiliser votre site web existant pour gérer les connexions/inscriptions des développeurs et l'abonnement aux produits au lieu de faire appel aux fonctionnalités intégrées du portail des développeurs. Ceci permet à votre site web de conserver les données utilisateur et de valider ces étapes de façon personnalisée.

## <a name="delegate-signin-up"> </a>Délégation de la connexion et de l’inscription des développeurs
Pour déléguer les connexions et inscriptions des développeurs à votre site web existant, vous devez créer un point de terminaison de délégation spécifique sur votre site qui agit en tant que point d’entrée pour toutes les demandes de ce type émanant du portail des développeurs Gestion des API.

Le processus final se présente comme suit :

1. Le développeur clique sur le lien de connexion ou d'inscription sur le portail des développeurs Gestion des API.
2. Le navigateur est redirigé vers le point de terminaison de délégation.
3. À son tour, le point de terminaison de délégation présente une interface demandant à l’utilisateur de se connecter ou de s’inscrire, ou bien le redirige vers cette interface.
4. Si l'authentification est réussie, l'utilisateur revient à la page du portail des développeurs Gestion des API d'où il est parti.

Pour commencer, configurons Gestion des API pour que les demandes soient acheminées via votre point de terminaison de délégation. Dans le portail des éditeurs de la gestion des API, cliquez sur **Sécurité**, puis sur l’onglet **Délégation**. Activez la case à cocher pour activer la connexion et l'inscription déléguées.

![Delegation page][api-management-delegation-signin-up]

* Décidez de l'URL de votre point de terminaison de délégation spécial, puis entrez-la dans le champ **URL du point de terminaison de délégation** . 
* Dans le champ Clé d’authentification de la délégation, entrez le secret utilisé pour calculer une signature qui vous sera fournie pour vérification afin de vous assurer que la demande provient bien de Gestion des API Azure. Vous pouvez cliquer sur le bouton **Générer** pour que Gestion des API génère une clé de manière aléatoire pour vous.

À présent, vous devez créer le **point de terminaison de délégation**. Il doit effectuer certaines actions :

1. Recevoir une demande au format suivant :
   
   > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&amp;returnUrl={URL of source page}&amp;salt={string}&amp;sig={string}*
   > 
   > 
   
    Paramètres de requête pour le cas connexion/inscription :
   
   * **operation** : identifie le type de demande de délégation. Il ne peut s’agir ici que de **SignIn**
   * **returnUrl**: URL de la page dans laquelle l’utilisateur a cliqué sur un lien de connexion ou d’inscription
   * **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
   * **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.
2. Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculez un code de hachage HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **returnUrl** et **salt** ([exemple de code ci-dessous]) :
     
     > HMAC(**salt** + ’\n’ + **returnUrl**)
     > 
     > 
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.
3. Vérifiez que vous recevez une demande de connexion/d’inscription : le paramètre de requête **operation** sera défini sur « **SignIn** ».
4. Présentez à l’utilisateur l’interface de connexion ou d’inscription.
5. Si l'utilisateur s'inscrit, vous devez créer un compte dans Gestion des API. [Créez un utilisateur] avec l'API REST de gestion de Gestion des API. Lors de cette opération, assurez-vous de bien définir l'identifiant utilisateur afin qu'il soit identique à celui de votre magasin utilisateur ou à un identifiant que vous pouvez suivre.
6. Lorsque l'utilisateur est bien authentifié :
   
   * [Demandez un jeton d'authentification unique (SSO)] via l'API REST Gestion des API.
   * Ajoutez un paramètre de requête returnUrl à l'URL SSO reçue de l'appel d'API ci-dessus :
     
     > Par exemple, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * Redirigez l'utilisateur vers l'URL générée.

En plus de l’opération **SignIn**, vous pouvez également effectuer la gestion des comptes en suivant les étapes précédentes et en utilisant l’une des opérations suivantes :

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Vous devez transmettre les paramètres de requête suivants pour les opérations de gestion de compte.

* **operation**: identifie le type de demande de délégation dont il s’agit (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: id d’utilisateur du compte à gérer
* **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
* **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.

## <a name="delegate-product-subscription"></a>Délégation de l’abonnement aux produits
La délégation de l'abonnement aux produits fonctionne de la même manière que la délégation de la connexion/inscription. Le processus final se présente comme suit :

1. Le développeur sélectionne un produit dans le portail des développeurs Gestion des API, puis clique sur le bouton d'abonnement.
2. Le navigateur est redirigé vers le point de terminaison de délégation.
3. Le point de terminaison de délégation effectue les étapes nécessaires pour l'abonnement au produit. Il est de votre responsabilité d'éventuellement rediriger l'utilisateur vers une autre page pour obtenir des informations de facturation, demander des informations supplémentaires ou simplement stocker les informations sans aucune action de l'utilisateur.

Pour activer la fonctionnalité, dans la page **Délégation**, cliquez sur **Déléguer l’abonnement au produit**.

Assurez-vous ensuite que le point de terminaison de délégation effectue bien les actions suivantes :

1. Recevoir une demande au format suivant :
   
   > *http://www.yourwebsite.com/apimdelegation?operation={opération}&amp;productId={produit auquel s’abonner}&amp;userId={utilisateur faisant la demande}&amp;salt={chaîne}&amp;sig={chaîne}*
   > 
   > 
   
    Paramètres de requête pour le cas abonnement à un produit :
   
   * **operation**: identifie le type de demande de délégation. Pour l'abonnement à un produit, les options valides sont les suivantes :
     * « Subscribe » : une demande d’abonnement de l’utilisateur à un produit donné avec l’identifiant fourni (voir ci-dessous).
     * « Unsubscribe » : une demande de désabonnement de l’utilisateur pour un produit.
     * « Renew » : une demande de renouvellement d’abonnement (par exemple, susceptible d’expirer).
   * **productId**: l’ID du produit auquel l’utilisateur demande l’abonnement
   * **userId**: l’ID de l’utilisateur pour qui la demande est envoyée
   * **salt**: chaîne salt spéciale utilisée pour calculer un code de hachage de sécurité.
   * **sig**: code de hachage de sécurité calculé à comparer avec votre propre code de hachage calculé.
2. Vérifiez si la demande émane bien de Gestion des API Azure (facultatif, mais fortement recommandé pour assurer la sécurité).
   
   * Calculez un code de hachage HMAC-SHA512 d’une chaîne basée sur les paramètres de requête **ProductId**, **userId et **salt** :
     
     > HMAC(**salt** + ’\n’ + **productId** + ’\n’ + **userId**)
     > 
     > 
   * Comparez le code de hachage calculé plus haut avec la valeur du paramètre de requête **sig**. Si les deux codes de hachage correspondent, passez à l'étape suivante. Sinon, rejetez la demande.
3. Effectuez le traitement de l’abonnement au produit en fonction du type d’opération demandé dans **operation**, par exemple, facturation, autres questions, etc.
4. Après avoir correctement abonné l'utilisateur au produit de votre côté, abonnez l'utilisateur au produit Gestion des API en [appelant l'API REST pour l'abonnement au produit].

## <a name="delegate-example-code"></a> Exemple de Code
Ces exemples de code montrent comment prendre la *clé de validation de délégation*, définie dans l’écran Délégation du portail du serveur de publication, pour créer un HMAC qui permet ensuite de valider la signature, et fournit la preuve de la validité de l’élément returnUrl transmis. Le même code fonctionne pour productId et userId avec de légères modifications.

****Code C# pour générer le hachage de returnUrl****

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

****Code NodeJS pour générer le hachage de returnUrl****

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>étapes suivantes
Pour plus d’informations sur la délégation, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Demandez un jeton d'authentification unique (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/User/GenerateSsoUrl
[create a user]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[appelant l'API REST pour l'abonnement au produit]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[exemple de code ci-dessous]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
