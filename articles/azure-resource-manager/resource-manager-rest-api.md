---
title: API REST Resource Manager | Microsoft Docs
description: "Une vue d’ensemble des exemples d’authentification et de cas d’utilisation des API REST Resource Manager"
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/13/2017
ms.author: navale;tomfitz;
ms.openlocfilehash: 2f7ba23775545637de865f9ef63680ae22c62164
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2018
---
# <a name="resource-manager-rest-apis"></a>API REST Resource Manager
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Interface de ligne de commande Azure](xplat-cli-azure-resource-manager.md)
> * [Portail](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Derrière chaque appel à Azure Resource Manager, derrière chaque modèle déployé, derrière chaque compte de stockage configuré se trouvent un ou plusieurs appels à une API RESTful de l’Azure Resource Manager. Cette rubrique est consacrée à ces API et à la manière dont vous pouvez les appeler sans utiliser aucun kit SDK. Cette approche est utile si vous souhaitez un contrôle total des requêtes à Azure ou si le kit SDK pour votre langue par défaut n’est pas disponible ou ne prend pas en charge les opérations dont vous avez besoin.

Cet article ne traite pas chaque API exposée dans Azure, mais en utilise certaines comme exemple pour vous montrer comment vous y connecter. Si vous comprenez les notions de base, vous pouvez lire la [Référence de l’API REST Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) (en anglais) pour trouver des informations détaillées sur la manière d’utiliser les autres API.

## <a name="authentication"></a>Authentification
L’authentification pour Resource Manager est gérée par Azure Active Directory (AD). Pour vous connecter à une API quelconque, vous devez tout d’abord vous authentifier auprès d’Azure AD pour recevoir un jeton d’authentification que vous pouvez transmettre à chaque requête. Comme nous décrivons un appel pur directement à l’API REST, nous partons du principe que vous ne souhaitez pas vous authentifier en étant invité à entrer un nom d’utilisateur et un mot de passe. Nous supposons également que vous n’utilisez pas les mécanismes d’authentification à deux facteurs. Pour cette raison, nous créons ce que l’on appelle une application Azure AD et un principal du service qui sont utilisés pour la connexion. Mais n’oubliez pas qu’Azure AD prend en charge plusieurs procédures d’authentification et qu’elles peuvent toutes être utilisées pour récupérer le jeton d’authentification dont nous avons besoin pour les requêtes API ultérieures.
Pour connaître la procédure détaillée, suivez les instructions figurant dans [Créer une application Azure AD et un principal du service](resource-group-create-service-principal-portal.md).

### <a name="generating-an-access-token"></a>Génération d’un jeton d’accès
L’authentification auprès d’Azure AD est effectuée en appelant Azure AD à l’adresse login.microsoftonline.com. Pour vous authentifier, vous devez disposer des informations suivantes :

* l’ID de client Azure AD (le nom de l’Azure AD que vous utilisez pour vous connecter, souvent celui de votre entreprise, mais pas nécessairement) ;
* l’ID de l’application (récupéré au cours de l’étape de création de l’application Azure AD) ;
* le mot de passe (que vous avez choisi lors de la création de l’application Azure AD).

Dans la requête HTTP suivante, veillez à remplacer l’« Azure AD Tenant ID (ID de client Azure AD) », l’« Application ID (ID de l’application) » et le « Password (Mot de passe) » par des valeurs correctes.

**Requête HTTP générique :**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... générera (une fois l’authentification réussie) une réponse similaire à celle-ci :

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(L’access_token dans la réponse précédente a été raccourci pour une meilleure lisibilité)

**Génération du jeton d’accès à l’aide d’un interpréteur de commandes (Bash) :**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net/&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Génération du jeton d’accès à l’aide de PowerShell :**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La réponse contient un jeton d’accès, des informations sur la durée de validité du jeton est valide et sur la ressource pour laquelle vous pouvez utiliser ce jeton.
Le jeton d’accès que vous avez reçu dans le précédent appel HTTP doit être transmis pour toutes les demandes à l’API Resource Manager. Vous le transmettez comme une valeur d’en-tête nommée « Authorization » avec la valeur « Bearer YOUR_ACCESS_TOKEN ». Notez l’espace entre « Bearer » et votre jeton d’accès.

Comme vous pouvez le voir à partir du résultat HTTP ci-dessus, le jeton est valide pendant un délai spécifique au cours duquel vous devez mettre en cache et réutiliser ce même jeton. Même s’il est possible de s’authentifier auprès d’Azure AD pour chaque appel d’API, cette procédure serait peu efficace.

## <a name="calling-resource-manager-rest-apis"></a>Appel d'API REST Resource Manager
Cette rubrique utilise uniquement quelques API pour expliquer l’utilisation basique des opérations REST. Pour plus d’informations sur la totalité des opérations, consultez la rubrique [API REST Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).

### <a name="list-all-subscriptions"></a>Répertorier tous les abonnements
L’une des opérations plus simples à exécuter consiste à répertorier les abonnements disponibles auxquels vous pouvez accéder. Dans la requête suivante, vous pouvez voir comment le jeton d’accès est transmis en tant qu’en-tête :

(Remplacez YOUR_ACCESS_TOKEN par votre véritable jeton d’accès.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

... et vous obtenez comme résultat une liste des abonnements auxquels ce principal du service est autorisé à accéder

(Les ID d’abonnement ont été raccourcis pour une meilleure lisibilité)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Répertorier tous les groupes de ressources dans un abonnement spécifique
Toutes les ressources disponibles avec les API Resource Manager sont imbriquées dans un groupe de ressources. Vous pouvez interroger Resource Manager pour connaître les groupes de ressources existants dans votre abonnement à l’aide de la requête HTTP GET suivante. Notez que, cette fois, l’ID d’abonnement est transmis comme élément de l’URL.

(Remplacez YOUR_ACCESS_TOKEN et SUBSCRIPTION_ID par vos véritables jetons d’accès et ID d’abonnement)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La réponse dépend de la définition ou non de groupes de ressources définis et, le cas échéant, de leur nombre.

(Les ID d’abonnement ont été raccourcis pour une meilleure lisibilité)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Jusqu’ici, nous avons interrogé uniquement les API Resource Manager pour obtenir des informations. Il est temps de créer des ressources, en commençant par la plus simple de toutes : un groupe de ressources. La demande HTTP suivante crée un groupe de ressources dans une région/un emplacement de votre choix et y ajoute une balise.

(Remplacez YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID et RESOURCE_GROUP_NAME par votre jeton d’accès réel, votre ID d’abonnement et nom du groupe de ressources à créer)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Vous avez créé un groupe de ressources dans Azure ! Félicitations !

### <a name="deploy-resources-to-a-resource-group-using-a-resource-manager-template"></a>Déployer des ressources dans un groupe de ressources à l’aide d’un modèle Resource Manager
Avec Resource Manager, vous pouvez déployer vos ressources à l’aide de modèles. Un modèle définit plusieurs ressources et leurs dépendances. Pour cette section, nous supposons que vous êtes familiarisé avec les modèles Resource Manager et vous montrons simplement comment effectuer l’appel d’API pour en commencer le déploiement. Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

Le déploiement d’un modèle ne diffère pas beaucoup de la manière dont vous appelez d’autres API. Un aspect important est le fait que le déploiement d’un modèle peut prendre beaucoup de temps. L’appel de l’API retourne et c’est à vous, en tant que développeur, de déterminer l’état du déploiement pour savoir à quel moment le déploiement s’effectue. Pour plus d’informations, consultez la rubrique [Suivre les opérations asynchrones Azure](resource-manager-async-operations.md).

Pour cet exemple, nous utilisons un modèle exposé publiquement disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates). Le modèle que nous utilisons déploie une machine virtuelle Linux pour la région États-Unis de l'Ouest. Bien que cet exemple utilise un modèle disponible dans un référentiel public tel que GitHub, vous pouvez transmettre le modèle complet dans le cadre de la requête. Notez que nous fournissons des valeurs de paramètre dans la requête qui sera utilisée dans le modèle déployé.

(Remplacez SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME,ADMIN_PASSWORD et DNS_NAME_FOR_PUBLIC_IP par des valeurs appropriées pour votre requête)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La réponse JSON longue pour cette requête a été omise afin d’améliorer la lisibilité de cette documentation. La réponse contient des informations sur le déploiement basé sur un modèle que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la gestion des opérations REST asynchrones, consultez [Suivre les opérations asynchrones Azure](resource-manager-async-operations.md).
