---
title: 'Azure Active Directory B2C : Migrer des utilisateurs avec des identités sociales'
description: Traite des concepts fondamentaux sur la migration des utilisateurs avec des identités sociales dans Azure AD B2C à l’aide de l’API Graph
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/03/2018
ms.author: davidmu
ms.openlocfilehash: 76ed4dac40872bf6db07b26c5805a4db62dc9dfc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C : Migrer des utilisateurs avec des identités sociales
Quand vous envisagez de migrer votre fournisseur d’identité vers Azure AD B2C, vous devrez peut-être également migrer les utilisateurs avec des identités sociales. Cet article explique comment migrer les comptes d’identités sociales existants, tels que les comptes Google, Microsoft, Facebook et LinkedIn, à Azure AD B2C. Cet article s’applique également aux identités fédérées, quoique ces migrations soient moins courantes.

## <a name="prerequisites"></a>Prérequis

Cet article est la suite de celui sur la migration des utilisateurs. Il est axé sur la migration des identités sociales. Avant de commencer, lisez [Migration utilisateur](active-directory-b2c-user-migration.md).

## <a name="social-identities-migration-introduction"></a>Présentation de la migration des identités sociales

* Dans Azure AD B2C, les noms de connexion des **comptes locaux** (nom d’utilisateur ou adresse e-mail) sont stockés dans la collection `signInNames` dans l’enregistrement utilisateur. `signInNames` contient un ou plusieurs enregistrements `signInName` qui spécifient les noms de connexion pour l’utilisateur. Chaque nom de connexion doit être unique dans le locataire.

* Les identités des **comptes sociaux** sont stockées dans la collection `userIdentities`. L’entrée spécifie l’`issuer` (nom du fournisseur d’identité), tel que facebook.com, et l’`issuerUserId`, qui est un identificateur d’utilisateur unique pour l’émetteur. L’attribut `userIdentities` contient un ou plusieurs enregistrements UserIdentity qui spécifient le type de compte social et l’identificateur d’utilisateur unique du fournisseur d’identité sociale.

* **Combiner un compte local avec une identité sociale**. Comme mentionné plus haut, les noms de connexion des comptes locaux et les identités de comptes sociaux sont stockés dans des attributs différents. `signInNames` est utilisé pour le compte local, tandis que `userIdentities` est utilisé pour le compte social. Un compte Azure AD B2C unique peut être un compte local uniquement, un compte social uniquement, ou il peut combiner un compte local avec une identité sociale dans un enregistrement utilisateur. Ce comportement vous permet de gérer un seul compte, l’utilisateur pouvant se connecter avec les informations du compte local ou avec les identités sociales.

* Type `UserIdentity` : contient des informations sur l’identité d’un utilisateur de compte social dans un locataire Azure AD B2C :
    * `issuer` : représentation sous forme de chaîne du fournisseur d’identité qui a émis l’identificateur d’utilisateur, par exemple facebook.com.
    * `issuerUserId` : identificateur d’utilisateur unique utilisé par le fournisseur d’identité sociale au format base64.

    ```JSON
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
    ```

* En fonction du fournisseur d’identité, l’**ID d’utilisateur social** est une valeur unique pour un utilisateur donné `per application` ou un compte de développement. Configurez la stratégie Azure AD B2C avec le même ID d’application que celui précédemment attribué par le fournisseur d’identité sociale. Ou une autre application `within the same development account`.

