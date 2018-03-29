---
title: Expressions et fonctions dans Azure Data Factory | Microsoft Docs
description: Cet article fournit des informations sur les expressions et fonctions que vous pouvez utiliser pour la création d’entités de fabrique de données.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 1625b37a41082f8536d103701b1356a13a5dd837
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressions et fonctions dans Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 - Disponibilité générale](v1/data-factory-functions-variables.md)
> * [Version 2 - Préversion](control-flow-expression-language-functions.md)

Cet article fournit des détails sur les expressions et fonctions prises en charge par Azure Data Factory (version 2). 

## <a name="introduction"></a>Introduction
Les valeurs JSON indiquées dans la définition peuvent être littérales. Il peut également s’agir d’expressions évaluées lors de l’exécution du runtime. Par exemple :   
  
```json
"name": "value"
```

 (ou)  
  
```json
"name": "@pipeline().parameters.password"
```


> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible (GA), voir [Fonctions et variables dans Data Factory V1](v1/data-factory-functions-variables.md).


## <a name="expressions"></a>Expressions  
Les expressions peuvent apparaître n’importe où dans une valeur de chaîne JSON, et entraînent toujours une autre valeur JSON. Si une valeur JSON est une expression, le corps de celle-ci est extrait en supprimant l’arobase (@). Si une chaîne littérale devant commencer par @ est nécessaire, elle doit être placée dans une séquence d’échappement en utilisant @@. Les exemples suivants montrent comment les expressions sont évaluées.  
  
|Valeur JSON|Résultat|  
|----------------|------------|  
|« parameters »|Les caractères « parameters » sont retournés.|  
|« parameters[1] »|Les caractères « parameters[1] » sont retournés.|  
|« @@ »|Une chaîne de 1 caractère contenant \«\@\» est retournée.|  
|\« \@»|Une chaîne de 2 caractères contenant \« \@ \» est retournée.|  
  
 Des expressions peuvent également apparaître dans des chaînes, qui utilisent une fonctionnalité appelée *interpolation de chaîne* où les expressions sont encapsulées dans `@{ ... }`. Par exemple : `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 En utilisant une interpolation de chaîne, le résultat est toujours une chaîne. Supposons que j’ai défini `myNumber` comme `42` et `myString` comme `foo` :  
  
|Valeur JSON|Résultat|  
|----------------|------------|  
|"@pipeline().parameters.myString"| Retourne `foo` en tant que chaîne.|  
|"@{pipeline().parameters.myString}"| Retourne `foo` en tant que chaîne.|  
|"@pipeline().parameters.myNumber"| Retourne `42` en tant que *nombre*.|  
|"@{pipeline().parameters.myNumber}"| Retourne `42` en tant que *chaîne*.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Retourne la chaîne `Answer is: 42`.|  
|"@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Retourne la chaîne `Answer is: 42`.|  
|"Answer is: @@{pipeline().parameters.myNumber}"| Retourne la chaîne `Answer is: @{pipeline().parameters.myNumber}`.|  
  
### <a name="examples"></a>Exemples

#### <a name="a-dataset-with-a-parameter"></a>Un jeu de données avec un paramètre
Dans l’exemple suivant, BlobDataset prend un paramètre nommé **path**. Sa valeur est utilisée pour donner une valeur à la propriété **folderPath** à l’aide des expressions suivantes : `@{dataset().path}`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

#### <a name="a-pipeline-with-a-parameter"></a>Un pipeline avec un paramètre
Dans l’exemple suivant, le pipeline prend les paramètres **inputPath** et **outputPath**. Le **path** du jeu de données blob paramétrable est défini par les valeurs de ces paramètres. Voici la syntaxe utilisée : `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
  
## <a name="functions"></a>Functions  
 Vous pouvez appeler des fonctions dans des expressions. Les sections suivantes fournissent des informations sur les fonctions qui peut être utilisées dans une expression.  

