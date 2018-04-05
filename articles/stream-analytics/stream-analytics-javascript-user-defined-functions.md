---
title: Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics | Microsoft Docs
description: Effectuer des requêtes avancées avec les fonctions JavaScript définies par l’utilisateur
keywords: JavaScript, fonctions définies par l’utilisateur
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: f2b14029ebea7f9cf1fa74a384ecbb72b08b7ad6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics
Azure Stream Analytics prend en charge les fonctions définies par l’utilisateur écrites en JavaScript. Avec le large éventail de méthodes **String**, **RegExp**, **Math**, **Array** et **Date** que fournit JavaScript, les transformations complexes de données des travaux Stream Analytics sont plus faciles à créer.

## <a name="javascript-user-defined-functions"></a>Fonctions JavaScript définies par l’utilisateur
Les fonctions JavaScript définies par l’utilisateur prennent en charge les fonctions scalaires de calcul pur sans état qui ne nécessitent pas de connectivité externe. La valeur renvoyée par une fonction ne peut être qu’une valeur (unique) scalaire. Une fois que vous avez ajouté une fonction JavaScript définie par l’utilisateur à un travail, vous pouvez utiliser la fonction n’importe où dans la requête, comme une fonction scalaire intégrée.

Voici quelques scénarios dans lesquels les fonctions JavaScript définies par l’utilisateur vous seront utiles :
* Analyse et manipulation de chaînes avec des fonctions d’expressions régulières, par exemple **Regexp_Replace()** et **Regexp_Extract()**
* Décodage de données encodées, par exemple la conversion de binaire en hexadécimal
* Calculs mathématiques avec les fonctions **Math** de JavaScript
* Opérations de tableaux comme le tri, la jointure, la recherche et le remplissage

Voici quelques actions que vous ne pouvez pas effectuer avec une fonction JavaScript définie par l’utilisateur dans Stream Analytics :
* Appel de points de terminaison REST externes, par exemple la recherche inversée d’une adresse IP ou l’extraction de données de référence à partir d’une source externe
* Sérialisation ou désérialisation à un format d’événement personnalisé sur les entrées/sorties
* Création d’agrégats personnalisés

Bien qu’elles ne soient pas bloquées dans la définition des fonctions, évitez d’utiliser des fonctions telles que **Date.GetDate()** ou **Math.random()**. Ces fonctions ne retournent **pas** le même résultat à chaque fois que vous les appelez et le service Azure Stream Analytics ne conserve pas de journal des appels de fonction et des résultats retournés. Si une fonction retourne un résultat différent sur les mêmes événements, la reproductibilité n’est pas garantie lorsqu’un travail est relancé par vos soins ou par le service Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Ajouter une fonction JavaScript définie par l’utilisateur dans le portail Azure
Pour créer une simple fonction JavaScript définie par l’utilisateur dans un travail Stream Analytics existant, suivez ces étapes :

1.  Dans le portail Azure, recherchez votre travail Stream Analytics.
2.  Sous **TOPOLOGIE DE LA TÂCHE**, sélectionnez votre fonction. Une liste vide de fonctions apparaît.
3.  Pour créer une fonction définie par l’utilisateur, sélectionnez **Ajouter**.
4.  Dans le panneau **Nouvelle fonction**, pour **Type de fonction**, sélectionnez **JavaScript**. Un modèle de fonction par défaut apparaît dans l’éditeur.
5.  Pour l’**alias de fonction définie par l’utilisateur**, entrez **hex2Int**, puis modifiez l’implémentation de la fonction de la façon suivante :

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Sélectionnez **Enregistrer**. La fonction apparaît dans la liste des fonctions.
7.  Sélectionnez la nouvelle fonction **hex2Int** pour vérifier sa définition. Toutes les fonctions ont un préfixe **UDF** ajouté à l’alias de la fonction. Vous devez *inclure le préfixe* quand vous appelez la fonction dans votre requête Stream Analytics. Dans ce cas, vous appelez **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Appeler une fonction JavaScript définie par l’utilisateur dans une requête

1. Dans l’éditeur de requêtes, sous **TOPOLOGIE DE LA TÂCHE**, sélectionnez **Requête**.
2.  Modifiez votre requête, puis appelez la fonction définie par l’utilisateur comme suit :

    ```
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Pour charger l’exemple de fichier de données, cliquez avec le bouton droit sur l’entrée du travail.
4.  Pour tester votre requête, sélectionnez **Test**.


## <a name="supported-javascript-objects"></a>Objets JavaScript pris en charge
Les fonctions JavaScript définies par l’utilisateur Azure Stream Analytics prennent en charge les objets prédéfinis standard du langage JavaScript. Pour obtenir la liste de ces objets, consultez [Objets globaux](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversion de type Stream Analytics et JavaScript

Il existe des différences entre les types pris en charge dans le langage de requête Stream Analytics et JavaScript. Le tableau suivant répertorie les mappages de conversion entre les deux :

Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript ne peut représenter les entiers que jusqu’à 2^53 précisément)
Datetime | Date (JavaScript ne prend en charge que les millisecondes)
double | Number
nvarchar(MAX) | Chaîne
Enregistrement | Object
Tableau | Tableau
NULL | Null


Voici les conversions de JavaScript à Stream Analytics :


JavaScript | Stream Analytics
--- | ---
Number | Bigint (si le nombre est rond et entre long.MinValue et long.MaxValue, sinon, double)
Date | Datetime
Chaîne | nvarchar(MAX)
Object | Enregistrement
Tableau | Tableau
Null, Undefined | NULL
Un autre type (par exemple une fonction ou une erreur) | Non pris en charge (entraîne une erreur d’exécution)

## <a name="troubleshooting"></a>Résolution de problèmes
Les erreurs d’exécution JavaScript sont considérées comme irrécupérables et remontées par le biais du journal d’activité. Pour récupérer le journal, dans le portail Azure, accédez à votre travail et sélectionnez **Journal d’activité**.


## <a name="other-javascript-user-defined-function-patterns"></a>Autres modèles de fonctions JavaScript définies par l’utilisateur

### <a name="write-nested-json-to-output"></a>Écrire du code JSON imbriqué en sortie
Si vous avez une étape de traitement de suivi qui prend la sortie du travail Stream Analytics en entrée et qu’un format JSON est requis, vous pouvez écrire une chaîne JSON comme sortie. L’exemple suivant appelle la fonction **JSON.stringify()** pour compresser toutes les paires nom/valeur de l’entrée, puis les écrire sous la forme d’une valeur de chaîne unique en sortie.

**Définition de la fonction JavaScript définie par l’utilisateur :**

```
function main(x) {
return JSON.stringify(x);
}
```

**Exemple de requête :**
```
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
