---
title: "API Collecte de données HTTP Log Analytics | Microsoft Docs"
description: "L’API Collecte de données HTTP Log Analytics permet d’ajouter des données POST JSON au référentiel de Log Analytics à partir de tout client pouvant appeler l’API REST. Cet article explique comment utiliser l’API, et contient des exemples montrant comment publier des données à l’aide de différents langages de programmation."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: 5c6f2b35b48988af533612cb48da8fe79a838cf6
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Transmettre des données à Log Analytics avec l’API Collecteur de données HTTP (préversion publique)
Cet article vous montre comment utiliser l’API Collecte de données HTTP pour transmettre des données à Log Analytics à partir d’un client API REST.  Il explique comment mettre en forme les données collectées par le script ou l’application, les inclure dans une requête et faire en sorte que Log Analytics autorise cette requête.  Il est illustré par des exemples pour PowerShell, C# et Python.

> [!NOTE]
> L’API Collecteur de données HTTP Log Analytics est en préversion publique.

## <a name="concepts"></a>Concepts
Vous pouvez utiliser l’API Collecte de données HTTP pour transmettre des données à Log Analytics à partir de tout client pouvant appeler l’API REST.  Il peut s’agir d’un runbook dans Azure Automation qui collecte les données de gestion à partir d’Azure ou d’un autre cloud, ou il peut s’agit d’un système de gestion alternatif utilisant Log Analytics pour consolider et analyser les données.

Toutes les données du référentiel Log Analysis sont stockées en tant qu’enregistrement avec un type d’enregistrement particulier.  Vous formatez vos données à transmettre à l’API Collecte de données HTTP en tant qu’enregistrements multiples dans JSON.  Lorsque vous envoyez vos données, un enregistrement individuel est créé dans le référentiel pour chaque enregistrement dans la charge utile de la requête.