## <a name="string-functions"></a>Fonctions de chaîne  
 Les fonctions suivantes s’appliquent uniquement aux chaînes. Vous pouvez également utiliser un certain nombre des fonctions de collection sur des chaînes.  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|concat|Combine plusieurs chaînes. Par exemple, si le paramètre 1 est `foo,`, l’expression suivante retourne `somevalue-foo-somevalue` : `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Numéro du paramètre** : 1 ... *n*<br /><br /> **Nom** : chaîne *n*<br /><br /> **Description** : obligatoire. Chaînes à combiner en une seule chaîne.|  
|substring|Retourne un sous-ensemble de caractères d’une chaîne. Par exemple, l’expression suivante :<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> retourne :<br /><br /> `foo`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne dont la sous-chaîne est extraite.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : index de début<br /><br /> **Description** : obligatoire. Index de début de la sous-chaîne dans le paramètre 1.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : longueur<br /><br /> **Description** : obligatoire. Longueur de la sous-chaîne.|  
|remplacer|Remplace une chaîne par une chaîne donnée. Par exemple, l’expression :<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> retourne :<br /><br /> `the new string`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire.  Si le paramètre 2 figure dans le paramètre 1, chaîne recherchée pour le paramètre 2 et mise à jour avec le paramètre 3.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : ancienne chaîne<br /><br /> **Description** : obligatoire. Chaîne à remplacer par le paramètre 3 quand une correspondance est trouvée dans le paramètre 1<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : nouvelle chaîne<br /><br /> **Description** : obligatoire. Chaîne utilisée pour remplacer la chaîne du paramètre 2 lorsqu’une correspondance est trouvée dans le paramètre 1.|  
|GUID| Génère une chaîne globale unique (guid). Par exemple, la sortie suivante peut être générée `c2ecc88d-88c8-4096-912c-d6f2e2b138ce` :<br /><br /> `guid()`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. Spécificateur de format unique qui indique [comment mettre en forme la valeur de ce GUID](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). Le paramètre de format peut être « N », « D », « B », « P » ou « X ». Si aucun format n’est indiqué, « D » est utilisé.|  
|toLower|Convertit une chaîne en minuscules. Par exemple, ce qui suit retourne `two by two is four` : `toLower('Two by Two is Four')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne à convertir en minuscules. Si un caractère de la chaîne n’a pas d’équivalent minuscule, il est inclus tel quel dans la chaîne retournée.|  
|toUpper|Convertit une chaîne en majuscules. Par exemple, l’expression suivante retourne `TWO BY TWO IS FOUR` : `toUpper('Two by Two is Four')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne à convertir en majuscules. Si un caractère de la chaîne n’a pas d’équivalent majuscule, il est inclus tel quel dans la chaîne retournée.|  
|indexof|Recherche l’index d’une valeur contenue dans une chaîne sans tenir compte de la casse. Par exemple, l’expression suivante retourne `7` : `indexof('hello, world.', 'world')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne pouvant contenir la valeur.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Valeur permettant d’en rechercher l’index.|  
|lastindexof|Recherche le dernier index d’une valeur contenue dans une chaîne sans tenir compte de la casse. Par exemple, l’expression suivante retourne `3` : `lastindexof('foofoo', 'foo')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne pouvant contenir la valeur.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Valeur permettant d’en rechercher l’index.|  
|startswith|Vérifie si la chaîne commence par une valeur sans tenir compte de la casse. Par exemple, l’expression suivante retourne `true` : `lastindexof('hello, world', 'hello')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne pouvant contenir la valeur.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Valeur de début susceptible de la chaîne.|  
|endswith|Vérifie si la chaîne se termine par une valeur sans tenir compte de la casse. Par exemple, l’expression suivante retourne `true` : `lastindexof('hello, world', 'world')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne pouvant contenir la valeur.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Valeur de fin susceptible de la chaîne.|  
|split|Fractionne la chaîne à l’aide d’un séparateur. Par exemple, l’expression suivante retourne `["a", "b", "c"]` : `split('a;b;c',';')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne qui est fractionnée.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Séparateur.|  
  
  
## <a name="collection-functions"></a>Fonctions de collection  
 Ces fonctions opèrent sur des collections telles que des tableaux, des chaînes et parfois des dictionnaires.  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|contains|Retourne la valeur true si le dictionnaire contient une clé, si la liste contient une valeur ou si la chaîne contient une sous-chaîne. Par exemple, l’expression suivante retourne `true:``contains('abacaba','aca')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : au sein de la collection<br /><br /> **Description** : obligatoire. Collection dans laquelle effectuer une recherche.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : rechercher un objet<br /><br /> **Description** : obligatoire. Objet à rechercher dans **Au sein de la collection**.|  
|length|Retourne le nombre d’éléments contenus dans un tableau ou une chaîne. Par exemple, l’expression suivante retourne `3` : `length('abc')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection dont obtenir la longueur.|  
|empty|Retourne la valeur true si l’objet, le tableau ou la chaîne est vide. Par exemple, l’expression suivante retourne `true` :<br /><br /> `empty('')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection vérifiée pour voir si elle est vide.|  
|intersection|Retourne un tableau ou un objet avec les éléments communs entre les tableaux ou objets qui lui sont transmis. Par exemple, cette fonction retourne `[1, 2]` :<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Les paramètres de la fonction peuvent être un ensemble d’objets ou un ensemble de tableaux (pas une combinaison de ceux-ci). Si deux objets portent le même nom, le dernier objet portant ce nom s’affiche dans l’objet final.<br /><br /> **Numéro du paramètre** : 1 ... *n*<br /><br /> **Nom** : collection *n*<br /><br /> **Description** : obligatoire. Collections à évaluer. Un objet doit figurer dans toutes les collections transmises pour apparaître dans le résultat.|  
|union|Retourne un tableau ou un objet avec tous les éléments contenus dans le tableau ou l’objet qui lui sont transmis. Par exemple, cette fonction retourne `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Les paramètres de la fonction peuvent être un ensemble d’objets ou un ensemble de tableaux (pas une combinaison de ceux-ci). Si deux objets portent le même nom dans la sortie finale, le dernier objet portant ce nom s’affiche dans l’objet final.<br /><br /> **Numéro du paramètre** : 1 ... *n*<br /><br /> **Nom** : collection *n*<br /><br /> **Description** : obligatoire. Collections à évaluer. Un objet apparaissant dans l’une des collections apparaît dans le résultat.|  
|first|Retourne le premier élément de la chaîne ou du tableau transmis. Par exemple, cette fonction retourne `0` :<br /><br /> `first([0,2,3])`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection dont il convient de prendre le premier objet.|  
|last|Retourne le dernier élément de la chaîne ou du tableau transmis. Par exemple, cette fonction retourne `3` :<br /><br /> `last('0123')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection dans laquelle prendre le dernier objet.|  
|take|Retourne les premiers éléments **Count** du tableau ou de la chaîne transmis. Par exemple, cette fonction retourne `[1, 2]` : `take([1, 2, 3, 4], 2)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection de laquelle extraire les premiers objets **Count**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : nombre<br /><br /> **Description** : obligatoire. Nombre d’objets à prendre dans la **Collection**. Cette valeur doit être un entier positif.|  
|skip|Retourne les éléments du tableau en commençant à l’index **Count**. Par exemple, cette fonction retourne `[3, 4]` :<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection<br /><br /> **Description** : obligatoire. Collection dans laquelle ignorer les premiers objets **Nombre**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : nombre<br /><br /> **Description** : obligatoire. Nombre d’objets à supprimer du début de la **Collection**. Cette valeur doit être un entier positif.|  
  
