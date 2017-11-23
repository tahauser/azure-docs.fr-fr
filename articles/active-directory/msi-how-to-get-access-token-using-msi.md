---
title: "Utilisation d’une identité du service administré de machine virtuelle Azure pour se connecter et obtenir des jetons"
description: "Procédure détaillée concernant l’utilisation d’un principal du service MSI d’une machine virtuelle Azure pour la connexion et l’acquisition de jetons d’accès."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 168b2ab3676d3f3e2830966f850e14adbe579f85
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Utilisation d’une identité du service administré (MSI) d’une machine virtuelle Azure pour se connecter et obtenir des jetons 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Une fois que vous avez activé l’identité du service administré sur une machine virtuelle Azure, vous pouvez l’utiliser pour vous connecter et pour demander un jeton d’accès. Cet article vous montre plusieurs façons d’utiliser un [principal du service](develop/active-directory-dev-glossary.md#service-principal-object) MSI pour vous connecter et obtenir un [jeton d’accès de l’application uniquement](develop/active-directory-dev-glossary.md#access-token) pour accéder à d’autres ressources, y compris :

- Connexion silencieuse/sans assistance à partir de PowerShell ou de Azure CLI
- Acquisition de jeton pour différents clients, y compris .NET, PowerShell, Bash/CURL, REST
- Se connecter à l’aide d’un kit de développement Azure, y compris .NET, Java, Node.js, Python, Ruby

## <a name="prerequisites"></a>Composants requis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Si vous envisagez d’utiliser les exemples PowerShell de cet article, veillez à installer [Azure PowerShell version 4.3.1](https://www.powershellgallery.com/packages/AzureRM) ou une version ultérieure. Si vous envisagez d’utiliser les exemples Azure CLI de cet article, vous disposez de trois options :
- Utiliser [Azure Cloud Shell](../cloud-shell/overview.md) à partir du portail Azure.
- Utiliser l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit des blocs de code de Azure CLI.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser CLI dans une invite de commandes locale. 


> [!IMPORTANT]
> - Tous les scripts/exemples de code de cet article supposent que le client exécute une machine virtuelle avec le paramètre MSI activé. Pour plus d’informations sur l’activation de MSI sur une machine virtuelle, consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide du portail Azure](msi-qs-configure-portal-windows-vm.md), ou l’un des autres articles (à l’aide de PowerShell, CLI, d’un modèle ou d’un kit de développement logiciel Azure). 
> - Pour éviter les erreurs d’autorisation (403/AuthorizationFailed) dans les exemples de code/script, les identités de la machine virtuelle doivent être avoir l’accès « Lecteur » au niveau de la machine virtuelle afin d’y autoriser les opérations de Azure Resource Manager. Voir [Attribuer à une identité du service administré (MSI) un accès à une ressource à l’aide du portail Azure](msi-howto-assign-access-portal.md) pour plus de détails.
> - Avant de passer à l’une des sections suivantes, utilisez la fonctionnalité « Se connecter » de la machine virtuelle depuis le portail Azure, afin de vous connecter à distance à votre machine virtuelle avec le paramètre MSI activé.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Comment se connecter à partir de PowerShell ou CLI à l’aide d’une identité du service administré

Auparavant, l’exécution d’un script sous sa propre identité signifiait :
- son inscription comme une application cliente web/confidentielle avec Azure AD
- une connexion à l’aide des informations d’identification secrètes/ID client de l’application

Avec MSI, votre script n’a plus besoin d’être inscrit auprès de Azure AD et les informations d’identification ne sont plus nécessaires. Dans les exemples ci-dessous, nous montrons comment utiliser le principal du service MSI d’une machine virtuelle pour se connecter.

### <a name="azure-powershell"></a>Azure PowerShell

Le script suivant montre comment :

- acquérir un jeton d’accès MSI pour une machine virtuelle Azure
- utiliser le jeton d’accès pour se connecter à Azure AD sous le principal du service MSI correspondant
- utiliser le principal du service MSI pour appeler Azure Resource Manager et obtenir l’ID du principal du service

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Interface de ligne de commande Azure

Le script suivant montre comment :

- se connecter à Azure AD sous le principal du service MSI de la machine virtuelle
- utiliser le principal du service MSI pour appeler Azure Resource Manager et obtenir l’ID du principal du service

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Comment acquérir un jeton d’accès par MSI

À l’aide de MSI, votre script ou application cliente n’a plus besoin de s’inscrire auprès de Azure AD, ni de présenter son ID client et sa clé secrète pour obtenir un jeton d’accès. Votre client peut simplement demander un jeton d’accès au point de terminaison MSI local, sans avoir à présenter les informations d’identification de l’application. Le jeton d’accès d’application uniquement est émis pour le principal du service MSI, pouvant être utilisé comme un jeton du porteur dans [les appels de service à service nécessitant des informations d’identification du client](active-directory-protocols-oauth-service-to-service.md).

Dans les exemples ci-dessous, nous montrons comment utiliser l’identité du service administré pour l’obtention d’un jeton.

### <a name="net"></a>.NET