![Présentation la collecte de données HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Créer une demande
Pour utiliser l’API Collecte de données HTTP, il vous suffit de créer une requête POST qui inclut les données à envoyer dans JavaScript Objet Notation (JSON).  Les trois tableaux suivants répertorient les attributs requis pour chaque requête. Nous décrivons chaque attribut de façon plus détaillée plus loin dans cet article.

### <a name="request-uri"></a>URI de demande
| Attribut | Propriété |
|:--- |:--- |
| Méthode |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Type de contenu |application/json |

### <a name="request-uri-parameters"></a>Paramètres de l’URI de demande
| Paramètre | Description |
|:--- |:--- |
| CustomerID |Identificateur unique de l’espace de travail Log Analytics. |
| Ressource |Nom de ressource de l’API : / api/logs. |
| Version de l'API |Version de l’API à utiliser avec cette demande. Actuellement, il s’agit de 2016-04-01. |

### <a name="request-headers"></a>En-têtes de requête
| En-tête | Description |
|:--- |:--- |
| Authorization |Signature de l’autorisation. Plus loin dans cet article, vous pouvez lire comment créer un en-tête HMAC-SHA256. |
| Log-Type |Spécifiez le type d’enregistrement des données envoyées. Actuellement, le type de journal prend en charge uniquement des caractères alphabétiques. Il ne prend pas en charge les caractères numériques ou spéciaux. |
| x-ms-date |Date à laquelle la requête a été traitée, au format RFC 1123. |
| time-generated-field |Nom d’un champ de données qui contient l’horodateur de l’élément de données. Si vous spécifiez un champ, son contenu est utilisé pour **TimeGenerated**. Si ce champ n’est pas spécifié, la valeur par défaut de **TimeGenerated** est l’heure d’ingestion du message. Le contenu du champ de message doit suivre le format ISO 8601 AAAA-MM-JJThh:mm:ssZ. |

## <a name="authorization"></a>Authorization
Toute demande adressée à l’API Collecte de données HTTP Log Analytics doit inclure un en-tête d’autorisation. Pour authentifier une demande, vous devez la signer avec la clé primaire ou secondaire de l’espace de travail qui effectue la demande. Ensuite, transmettez cette signature dans le cadre de la demande.   

Voici le format de l’en-tête d’autorisation :

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* est l’identificateur unique de l’espace de travail Log Analytics. *Signature* est une clé [HMAC](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) construite à partir de la demande, puis calculée à l’aide de l’[algorithme SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Ensuite, vous l’encodez à l’aide d’un encodage Base64.

Utilisez ce format pour encoder la chaîne de signature **SharedKey** :

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Voici un exemple de chaîne de signature :

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Lorsque vous avez la chaîne de signature, encodez-la en utilisant l’algorithme HMAC-SHA256 sur la chaîne encodée en UTF-8, puis encodez le résultat en Base64. Utilisez le format suivant :

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Les exemples fournis dans les sections suivantes comportent un exemple de code pour vous aider à créer un en-tête d’autorisation.

## <a name="request-body"></a>Corps de la demande
Le corps du message doit être au format JSON. Il doit inclure un ou plusieurs enregistrements avec les paires nom de propriété/valeur au format suivant :

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Vous pouvez regrouper plusieurs enregistrements par lot dans une seule requête en utilisant le format suivant. Tous les enregistrements doivent être du même type.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Type et propriétés d’enregistrement
Vous définissez un type d’enregistrement personnalisé lorsque vous envoyez des données via l’API Collecte de données HTTP Log Analytics. Actuellement, vous ne pouvez pas écrire de données dans des types d’enregistrements existants qui ont été créés par d’autres types de données et solutions. Log Analytics lit les données entrantes, puis crée des propriétés correspondant aux types de données des valeurs que vous entrez.

Chaque demande adressée à l’API Log Analytics doit inclure un en-tête **Log-Type** avec le nom du type d’enregistrement. Le suffixe **_CL** est automatiquement ajouté au nom que vous entrez pour le distinguer d’autres types de journaux en tant que journal personnalisé. Par exemple, si vous entrez le nom **MyNewRecordType**, Log Analytics crée un enregistrement du type **MyNewRecordType_CL**. Cela évite tout conflit entre les noms de type créés par l’utilisateur et ceux fournis dans les solutions Microsoft actuelles ou futures.

Pour identifier le type de données d’une propriété, Log Analytics ajoute un suffixe au nom de celle-ci. Si une propriété contient une valeur null, elle n’est pas incluse dans cet enregistrement. Ce tableau répertorie les types de données de propriété et les suffixes correspondants :

| Type de données de propriété | Suffixe |
|:--- |:--- |
| Chaîne |_s |
| Booléen |_b |
| Double |_d |
| Date/time |_t |
| GUID |_g |

Le type de données que Log Analytics utilise pour chaque propriété dépend de l’existence préalable ou non du type d’enregistrement pour le nouvel enregistrement.

* Si le type d’enregistrement n’existe pas, Log Analytics en crée un. Log Analytics utilise une inférence de type JSON pour déterminer le type de données pour chaque propriété du nouvel enregistrement.
* Si le type d’enregistrement existe, Log Analytics tente de créer un enregistrement en fonction des propriétés existantes. Si le type de données d’une propriété dans le nouvel enregistrement ne correspond pas et ne peut pas être converti vers le type existant, ou si l’enregistrement contient une propriété inexistante, Log Analytics crée une propriété portant le suffixe approprié.

Par exemple, l’entrée de soumission suivante créerait un enregistrement avec trois propriétés, **number_d**, **boolean_b**, et **string_s** :

![Exemple d’enregistrement 1](media/log-analytics-data-collector-api/record-01.png)

Si vous envoyiez ensuite l’entrée suivante, avec toutes les valeurs mises en forme de chaînes, les propriétés ne changeraient pas. Ces valeurs peuvent être converties en types de données existants :

![Exemple d’enregistrement 2](media/log-analytics-data-collector-api/record-02.png)

Mais, si vous faisiez ensuite l’envoi suivant, Log Analytics créerait les propriétés **boolean_d** et **string_d**. Les valeurs suivantes ne peuvent pas être converties :

![Exemple d’enregistrement 3](media/log-analytics-data-collector-api/record-03.png)

Si vous envoyiez ensuite l’entrée suivante, avant que la création du type d’enregistrement, Log Analytics créerait un enregistrement avec trois propriétés, **number_s**, **boolean_s** **string_s**. Dans cette entrée, toutes les valeurs initiales sont au format de chaîne :

![Exemple d’enregistrement 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Limites de données
Il existe certaines contraintes sur les données publiées sur l’API de collecte de données de Log Analytics.

* Un maximum de 30 Mo par publication sur l’API de collecte de données de Log Analytics. Il s’agit d’une limite de taille pour une publication unique. Si les données d’une publication unique sont supérieures à 30 Mo, vous devrez fractionner les données en segments de plus petite taille et les envoyer simultanément.
* Maximum de 32 Ko pour les valeurs de champ. Si la valeur de champ est supérieure à 32 Ko, les données seront tronquées.
* Le nombre maximal recommandé de champs pour un type donné est 50. Il s’agit d’une limite pratique du point de vue de la facilité d’utilisation et de l’expérience de recherche.  

## <a name="return-codes"></a>Codes de retour
Le code d’état HTTP 200 signifie que la demande a été reçue et doit être traitée. Cela indique que l’opération a été accomplie avec succès.

Ce tableau répertorie l’ensemble complet de codes d’état que le service peut retourner :

| Code | Statut | Code d'erreur | Description |
|:--- |:--- |:--- |:--- |
| 200 |OK | |La demande a été acceptée. |
| 400 |Demande incorrecte |InactiveCustomer |L’espace de travail a été fermé. |
| 400 |Demande incorrecte |InvalidApiVersion |La version d’API que vous avez spécifiée n’est pas reconnue par le service. |
| 400 |Demande incorrecte |InvalidCustomerId |L’ID d’espace de travail spécifié n’est pas valide. |
| 400 |Demande incorrecte |InvalidDataFormat |Un JSON non valide a été envoyé. Il se peut que le corps de la réponse contiennent des informations supplémentaires sur la manière de résoudre l’erreur. |
| 400 |Demande incorrecte |InvalidLogType |Le type de journal spécifié contient des caractères spéciaux ou numériques. |
| 400 |Demande incorrecte |MissingApiVersion |La version de l’API n’était pas spécifiée. |
| 400 |Demande incorrecte |MissingContentType |Le type de contenu n’était pas spécifié. |
| 400 |Demande incorrecte |MissingLogType |Le type de journal de valeur requis n’était pas spécifié. |
| 400 |Demande incorrecte |UnsupportedContentType |Le type de contenu n’était pas défini sur **application/json**. |
| 403 |Interdit |InvalidAuthorization |Le serveur n’a pas pu authentifier la demande. Vérifiez que la clé de connexion et l’ID de l’espace de travail sont valides. |
| 404 |Introuvable | | L’URL fournie est incorrecte, ou la demande est trop grande. |
| 429 |Trop de demandes | | Le service reçoit un volume important de données à partir de votre compte. Relancez la requête ultérieurement. |
| 500 |Erreur interne du serveur |UnspecifiedError |Le service a rencontré une erreur interne. Relancez la requête. |
| 503 |Service indisponible |ServiceUnavailable |Le service est actuellement indisponible pour recevoir des demandes. Relancez la requête. |

## <a name="query-data"></a>Données de requête
Pour interroger des données soumises par l’API Collecte de données HTTP Log Analytics, recherchez des enregistrements dont la valeur **Type** correspond à la valeur **LogType** que vous avez spécifiée, assortie de **_CL**. Par exemple, si vous avez utilisé **MyCustomLog**, vous devriez retourner tous les enregistrements avec **Type=MyCustomLog_CL**.

>[!NOTE]
> Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Exemples de demandes
Les sections suivantes contiennent des exemples montrant comment envoyer des données à l’API Collecte de données HTTP Log Analytics à l’aide de différents langages de programmation.

Pour chaque exemple, procédez comme suit pour définir les variables de l’en-tête d’autorisation :

1. Dans le portail Azure, recherchez votre espace de travail Log Analytics.
2. Sélectionnez **Paramètres avancés**, puis **Sources connectées**.
2. À droite de **ID de l’espace de travail**, sélectionnez l’icône de copie, puis collez l’ID en tant que valeur de la variable **ID client**.
3. À droite de **Clé primaire**, sélectionnez l’icône de copie, puis collez l’ID en tant que valeur de la variable **Clé partagée**.

Vous pouvez également modifier les variables pour le type de journal et les données JSON.

### <a name="powershell-sample"></a>Exemple de code PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemple de code C#
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(message);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Exemple de code Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Étapes suivantes
- Utilisez l’[API Recherche de journal](log-analytics-log-search-api.md) pour récupérer des données à partir du référentiel Log Analytics.
