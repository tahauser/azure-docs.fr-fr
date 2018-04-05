---
title: Protéger une API à l’aide d’OAuth 2.0 avec Azure Active Directory et Gestion des API | Microsoft Docs
description: Découvrez comment protéger un backend d’API web avec Azure Active Directory et Gestion des API.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 3caa3d2b8640c83f1001aeac3b0a5e9ada143183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Guide pratique pour protéger une API à l’aide d’OAuth 2.0 avec Azure Active Directory et Gestion des API

Ce guide montre comment configurer votre instance de Gestion des API (APIM) pour protéger une API à l’aide du protocole OAuth 2.0 avec Azure Active Directory (AAD). 

## <a name="prerequisite"></a>Prérequis
Pour suivre les étapes décrites dans cet article, vous devez avoir :
* Une instance APIM
* Une API publiée à l’aide de l’instance APIM
* Un locataire Azure AD

## <a name="overview"></a>Vue d'ensemble

Ce guide montre comment protéger une API avec OAuth 2.0 dans APIM. Dans cet article, nous utilisons Azure AD comme serveur d’autorisation (OAuth). Voici un petit aperçu des étapes :

1. Inscrire une application (backend-app) dans Azure AD pour représenter l’API
2. Inscrire une autre application (client-app) dans Azure AD pour représenter une application cliente qui doit appeler l’API
3. Dans Azure AD, accorder des autorisations pour permettre à client-app d’appeler backend-app
4. Configurer la console de développeur pour utiliser l’autorisation utilisateur OAuth 2.0
5. Ajouter une stratégie validate-jwt afin de valider le jeton OAuth pour chaque requête entrante

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Inscrire une application dans Azure AD pour représenter l’API

Pour protéger une API avec Azure AD, la première étape consiste à inscrire dans Azure AD une application qui représente l’API. 

Accédez à votre locataire Azure AD, puis accédez à **Inscriptions des applications**.

Sélectionnez **Nouvelle inscription d’application**. 

Spécifiez le nom de l’application. Dans cet exemple, nous utilisons `backend-app`.  

Choisissez **Application web/API** comme **Type d’application**. 

Pour **URL de connexion**, vous pouvez utiliser `https://localhost` comme espace réservé.

Cliquez sur **Créer**.

Une fois l’application créée, prenez note de l’**ID d’application** ; vous en aurez besoin lors d’une étape ultérieure. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Inscrire une autre application dans Azure AD pour représenter une application cliente

Chaque application cliente qui doit appeler l’API doit également être inscrite en tant qu’application dans Azure AD. Dans ce guide, nous utiliserons la console de développeur dans le portail des développeurs APIM comme exemple d’application cliente. 

Nous devons inscrire une autre application dans Azure AD pour représenter la console de développeur.

Cliquez à nouveau sur **Nouvelle inscription d’application**. 

Spécifiez le nom de l’application et choisissez **Application web/API** comme **Type d’application**. Dans cet exemple, nous utilisons `client-app`.  

Pour **URL de connexion**, vous pouvez utiliser `https://localhost` comme espace réservé ou utiliser l’URL de connexion de votre instance APIM. Dans cet exemple, nous utilisons `https://contoso5.portal.azure-api.net/signin`.

Cliquez sur **Créer**.

Une fois l’application créée, prenez note de l’**ID d’application** ; vous en aurez besoin lors d’une étape ultérieure. 

Nous devons maintenant créer un secret client pour cette application, que nous utiliserons lors d’une étape ultérieure.

Cliquez à nouveau sur **Paramètres** et accédez à **Clés**.

Sous **Mots de passe**, spécifiez une **Description de la clé**, choisissez quand la clé doit expirer et cliquez sur **Enregistrer**.

Prenez note de la valeur de la clé. 

## <a name="grant-permissions-in-aad"></a>Accorder des autorisations dans AAD

Maintenant que nous avons inscrit deux applications pour représenter l’API (autrement dit, backend-app) et la console de développeur (autrement dit, client-app), nous devons accorder des autorisations pour permettre à client-app d’appeler backend-app.  

Accédez à **Inscriptions des applications**. 

Cliquez sur `client-app` et accédez à **Paramètres**.

Cliquez sur **Autorisations requises**, puis sur **Ajouter**.

Cliquez sur **Sélectionner une API** et recherchez `backend-app`.

Cochez `Access backend-app` sous **Autorisations déléguées**. 

Cliquez sur **Sélectionner**, puis sur **Terminé**. 

> [!NOTE]
> Si **Windows** **Azure Active Directory** n’est pas listé sous les autorisations d’autres applications, cliquez sur **Ajouter** et ajoutez-le à partir de la liste.
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Activer l’autorisation utilisateur OAuth 2.0 dans la console de développeur

À ce stade, nous avons créé nos applications dans Azure AD et nous avons accordé les autorisations appropriées pour que client-app puisse appeler backend-app. 

Dans ce guide, nous utiliserons la console de développeur comme client-app. Les étapes ci-dessous décrivent comment activer l’autorisation utilisateur OAuth 2.0 dans la console de développeur. 

