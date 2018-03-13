---
title: "Comprendre le langage de requête d’Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Description du langage de requête IoT Hub de type SQL utilisé pour récupérer des informations sur les jumeaux d’appareil et les travaux à partir de votre hub IoT."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 624f706532645034f19af15d10352dbc6db0b6c1
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>Langage de requête IoT Hub pour les jumeaux d’appareil, les travaux et le routage des messages

IoT Hub fournit un puissant langage de type SQL pour récupérer des informations concernant les [jumeaux d’appareil][lnk-twins], les [travaux][lnk-jobs] et le [routage des messages][lnk-devguide-messaging-routes]. Cet article présente les éléments suivants :

* Une introduction aux principales fonctionnalités du langage de requête d’IoT Hub
* Une description détaillée du langage

## <a name="device-twin-queries"></a>Requêtes de représentations d’appareil
Des [jumeaux d’appareil][lnk-twins] peuvent contenir des objets JSON arbitraires tels que des balises (tags) et des propriétés. IoT Hub vous permet d’interroger des jumeaux d’appareil sous la forme d’un seul document JSON contenant toutes les informations sur les jumeaux d’appareil.
Par exemple, supposons que vos jumeaux d’appareil IoT Hub présentent la structure suivante :

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT Hub expose les jumeaux d’appareil en tant que collection de documents appelée **appareils**.
Par conséquent, la requête suivante récupère l’ensemble des jumeaux d’appareil :

```sql
SELECT * FROM devices
```

> [!NOTE]
> Les kits [Azure IoT SDK][lnk-hub-sdks] prennent en charge la pagination des résultats volumineux.

IoT Hub vous permet de récupérer les jumeaux d’appareil en les filtrant avec des conditions arbitraires. Par exemple, pour obtenir les jumeaux d’appareil dont la balise **location.region** est définie sur **US** (États-Uns), utilisez la requête suivante :

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Les opérateurs booléens et les comparaisons arithmétiques sont également pris en charge. Par exemple, pour récupérer les jumeaux d’appareil situés aux États-Unis et configurés pour envoyer des données de télémétrie à un intervalle supérieur à une minute, utilisez la requête suivante :

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Pour des raisons pratiques, il est également possible d’utiliser des constantes de matrice avec les opérateurs **IN** (dans) et **NIN** (pas dans). Par exemple, pour récupérer les jumeaux d’appareil signalant l’utilisation d’une connectivité Wi-Fi ou câblée, utilisez la requête suivante :

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Il est souvent nécessaire d’identifier tous les jumeaux d’appareil qui contiennent une propriété spécifique. IoT Hub prend en charge la fonction `is_defined()` à cette fin. Par exemple, pour récupérer les jumeaux d’appareil qui définissent la propriété `connectivity`, utilisez la requête suivante :

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Pour une référence complète sur les fonctionnalités de filtrage, consultez la section [clause WHERE][lnk-query-where].

Le regroupement et les agrégations sont également pris en charge. Par exemple, pour savoir combien d’appareils présentent chaque état de configuration de télémétrie, utilisez la requête suivante :

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Cette requête de regroupement retourne un résultat similaire à l’exemple ci-dessous :

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

Dans cet exemple, trois appareils ont signalé une configuration réussie, deux appliquent toujours la configuration et un signale une erreur.