## <a name="logical-functions"></a>Fonctions logiques  
 Ces fonctions sont utiles à l’intérieur de conditions, et permettent d’évaluer tout type de logique.  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|equals|Retourne la valeur true si deux valeurs sont égales. Par exemple, si le paramètre 1 est foo, l’expression suivante retourne `true` : `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : objet 1<br /><br /> **Description** : obligatoire. Objet à comparer à **Objet 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : objet 2<br /><br /> **Description** : obligatoire. Objet à comparer à **Objet 1**.|  
|less|Retourne la valeur true si le premier argument est inférieur au second. Les valeurs ne peuvent être que du type entier, flottant ou chaîne. Par exemple, l’expression suivante retourne `true` : `less(10,100)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : objet 1<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est inférieur à **Objet 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : objet 2<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est supérieur à **Objet 1**.|  
|lessOrEquals|Retourne la valeur true si le premier argument est inférieur ou égal au second. Les valeurs ne peuvent être que du type entier, flottant ou chaîne. Par exemple, l’expression suivante retourne `true` : `lessOrEquals(10,10)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : objet 1<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est inférieur ou égal à **Objet 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : objet 2<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est supérieur ou égal à **Objet 1**.|  
|greater|Retourne la valeur true si le premier argument est supérieur au second. Les valeurs ne peuvent être que du type entier, flottant ou chaîne. Par exemple, l’expression suivante retourne `false` : `greater(10,10)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : objet 1<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est supérieur à **Objet 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : objet 2<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est inférieur à **Objet 1**.|  
|greaterOrEquals|Retourne la valeur true si le premier argument est supérieur ou égal au second. Les valeurs ne peuvent être que du type entier, flottant ou chaîne. Par exemple, l’expression suivante retourne `false` : `greaterOrEquals(10,100)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : objet 1<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est supérieur ou égal à **Objet 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : objet 2<br /><br /> **Description** : obligatoire. Objet à vérifier pour voir s’il est inférieur ou égal à **Objet 1**.|  
|and|Retourne la valeur true si les deux paramètres ont la valeur true. Les deux arguments doivent être des booléens. L’argument suivant retourne `false` : `and(greater(1,10),equals(0,0))`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : booléen 1<br /><br /> **Description** : obligatoire. Premier argument qui doit être `true`.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : booléen 2<br /><br /> **Description** : obligatoire. Second argument qui doit être `true`.|  
|or|Retourne la valeur true si l’un des paramètres a la valeur true. Les deux arguments doivent être des booléens. L’argument suivant retourne `true` : `or(greater(1,10),equals(0,0))`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : booléen 1<br /><br /> **Description** : obligatoire. Premier argument pouvant être `true`.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : booléen 2<br /><br /> **Description** : obligatoire. Second argument pouvant être `true`.|  
|not|Retourne la valeur true si le paramètre a la valeur `false`. Les deux arguments doivent être des booléens. L’argument suivant retourne `true` : `not(contains('200 Success','Fail'))`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : booléen<br /><br /> **Description** : retourne la valeur true si le paramètre a la valeur `false`. Les deux arguments doivent être des booléens. L’argument suivant retourne `true` : `not(contains('200 Success','Fail'))`|  
|if|Retourne une valeur spécifiée selon que le résultat de l’expression fournie est `true` ou `false`.  Par exemple, l’expression suivante retourne `"yes"` : `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : expression<br /><br /> **Description** : obligatoire. Valeur booléenne déterminant la valeur retournée par l’expression.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : true<br /><br /> **Description** : obligatoire. Valeur à retourner si l’expression est `true`.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : false<br /><br /> **Description** : obligatoire. Valeur à retourner si l’expression est `false`.|  
  