## <a name="use-graph-api-to-migrate-users"></a>Utiliser l’API Graph pour migrer des utilisateurs
Vous créez le compte d’utilisateur Azure AD B2C par le biais de l’[API Graph](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet). Pour communiquer avec l’API Graph, vous devez d’abord disposer d’un compte de service avec des privilèges Administrateur. Dans Azure AD, vous inscrivez une application et une authentification auprès d’Azure AD. Les informations d’identification de l’application sont ID de l’application et Secret de l’application. L’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph. Suivez les instructions fournies à l’étape 1 de l’article [Migration utilisateur](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-user-migration#step-1-use-graph-api-to-migrate-users).

## <a name="required-properties"></a>Propriétés requises
La liste suivante présente les propriétés qui sont requises quand vous créez un utilisateur.
* **accountEnabled** : true
* **displayName** : nom à afficher dans le carnet d’adresses pour l’utilisateur.
* **passwordProfile** : profil de mot de passe pour l’utilisateur. 

> [!NOTE]
> Pour les comptes sociaux uniquement (sans informations d’identification de comptes locaux), vous devez toujours spécifier le mot de passe. Azure AD B2C ignore le mot de passe que vous spécifiez pour les comptes sociaux.

* **userPrincipalName** : nom d’utilisateur principal (someuser@contoso.com). Le nom d’utilisateur principal doit contenir l’un des domaines vérifiés du locataire. Pour spécifier le nom UPN, générez une nouvelle valeur GUID et concaténez-la avec `@` et le nom de votre locataire.
* **mailNickname** : alias de messagerie de l’utilisateur. Cette valeur peut être le même ID que celui utilisé pour userPrincipalName. 
* **signInNames** : un ou plusieurs enregistrements SignInName qui spécifient les noms de connexion pour l’utilisateur. Chaque nom de connexion doit être unique dans l’entreprise/le locataire. Pour les comptes sociaux uniquement, cette propriété peut être laissée vide.
* **userIdentities** : un ou plusieurs enregistrements UserIdentity qui spécifient le type de compte social et l’identificateur d’utilisateur unique du fournisseur d’identité sociale.
* [facultatif] **otherMails** : pour les comptes sociaux uniquement, il s’agit des adresses e-mail de l’utilisateur. 

Pour plus d’informations, consultez la [référence de l’API Graph](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser).

## <a name="migrate-social-account-only"></a>Migrer un compte social (uniquement)
Pour créer uniquement un compte social, sans informations d’identification de compte local, envoyez une requête POST HTTPS à l’API Graph. Le corps de la requête contient les propriétés de l’utilisateur du compte social à créer. Au minimum, vous devez spécifier les propriétés requises. 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envoyer les données de formulaire suivantes : 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>Migrer un compte social avec un compte local
Pour créer un compte local combiné avec des identités sociales, envoyez une requête POST HTTPS à l’API Graph. Le corps de la requête contient les propriétés de l’utilisateur du compte social à créer. Au minimum, vous devez spécifier les propriétés requises. 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

Envoyez les données de formulaire suivantes : 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)
### <a name="how-can-i-know-the-issuer-name"></a>Comment puis-je connaître le nom de l’émetteur ?
Le nom de l’émetteur, ou le nom du fournisseur d’identité, est configuré dans votre stratégie. Si vous ne savez pas quelle valeur spécifier dans `issuer`, effectuez les étapes suivantes :
1. Connectez-vous avec l’un des comptes sociaux.
2. À partir du jeton JWT, copiez la valeur `sub`. `sub` contient généralement l’ID d’objet de l’utilisateur dans Azure AD B2C. Ou, à partir du portail Azure, ouvrez les propriétés de l’utilisateur et copiez l’ID d’objet.
3. Ouvrez l’[Explorateur Azure AD Graph](https://graphexplorer.azurewebsites.net).
4. Connectez-vous avec vos informations d’administrateur. N
5. Exécutez la requête GET suivante. Remplacez userObjectId par l’ID d’utilisateur que vous avez copié. **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Recherchez l’élément `userIdentities` à l’intérieur du code JSON retourné à partir d’Azure AD B2C.
7. [Facultatif] Vous souhaiterez peut-être aussi décoder la valeur `issuerUserId`.

> [!NOTE]
> Utilisez un compte d’administrateur de locataire B2C qui est local pour le locataire B2C. La syntaxe du nom du compte est admin@tenant-name.onmicrosoft.com.

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>Est-il possible d’ajouter une identité sociale à un compte local existant ?
Oui. Vous pouvez ajouter l’identité sociale après avoir créé le compte local. Exécutez une requête HTTPS PATCH. Remplacez userObjectId par l’ID d’utilisateur que vous souhaitez mettre à jour. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envoyez les données de formulaire suivantes : 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>Est-il possible d’ajouter plusieurs identités sociales ?
Oui. Vous pouvez ajouter plusieurs identités sociales pour un même compte Azure AD B2C. Exécutez une requête HTTPS PATCH. Remplacez userObjectId par l’ID d’utilisateur. 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

Envoyez les données de formulaire suivantes : 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[Facultatif] Exemple d’application de migration d’utilisateur
[Télécharger et exécuter l’exemple d’application V2](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration). L’exemple d’application V2 utilise un fichier JSON qui contient des données utilisateur factices, notamment compte local, compte social et identités locales & sociales dans un même compte.  Pour modifier le fichier JSON, ouvrez la solution Visual Studio `AADB2C.UserMigration.sln`. Dans le projet `AADB2C.UserMigration`, ouvrez le fichier `UsersData.json`. Le fichier contient une liste d’entités d’utilisateur. Chaque entité d’utilisateur présente les propriétés suivantes :
* **signInName** : pour le compte local, adresse e-mail avec laquelle se connecter.
* **displayName** : nom complet de l’utilisateur.
* **firstName** : prénom de l’utilisateur.
* **lastName** : nom de famille de l’utilisateur.
* **mot de passe** : pour le compte local, mot de passe de l’utilisateur (peut être vide).
* **émetteur** : pour le compte social, nom du fournisseur d’identité.
* **issuerUserId** : pour le compte social, identificateur d’utilisateur unique utilisé par le fournisseur d’identité sociale. La valeur doit être en texte clair. L’exemple d’application encode cette valeur en base64.
* **email** : pour le compte social uniquement (non combiné), adresse e-mail de l’utilisateur.

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> Si vous ne mettez pas à jour le fichier UsersData.json dans l’exemple avec vos données, vous pouvez vous connecter avec les exemples d’informations d’identification de compte local, mais pas avec les exemples de compte sociaux. Pour migrer vos comptes sociaux, fournissez des données réelles.

Pour plus d’informations sur la façon d’utiliser l’exemple d’application, consultez [Azure Active Directory B2C : migration utilisateur](active-directory-b2c-user-migration.md).