Les requêtes de projection permettent aux développeurs de retourner uniquement les propriétés qui les intéressent. Par exemple, pour récupérer l’heure de la dernière activité de tous les appareils déconnectés, utilisez la requête suivante :

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="c-example"></a>Exemple en code C#
La fonctionnalité de requête est exposée par [Service SDK C#][lnk-hub-sdks] dans la classe **RegistryManager**.
Voici un exemple de requête simple :

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

L’objet **query** est instancié avec une taille de page (jusqu’à 100). Ensuite, plusieurs pages sont récupérées en appelant plusieurs fois les méthodes **GetNextAsTwinAsync**.

L’objet de requête expose plusieurs valeurs **Next**, en fonction de l’option de désérialisation requise par la requête. Par exemple, les objets de travail ou de jumeau d’appareil, ou le code JSON brut lors de l’utilisation de projections.

### <a name="nodejs-example"></a>Exemple de Node.js
La fonctionnalité de requête est exposée par le [Kit de développement logiciel (SDK) de service Azure IoT pour Node.js][lnk-hub-sdks] dans l’objet **Registry**.
Voici un exemple de requête simple :

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

L’objet **query** est instancié avec une taille de page (jusqu’à 100). Ensuite, plusieurs pages sont récupérées en appelant plusieurs fois la méthode **nextAsTwin**.

L’objet de requête expose plusieurs valeurs **Next**, en fonction de l’option de désérialisation requise par la requête. Par exemple, les objets de travail ou de jumeau d’appareil, ou le code JSON brut lors de l’utilisation de projections.

### <a name="limitations"></a>Limites

> [!IMPORTANT]
> Les résultats de la requête peuvent être produits avec quelques minutes de retard par rapport aux dernières valeurs dans les jumeaux d’appareil. Si vous interrogez des jumeaux d’appareil par ID, utilisez l’API de récupération de jumeau d’appareil. Cette API contient toujours les valeurs les plus récentes et ses seuils de limitation sont plus élevés.

Actuellement, les comparaisons ne sont prises en charge qu’entre types primitifs (aucun objet), par exemple `... WHERE properties.desired.config = properties.reported.config` est pris en charge uniquement si ces propriétés ont des valeurs primitives.

## <a name="get-started-with-jobs-queries"></a>Bien démarrer avec les requêtes de travaux

Les [travaux][lnk-jobs] constituent un moyen d’exécuter des opérations sur des ensembles d’appareils. Chaque jumeau d’appareil contient les informations des travaux auxquels il participe dans un regroupement nommé **travaux**.
Sous forme logique,

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Actuellement, ce regroupement peut être interrogé en tant que **devices.jobs** dans le langage de requête d’IoT Hub.

> [!IMPORTANT]
> Actuellement, la propriété jobs n’est jamais retournée lors de l’interrogation de jumeaux d’appareil (autrement dit, les requêtes qui contiennent « FROM devices »). Elle est accessible directement uniquement avec des requêtes utilisant `FROM devices.jobs`.
>
>

Par exemple, pour obtenir tous les travaux (passés et planifiés) qui affectent un appareil donné, vous pouvez utiliser la requête suivante :

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Notez comment cette requête fournit l’état spécifique de l’appareil (et éventuellement la réponse de méthode directe) pour chaque travail retourné.
Il est également possible de filtrer avec des conditions booléennes arbitraires sur toutes les propriétés d’objet du regroupement **devices.jobs**.
Par exemple, pour récupérer tous les travaux de mise à jour de jumeau d’appareil créés après septembre 2016 pour un appareil spécifique, utilisez la requête suivante :

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Vous pouvez également récupérer les résultats d’un travail unique par appareil.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limites
Actuellement, les requêtes sur **devices.jobs** ne prennent pas en charge :

* Les projections, par conséquent seul `SELECT *` est possible.
* Les conditions faisant référence au jumeau d’appareil en plus des propriétés du travail (voir section précédente).
* L’exécution d’agrégations, par exemple, count, avg, group by.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Expressions de requête d’itinéraires de messages appareil-à-cloud

À l’aide des [itinéraires appareil-à-cloud][lnk-devguide-messaging-routes], vous pouvez configurer IoT Hub pour distribuer des messages appareil-à-cloud sur différents points de terminaison. La distribution est basée sur des expressions évaluées par rapport à des messages individuels.

La [condition][lnk-query-expressions] d’itinéraire utilise le même langage de requête IoT Hub que les conditions des requêtes de jumeau et de travail. Les conditions de routage sont évaluées sur les en-têtes et le corps des messages. Votre expression de requête de routage peut impliquer uniquement des en-têtes de message, uniquement le corps du message ou les deux. IoT Hub suppose un schéma spécifique pour les en-têtes et le corps du message afin d’acheminer les messages. Les sections suivantes décrivent ce dont IoT Hub a besoin pour effectuer correctement le routage.

### <a name="routing-on-message-headers"></a>Routage sur les en-têtes de message

IoT Hub suppose la représentation JSON suivante d’en-têtes de message pour le routage des messages :

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Les propriétés système du message ont pour préfixe le symbole `'$'`.
Les propriétés de l’utilisateur sont toujours accessibles par leur nom. Si un nom de propriété d’utilisateur coïncide avec une propriété système (telle que `$contentType`), la propriété de l’utilisateur est récupérée avec l’expression `$contentType`.
Vous pouvez toujours accéder à la propriété système à l’aide de crochets `{}` : par exemple, vous pouvez utiliser l’expression `{$contentType}` pour accéder à la propriété système `contentType`. Les noms de propriétés entre crochets récupèrent toujours la propriété système correspondante.

N’oubliez pas que les noms de propriété respectent la casse.

> [!NOTE]
> Toutes les propriétés de message sont des chaînes. Les propriétés système, comme décrit dans le [guide du développeur][lnk-devguide-messaging-format], ne sont actuellement pas disponibles pour utilisation dans les requêtes.
>

Par exemple, si vous utilisez une propriété `messageType`, vous souhaiterez peut-être acheminer toutes les données de télémétrie vers un point de terminaison et toutes les alertes vers un autre point de terminaison. Vous pouvez écrire l’expression suivante pour acheminer les données de télémétrie :

```sql
messageType = 'telemetry'
```

Et l’expression suivante pour acheminer les messages d’alerte :

```sql
messageType = 'alert'
```

Les fonctions et expressions booléennes sont également prises en charge. Cette fonctionnalité vous permet de faire la distinction entre les niveaux de gravité, par exemple :

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Reportez-vous à la section [Expression et conditions][lnk-query-expressions] pour obtenir la liste complète des fonctions et opérateurs pris en charge.

### <a name="routing-on-message-bodies"></a>Routage sur les corps de message

IoT Hub peut effectuer le routage en fonction du contenu du corps du message seulement si le corps du message se présente sous un format JSON adéquat encodé en UTF-8, UTF-16 ou UTF-32. Affectez `application/json` comme type de contenu du message. Définissez l’un des encodages UTF pris en charge dans les en-têtes de message comme encodage du contenu. Si l’un des en-têtes n’est pas spécifié, IoT Hub n’essaie d’évaluer aucune expression de requête impliquant le corps de message. Si votre message n’est pas un message JSON ou s’il ne spécifie pas le type de contenu et l’encodage du contenu, vous pouvez toujours utiliser le routage des messages pour acheminer le message en fonction des en-têtes de message.

L’exemple suivant montre comment créer un message avec un corps JSON correctement formé et encodé :

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

Vous pouvez utiliser `$body` dans l’expression de requête pour acheminer le message. Vous pouvez utiliser une référence de corps simple, une référence de tableau de corps ou plusieurs références de corps dans l’expression de requête. Votre expression de requête peut également combiner une référence de corps avec une référence d’en-tête de message. Par exemple, toutes les expressions de requête suivantes sont valides :

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Principes de base d’une requête IoT Hub
Chaque requête IoT Hub se compose de clauses SELECT et FROM, avec des clauses WHERE et GROUP BY facultatives. Chaque requête est exécutée sur un regroupement de documents JSON, par exemple des jumeaux d’appareil. La clause FROM indique le regroupement de documents sur lequel elle doit être itérée (**devices** ou **devices.jobs**). Ensuite, le filtre dans la clause WHERE est appliqué. Avec des agrégations, les résultats de cette étape sont regroupés tel que spécifié dans la clause GROUP BY. Pour chaque groupe, une ligne est générée tel que spécifié dans la clause SELECT.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Clause FROM
La clause **FROM <from_specification>** ne peut supposer que deux valeurs : **FROM devices** pour interroger les jumeaux d’appareil ou **FROM devices.jobs** pour interroger les détails de travaux par appareil.

## <a name="where-clause"></a>Clause WHERE
La clause **WHERE <filter_condition>** est facultative. Elle indique une ou plusieurs conditions que les documents JSON du regroupement FROM doivent remplir pour être inclus dans le résultat. Pour être inclus dans le résultat, chaque document JSON doit évaluer les conditions spécifiées comme « true ».

Les conditions autorisées sont décrites dans la section [Expressions et conditions][lnk-query-expressions].

## <a name="select-clause"></a>Clause SELECT
La clause **SELECT <select_list>**) est obligatoire. Elle spécifie les valeurs qui sont récupérées de la requête. Elle spécifie les valeurs JSON à utiliser pour générer de nouveaux objets JSON.
Pour chaque élément du sous-ensemble filtré (et éventuellement groupé) du regroupement FROM, la phase de projection génère un nouvel objet JSON. Cet objet est construit avec les valeurs spécifiées dans la clause SELECT.

