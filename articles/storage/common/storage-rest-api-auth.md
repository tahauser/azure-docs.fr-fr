---
title: "Opérations d’appel de l’API REST des services Stockage Azure, notamment l’authentification | Microsoft Docs"
description: "Opérations d’appel de l’API REST des services Stockage Azure, notamment l’authentification"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/27/2017
ms.author: robinsh
ms.openlocfilehash: 521487c3ed38f191308e14e4d542358438945556
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="using-the-azure-storage-rest-api"></a>Utilisation de l’API REST Stockage Azure

Cet article vous indique comment utiliser les API REST du service Stockage Blob et comment authentifier l’appel au service. Il est écrit du point de vue d’un développeur qui n’a aucune connaissance sur REST et sur la manière de passer un appel REST. Nous consultons la documentation de référence pour un appel REST et expliquons comment la traduire en un appel REST réel : quels champs sont à placer et où ? Après avoir appris comment configurer un appel REST, vous pouvez utiliser ces connaissances pour utiliser les autres API REST du service Stockage.

## <a name="prerequisites"></a>Conditions préalables 

L’application répertorie les conteneurs dans le Stockage Blob pour un compte de stockage. Pour tester le code de cet article, vous avez besoin des éléments suivants : 

* Installez [Visual Studio 2017](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) avec la charge de travail suivante :
    - Développement Azure

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Un compte de stockage à usage général. Si vous ne possédez pas encore de compte de stockage, consultez [Création d’un compte de stockage](storage-quickstart-create-account.md).

* L’exemple dans cet article illustre comment répertorier les conteneurs dans un compte de stockage. Pour voir la sortie, ajoutez des conteneurs au Stockage Blob dans le compte de stockage avant de commencer.

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

L’exemple d’application est une application console écrite en C#.