> [!IMPORTANT]
> La bibliothèque Azure AD Authentication (ADAL) n’est pas intégré à MSI. Pour obtenir un jeton d’accès à l’aide de MSI, effectuez une demande au point de terminaison MSI local dans une machine virtuelle. Pour plus d’informations, consultez [FAQ et problèmes connus de MSI](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Exemple de demande :

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Élément | Description |
| ------- | ----------- |
| `GET` | Le verbe HTTP, indiquant votre souhait de récupérer des données du point de terminaison. Dans ce cas, un jeton d’accès OAuth. | 
| `http://localhost:50342/oauth2/token` | Le point de terminaison MSI, où 50342 est le numéro de port par défaut (configurable). |
| `resource` | Un paramètre de chaîne de requête, indiquant l’URI ID d’application de la ressource cible. Il apparaît également dans la revendication `aud` (audience) du jeton émis. Dans cet exemple, nous demandons un jeton pour accéder à Azure Resource Manager, qui possède un URI ID d’application, https://management.azure.com/. |
| `Metadata` | Un champ d’en-tête de requête HTTP, requis par MSI afin de limiter une attaque de falsification de requête côté serveur (SSRF). Cette valeur doit être définie sur « true », en minuscules.

Exemple de réponse :

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Élément | Description |
| ------- | ----------- |
| `access_token` | Le jeton d’accès demandé. Lors de l’appel d’une API REST, le jeton est incorporé dans le champ d’en-tête de requête `Authorization` comme un jeton « du porteur », autorisant l’API à authentifier l’appelant. | 
| `refresh_token` | Non utilisé par MSI. |
| `expires_in` | Le nombre de secondes pendant lesquelles le jeton d’accès est toujours valide, avant d’expirer, à partir de son émission. L’heure d’émission est accessible dans la revendication `iat` du jeton. |
| `expires_on` | L’intervalle de temps lorsque le jeton d’accès expire. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `exp` du jeton). |
| `not_before` | L’intervalle de temps pendant lequel le jeton d’accès prend effet, et peut être accepté. La date est exprimée en nombre de secondes à partir de « 1970-01-01T0:0:0Z UTC » (correspond à la revendication `nbf` du jeton). |
| `resource` | La ressource pour laquelle le jeton d’accès a été demandé, correspondant au paramètre de chaîne de requête `resource` de la requête. |
| `token_type` | Le type de jeton, qui est un jeton d’accès « du porteur », ce qui signifie que la ressource peut donner l’accès au porteur de ce jeton. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Utilisation de l’identité MSI avec les bibliothèques du SDK Azure

Azure prend en charge plusieurs plates-formes de programmation via une série de [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads). Plusieurs d'entre eux ont été mis à jour pour prendre en charge l’authentification à l’aide de MSI, et donnent des exemples illustrant leur utilisation. Cette liste a été mise à jour, suite à l’ajout d’une prise en charge supplémentaire :

| Foundation | Exemple |
| --- | ------ | 
| .NET | [Déployer un modèle ARM à partir d’une machine virtuelle Windows à l’aide de Managed Service Identity](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [Appeler les services Azure à partir d’une machine virtuelle Linux à l’aide de Managed Service Identity](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [Gérer des ressources à l’aide d’une identité du service administré](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [Utiliser une identité du service administré pour s’authentifier simplement depuis l’intérieur d’une machine virtuelle](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Gérer des ressources à partir d’une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Informations supplémentaires

### <a name="token-expiration"></a>Expiration du jeton

Le sous-système MSI local met en cache des jetons. Par conséquent, vous pouvez l’appeler autant de fois que vous le souhaitez et un appel réseau vers Azure AD survient uniquement si :
- une absence dans le cache se produit du fait d’une absence de jeton dans le cache
- le jeton a expiré

Si vous mettez le jeton en cache dans votre code, vous devez être prêt à gérer les scénarios dans lesquels la ressource indique que le jeton a expiré.

### <a name="resource-ids-for-azure-services"></a>ID de ressource pour les services Azure

Consultez [Services Azure prenant en charge l’authentification de Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) pour obtenir une liste des services qui prennent en charge l’identité du service administré et leur ID de ressources respectif.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="sign-in-or-token-acquisition-fails"></a>Échec de la connexion ou de l’acquisition du jeton

Vérifiez que l’identité du service administré a été activée correctement. Revenez à la machine virtuelle Azure dans le [portail Azure](https://portal.azure.com) et :

- Examinez la page « Configuration » et s’assurer que le paramètre d’identité du service administré est activé.
- Examinez la page « Extensions » et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

### <a name="http-request-error-responses"></a>Réponses d’erreur pour une requête HTTP

- *bad_request_102 : en-tête de métadonnées requis non spécifié*

  Le champ d’en-tête de métadonnées `Metadata` est absent de votre requête, ou bien il n’est pas correctement formaté. La valeur spécifiée doit être `true`, en minuscules. Consultez « Exemple de requête » dans la [section REST précédente](#rest) pour avoir un exemple.

- *unknown : impossible de récupérer le jeton depuis Active Directory. Pour plus d’informations, consultez les journaux dans \<Chemin d’accès de fichier\>*

  Vérifiez que votre URI de requête HTTP GET est correctement mise en forme, en particulier l’URI de la ressource spécifiée dans la chaîne de requête. Consultez « l’exemple de demande » dans la [section REST précédente](#rest) pour obtenir un exemple, ou les [services Azure prenant en charge l’authentification Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) pour obtenir une liste des services et leur ID de ressource respectif.

- *unknown_source : \<URI\>* de source inconnue

  Vérifiez que votre URI de requête HTTP GET est correctement mise en forme. La partie `scheme:host/resource-path` doit être spécifiée comme `http://localhost:50342/oauth2/token`. Consultez « Exemple de requête » dans la [section REST précédente](#rest) pour avoir un exemple.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’Identité du service administré (MSI) d’une machine virtuelle Azure à l’aide de PowerShell](msi-qs-configure-powershell-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