La grammaire de la clause SELECT est la suivante :

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** fait référence à n’importe quelle propriété du document JSON dans le regroupement FROM. Vous trouverez des exemples de clauses SELECT dans la section [Bien démarrer avec les requêtes de jumeau d’appareil][lnk-query-getstarted].

Actuellement, les clauses de sélection autres que **SELECT*** sont prises en charge uniquement dans les requêtes d’agrégation sur des jumeaux d’appareil.

## <a name="group-by-clause"></a>Clause GROUP BY
La clause **GROUP BY <spécification_groupe>** est une étape facultative exécutée après le filtre spécifié dans la clause WHERE et avant la projection spécifiée dans la clause SELECT. Elle groupe des documents en fonction de la valeur d’un attribut. Ces groupes sont utilisés pour générer des valeurs agrégées comme spécifié dans la clause SELECT.

Voici un exemple de requête utilisant la clause GROUP BY :

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

La syntaxe formelle de la clause GROUP BY est la suivante :

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** fait référence à n’importe quelle propriété du document JSON dans le regroupement FROM.

Actuellement, la clause GROUP BY est prise en charge uniquement lors de l’interrogation de jumeaux d’appareil.

## <a name="expressions-and-conditions"></a>Expressions et conditions
À un niveau élevé, une *expression* :