Utilisez [git](https://git-scm.com/) pour télécharger une copie de l’application dans votre environnement de développement. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Cette commande clone le dépôt dans votre dossier git local. Pour ouvrir la solution Visual Studio, recherchez le dossier storage-dotnet-rest-api-with-auth, ouvrez-le et double-cliquez sur StorageRestApiAuth.sln. 

## <a name="why-do-i-need-to-know-rest"></a>Pourquoi dois-je connaître REST ?

Savoir comment utiliser REST est une compétence utile. L’équipe produit Azure publie souvent de nouvelles fonctionnalités. Souvent, les nouvelles fonctionnalités sont accessibles via l’interface REST mais n’apparaissent pas encore dans **toutes** les bibliothèques clientes de stockage ou l’UI (comme le portail Azure). Si vous souhaitez toujours utiliser les fonctionnalités les plus récentes, vous devez apprendre REST. En outre, si vous voulez écrire votre propre bibliothèque pour interagir avec le Stockage Azure, ou si vous voulez accéder au Stockage Azure à l’aide d’un langage de programmation sans Kit SDK ou bibliothèque cliente de stockage, vous pouvez utiliser l’API REST.

## <a name="what-is-rest"></a>Qu’est-ce que REST ?

REST signifie *representational state transfer*. Consultez [Wikipedia](http://en.wikipedia.org/wiki/Representational_state_transfer) pour obtenir une définition spécifique.

En fait, REST est une architecture que vous pouvez utiliser lors de l’appel d’API ou lorsque vous rendez des API disponibles pour être appelées. L’architecture REST est indépendante de ce qui se passe de chaque côté d’elle et du logiciel utilisé lors de l’envoi ou de la réception d’appels REST. Vous pouvez écrire une application sur Mac, Windows, Linux, un téléphone ou une tablette Android, iPhone, iPod, ou un site web, et vous pouvez utiliser la même API REST pour toutes ces plateformes. Les données peuvent être reçues et/ou envoyées lorsque l’API REST est appelée. L’API REST ne se soucie pas de la plateforme à partir de laquelle elle est appelée ; ce qui est important ce sont les informations transmises dans la requête et les données fournies dans la réponse.

## <a name="heres-the-plan"></a>Voici le plan

L’exemple de projet répertorie les conteneurs dans un compte de stockage. Une fois que vous comprenez la corrélation entre les informations dans la documentation sur l’API REST et votre code réel, les autres appels REST sont simples à comprendre. 

Si vous regardez [l’API REST du service Blob](/rest/api/storageservices/fileservices/Blob-Service-REST-API), vous voyez toutes les opérations que vous pouvez effectuer sur le Stockage Blob. Les bibliothèques clientes de stockage sont des wrappers autour des API REST : elles vous permettent d’accéder facilement au stockage sans utiliser les API REST directement. Mais, comme indiqué précédemment, vous voulez parfois utiliser l’API REST au lieu d’une bibliothèque cliente de stockage.

## <a name="rest-api-reference-list-containers-api"></a>Référence d’API REST : API List Containers

Consultons la page dans la Référence d’API REST pour l’opération [ListContainers](/rest/api/storageservices/fileservices/List-Containers2) pour que vous compreniez d’où proviennent certains champs dans la requête et la réponse dans la section suivante avec le code.

**Méthode de requête** : GET. Ce verbe est la méthode HTTP que vous spécifiez en tant que propriété de l’objet de requête. Les autres valeurs pour ce verbe incluent HEAD, PUT et DELETE en fonction de l’API que vous appelez.

**URI de requête** : https://myaccount.blob.core.windows.net/?comp=list  Ceci est créé à partir du point de terminaison du compte de stockage Blob `http://myaccount.blob.core.windows.net` et de la chaîne de ressource `/?comp=list`.

[Paramètres d’URI](/rest/api/storageservices/fileservices/List-Containers2#uri-parameters) : il s’agit de paramètres de requête supplémentaires que vous pouvez utiliser lors de l’appel de ListContainers. Certains de ces paramètres sont *timeout* pour l’appel (en secondes) et *prefix*, utilisé pour le filtrage.

Un autre paramètre utile est *maxresults:* s’il existe plus de conteneurs disponibles que cette valeur, le corps de la réponse contiendra un élément *NextMarker* indiquant le conteneur suivant à retourner pour la requête suivante. Pour utiliser cette fonctionnalité, vous fournissez la valeur *NextMarker* sous la forme du paramètre *marker* dans l’URI lors de la requête suivante. L’utilisation de cette fonctionnalité est similaire à la pagination des résultats. 

Pour utiliser d’autres paramètres, ajoutez-les à la chaîne de ressource avec la valeur, comme par exemple :

```
/?comp=list&timeout=60&maxresults=100
```

[En-têtes de requête](/rest/api/storageservices/fileservices/List-Containers2#request-headers) **:** cette section répertorie les en-têtes de requête obligatoires et facultatifs. Trois des en-têtes sont obligatoires : un en-tête *Authorization*, *x-ms-date* (contient l’heure UTC de la requête) et *x-ms-version* (spécifie la version de l’API REST à utiliser). Inclure *x-ms-client-request-id* dans les en-têtes est facultatif. Vous pouvez définir ce champ sur n’importe quelle valeur ; il est écrit dans les journaux d’analytique du stockage lorsque la journalisation est activée.

[Corps de la requête](/rest/api/storageservices/fileservices/List-Containers2#request-body) **:** il n’existe pas de corps de requête pour ListContainers. Le paramètre Corps de la requête est utilisé pour toutes les opérations PUT lors de la mise à jour d’objets Blob, tout comme SetContainerAccessPolicy, qui vous permet d’envoyer une liste XML des stratégies d’accès stockées à appliquer. Les stratégies d’accès stockées sont traitées dans l’article [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md).

[Code d’état de la réponse](/rest/api/storageservices/fileservices/List-Containers2#status-code) **:** indique tous les codes d’état que vous devez connaître. Dans cet exemple, un code d’état HTTP de 200 est acceptable. Pour obtenir la liste complète des codes d’état HTTP, consultez [Définitions des codes d’état](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Pour consulter les codes d’erreur spécifiques aux API REST de Stockage, consultez [Codes d’erreur API REST courants](/rest/api/storageservices/common-rest-api-error-codes)

[En-têtes de réponse](/rest/api/storageservices/fileservices/List-Containers2#response-headers) **:** ils incluent *Type de contenu* ; *x-ms-request-id* (l’ID de requête que vous avez transmis, le cas échéant) ; *x-ms-version* (indique la version du service Blob utilisé) et *Date* (format UTC, indique l’heure à laquelle la requête a été faite).

[Corps de la réponse](/rest/api/storageservices/fileservices/List-Containers2#response-body) : ce champ est une structure XML qui fournit les données demandées. Dans cet exemple, la réponse est une liste de conteneurs et leurs propriétés.

## <a name="creating-the-rest-request"></a>Création de la requête REST

Quelques remarques avant de commencer : à des fins de sécurité lors de l’exécution en production, utilisez toujours HTTPS plutôt que HTTP. Pour cet exercice, nous vous recommandons d’utiliser HTTP pour pouvoir afficher la requête et les données de la réponse. Pour afficher la requête et les informations de la réponse dans les appels REST réels, vous pouvez télécharger [Fiddler](http://www.telerik.com/fiddler) ou une application similaire. Dans la solution Visual Studio, le nom et la clé du compte de stockage sont codés en dur dans la classe, et la méthode ListContainersAsyncREST transmet le nom du compte de stockage et la clé du compte de stockage aux méthodes utilisées pour créer les divers composants de la requête REST. Dans une application réelle, le nom et la clé du compte de stockage résident dans un fichier de configuration, des variables d’environnement ou sont récupérés à partir d’Azure Key Vault.

Dans notre exemple de projet, le code pour créer l’en-tête Autorisation se trouve dans une classe séparée. L’idée est que vous pouvez ajouter l’ensemble de la classe à votre propre solution et l’utiliser « telle quelle ». Le code d’en-tête Autorisation fonctionne pour la plupart des appels d’API REST au Stockage Azure.

Pour générer la requête, qui est un objet HttpRequestMessage, accédez à ListContainersAsyncREST dans Program.cs. Les étapes de génération de la requête sont les suivantes : 

* Créez l’URI à utiliser pour appeler le service. 
* Créez l’objet HttpRequestMessage et définissez la charge utile. La charge utile a la valeur null pour ListContainersAsyncREST car nous ne transmettons rien.
* Ajoutez les en-têtes de la requête pour x-ms-date et x-ms-version.
* Obtenez l’en-tête d’autorisation et ajoutez-le.

Informations de base dont vous avez besoin : 

*  Pour ListContainers, la **méthode** est `GET`. Cette valeur est définie lorsque la requête est instanciée. 
*  La **ressource** est la partie de requête de l’URI qui indique quelle API est appelée, donc la valeur est `/?comp=list`. Comme indiqué précédemment, la ressource se trouve sur la page de documentation de référence qui affiche les informations sur [l’API ListContainers](/rest/api/storageservices/fileservices/List-Containers2).
*  L’URI est construit en créant le point de terminaison du service Blob pour ce compte de stockage et en concaténant la ressource. La valeur de **l’URI de requête** devient `http://contosorest.blob.core.windows.net/?comp=list`.
*  Pour ListContainers, **requestBody** a la valeur null et il n’y a aucun **en-tête** supplémentaire.

D’autres API peuvent avoir d’autres paramètres à transmettre, comme *ifMatch*. L’appel de PutBlob est un exemple d’utilisation de ifMatch. Dans ce cas, vous définissez ifMatch sur une eTag, et l’objet Blob n’est mis à jour que si l’eTag que vous fournissez correspond à l’eTag actuelle de l’objet Blob. Si un autre utilisateur a mis à jour l’objet Blob depuis la récupération de l’eTag, sa modification ne sera pas écrasée. 

Premièrement, définissez `uri` et `payload`. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Ensuite, instanciez la requête, en définissant la méthode sur `GET` et en fournissant l’URI.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Ajoutez les en-têtes de la requête pour x-ms-date et x-ms-version. C’est à cet emplacement dans le code que vous ajoutez les en-têtes de requête supplémentaires requis pour l’appel. Cet exemple ne contient pas d’en-tête supplémentaire. SetContainerACL est un exemple d’API transmettant des en-têtes supplémentaires. Pour le stockage Blob, elle ajoute un en-tête appelé « x-ms-blob-public-access » et la valeur du niveau d’accès.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-04-17");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Appelez la méthode qui crée l’en-tête d’autorisation et ajoutez-le aux en-têtes de la requête. Vous découvrirez comment créer l’en-tête d’autorisation plus loin dans cet article. Le nom de la méthode est GetAuthorizationHeader, que vous pouvez voir dans cet extrait de code :

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

`httpRequestMessage` contient la requête REST complète avec les en-têtes d’autorisation. 

## <a name="call-the-rest-api-with-the-request"></a>Appeler l’API REST avec la requête

Maintenant que vous avez la requête, vous pouvez appeler SendAsync pour envoyer la requête REST. SendAsync appelle l’API et reçoit la réponse en retour. Examinez le code d’état de la réponse (200 est OK), puis analysez la réponse. Dans ce cas, vous obtenez une liste XML de conteneurs. Observons le code utilisé pour appeler la méthode GetRESTRequest pour créer la requête, exécuter la requête, puis examiner dans la réponse la liste de conteneurs.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Si vous exécutez un sniffer réseau comme [Fiddler](https://www.telerik.com/fiddler) lors de l’appel à SendAsync, vous pouvez voir la requête et les informations de la réponse. Jetons un œil sur cette configuration. Le nom du compte de stockage est *contosorest*.

**Requête :**

```
GET /?comp=list HTTP/1.1
```

**En-têtes de requête :**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Code d’état et en-têtes de réponse retournés après l’exécution :**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 04-17
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Corps de la réponse (XML) :** pour ListContainers, ce paramètre affiche la liste des conteneurs et leurs propriétés.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Maintenant que vous comprenez comment créer la requête, appeler le service et analyser les résultats, intéressons-nous à la création de l’en-tête d’autorisation. La création de cet en-tête est compliquée, mais la bonne nouvelle est que dès que vous disposez d’un code qui fonctionne, il fonctionne pour toutes les API REST du service Stockage.

## <a name="creating-the-authorization-header"></a>Création de l’en-tête d’autorisation

Il existe un article qui explique de manière conceptuelle (sans code) comment exécuter [l’authentification pour les services Stockage Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services).
Examinons cet article de plus près pour montrer le code.

Premièrement, utilisez l’authentification par clé partagée. Le format de l’en-tête d’autorisation ressemble à ce qui suit :

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Le champ de la signature est un code d’authentification de message basé sur le hachage (HMAC) créé à partir de la requête et calculé à l’aide de l’algorithme SHA256, puis encodé à l’aide de l’encodage Base64. Vous avez compris ? (Attendez, vous n’avez pas encore entendu l’expression *rendre canonique*.)

Cet extrait de code affiche le format de la chaîne de signature par clé partagée :

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

La plupart de ces champs est rarement utilisée. Pour le stockage Blob, vous spécifiez VERB, md5, content length (longueur du contenu), Canonicalized Headers (en-têtes rendus canoniques) et Canonicalized Resource (ressource rendue canonique). Vous pouvez laisser les autres champs vides (mais insérez `\n` pour montrer qu’ils sont vides).

Que sont les champs CanonicalizedHeaders et CanonicalizedResource ? Bonne question. En fait, que signifie l’expression « rendre canonique » ? Microsoft Word ne la reconnaît même pas. Voici ce que [Wikipédia indique sur le mot « canonique »](http://en.wikipedia.org/wiki/Canonicalization) : *En informatique, la mise en forme canonique est le procédé par lequel on convertit des données qui ont plusieurs représentations possibles vers un format standard.* En langage courant, cela signifie prendre la liste d’éléments (comme des en-têtes dans le cas des En-têtes rendus canoniques) et les normaliser dans un format requis. En résumé, Microsoft a choisi un format et vous devez vous y conformer.

Commençons par ces deux champs rendus canoniques, car ils sont obligatoires pour créer l’en-tête d’autorisation.

**En-têtes rendus canoniques**

Pour créer cette valeur, récupérez les en-têtes qui commencent par « x-ms- » et triez-les, puis mettez-les en forme de chaîne d’instances `[key:value\n]`, concaténées en une seule chaîne. Pour cet exemple, les en-têtes rendus canoniques ressemblent à ce qui suit : 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-04-17\n
```

Voici le code utilisé pour créer cette sortie :

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
                    where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
                    orderby kvp.Key
                    select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder sb = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. http://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        StringBuilder headerBuilder = new StringBuilder(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValues in kvp.Value)
        {
            string trimmedValue = headerValues.TrimStart().Replace("\r\n", String.Empty);
            headerBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used 
            //   if there are multiple values for one of the headers.
            separator = ',';
        }
        sb.Append(headerBuilder.ToString()).Append("\n");
    }
    return sb.ToString();
}      
```

**Ressource rendue canonique**

Cette partie de la chaîne de signature représente le compte de stockage ciblé par la requête. N’oubliez pas que l’URI de requête est `<http://contosorest.blob.core.windows.net/?comp=list>`, avec le nom de compte réel (`contosorest` dans ce cas). Dans cet exemple, voici ce qui est retourné :

```
/contosorest/\ncomp:list
```

Si vous avez des paramètres de requête, ils sont également inclus. Voici le code, qui traite aussi les paramètres de requête supplémentaires et les paramètres de requête avec plusieurs valeurs. N’oubliez pas que vous générez ce code pour qu’il fonctionne pour toutes les API REST, donc vous voulez inclure toutes les possibilités, même si la méthode ListContainers n’en a pas besoin.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Maintenant que les chaînes rendues canoniques sont définies, jetons un œil à la création de l’en-tête d’autorisation même. Vous commencez par créer une chaîne de la signature du message au format StringToSign précédemment illustré dans cet article. Ce concept est plus facile à expliquer à l’aide de commentaires dans le code, donc voici la méthode finale qui retourne l’en-tête d’autorisation :

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Lorsque vous exécutez ce code, le paramètre MessageSignature qui en résulte ressemble à ce qui suit :

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-04-17\n/contosorest/\ncomp:list
```

Voici la valeur finale pour AuthorizationHeader :

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader est le dernier en-tête placé dans les en-têtes de la requête avant de publier la réponse.

Ceci conclut tout ce que vous devez savoir, le code y compris, pour créer une classe que vous pouvez utiliser pour créer une requête à utiliser pour appeler l’API REST des services Stockage.

## <a name="how-about-another-example"></a>Un autre exemple ? 

Consultons comment changer le code pour appeler ListBlobs pour le conteneur *container-1*. Il est presque identique au code utilisé pour lister les conteneurs, les seules différences étant l’URI et la façon d’analyser la réponse. 

Si vous consultez la documentation de référence pour [ListBlobs](/rest/api/storageservices/fileservices/List-Blobs), vous constaterez que la méthode est *GET* et l’URI de la requête est :

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Dans ListContainersAsyncREST, changez le code qui définit l’URI sur l’API pour ListBlobs. Le nom du conteneur est **container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Puis, là où vous traitez la réponse, changez le code pour rechercher des objets Blob au lieu de conteneurs.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Lorsque vous exécutez cet exemple, vous obtenez des résultats comme ceux ci-dessous :

**En-têtes rendus canoniques :**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-04-17\n
```

**Ressource rendue canonique :**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Signature du message :**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-04-17\n/contosorest/container-1\ncomp:list\nrestype:container
```

**En-tête d’autorisation :**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Les valeurs suivantes proviennent de [Fiddler](http://www.telerik.com/fiddler) :

**Requête :**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**En-têtes de requête :**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-04-17
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Code d’état et en-têtes de réponse retournés après l’exécution :**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-04-17
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Corps de la réponse (XML) :** cette réponse XML affiche la liste des objets Blob et leurs propriétés. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Résumé

Dans cet article, vous avez appris à effectuer une requête à l’API REST du stockage Blob pour récupérer une liste de conteneurs ou une liste d’objets Blob dans un conteneur. Vous avez également appris à créer la signature d’autorisation pour l’appel de l’API REST, à l’utiliser dans la requête REST et à examiner la réponse.

## <a name="next-steps"></a>Étapes suivantes

* [API REST du service Blob](/rest/api/storageservices/blob-service-rest-api)
* [API REST du service de fichiers](/rest/api/storageservices/file-service-rest-api)
* [API REST du service de File d’attente](/rest/api/storageservices/queue-service-rest-api)