## <a name="conversion-functions"></a>Fonctions de conversion  
 Ces fonctions permettent de convertir chacun des types natifs du langage :  
  
-   chaîne  
  
-   integer  
  
-   float  
  
-   booléenne  
  
-   tableaux  
  
-   dictionnaires  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|int|Convertit le paramètre en entier. Par exemple, l’expression suivante retourne 100 en tant que nombre, au lieu d’une chaîne : `int('100')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie en entier.|  
|chaîne|Convertit le paramètre en chaîne. Par exemple, l’expression suivante retourne `'10'` : `string(10)` vous pouvez également convertir un objet en chaîne, par exemple, si le paramètre **foo** est un objet avec une seule propriété `bar : baz`, puis les paramètres suivants retournent `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie en chaîne.|  
|json|Convertissez le paramètre en valeur de type JSON. C’est l’opposé de string(). Par exemple, l’expression suivante retourne `[1,2,3]` en tant que tableau, plutôt qu’en tant que chaîne :<br /><br /> `parse('[1,2,3]')`<br /><br /> Vous pouvez également convertir une chaîne en objet. Par exemple, `json('{"bar" : "baz"}')` retourne :<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne qui est convertie en valeur de type natif.<br /><br /> La fonction json prend en charge également les entrées xml. Par exemple, la valeur du paramètre :<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> est convertie dans le code json suivant :<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|Convertit l’argument de paramètre en nombre à virgule flottante. Par exemple, l’expression suivante retourne `10.333` : `float('10.333')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie en nombre à virgule flottante.|  
|bool|Convertit le paramètre en booléen. Par exemple, l’expression suivante retourne `false` : `bool(0)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie en booléen.|  
|coalesce|Retourne le premier objet non Null dans les arguments transmis. Remarque : une chaîne vide n’a pas la valeur Null. Par exemple, si les paramètres 1 et 2 ne sont pas définis, cette expression retourne `fallback` : `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Numéro du paramètre** : 1 ... *n*<br /><br /> **Nom** : Objet *n*<br /><br /> **Description** : obligatoire. Objets dans lesquels rechercher `null`.|  
|base64|Retourne la représentation en base 64 de la chaîne d'entrée. Par exemple, l’expression suivante retourne `c29tZSBzdHJpbmc=` : `base64('some string')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne 1<br /><br /> **Description** : obligatoire. Chaîne à encoder en représentation en base64.|  
|base64ToBinary|Retourne une représentation binaire d’une chaîne encodée en base64. Par exemple, l’expression suivante retourne la représentation binaire d’une chaîne : `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne encodée en Base64.|  
|base64ToString|Retourne une représentation de chaîne d’une chaîne encodée en base64. Par exemple, l’expression suivante retourne une chaîne : `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne encodée en Base64.|  
|Binary|Retourne une représentation binaire d’une valeur.  Par exemple, l’expression suivante retourne une représentation binaire d’une chaîne : `binary(‘some string’).`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie au format binaire.|  
|dataUriToBinary|Retourne une représentation binaire d’un URI de données. Par exemple, l’expression suivante retourne la représentation binaire d’une chaîne : `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. URI de données à convertir en représentation binaire.|  
|dataUriToString|Retourne une représentation de chaîne d’un URI de données. Par exemple, l’expression suivante retourne une chaîne : `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br />**Description** : obligatoire. URI de données à convertir en représentation de chaîne.|  
|dataUri|Retourne un URI de données d’une valeur. Par exemple, l’expression suivante retourne des données : `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Numéro du paramètre** : 1<br /><br />**Nom** : valeur<br /><br />**Description** : obligatoire. Valeur à convertir en URI de données.|  
|decodeBase64|Retourne une représentation de chaîne d’une chaîne en base64 d’entrée. Par exemple, l’expression suivante retourne `some string` : `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : retourne une représentation de chaîne d’une chaîne en base64 d’entrée.|  
|encodeUriComponent|Place la chaîne transmise dans un échappement d’URL. Par exemple, l’expression suivante retourne `You+Are%3ACool%2FAwesome` : `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne à partir de laquelle placer les caractères non sécurisés pour les URL dans une séquence d’échappement.|  
|decodeUriComponent|Retire la chaîne transmise d’un échappement d’URL. Par exemple, l’expression suivante retourne `You Are:Cool/Awesome` : `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. Chaîne à partir de laquelle décoder les caractères non sécurisés pour les URL.|  
|decodeDataUri|Retourne une représentation binaire d’une chaîne d’URI de données d’entrée. Par exemple, l’expression suivante retourne la représentation binaire de `some string` : `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br /> **Description** : obligatoire. DataURI à décoder en représentation binaire.|  
|uriComponent|Retourne une représentation encodée d’URI d’une valeur. Par exemple, l’expression suivante retourne `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Détails du paramètre : Nombre : 1, Nom : Chaîne, Description : Obligatoire. Chaîne à encoder en URI.|  
|uriComponentToBinary|Retourne une représentation binaire d’une chaîne encodée d’URI. Par exemple, l’expression suivante retourne une représentation binaire de `You Are:Cool/Awesome` : `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : chaîne<br /><br />**Description** : obligatoire. Chaîne encodée d’URI.|  
|uriComponentToString|Retourne une représentation de chaîne d’une chaîne encodée d’URI. Par exemple, l’expression suivante retourne `You Are:Cool/Awesome` : `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Numéro du paramètre** : 1<br /><br />**Nom** : chaîne<br /><br />**Description** : obligatoire. Chaîne encodée d’URI.|  
|xml|Retourne une représentation xml de la valeur. Par exemple, l’expression suivante retourne un contenu xml représenté par `'\<name>Alan\</name>'` : `xml('\<name>Alan\</name>')`. La fonction xml prend également en charge l’entrée d’objet JSON. Par exemple, le paramètre `{ "abc": "xyz" }` est converti en contenu xml `\<abc>xyz\</abc>`<br /><br /> **Numéro du paramètre** : 1<br /><br />**Nom** : valeur<br /><br />**Description** : obligatoire. Valeur à convertir au format XML.|  
|xpath|Retourne un tableau de nœuds xml correspondant à l’expression xpath d’une valeur que prend l’expression xpath.<br /><br />  **Exemple 1**<br /><br /> Supposons que la valeur du paramètre « p1 » soit une représentation de chaîne du fichier xml suivant :<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Ce code : `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retournerait<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> alors que<br /><br /> 2. Ce code : `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retournerait<br /><br /> `13`<br /><br /> <br /><br /> **Exemple 2**<br /><br /> Étant donné le contenu XML suivant :<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Ce code : `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> or<br /><br /> 2. Ce code : `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> retourne<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 3. Ce code : `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> retourne<br /><br /> ``bar``<br /><br /> **Numéro du paramètre** : 1<br /><br />**Nom** : Xml<br /><br />**Description** : obligatoire. Valeur XML que prend l’expression XPath.<br /><br /> **Numéro du paramètre** : 2<br /><br />**Nom** : XPath<br /><br />**Description** : obligatoire. Expression XPath à évaluer.|  
|array|Convertit le paramètre en tableau.  Par exemple, l’expression suivante retourne `["abc"]` : `array('abc')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : valeur<br /><br /> **Description** : obligatoire. Valeur qui est convertie en tableau.|
|createArray|Crée un tableau à partir des paramètres.  Par exemple, l’expression suivante retourne `["a", "c"]` : `createArray('a', 'c')`<br /><br /> **Numéro du paramètre** : 1 ... n<br /><br /> **Nom** : tout n<br /><br /> **Description** : obligatoire. Valeurs à combiner en un tableau.|