Accédez à votre instance APIM.

Cliquez sur **OAuth 2.0**, puis sur **Ajouter**.

Spécifiez un **Nom d’affichage** et une **Description**.

Pour l’URL de la page d’inscription client,** entrez une valeur d’espace réservé telle que `http://localhost`.  L’**URL de la page d'inscription client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs OAuth 2.0 qui prennent en charge la gestion des comptes utilisateur. Dans cet exemple, les utilisateurs ne peuvent pas créer et configurer leurs propres comptes. Un espace réservé est donc utilisé.

Sélectionnez **Code d’autorisation** comme **Types d’octroi d’autorisation**.

Ensuite, spécifiez l’**URL de point de terminaison d’autorisation** et l’**URL de point de terminaison de jeton**.

Ces valeurs peuvent être récupérées à partir de la page **Points de terminaison** dans votre locataire Azure AD. Pour accéder aux points de terminaison, revenez à la page **Inscriptions des applications**, puis cliquez sur **Points de terminaison**.

Copiez la valeur de **Point de terminaison d’autorisation OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison d’autorisation**.

Copiez la valeur de **Point de terminaison de jeton OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison de jeton**.

En plus de coller le point de terminaison de jeton, ajoutez un paramètre de corps supplémentaire nommé **resource** et, comme valeur, utilisez l’**ID d’application** de backend-app.

Ensuite, spécifiez les informations d’identification du client. Il s’agit des informations d’identification de client-app.

Pour **ID client**, utilisez l’**ID d’application** de client-app.

Pour **Secret client**, utilisez la clé que vous avez créée pour client-app. 

Juste après le secret client figure la valeur **redirect_url** pour le type d’octroi du code d’autorisation.

Notez cette URL.

Cliquez sur **Créer**.

Revenez à la page **Paramètres** de votre client-app.

Cliquez sur **URL de réponse** et collez la valeur **redirect_url** sur la première ligne. Dans cet exemple, nous avons remplacé `https://localhost` par l’URL sur la première ligne.  

Maintenant que nous avons configuré un serveur d’autorisation OAuth 2.0, la console de développeur doit pouvoir obtenir des jetons d’accès à partir d’Azure AD. 

L’étape suivante consiste à activer l’autorisation utilisateur OAuth 2.0 pour notre API, afin que la console de développeur sache qu’elle doit obtenir un jeton d’accès pour le compte de l’utilisateur avant d’effectuer des appels à notre API.

Accédez à **API** dans votre instance APIM.

Cliquez sur l’API que vous souhaitez protéger. Dans cet exemple, nous utiliserons `Echo API`.

Accédez à **Paramètres**.

Sous **Sécurité**, choisissez **OAuth 2.0** et sélectionnez le serveur OAuth 2.0 que nous avons configuré précédemment. 

Cliquez sur **Enregistrer**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>Appel de l’API à partir du portail des développeurs

Maintenant que l’autorisation utilisateur OAuth 2.0 est activée sur `Echo API`, la console de développeur obtient un jeton d’accès pour le compte de l’utilisateur avant d’appeler l’API.

Accédez à une opération sous `Echo API` dans le portail des développeurs et cliquez sur **Essayer**. Vous accéderez alors à la console de développeur.

Observez le nouvel élément dans la section **Autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

Sélectionnez **Code d’autorisation** dans la liste déroulante d’autorisations. Vous êtes alors invité à vous connecter au locataire Azure AD. Si vous êtes déjà connecté avec ce compte, il est possible qu’aucune invite ne s’affiche.

Une fois la connexion établie, un en-tête `Authorization` est ajouté à la requête avec un jeton d’accès d’Azure AD. 

Voici un exemple de jeton, codé en Base64.

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

Cliquez sur **Envoyer** ; vous devriez pouvoir appeler l’API.


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Configurer une stratégie de validation JWT pour pré-autoriser des requêtes

À ce stade, quand un utilisateur tente d’effectuer un appel à partir de la console de développeur, il est invité à se connecter et la console de développeur obtient un jeton d’accès pour son compte. Tout fonctionne comme prévu. Toutefois, que se passe-t-il si quelqu’un appelle notre API sans jeton ou avec un jeton non valide ? Par exemple, vous essayez de supprimer l’en-tête `Authorization` et vous vous rendez compte que vous pouvez toujours appeler l’API. Cela est dû au fait qu’APIM ne valide pas le jeton d’accès à ce stade. Il transmet l’en-tête `Auhtorization` à l’API backend.

Nous pouvons utiliser la stratégie [Validate JWT](api-management-access-restriction-policies.md#ValidateJWT) pour pré-autoriser les requêtes dans APIM en validant les jetons d’accès de chaque requête entrante. Si une requête n’a pas de jeton valide, elle est bloquée par Gestion des API et n’est pas transmise au backend. Nous pouvons ajouter la stratégie ci-dessous à `Echo API`. 

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
* Pour les autres méthodes permettant de sécuriser votre service backend, consultez [Authentification mutuelle des certificats](api-management-howto-mutual-certificates.md).

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
