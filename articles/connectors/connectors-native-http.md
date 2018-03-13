---
title: Communiquer avec un point de terminaison via HTTP - Azure Logic Apps | Microsoft Docs
description: "Créer des applications logiques qui peuvent communiquer avec n’importe quel point de terminaison via HTTP"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3eae7a4a47680fc36849fd413b76a80865cf3c9f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-http-action"></a>Prise en main de l’action HTTP

Avec l’action HTTP, vous pouvez étendre les workflows pour votre organisation et communiquer avec n’importe quel point de terminaison par le biais de HTTP.

Vous pouvez :

* Créez des workflows d’application logique qui s’activent (se déclenchent) lors d’une défaillance d’un site Web que vous gérez.
* Communiquez avec n’importe quel point de terminaison par le biais de HTTP afin d’étendre vos workflows à d’autres services.

Pour commencer à utiliser l’action HTTP dans une application logique, consultez [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-trigger"></a>Utilisation du déclencheur HTTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration du déclencheur HTTP dans le concepteur d’application logique.

1. Ajoutez le déclencheur HTTP dans votre application logique.
2. Renseignez les paramètres du point de terminaison HTTP que vous souhaitez interroger.
3. Modifiez l’intervalle de périodicité sur la fréquence d’interrogation souhaitée.

   L’application logique se déclenche maintenant avec n’importe quel contenu retourné lors de chaque vérification.

   ![Déclencheur HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Fonctionnement du déclencheur HTTP

Le déclencheur HTTP effectue un appel sur un point de terminaison HTTP selon un intervalle récurrent. Par défaut, tout code de réponse HTTP inférieur à 300 entraîne l’exécution d’une application logique. Pour spécifier si l’application logique doit se déclencher, vous pouvez modifier l’application logique en mode code et ajouter une condition qui prend une valeur donnée après l’appel HTTP. Voici un exemple de déclencheur HTTP qui se déclenche chaque fois que le code d’état renvoyé est supérieur ou égal à `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Consultez [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)pour obtenir des informations complètes sur les paramètres du déclencheur HTTP.

## <a name="use-the-http-action"></a>Utilisation de l’action HTTP

Une action est une opération effectuée par le flux de travail défini dans une application logique. 
[Apprenez-en davantage sur les actions](connectors-overview.md).

1. Choisissez **Nouvelle étape** > **Ajouter une action**.
3. Dans la zone de recherche Action , **http** pour répertorier les actions HTTP.
   
    ![Sélection de l’action HTTP](./media/connectors-native-http/using-action-1.png)

4. Ajoutez tout paramètre nécessaire à l’appel HTTP.
   
    ![Exécution de l’action HTTP](./media/connectors-native-http/using-action-2.png)

5. Dans la barre d’outils du concepteur, cliquez sur **Enregistrer**. Votre application logique est enregistrée et publiée (activée) en même temps.

## <a name="http-trigger"></a>Déclencheur HTTP
Voici les détails du déclencheur que ce connecteur prend en charge. Le connecteur HTTP possède un déclencheur.

| Déclencheur | DESCRIPTION |
| --- | --- |
| HTTP |Exécute un appel HTTP et renvoie le contenu de la réponse. |

## <a name="http-action"></a>Action HTTP
Voici les détails de l’action que ce connecteur prend en charge. Le connecteur HTTP n’a qu’une seule action possible.

| Action | DESCRIPTION |
| --- | --- |
| HTTP |Exécute un appel HTTP et renvoie le contenu de la réponse. |

## <a name="http-details"></a>Détails HTTP
Les tableaux suivants décrivent les champs de saisie obligatoires et facultatifs pour l’action, ainsi que les détails des résultats correspondants associés à son utilisation.

#### <a name="http-request"></a>Demande HTTP
Vous trouverez ci-dessous les champs de saisie de l’action permettant de générer une demande HTTP sortante.
Le symbole * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | DESCRIPTION |
| --- | --- | --- |
| Method (Méthode)* |statique |Verbe HTTP à utiliser |
| URI* |URI |URI de la requête HTTP |
| En-têtes |headers |Un objet JSON d’en-têtes HTTP à inclure |
| body |Corps |Le texte de la requête HTTP |
| Authentification |Authentification |Détails contenus dans la section [Authentification](#authentication) |

<br>

#### <a name="output-details"></a>Détails des résultats
Vous trouverez ci-dessous les détails de sortie correspondant à la requête HTTP.

| Nom de la propriété | Type de données | DESCRIPTION |
| --- | --- | --- |
| headers |objet |En-têtes de réponse |
| body |objet |Objet Réponse |
| Code d’état |int |Code d'état HTTP |

## <a name="authentication"></a>Authentification
La fonctionnalité Logic Apps vous permet d’utiliser différents types d’authentification sur vos points de terminaison HTTP. Vous pouvez utiliser cette authentification avec les connecteurs **HTTP**, **[HTTP + Swagger](connectors-native-http-swagger.md)** et **[HTTP Webhook](connectors-native-webhook.md)**. Les types d’authentification suivants sont configurables :

* [Authentification de base](#basic-authentication)
* [Authentification par certificat client](#client-certificate-authentication)
* [Authentification OAuth Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Authentification de base

L’objet d’authentification suivant est obligatoire pour l’authentification de base.
Le symbole * désigne est un champ obligatoire.

| Nom de la propriété | Type de données | DESCRIPTION |
| --- | --- | --- |
| Entrez* |Type |Type d’authentification (doit être `Basic` dans le cas d’une authentification de base) |
| Nom d’utilisateur* |username |Nom d’utilisateur utilisé pour l’authentification |
| Mot de passe* |password |Mot de passe à authentifier |

> [!TIP]
> Si vous souhaitez utiliser un mot de passe qui ne peut pas être récupéré à partir de la définition, utilisez un paramètre `securestring` et la `@parameters()` 
> [fonction de définition de flux de travail](http://aka.ms/logicappdocs).

Par exemple : 

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Authentification par certificat client

L’objet d’authentification suivant est requis pour l’authentification du certificat client. Le symbole * désigne est un champ obligatoire.

| Nom de la propriété | Type de données | DESCRIPTION |
| --- | --- | --- |
| Entrez* |Type |Type d’authentification (doit être `ClientCertificate` pour les certificats client SSL) |
| PFX* |pfx |Contenu codé en Base64 du fichier Personal Information Exchange (PFX) |
| Mot de passe* |password |Mot de passe d’accès au fichier PFX |

> [!TIP]
> Pour utiliser un paramètre qui ne sera pas lisible dans la définition après l’enregistrement de votre application logique, vous pouvez utiliser un paramètre `securestring` et la `@parameters()` 
> [fonction de définition de flux de travail](http://aka.ms/logicappdocs).

Par exemple : 

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Authentification OAuth Azure AD
L’objet d’authentification suivant est obligatoire pour l’authentification OAuth Azure AD. Le symbole * désigne est un champ obligatoire.

| Nom de la propriété | Type de données | DESCRIPTION |
| --- | --- | --- |
| Entrez* |Type |Type d’authentification (doit être `ActiveDirectoryOAuth` dans le cas d’une authentification OAuth Azure AD) |
| Locataire* |locataire |L’identifiant de locataire pour le locataire Azure AD |
| Public ciblé* |audience |Ressource pour laquelle vous demandez une autorisation d’utilisation. Par exemple : `https://management.core.windows.net/` |
| ID de client* |clientId |Identifiant client de l’application Azure AD |
| Secret* |secret |Phrase secrète du client qui demande le jeton |

> [!TIP]
> Vous pouvez utiliser un paramètre `securestring` et la [fonction de définition de flux de travail](http://aka.ms/logicappdocs) `@parameters()` pour utiliser un paramètre qui ne sera pas lisible dans la définition après l’enregistrement.
> 
> 

Par exemple : 

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Étapes suivantes
Essayez maintenant la plateforme et [créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).