## <a name="math-functions"></a>Fonctions mathématiques  
 Ces fonctions peuvent être utilisées pour les deux types de nombre : **entiers** et **flottants**.  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|add|Retourne le résultat de l’addition des deux nombres. Par exemple, cette fonction retourne `20.333` : `add(10,10.333)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : opérande 1<br /><br /> **Description** : obligatoire. Nombre à ajouter à **Opérande 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : opérande 2<br /><br /> **Description** : obligatoire. Nombre à ajouter à **Opérande 1**.|  
|sub|Retourne le résultat de la soustraction des deux nombres. Par exemple, cette fonction retourne : `-0.333` :<br /><br /> `sub(10,10.333)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : diminuende<br /><br /> **Description** : obligatoire. Nombre duquel le **diminuteur** est soustrait.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : diminuteur<br /><br /> **Description** : obligatoire. Nombre à soustraire du **diminuende**.|  
|mul|Retourne le résultat de la multiplication de deux nombres. Par exemple, l’expression suivante retourne `103.33` :<br /><br /> `mul(10,10.333)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : multiplicande 1<br /><br /> **Description** : obligatoire. Nombre à multiplier avec le **multiplicande 2**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : multiplicande 2<br /><br /> **Description** : obligatoire. Nombre à multiplier avec le **multiplicande 1**.|  
|div|Retourne le résultat de la division des deux nombres. Par exemple, l’expression suivante retourne `1.0333` :<br /><br /> `div(10.333,10)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : dividende<br /><br /> **Description** : obligatoire. Nombre à diviser par le **diviseur**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : diviseur<br /><br /> **Description** : obligatoire. Nombre par lequel diviser le **dividende**.|  
|mod|Retourne le résultat du reste après la division des deux nombres (modulo). Par exemple, l’expression suivante retourne `2` :<br /><br /> `mod(10,4)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : dividende<br /><br /> **Description** : obligatoire. Nombre à diviser par le **diviseur**.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : diviseur<br /><br /> **Description** : obligatoire. Nombre par lequel diviser le **dividende**. Après la division, le reste est extrait.|  
|min|Il existe deux modèles différents pour appeler cette fonction : `min([0,1,2])` Ici la valeur min extrait un tableau. Cette expression retourne `0`. Cette fonction peut également extraire une liste de valeurs séparées par des virgules : `min(0,1,2)` Cette fonction retourne également 0. Remarque : toutes les valeurs doivent être des nombres. Si le paramètre est un tableau, il ne doit contenir que des nombres.<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection ou valeur<br /><br /> **Description** : obligatoire. Il peut s’agir d’un tableau de valeurs dans lequel rechercher la valeur minimale ou la première valeur d’un ensemble.<br /><br /> **Numéro du paramètre** : 2 ... *n*<br /><br /> **Nom** : valeur *n*<br /><br /> **Description** : facultatif. Si le premier paramètre est une valeur, vous pouvez transmettre des valeurs supplémentaires, et la valeur minimale de toutes les valeurs transmises est retournée.|  
|max|Il existe deux modèles différents pour appeler cette fonction : `max([0,1,2])`<br /><br /> Ici, la valeur max extrait un tableau. Cette expression retourne `2`. Cette fonction peut également extraire une liste de valeurs séparées par des virgules : `max(0,1,2)` Cette fonction retourne 2. Remarque : toutes les valeurs doivent être des nombres. Si le paramètre est un tableau, il ne doit contenir que des nombres.<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : collection ou valeur<br /><br /> **Description** : obligatoire. Il peut s’agir d’un tableau de valeurs dans lequel rechercher la valeur maximale ou la première valeur d’un ensemble.<br /><br /> **Numéro du paramètre** : 2 ... *n*<br /><br /> **Nom** : valeur *n*<br /><br /> **Description** : facultatif. Si le premier paramètre est une valeur, vous pouvez transmettre des valeurs supplémentaires, et la valeur maximale de toutes les valeurs transmises est retournée.|  
|range| Génère un tableau d’entiers commençant à partir d’un certain nombre, et vous définissez la longueur du tableau retourné. Par exemple, cette fonction retourne `[3,4,5,6]` :<br /><br /> `range(3,4)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : index de début<br /><br /> **Description** : obligatoire. Premier entier dans le tableau.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : nombre<br /><br /> **Description** : obligatoire. Nombre d’entiers figurant dans le tableau.|  
|rand| Génère un entier aléatoire compris dans la plage spécifiée (extrémités de la plage incluses. Par exemple, cela pourrait retourner `42` :<br /><br /> `rand(-1000,1000)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : minimum<br /><br /> **Description** : obligatoire. Entier le plus bas qui pourrait être retourné.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : maximum<br /><br /> **Description** : obligatoire. Entier le plus élevé qui pourrait être retourné.|  
  
## <a name="date-functions"></a>Fonctions de date  
  
|Nom de la fonction|Description|  
|-------------------|-----------------|  
|utcnow|Retourne l’horodateur actuel sous forme de chaîne. Exemple `2015-03-15T13:27:36Z` :<br /><br /> `utcnow()`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  
|addseconds|Ajoute un nombre entier de secondes à un horodateur de type chaîne transmis. Le nombre de secondes peut être positif ou négatif. Par défaut, le résultat est une chaîne au format ISO 8601 (« o »), sauf si un spécificateur de format est indiqué. Exemple `2015-03-15T13:27:00Z` :<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : horodateur<br /><br /> **Description** : obligatoire. Chaîne qui contient l’heure.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : secondes<br /><br /> **Description** : obligatoire. Nombre de secondes à ajouter. Peut être négatif pour soustraire des secondes.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  
|addminutes|Ajoute un nombre entier de minutes à un horodateur de type chaîne transmis. Le nombre de minutes peut être positif ou négatif. Par défaut, le résultat est une chaîne au format ISO 8601 (« o »), sauf si un spécificateur de format est indiqué. Par exemple, `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : horodateur<br /><br /> **Description** : obligatoire. Chaîne qui contient l’heure.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : minutes<br /><br /> **Description** : obligatoire. Nombre de minutes à ajouter. Peut être négatif pour soustraire des minutes.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  
|addhours|Ajoute un nombre entier d’heures à un horodateur de type chaîne transmis. Le nombre d’heures peut être positif ou négatif. Par défaut, le résultat est une chaîne au format ISO 8601 (« o »), sauf si un spécificateur de format est indiqué. Exemple `2015-03-16T01:27:36Z` :<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : horodateur<br /><br /> **Description** : obligatoire. Chaîne qui contient l’heure.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : heures<br /><br /> **Description** : obligatoire. Nombre d’heures à ajouter. Peut être négatif pour soustraire des heures.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  
|adddays|Ajoute un nombre entier de jours à un horodateur de type chaîne transmis. Le nombre de jours peut être positif ou négatif. Par défaut, le résultat est une chaîne au format ISO 8601 (« o »), sauf si un spécificateur de format est indiqué. Exemple `2015-02-23T13:27:36Z` :<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : horodateur<br /><br /> **Description** : obligatoire. Chaîne qui contient l’heure.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : jours<br /><br /> **Description** : obligatoire. Nombre de jours à ajouter. Peut être négatif pour soustraire des jours.<br /><br /> **Numéro du paramètre** : 3<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  
|formatDateTime|Retourne une chaîne au format de date. Par défaut, le résultat est une chaîne au format ISO 8601 (« o »), sauf si un spécificateur de format est indiqué. Exemple `2015-02-23T13:27:36Z` :<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Numéro du paramètre** : 1<br /><br /> **Nom** : date<br /><br /> **Description** : obligatoire. Chaîne qui contient la date.<br /><br /> **Numéro du paramètre** : 2<br /><br /> **Nom** : format<br /><br /> **Description** : facultatif. [Caractère de spécificateur de format unique](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou [modèle de format personnalisé](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) qui indique comment mettre en forme la valeur de cet horodateur. Si aucun format n’est indiqué, le format ISO 8601 (« o ») est utilisé.|  

## <a name="next-steps"></a>Étapes suivantes
Pour la liste des variables système que vous pouvez utiliser dans des expressions, voir [variables système](control-flow-system-variables.md).
