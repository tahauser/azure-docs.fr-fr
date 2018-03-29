---
title: Mappages de champs dans les indexeurs de la Recherche Azure
description: Configurer les mappages de champs de l'indexeur Azure Search pour tenir compte des différences dans les noms de champs et les représentations des données
services: search
documentationcenter: ''
author: chaosrealm
manager: pablocas
editor: ''
ms.assetid: 0325a4de-0190-4dd5-a64d-4e56601d973b
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/30/2017
ms.author: eugenesh
ms.openlocfilehash: e4d6960e540641405b879064a8064d45521dc04f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="field-mappings-in-azure-search-indexers"></a>Mappages de champs dans les indexeurs de la Recherche Azure
Lorsque vous utilisez des indexeurs Azure Search, vous pouvez parfois vous retrouver dans des situations où vos données d'entrée ne correspondent pas tout à fait au schéma de votre index cible. Dans ce cas, vous pouvez utiliser les **mappages de champs** pour transformer vos données au format souhaité.

Quelques situations où les mappages de champs sont utiles :

* Votre source de données a un champ `_id`, mais Azure Search n'autorise pas les noms de champs commençant par un trait de soulignement. Un mappage de champs vous permet de « renommer » un champ.
* Vous souhaitez remplir plusieurs champs de l'index avec les mêmes données de source de données, par exemple parce que vous souhaitez appliquer différents analyseurs à ces champs. Les mappages de champs vous permettent de « répliquer » un champ de source de données.
* Vous avez besoin d’encoder ou de décoder vos données en Base64. Les mappages de champs prennent en charge plusieurs **fonctions de mappage**, y compris les fonctions d’encodage et de décodage en Base64.   