* prend la valeur d’une instance d’un type JSON (par exemple, Boolean, number, string, array ou object) ;
* est définie en manipulant des données provenant du document JSON de l’appareil et des constantes à l’aide de fonctions et d’opérateurs intégrés.

Les *Conditions* sont des expressions qui correspondent à une valeur booléenne. Toute constante autre qu’une valeur booléenne **true** est considérée comme **false**. Cette règle inclut **null**, **undefined**, toute instance d’objet ou de tableau, toute chaîne et la valeur booléenne **false**.

La syntaxe des expressions est la suivante :

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Pour comprendre ce que signifie chaque symbole dans la syntaxe des expressions, consultez le tableau suivant :

| Symbole | Définition |
| --- | --- |
| attribute_name | Toute propriété du document JSON dans le regroupement **FROM**. |
| binary_operator | Tout opérateur binaire répertorié dans la section [Operators](#operators). |
| function_name| Toutes les fonctions répertoriées dans la section [Fonctions](#functions). |
| decimal_literal |Variable exprimée en notation décimale. |
| hexadecimal_literal |Nombre exprimé par la chaîne « 0x » suivi d’une chaîne de chiffres hexadécimaux. |
| string_literal |Les littéraux de chaîne sont des chaînes Unicode représentées par une séquence de zéro ou plusieurs caractères Unicode ou séquences d’échappement. Les littéraux de chaîne sont placés entre guillemets simples ou guillemets doubles. Échappements autorisés : `\'`, `\"`, `\\`, `\uXXXX` pour les caractères Unicode définis par 4 chiffres hexadécimaux. |

### <a name="operators"></a>Opérateurs
Les opérateurs suivants sont pris en charge :

| Famille | Operators |
| --- | --- |
| Opérateurs arithmétiques |+, -, *, /, % |
| Opérateurs logiques |AND, OR, NOT |
| Opérateurs de comparaison |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Lors des requêtes de jumeaux ou de travaux, la seule fonction prise en charge est :

| Fonction | Description |
| -------- | ----------- |
| IS_DEFINED(property) | Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété (dont `null`). |

Dans les conditions d’itinéraire, les fonctions mathématiques suivantes sont prises en charge :

| Fonction | Description |
| -------- | ----------- |
| ABS(x) | Retourne la valeur (positive) absolue de l'expression numérique spécifiée. |
| EXP(x) | Retourne la valeur exponentielle de l'expression numérique spécifiée (e^x). |
| POWER(x,y) | Retourne la valeur de l’expression spécifiée élevée à la puissance spécifiée (x^y).|
| SQUARE(x) | Retourne le carré de la valeur numérique spécifiée. |
| CEILING(x) | Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée. |
| FLOOR(x) | Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée. |
| SIGN(x) | Retourne le signe positif (+1), nul (0) ou négatif (-1) de l'expression numérique spécifiée.|
| SQRT(x) | Retourne la racine carrée de la valeur numérique spécifiée. |

Dans les conditions d’itinéraire, les fonctions de vérification et de conversion de type suivantes sont prises en charge :

| Fonction | Description |
| -------- | ----------- |
| AS_NUMBER | Convertit la chaîne d’entrée en nombre. `noop` si l’entrée est un nombre ; `Undefined` si la chaîne ne représente pas un nombre.|
| IS_ARRAY | Retourne une valeur booléenne indiquant si l’expression spécifiée est du type tableau. |
| IS_BOOL | Retourne une valeur booléenne indiquant si l’expression spécifiée est du type booléen. |
| IS_DEFINED | Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété. |
| IS_NULL | Retourne une valeur booléenne indiquant si l’expression spécifiée est de type null. |
| IS_NUMBER | Retourne une valeur booléenne indiquant si l’expression spécifiée est du type nombre. |
| IS_OBJECT | Retourne une valeur booléenne indiquant si l’expression spécifiée est du type objet JSON. |
| IS_PRIMITIVE | Retourne une valeur booléenne indiquant si l’expression spécifiée est de type primitif (chaîne, booléen, numérique ou `null`). |
| IS_STRING | Retourne une valeur booléenne indiquant si l’expression spécifiée est du type chaîne. |

Dans les conditions d’itinéraire, les fonctions de chaîne suivantes sont prises en charge :

| Fonction | Description |
| -------- | ----------- |
| CONCAT(x, y, …) | Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne. |
| LENGTH(x) | Retourne le nombre de caractères de l’expression de chaîne spécifiée.|
| LOWER(x) | Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules. |
| UPPER(x) | Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules. |
| SUBSTRING(string, start [, length]) | Renvoie une partie d’une expression de chaîne commençant à la position de caractère spécifiée (avec base zéro) et se poursuit jusqu'à la longueur spécifiée ou à la fin de la chaîne. |
| INDEX_OF(string, fragment) | Retourne la position de départ de la première occurrence de la seconde expression de chaîne dans la première expression de chaîne spécifiée, ou -1 si la chaîne est introuvable.|
| STARTS_WITH(x, y) | Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde. |
| ENDS_WITH(x, y) | Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde. |
| CONTAINS(x,y) | Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde. |

## <a name="next-steps"></a>étapes suivantes
Découvrez comment exécuter des requêtes dans vos applications à l’aide des [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
