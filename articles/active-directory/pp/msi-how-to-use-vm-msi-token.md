---
title: "Guide pratique pour utiliser une identité MSI affectée par l’utilisateur pour acquérir un jeton d’accès sur une machine virtuelle"
description: "Instructions pas à pas et exemples montrant comment utiliser une identité MSI affectée par l’utilisateur à partir d’une machine virtuelle Azure pour acquérir un jeton d’accès OAuth."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a9513a59ec4540c6d63236519873c6e1e177b65a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Acquérir un jeton d’accès pour une identité MSI affectée par l’utilisateur sur une machine virtuelle

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Cet article fournit divers exemples de code et de script pour l’acquisition de jeton, ainsi que des conseils sur les rubriques importantes telles que la gestion des erreurs HTTP et des expirations de jeton.

## <a name="prerequisites"></a>configuration requise

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Si vous envisagez d’utiliser les exemples de Azure PowerShell dans cet article, veillez à installer la dernière version de [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - Tous les scripts/exemples de code de cet article supposent que le client exécute une machine virtuelle avec le paramètre MSI activé. Utilisez la fonctionnalité « Se connecter » de machine virtuelle dans le portail Azure, pour vous connecter à distance à votre machine virtuelle. Pour plus d’informations sur l’activation de MSI sur une machine virtuelle, consultez [Configurer une identité MSI (Managed Service Identity) affectée par l’utilisateur pour une machine virtuelle, à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md), ou l’un des autres articles (à l’aide du portail, de PowerShell, d’un modèle ou d’un SDK Azure). 

## <a name="overview"></a>Vue d'ensemble

Une application cliente peut demander un [jeton d’accès d’application uniquement](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) de MSI pour accéder à une ressource donnée. Le jeton est [basé sur le principal du service MSI](msi-overview.md#how-does-it-work). Par conséquent, il n’est pas nécessaire que le client s’inscrive pour obtenir un jeton d’accès sous son propre principal du service. Le jeton peut être utilisé comme un jeton du porteur dans [les appels de service à service nécessitant des informations d’identification du client](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Obtenir un jeton par HTTP](#get-a-token-using-http) | Détails du protocole pour le point de terminaison de jeton de MSI |
| [Obtenir un jeton par CURL](#get-a-token-using-curl) | Exemple d’utilisation du point de terminaison REST de MSI à partir d’un client Bash/CURL |
| [Gestion de l’expiration du jeton](#handling-token-expiration) | Conseils pour la gestion des jetons d’accès expirés |
| [Gestion des erreurs](#error-handling) | Conseils pour la gestion des erreurs HTTP retournées par le point de terminaison de jeton de MSI |
| [ID de ressource pour les services Azure](#resource-ids-for-azure-services) | Où obtenir les ID de ressource pour les services Azure pris en charge |

## <a name="get-a-token-using-http"></a>Obtenir un jeton par HTTP 

L’interface fondamentale pour l’acquisition d’un jeton d’accès est basée sur REST, le rendant accessible à toute application cliente en cours d’exécution sur la machine virtuelle et pouvant effectuer des appels REST par HTTP. Cela est similaire au modèle de programmation Azure AD, sauf si le client utilise un point de terminaison localhost sur la machine virtuelle (à la place d’un point de terminaison Azure AD).

Exemple de demande :

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| Élément | Description |
| ------- | ----------- |
| `GET` | Le verbe HTTP, indiquant votre souhait de récupérer des données du point de terminaison. Dans ce cas, un jeton d’accès OAuth. | 
| `http://localhost:50342/oauth2/token` | Le point de terminaison MSI, où 50342 est le numéro de port par défaut (configurable). |
| `resource` | Un paramètre de chaîne de requête, indiquant l’URI ID d’application de la ressource cible. Il apparaît également dans la revendication `aud` (audience) du jeton émis. Cet exemple demande un jeton pour accéder à Azure Resource Manager, qui possède un URI ID d’application, https://management.azure.com/. |
| `client_id` | Un paramètre de chaîne de requête, indiquant l’ID client (également appelé ID d’application) du principal de service qui représente l’identité MSI affectée par l’utilisateur. Cette valeur est retournée dans la propriété `clientId` lors de la création d’une identité MSI affectée par l’utilisateur. Cet exemple demande un jeton pour l’ID client « 712eac09-e943-418c-9be6-9fd5c91078bl ». |
| `Metadata` | Un champ d’en-tête de requête HTTP, requis par MSI afin de limiter une attaque de falsification de requête côté serveur (SSRF). Cette valeur doit être définie sur « true », en minuscules.

Exemple de réponse :

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Élément | Description |
| ------- | ----------- |
| `access_token` | Le jeton d’accès demandé. Lors de l’appel d’une API REST sécurisée, le jeton est incorporé dans le champ d’en-tête de requête `Authorization` comme un jeton « du porteur », autorisant l’API à authentifier l’appelant. | 
| `expires_in` | Le nombre de secondes pendant lesquelles le jeton d’accès est toujours valide, avant d’expirer, à partir de son émission. L’heure d’émission est accessible dans la revendication `iat` du jeton. |
| `expires_on` | L’intervalle de temps lorsque le jeton d’accès expire. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `exp` du jeton). |
| `not_before` | L’intervalle de temps pendant lequel le jeton d’accès prend effet, et peut être accepté. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `nbf` du jeton). |
| `resource` | La ressource pour laquelle le jeton d’accès a été demandé, correspondant au paramètre de chaîne de requête `resource` de la requête. |
| `token_type` | Le type de jeton, qui est un jeton d’accès « du porteur », ce qui signifie que la ressource peut donner l’accès au porteur de ce jeton. |
| `client_id` | L’ID client (également appelé ID d’application) du principal de service représentant l’identité MSI affectée par l’utilisateur, pour lequel le jeton a été demandé. |

## <a name="get-a-token-using-curl"></a>Obtenir un jeton par CURL

Veillez à remplacer la valeur <MSI CLIENT ID> du paramètre `client_id` par l’ID client (également appelé ID d’application) du principal de service de votre identité MSI affectée par l’utilisateur. Cette valeur est retournée dans la propriété `clientId` lors de la création d’une identité MSI affectée par l’utilisateur.

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Exemples de réponses :

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>Gestion de l’expiration du jeton

Le sous-système MSI local met en cache des jetons. Par conséquent, vous pouvez l’appeler autant de fois que vous le souhaitez et un appel réseau vers Azure AD survient uniquement si :
- une absence dans le cache se produit du fait d’une absence de jeton dans le cache
- le jeton a expiré

Si vous mettez le jeton en cache dans votre code, vous devez être prêt à gérer les scénarios dans lesquels la ressource indique que le jeton a expiré.

## <a name="error-handling"></a>Gestion des erreurs 

Le point de terminaison MSI signale des erreurs via le champ de code d’état de l’en-tête du message de la réponse HTTP en tant qu’erreurs 4xx ou 5xx :

| Code d’état | Motif de l’erreur | Procédure de gestion |
| ----------- | ------------ | ------------- |
| Erreur 4xx dans la requête. | Un ou plusieurs des paramètres de la requête étaient incorrects. | Ne faites pas de nouvel essai.  Examinez les détails de l’erreur pour plus d’informations.  Les erreurs 4xx sont des erreurs au moment de la conception.|
| Erreur 5xx temporaire de service. | Le sous-système de MSI ou Azure Active Directory a renvoyé une erreur temporaire. | Il est possible de faire une nouvelle tentative après un délai d’une seconde.  Si vous réessayez trop souvent ou trop rapidement, Azure AD peut renvoyer une erreur de limite de débit (429).|

Si une erreur se produit, le corps de réponse HTTP correspondant contient des données JSON avec les détails de l’erreur :

| Élément | Description |
| ------- | ----------- |
| error   | Identificateur de l’erreur. |
| error_description | Description détaillée de l’erreur. **Les descriptions des erreurs peuvent changer à tout moment. N’écrivez pas de codes créant des branches en fonction des valeurs dans la description de l’erreur.**|

### <a name="http-response-reference"></a>Référence de réponse HTTP

Cette section documente les réponses possibles aux erreurs. Un état « 200 OK » est une réponse correcte, et le jeton d’accès est contenu au sein du corps de réponse JSON, dans l’élément access_token.

| Code d’état | Error | Description de l’erreur | Solution |
| ----------- | ----- | ----------------- | -------- |
| 400 Demande incorrecte | invalid_resource | AADSTS50001 : L’application nommée *\<URI\>* est introuvable dans le locataire nommé *\<ID DE LOCATAIRE\>*. Cela peut se produire si l’application n’a pas été installée par l’administrateur du locataire ni acceptée par un utilisateur dans le locataire. Vous avez peut-être envoyé votre requête d’authentification au locataire incorrect.\ | (Linux uniquement) |
| 400 Demande incorrecte | bad_request_102 | En-tête de métadonnées requis non spécifié | Le champ d’en-tête de métadonnées `Metadata` est absent de votre requête, ou bien il n’est pas correctement formaté. La valeur spécifiée doit être `true`, en minuscules. Pour obtenir un exemple, consultez « Exemple de demande » dans la section [Obtenir un jeton par HTTP](#get-a-token-using-http).|
| 401 Non autorisé | unknown_source | *\<URI\>* de source inconnue | Vérifiez que votre URI de requête HTTP GET est correctement mise en forme. La partie `scheme:host/resource-path` doit être spécifiée comme `http://localhost:50342/oauth2/token`. Pour obtenir un exemple, consultez « Exemple de demande » dans la section [Obtenir un jeton par HTTP](#get-a-token-using-http).|
|           | invalid_request | Il manque un paramètre nécessaire à la requête, elle comprend une valeur de paramètre non valide, plus d’un paramètre à la fois, ou bien elle est incorrecte. |  |
|           | unauthorized_client | Le client n’est pas autorisé à demander un jeton d’accès avec cette méthode. | Engendré par une demande n’ayant pas utilisé le bouclage local pour appeler l’extension, ou sur une machine virtuelle dont la MSI n’est pas correctement configurée. Consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure](msi-qs-configure-portal-windows-vm.md) si vous avez besoin d’aide pour la configuration d’une machine virtuelle. |
|           | access_denied | Le propriétaire de la ressource ou le serveur d’autorisation a refusé la requête. |  |
|           | unsupported_response_type | Le serveur d’autorisation ne prend pas en charge l’obtention d’un jeton d’accès par cette méthode. |  |
|           | invalid_scope | L’étendue demandée est incorrecte, inconnue ou non valide. |  |
| Erreur interne 500 du serveur | unknown | Impossible de récupérer le jeton depuis Active Directory. Pour plus d’informations, consultez les journaux dans *\<Chemin d’accès de fichier\>* | Vérifiez que l’identité du service administré a été activée dans la machine virtuelle. Consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure](msi-qs-configure-portal-windows-vm.md) si vous avez besoin d’aide pour la configuration d’une machine virtuelle.<br><br>Vérifiez également que votre URI de requête HTTP GET est correctement mise en forme, en particulier l’URI de la ressource spécifiée dans la chaîne de requête. Pour obtenir une liste des services et leur ID de ressource respectif, consultez « Exemple de demande » dans la section [Obtenir un jeton par HTTP](#get-a-token-using-http) ou [Services Azure prenant en charge l’authentification Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="resource-ids-for-azure-services"></a>ID de ressource pour les services Azure

Consultez [Services Azure prenant en charge l’authentification de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) pour obtenir une liste des ressources qui prennent en charge Azure AD et qui ont été testées avec l’identité du service administré et leur ID de ressources respectif.


## <a name="next-steps"></a>étapes suivantes

- Pour activer MSI sur une machine virtuelle Azure, consultez [Configurer une identité MSI (Managed Service Identity) affectée par l’utilisateur pour une machine virtuelle, à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.