## <a name="setting-up-field-mappings"></a>Configuration des mappages de champs
Vous pouvez ajouter des mappages de champs lors de la création d'un indexeur avec l’API [Créer un indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx) . Vous pouvez gérer les mappages de champs sur un indexeur d'indexation avec l’API [Mettre à jour l’indexeur](https://msdn.microsoft.com/library/azure/dn946892.aspx) .

Un mappage de champs se compose de trois parties :

1. Un `sourceFieldName`, qui représente un champ de votre source de données. Cette propriété est requise.
2. Un `targetFieldName`facultatif, qui représente un champ de votre index de recherche. Si omis, le nom de la source de données est utilisé.
3. Une `mappingFunction`facultative, qui peut transformer vos données à l'aide d'une des fonctions prédéfinies. La liste complète des fonctions est présentée [ci-dessous](#mappingFunctions).

Les mappages de champs sont ajoutés au tableau `fieldMappings` sur la définition de l'indexeur.

Par exemple, voici comment gérer les différences dans les noms de champs :

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Un indexeur peut avoir plusieurs mappages de champs. Par exemple, voici comment « répliquer » un champ :

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search utilise une comparaison qui ne respecte pas la casse pour résoudre les noms de champ et de fonction dans les mappages de champs. C'est pratique (vous n'avez besoin de faire attention à la casse), mais cela signifie que votre source de données et votre index ne peuvent pas comporter des champs qui diffèrent uniquement par la casse.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fonctions de mappage de champs
Ces fonctions sont actuellement prises en charge :

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
Exécute l’encodage Base64 *sécurisé pour les URL* de la chaîne d'entrée. Suppose que l'entrée est encodée en UTF-8.

### <a name="sample-use-case---document-key-lookup"></a>Exemple de cas d’utilisation : recherche de clé de document
Seuls les caractères sécurisés pour les URL peuvent apparaître dans une clé de document Recherche Azure (car les clients doivent pouvoir traiter le document à l’aide de l’[API de recherche](https://docs.microsoft.com/rest/api/searchservice/lookup-document), par exemple). Si vos données contiennent des caractères non sécurisés pour les URL et que vous souhaitez les utiliser pour remplir un champ de clé de votre index de recherche, utilisez cette fonction. Une fois la clé encodée, vous pouvez utiliser le décodage base64 pour récupérer la valeur d’origine. Pour plus d’informations, consultez la section [Encodage et décodage base64](#base64details).

#### <a name="example"></a>Exemples
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>Exemple de cas d’utilisation : récupérer la clé d’origine
Vous disposez d’un indexeur d’objets blob qui indexe les objets blob avec les métadonnées des chemins d’objets blob en tant que clé de document. Après avoir récupéré la clé de document encodée, vous pouvez décoder le chemin et télécharger l’objet blob.

#### <a name="example"></a>Exemples
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
```

Si vous n’avez pas besoin de rechercher des documents par clés ni même de décoder le contenu encodé, vous pouvez omettre `parameters` pour la fonction de mappage, qui attribue par défaut à `useHttpServerUtilityUrlTokenEncode` la valeur `true`. Sinon, consultez la section [Détails de base64](#base64details) pour décider de quels paramètres utiliser.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
Effectue le décodage en Base64 de la chaîne d'entrée. L'entrée est considérée comme une chaîne encodée en Base64 *sécurisée pour les URL* .

### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Les valeurs des métadonnées personnalisées des objets blob doivent être encodées en ASCII. Vous pouvez utiliser l’encodage Base64 pour représenter des chaînes UTF-8 arbitraires dans les métadonnées personnalisées des objets blob. Toutefois, pour rendre la recherche explicite, vous pouvez utiliser cette fonction pour transformer les données encodées en chaînes « normales » lors du remplissage de votre index de recherche.

#### <a name="example"></a>Exemples
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

Si vous ne spécifiez pas `parameters`, la valeur par défaut de `useHttpServerUtilityUrlTokenDecode` est `true`. Consultez la section [Détails de base64](#base64details) pour décider de quels paramètres utiliser.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>Détails de l’encodage et du décodage base64
Recherche Azure prend en charge deux encodages base64 : l’encodage de jetons d’URL HttpServerUtility et l’encodage base64 sécurisé pour les URL sans remplissage. Si vous voulez encoder une clé de document pour la recherche, encoder une valeur à faire décoder par l’indexeur ou décoder un champ encodé par l’indexeur, vous devez utiliser le même encodage que les fonctions de mappage.

Si vous utilisez le .NET Framework, vous pouvez définir `useHttpServerUtilityUrlTokenEncode` et `useHttpServerUtilityUrlTokenDecode` sur `true` pour encoder et décoder, respectivement. Dès lors, `base64Encode` se comporte comme [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) et `base64Decode` comme [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Si vous n’utilisez pas le .NET Framework, vous devez attribuer à `useHttpServerUtilityUrlTokenEncode` et `useHttpServerUtilityUrlTokenDecode` la valeur `false`. Selon la bibliothèque que vous utilisez, les fonctions utilitaires d’encodage et de décodage base64 peuvent être différentes de Recherche Azure.

Le tableau suivant compare les différents encodages base64 de la chaîne `00>00?00`. Pour déterminer quel traitement supplémentaire s’avère nécessaire (le cas échéant) pour vos fonctions base64, appliquez votre fonction d’encodage de bibliothèque à la chaîne `00>00?00` et comparez le résultat obtenu au résultat attendu `MDA-MDA_MDA`.

| Encodage | Résultat d’encodage base64 | Traitement supplémentaire après l’encodage de bibliothèque | Traitement supplémentaire avant l’encodage de bibliothèque |
| --- | --- | --- | --- |
| Base64 avec remplissage | `MDA+MDA/MDA=` | Utiliser des caractères sécurisés pour les URL et supprimer le remplissage | Utiliser des caractères base64 standard et ajouter le remplissage |
| Base64 sans remplissage | `MDA+MDA/MDA` | Utiliser des caractères sécurisés pour les URL | Utiliser des caractères base64 standard |
| Base64 sécurisé pour les URL avec remplissage | `MDA-MDA_MDA=` | Supprimer le remplissage | Ajouter le remplissage |
| Base64 sécurisé pour les URL sans remplissage | `MDA-MDA_MDA` | Aucun | Aucun |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
Divise un champ de chaîne en utilisant le séparateur spécifié et récupère le jeton à la position spécifiée dans le fractionnement résultant.

Par exemple, si l’entrée est `Jane Doe`, que le `delimiter` est `" "` (espace) et que la `position` est 0, le résultat est `Jane` ; si la `position` est 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n’existe pas, une erreur est retournée.

### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Votre source de données contient un champ `PersonName` et vous souhaitez l’indexer en tant que deux champs `FirstName` et `LastName` distincts. Vous pouvez utiliser cette fonction pour fractionner l'entrée en utilisant l'espace comme séparateur.

### <a name="parameters"></a>parameters
* `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d'entrée.
* `position`: une position entière à base zéro du jeton à choisir une fois la chaîne d'entrée fractionnée.    

### <a name="example"></a>Exemples
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
Transforme une chaîne formatée en tant que tableau de chaînes JSON en un tableau de chaînes utilisable pour remplir un champ `Collection(Edm.String)` dans l'index.

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` est rempli avec les valeurs `red`, `white` et `blue`. Pour les valeurs d’entrée qui ne peuvent pas être analysées en tant que tableaux de chaînes JSON, une erreur est retournée.

### <a name="sample-use-case"></a>Exemple de cas d’utilisation
Une base de données SQL Azure n'a pas de type de données intégré qui se mappe naturellement aux champs `Collection(Edm.String)` dans Azure Search. Pour remplir les champs de la collection de chaînes, mettez vos données sources sous forme de tableau de chaînes JSON et utilisez cette fonction.

### <a name="example"></a>Exemples
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous souhaitez nous soumettre des demandes d’ajout de fonctionnalités ou des idées d’amélioration, n’hésitez pas à nous contacter sur notre [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
