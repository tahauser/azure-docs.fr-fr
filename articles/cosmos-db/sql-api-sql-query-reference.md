---
title: 'Azure Cosmos DB : référence sur les requêtes suivant la syntaxe SQL | Microsoft Docs'
description: Documentation de référence sur le langage de requête SQL Azure Cosmos DB.
services: cosmos-db
author: LalithaMV
manager: jhubbard
editor: mimig
documentationcenter: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: 012fa27fdebebf1c86a324c49c53d665a15a91c2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Référence sur la syntaxe SQL Azure Cosmos DB

Azure Cosmos DB prend en charge l’interrogation de documents suivant une grammaire familière de type SQL (Structured Query Language) sur des documents JSON hiérarchiques sans nécessiter de schéma explicite ou de création d’index secondaires. Cette rubrique constitue la documentation de référence sur le langage de requête SQL, qui est compatible avec les comptes d’API SQL.

Vous trouverez une présentation détaillée du langage de requête SQL sur la page [Requêtes SQL pour Azure Cosmos DB](sql-api-sql-query.md).  
  
Nous vous invitons également à visiter le [Query Playground](http://www.documentdb.com/sql/demo), où vous pouvez essayer Azure Cosmos DB et exécuter des requêtes SQL sur notre jeu de données.  
  
## <a name="select-query"></a>Requête SELECT  
Récupère les documents JSON à partir de la base de données. Prend en charge d’évaluation d’expressions, les projections, le filtrage et les jointures.  Les conventions utilisées pour décrire les instructions SELECT sont présentées sous forme de tableau dans la section Conventions de syntaxe.  
  
**Syntaxe**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Remarques**  
  
 Pour plus d’informations sur chaque clause, consultez les sections suivantes :  
  
-   [Clause SELECT](#bk_select_query)  
  
-   [Clause FROM](#bk_from_clause)  
  
-   [Clause WHERE](#bk_where_clause)  
  
-   [Clause ORDER BY](#bk_orderby_clause)  
  
Les clauses de l’instruction SELECT doivent être classées comme indiqué ci-dessus. N’importe laquelle des clauses facultatives peut être omise. Mais lorsque des clauses facultatives sont utilisées, elles doivent apparaître dans le bon ordre.  
  
**Ordre logique de traitement de l’instruction SELECT**  
  
L’ordre dans lequel les clauses sont traitées est le suivant :  

1.  [Clause FROM](#bk_from_clause)  
2.  [Clause WHERE](#bk_where_clause)  
3.  [Clause ORDER BY](#bk_orderby_clause)  
4.  [Clause SELECT](#bk_select_query)  

Notez que cela diffère de l’ordre dans lequel elles apparaissent dans la syntaxe. Le classement se fait de telle façon que tous les nouveaux symboles introduits par une clause traitée sont visibles et peuvent être utilisés dans des clauses traitées ultérieurement. Par exemple, les alias déclarés dans une clause FROM sont accessibles dans les clauses WHERE et SELECT.  

**Commentaires et espaces blancs**  

Tous les espaces blancs qui ne font pas partie d’une chaîne entre guillemets ou d’un identificateur entre guillemets ne font pas partie de la grammaire du langage et sont ignorés lors de l’analyse.  

Le langage de requête prend en charge les commentaires de style T-SQL, comme  

-   Instruction SQL `-- comment text [newline]`  

Bien que les commentaires et espaces blancs n’ont pas d’importance dans la grammaire, ils doivent être utilisés pour séparer les jetons. Par exemple : `-1e5` est un jeton à numéro unique, alors que `: – 1 e5` est un jeton moins suivi du chiffre 1 et de l’identificateur e5.  

##  <a name="bk_select_query"></a> Clause SELECT  
Les clauses de l’instruction SELECT doivent être classées comme indiqué ci-dessus. N’importe laquelle des clauses facultatives peut être omise. Mais lorsque des clauses facultatives sont utilisées, elles doivent apparaître dans le bon ordre.  

**Syntaxe**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Arguments**  
  
 `<select_specification>`  
  
 Propriétés ou valeur à sélectionner pour le jeu de résultats.  
  
 `'*'`  
  
Spécifie que la valeur doit être récupérée sans apporter de modifications. Plus spécifiquement, si la valeur traitée est un objet, toutes les propriétés sont récupérées.  
  
 `<object_property_list>`  
  
Spécifie la liste des propriétés à récupérer. Chaque valeur retournée sera un objet avec les propriétés spécifiées.  
  
`VALUE`  
  
Spécifie que la valeur JSON doit être récupérée au lieu de l’objet JSON complet. Cela, contrairement à `<property_list>`, n’encapsule pas la valeur projetée dans un objet.  
  
`<scalar_expression>`  
  
Expression représentant la valeur à calculer. Consultez la section [Expressions scalaires](#bk_scalar_expressions) pour plus d’informations.  
  
**Remarques**  
  
La syntaxe `SELECT *` est valide uniquement si la clause FROM a déclaré exactement un alias. `SELECT *` fournit une projection d’identité, ce qui peut être utile si aucune projection n’est nécessaire. SELECT * est valide uniquement si la clause FROM est spécifiée et n’a introduit qu’une seule source d’entrée.  
  
Notez que `SELECT <select_list>` et `SELECT *` sont ce qu’on appelle du « sucre syntaxique » et que vous pouvez également les exprimer à l’aide d’instructions SELECT simples, comme indiqué ci-dessous.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     équivaut à :  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     équivaut à :  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Voir aussi**  
  
[Expressions scalaires](#bk_scalar_expressions)  
[Clause SELECT](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> Clause FROM  
Spécifie la ou les sources de jointure. La clause FROM est facultative. Si elle n’est pas spécifiée, les autres clauses sont exécutées comme si la clause FROM fournissait un document unique.  
  
**Syntaxe**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Arguments**  
  
`<from_source>`  
  
Spécifie une source de données, avec ou sans alias. Si aucun alias n’est spécifié, il est déduit à partir de `<collection_expression>` à l’aide des règles suivantes :  
  
-   Si l’expression est un collection_name, collection_name sera être utilisé en tant qu’alias.  
  
-   Si l’expression est `<collection_expression>`, puis property_name, alors property_name sera être utilisé en tant qu’alias. Si l’expression est un collection_name, collection_name sera être utilisé en tant qu’alias.  
  
AS `input_alias`  
  
Spécifie que `input_alias` est un ensemble de valeurs renvoyées par l’expression de collection sous-jacente.  
 
`input_alias` IN  
  
Spécifie que `input_alias` doit représenter l’ensemble des valeurs obtenues en effectuant une itération sur tous les éléments de tableau de chaque tableau retourné par l’expression de collection sous-jacente. Toute valeur retournée par l’expression de collection sous-jacente qui n’est pas un tableau est ignorée.  
  
`<collection_expression>`  
  
Spécifie l’expression de collection à utiliser pour récupérer les documents.  
  
`ROOT`  
  
Spécifie qu’un document doit être récupéré à partir de la collection par défaut, actuellement connectée.  
  
`collection_name`  
  
Spécifie qu’un document doit être récupéré à partir de la collection fournie. Le nom de la collection doit correspondre au nom de la collection actuellement connectée.  
  
`input_alias`  
  
Spécifie que le document doit être récupéré à partir de l’autre source définie par l’alias fourni.  
  
`<collection_expression> '.' property_`  
  
Spécifie que le document doit être récupéré en accédant à la propriété `property_name` ou à l’élément de tableau array_index pour tous les documents récupérés par l’expression de collection spécifiée.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Spécifie que le document doit être récupéré en accédant à la propriété `property_name` ou à l’élément de tableau array_index pour tous les documents récupérés par l’expression de collection spécifiée.  
  
**Remarques**  
  
Tous les alias fournis ou déduits dans la ou les `<from_source>(` doivent être uniques. La syntaxe `<collection_expression>.`property_name est identique à `<collection_expression>' ['"property_name"']'`. Toutefois, cette dernière syntaxe peut être utilisée si un nom de propriété contient des caractères hors identificateur.  
  
**Gestion des propriétés manquantes, des éléments de tableau manquants et des valeurs non définies**  
  
Si une expression de collection accède à des propriétés ou éléments de tableau et que la valeur n’existe pas, cette valeur sera ignorée et n’est plus traitée.  
  
**Étendue de contexte d’expression de collection**  
  
Une expression de collection peut avoir une étendue de document ou de collection :  
  
-   Une expression est étendue à une collection si la source sous-jacente de l’expression de collection est ROOT ou `collection_name`. Une telle expression représente un ensemble de documents récupérés directement à partir de la collection, et n’est pas dépendante du traitement d’autres expressions de collection.  
  
-   Une expression est étendue à un document si la source sous-jacente de l’expression de collection est `input_alias`, introduit plus tôt dans la requête. Une telle expression représente un ensemble de documents obtenus en évaluant l’expression de collection dans l’étendue de chaque document appartenant au jeu associé à la collection avec alias.  Le jeu résultant sera une union de jeux obtenus en évaluant l’expression de collection pour chacun des documents du jeu sous-jacent.  
  
**Jointures**  
  
Dans la version actuelle, Azure Cosmos DB prend en charge les jointures internes. Des fonctionnalités de jointure supplémentaires sont à venir.

Les jointures internes aboutissent à un produit croisé complet des ensembles participants à la jointure. Le résultat d’une jointure à N voies est un jeu de tuples à N éléments, où chaque valeur dans le tuple est associée à l’alias défini participant à la jointure et est accessible en référençant cet alias dans d’autres clauses.  
  
L’évaluation de la jointure dépend de l’étendue du contexte des jeux qui participent :  
  
-  Une jointure entre un jeu de collection A et un jeu à étendue de collection B aboutit à un produit croisé de tous les éléments des jeux A et B.
  
-   Une jointure entre le jeu A et le jeu à étendue de document B aboutit à une union de tous les jeux obtenus en évaluant le jeu à étendue de document B pour chaque document du jeu A.  
  
 Dans la version actuelle, un maximum d’une expression à étendue de collection est pris en charge par le processeur de requêtes.  
  
**Exemples de jointures :**  
  
Examinons la clause FROM suivante : `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Laissez chaque source définir `input_alias1, input_alias2, …, input_aliasN`. La close FROM renvoie un ensemble de N-tuples (un tuple avec N valeurs). Les valeurs de chaque tuple sont produites par l'itération de tous les alias de la collection sur leurs ensembles respectifs.  
  
*Exemple de JOIN 1, avec 2 sources :*  
  
- Laissez `<from_source1>` être étendu à une collection et représenter le jeu {A, B, C}.  
  
- Laissez `<from_source2>` être étendu à un document référençant input_alias1 et représenter les jeux :  
  
    {1, 2} pour `input_alias1 = A,`  
  
    {3} pour `input_alias1 = B,`  
  
    {4, 5} pour `input_alias1 = C,`  
  
- La clause FROM `<from_source1> JOIN <from_source2>` engendre les tuples suivants :  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Exemple de JOIN 2, avec 3 sources :*  
  
- Laissez `<from_source1>` être étendu à une collection et représenter le jeu {A, B, C}.  
  
- Laissez `<from_source2>` être étendu à un document référençant input_`input_alias1` et représenter les jeux :  
  
    {1, 2} pour `input_alias1 = A,`  
  
    {3} pour `input_alias1 = B,`  
  
    {4, 5} pour `input_alias1 = C,`  
  
- Laissez `<from_source3>` être étendu à un document référençant input_`input_alias2` et représenter les jeux :  
  
    {100, 200} pour `input_alias2 = 1,`  
  
    {300} pour `input_alias2 = 3,`  
  
- La clause FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` engendre les tuples suivants :  
  
    (input_alias1, input_alias2, input_alias3) :  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
> [!NOTE]
> Absence de tuples pour les autres valeurs de `input_alias1`, `input_alias2`, pour lesquelles `<from_source3>` n’a retourné aucune valeur.  
  
*Exemple de JOIN 3, avec 3 sources :*  
  
- Laissez <from_source1> être étendu à une collection et représenter le jeu {A, B, C}.  
  
- Laissez `<from_source1>` être étendu à une collection et représenter le jeu {A, B, C}.  
  
- Laissez <from_source2> être étendu à un document référençant input_source1 et représenter les jeux :  
  
    {1, 2} pour `input_alias1 = A,`  
  
    {3} pour `input_alias1 = B,`  
  
    {4, 5} pour `input_alias1 = C,`  
  
- Laissez `<from_source3>` être étendu à `input_alias1` et représenter les jeux :  
  
    {100, 200} pour `input_alias2 = A,`  
  
    {300} pour `input_alias2 = C,`  
  
- La clause FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` engendre les tuples suivants :  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
> [!NOTE]
> Cela a abouti à un produit croisé entre `<from_source2>` et `<from_source3>`, car les deux sont étendus à la même `<from_source1>`.  Cela a about à 4 (2 x 2) tuples de valeur A, 0 tuple de valeur B (1 x 0) et 2 (2 x 1) tuples de valeur C.  
  
**Voir aussi**  
  
 [Clause SELECT](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> Clause WHERE  
 Spécifie la condition de recherche pour les documents renvoyés par la requête.  
  
 **Syntaxe**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Arguments**  
  
-   `<filter_condition>`  
  
     Spécifie la condition à remplir pour les documents à retourner.  
  
-   `<scalar_expression>`  
  
     Expression représentant la valeur à calculer. Consultez la section [Expressions scalaires](#bk_scalar_expressions) pour plus d’informations.  
  
 **Remarques**  
  
 Pour que le document soit retourné, une expression spécifiée en tant que filtre de condition doit correspondre à la valeur true. Seule la valeur booléenne true satisfait la condition. Les autres valeurs : undefined, null, false, nombre, tableau ou objet ne satisfont pas la condition.  
  
##  <a name="bk_orderby_clause"></a> Clause ORDER BY  
 Spécifie l’ordre de tri des résultats retournés par la requête.  
  
 **Syntaxe**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Arguments**  
  
-   `<sort_specification>`  
  
     Spécifie une propriété ou expression sur laquelle trier le jeu de résultats de requête. Une colonne de tri peut être spécifiée comme un alias de nom ou de la colonne.  
  
     Plusieurs colonnes de tri peuvent être spécifiées. Les noms de colonne doivent être uniques. La séquence des colonnes de tri dans la clause ORDER BY détermine l’organisation du jeu de résultats trié. Autrement dit, le jeu de résultats est trié par la première propriété, puis cette liste triée est triée par la deuxième propriété, et ainsi de suite.  
  
     Les noms de colonnes référencés dans la clause ORDER BY doivent correspondre à une colonne dans la liste de sélection ou à une colonne définie dans une table spécifiée dans la clause FROM sans ambiguïté.  
  
-   `<sort_expression>`  
  
     Spécifie une propriété ou expression unique sur laquelle trier le jeu de résultats de requête.  
  
-   `<scalar_expression>`  
  
     Consultez la section [Expressions scalaires](#bk_scalar_expressions) pour plus d’informations.  
  
-   `ASC | DESC`  
  
     Spécifie que les valeurs dans la colonne spécifiée doivent être triées dans l’ordre croissant ou décroissant. ASC effectue le tri de la valeur la plus basse à la valeur la plus élevée. DESC effectue le tri de la valeur la plus élevée à la valeur la plus basse. ASC est l’ordre de tri par défaut. Les valeurs NULL sont traitées comme les valeurs les plus basses possible.  
  
 **Remarques**  
  
 Même si la grammaire de la requête prend en charge plusieurs propriétés Order by, l’exécution de la requête Azure Cosmos DB prend seulement en charge le tri sur une seule propriété et uniquement sur les noms de propriété (autrement dit, pas sur les propriétés calculées). Le tri requiert également que la stratégie d’indexation comprenne un index de plage pour la propriété et le type spécifiés, avec la précision maximale. Consultez la documentation sur la stratégie d’indexation pour plus de détails.  
  
##  <a name="bk_scalar_expressions"></a> Expressions scalaires  
 Une expression scalaire est une combinaison de symboles et d’opérateurs qui peut être évaluée pour obtenir une valeur unique. Les expressions simples peuvent être des constantes, des références de propriété, des références d’élément de tableau, des références d’alias ou des appels de fonction. Les expressions simples peuvent être combinées dans des expressions complexes utilisant des opérateurs.  
  
 Pour plus d’informations sur les valeurs qu’une expression scalaire peut avoir, consultez la section [constantes](#bk_constants).  
  
 **Syntaxe**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Arguments**  
  
-   `<constant>`  
  
     Représente une valeur constante. Consultez la section [Constantes](#bk_constants) pour plus d’informations.  
  
-   `input_alias`  
  
     Représente une valeur définie par le `input_alias` introduit dans la clause `FROM`.  
    Cette valeur est assurée de ne pas être **Undefined**, car les valeurs **Undefined** dans l’entrée sont ignorées.  
  
-   `<scalar_expression>.property_name`  
  
     Représente une valeur de la propriété d’un objet. Si la propriété n’existe pas ou est référencée sur une valeur qui n’est pas un objet, alors l’expression évaluée présente la valeur **Undefined**.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Représente une valeur de la propriété portant le nom `property_name` ou l’élément de tableau avec l’index `array_index` d’un objet/tableau. Si la propriété/l’index de tableau n’existe pas ou est référencée sur une valeur qui n’est pas un objet/tableau, alors l’expression évaluée présente la valeur Undefined.  
  
-   `unary_operator <scalar_expression>`  
  
     Représente un opérateur appliqué à une valeur unique. Consultez la section [Opérateurs](#bk_operators) pour plus d’informations.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Représente un opérateur appliqué à deux valeurs. Consultez la section [Opérateurs](#bk_operators) pour plus d’informations.  
  
-   `<scalar_function_expression>`  
  
     Représente une valeur définie par le résultat d’un appel de fonction.  
  
-   `udf_scalar_function`  
  
     Nom de la fonction scalaire définie par l’utilisateur.  
  
-   `builtin_scalar_function`  
  
     Nom de la fonction scalaire intégrée.  
  
-   `<create_object_expression>`  
  
     Représente une valeur obtenue en créant un nouvel objet avec les propriétés spécifiées et leurs valeurs.  
  
-   `<create_array_expression>`  
  
     Représente une valeur obtenue en créant un nouveau tableau avec les valeurs spécifiées en tant qu’éléments  
  
-   `parameter_name`  
  
     Représente une valeur du nom de paramètre spécifié. Les noms de paramètre doivent avoir un @ unique comme premier caractère.  
  
 **Remarques**  
  
 Lors de l’appel à une fonction scalaire intégrée ou définie par l’utilisateur, tous les arguments doivent être définis. Si un des arguments n’est pas défini, la fonction n’est pas appelée et le résultat est Undefined.  
  
 Lorsque vous créez un objet, toute propriété à laquelle est affectée une valeur Undefined est ignorée et n’est pas incluse dans l’objet créé.  
  
 Lorsque vous créez un tableau, toute valeur d’élément à laquelle est affectée une valeur **Undefined** est ignorée et n’est pas incluse dans l’objet créé. Dans ce cas, l’élément défini suivant prend sa place de telle sorte que le tableau créé n’aura pas d’index ignorés.  
  
##  <a name="bk_operators"></a> Opérateurs  
 Cette section décrit les opérateurs pris en charge. Chaque opérateur peut être affecté à exactement une catégorie.  
  
 Consultez le tableau **Catégories d’opérateurs** ci-dessous pour plus d’informations sur la gestion des valeurs **Undefined**, des exigences de type pour les valeurs d’entrée et la gestion des valeurs sans types correspondants.  
  
 **Catégories d’opérateurs :**  
  
|**Catégorie**|**Détails**|  
|-|-|  
|**Opérateurs arithmétiques**|L’opérateur attend des entrées de type nombre. La sortie est également un nombre. Si une des entrées est **Undefined** ou d’un type autre qu’un nombre, le résultat est **Undefined**.|  
|**Opérateurs au niveau du bit**|L’opérateur attend des entrées de type nombre entier 32 bits signé. La sortie est également un nombre entier signé 32 bits.<br /><br /> Toute valeur non entière est arrondie. Les valeurs positives sont arrondies vers le bas, et les valeurs négatives vers le haut.<br /><br /> Toute valeur qui se trouve en dehors de la plage d’entiers 32 bits sera convertie, en prenant les derniers 32 bits de ses deux notations de complément.<br /><br /> Si une des entrées est **Undefined** ou d’un type autre qu’un nombre, le résultat est **Undefined**.<br /><br /> **Remarque :** le comportement ci-dessus est compatible avec le comportement de l’opérateur de niveau de bit de JavaScript.|  
|**Opérateurs logiques**|L’opérateur attend des entrées de type booléen. La sortie est également une valeur booléenne.<br />Si une des entrées est **Undefined** ou d’un type autre qu’un booléen, le résultat est **Undefined**.|  
|**Opérateurs de comparaison**|L’opérateur attend que des entrées du même type et n’étant pas Undefined. La sortie est une valeur booléenne.<br /><br /> Si une des entrées est **Undefined** ou que les entrées ont des types différents, le résultat est **Undefined**.<br /><br /> Consultez le tableau **Classement des valeurs pour comparaison** pour plus de détails sur le classement des valeurs.|  
|**string**|L’opérateur attend des entrées de type chaîne. La sortie est également une chaîne.<br />Si une des entrées est **Undefined** ou d’un type autre qu’une chaîne, le résultat est **Undefined**.|  
  
 **Opérateurs unaires :**  
  
|**Name**|**Opérateur**|**Détails**|  
|-|-|-|  
|**Opérateurs arithmétiques**|+<br /><br /> -|Retourne la valeur de nombre.<br /><br /> Négation au niveau du bit. Retourne une valeur numérique avec négation.|  
|**Opérateurs au niveau du bit**|~|Complément d’une valeur. Retourne un complément d’une valeur numérique.|  
|**Opérateurs logiques**|**NOT**|Négation. Retourne une valeur booléenne avec négation.|  
  
 **Opérateurs binaires :**  
  
|**Name**|**Opérateur**|**Détails**|  
|-|-|-|  
|**Opérateurs arithmétiques**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Addition.<br /><br /> Soustraction.<br /><br /> Multiplication.<br /><br /> Division.<br /><br /> Modulation.|  
|**Opérateurs au niveau du bit**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Opérateur OR au niveau du bit.<br /><br /> Opérateur AND au niveau du bit.<br /><br /> XOR au niveau du bit.<br /><br /> Décalage vers la gauche.<br /><br /> Décalage vers la droite.<br /><br /> Décalage vers la droite avec remplissage de zéros.|  
|**Opérateurs logiques**|**AND**<br /><br /> **OU**|Conjonction logique. Retourne **true** si les deux arguments sont **true**, retourne **false** dans le cas contraire.<br /><br /> Conjonction logique. Retourne **true** si les deux arguments sont **true**, retourne **false** dans le cas contraire.|  
|**Opérateurs de comparaison**|**=**<br /><br /> **!=, &lt;&gt;**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Égal à. Retourne **true** si les arguments sont égaux, **false** dans le cas contraire.<br /><br /> Non égal à. Retourne **true** si les arguments ne sont pas égaux, **false** dans le cas contraire.<br /><br /> Supérieur à. Retourne **true** si le premier argument est supérieur au second, **false** dans le cas contraire.<br /><br /> Supérieur ou égal à. Retourne **true** si le premier argument est supérieur ou égal au second, **false** dans le cas contraire.<br /><br /> Inférieur à. Retourne **true** si le premier argument est inférieur au second, **false** dans le cas contraire.<br /><br /> Inférieur ou égal à. Retourne **true** si le premier argument est inférieur ou égal au second, **false** dans le cas contraire.<br /><br /> Coalesce. Retourne le deuxième argument si le premier argument est une valeur **Undefined**.|  
|**Chaîne**|**&amp;#124;&amp;#124;**|Concaténation. Renvoie une concaténation de deux arguments.|  
  
 **Opérateurs ternaires :**  
  
|Opérateur ternaire|?|Retourne le deuxième argument si le premier argument prend la valeur **true**, ou le troisième argument dans le cas contraire.|  
|-|-|-|  
  
 **Ordre des valeurs pour comparaison**  
  
|**Type**|**Ordre des valeurs**|  
|-|-|  
|**Undefined**|Non comparables.|  
|**Null**|Valeur unique : **null**|  
|**Nombre**|Nombre réel naturel.<br /><br /> La valeur d’infini négatif est inférieure à toute autre valeur numérique.<br /><br /> La valeur d’infini positif est supérieure à toute autre valeur numérique. La valeur **NaN** n’est pas comparable. La comparaison avec **NaN** entraîne une valeur **Undefined**.|  
|**Chaîne**|Ordre lexicographique.|  
|**Tableau**|Aucun ordre, mais équitable.|  
|**Object**|Aucun ordre, mais équitable.|  
  
 **Remarques**  
  
 Dans la base de données Azure Cosmos, les types des valeurs sont souvent inconnus jusqu'à leur récupération effective à partir de la base de données. Afin d’assurer l’exécution des requêtes de manière efficace, la plupart des opérateurs ont des exigences de type strictes. De plus, les opérateurs n’effectuent pas de conversions implicites eux-mêmes.  
  
 Cela signifie qu’une requête, comme : SELECT * FROM ROOT r WHERE r.Age = 21 retourne uniquement les documents dont la propriété Age est égale au nombre 21. Les documents dont la propriété Age est égale à la chaîne "21" ou à la chaîne "0021" ne correspondent pas, car l’expression "21" = 21 produit le résultat Undefined. Cela permet une meilleure utilisation des index, car la recherche d’une valeur spécifique (c'est-à-dire le numéro 21) est plus rapide que la recherche d’un nombre indéfini de correspondances potentielles (à savoir le nombre 21 ou des chaînes de type "21", "021", "21.0"...). Cela est différent de la manière dont JavaScript évalue les opérateurs sur les valeurs de types différents.  
  
 **Comparaison et égalité pour les objets et tableaux**  
  
 La comparaison des valeurs de tableau ou d’objet à l’aide des opérateurs de plage (>, > =, <, < =) entraînera un résultat Undefined, car il n’existe pas d’ordre défini sur les valeurs d’objet ou de tableau. Toutefois, l’utilisation d’opérateurs d’égalité/inégalité (=, ! =, <>) est prise en charge et les valeurs seront comparées structurellement.  
  
 Les tableaux sont égaux si les deux tableaux ont le même nombre d’éléments et que les éléments aux positions correspondantes sont également égaux. Si la comparaison d’une paire d’éléments produit un résultat Undefined, le résultat de la comparaison de tableaux est Undefined.  
  
 Les objets sont égaux si les deux objets ont les mêmes propriétés définies, et si les valeurs des propriétés correspondantes sont également égales. Si la comparaison d’une paire de valeurs de propriété produit un résultat Undefined, le résultat de la comparaison d’objets est Undefined.  
  
##  <a name="bk_constants"></a> Constantes  
 Une constante, également appelée un littéral ou une valeur scalaire, est un symbole représentant une valeur de données spécifique. Le format d’une constante dépend du type de données de la valeur qu’elle représente.  
  
 **Prise en charge des types de données scalaires :**  
  
|**Type**|**Ordre des valeurs**|  
|-|-|  
|**Undefined**|Valeur unique : **Undefined**|  
|**Null**|Valeur unique : **null**|  
|**Booléen**|Valeurs : **false**, **true**.|  
|**Nombre**|Un nombre à virgule flottante double précision répondant à la norme IEEE 754.|  
|**Chaîne**|Une séquence de zéro ou plusieurs caractères Unicode. Les chaînes doivent figurer entre guillemets simples ou doubles.|  
|**Tableau**|Une séquence de zéro ou plusieurs éléments. Chaque élément peut être une valeur de tout type de données scalaires, à l’exception de Undefined.|  
|**Object**|Un jeu non ordonné de zéro ou plusieurs paires nom/valeur. Le nom est une chaîne Unicode, la valeur peut être de n’importe quel type de données scalaire, sauf **Undefined**.|  
  
 **Syntaxe**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Arguments**  
  
1.  `<undefined_constant>; undefined`  
  
     Représente la valeur undefined du type Undefined.  
  
2.  `<null_constant>; null`  
  
     Représente la valeur **null** du type **Null**.  
  
3.  `<boolean_constant>`  
  
     Représente une constante de type booléen.  
  
4.  `false`  
  
     Représente une valeur **false** de type booléen.  
  
5.  `true`  
  
     Représente une valeur **true** de type booléen.  
  
6.  `<number_constant>`  
  
     Représente une constante.  
  
7.  `decimal_literal`  
  
     Les littéraux décimaux sont représentés à l’aide de la notation décimale, ou de la notation scientifique.  
  
8.  `hexadecimal_literal`  
  
     Les littéraux hexadécimaux sont représentés à l’aide du préfixe « 0 x », suivi d’un ou plusieurs chiffres hexadécimaux.  
  
9. `<string_constant>`  
  
     Représente une constante de type chaîne.  
  
10. `string _literal`  
  
     Les littéraux de chaîne sont des chaînes Unicode représentées par une séquence de zéro ou plusieurs caractères Unicode ou séquences d’échappement. Les littéraux de chaîne sont placés entre guillemets simples (apostrophes, ’) ou guillemets doubles (").  
  
 Les séquences d’échappement suivantes sont autorisées :  
  
|**Séquence d’échappement**|**Description**|**Caractères Unicode**|  
|-|-|-|  
|\\'|apostrophe (')|U+0027|  
|\\"|guillemet (")|U+0022|  
|\\\|barre oblique inverse (\\)|U+005C|  
|\\/|barre oblique (/)|U+002F|  
|\b|retour arrière|U+0008|  
|\f|saut de page|U+000C|  
|\n|saut de ligne|U+000A|  
|\r|retour chariot|U+000D|  
|\t|tab|U+0009|  
|\uXXXX|Un caractère Unicode défini par 4 chiffres hexadécimaux.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Instructions relatives aux performances de requête  
 Pour qu’une requête puisse s’exécuter efficacement sur une grande collection, elle doit utiliser des filtres qui peuvent être appliqués via un ou plusieurs index.  
  
 Les filtres suivants sont considérés pour la recherche d’index :  
  
-   Utilisez l’opérateur d’égalité (=) avec une expression de chemin d’accès de document et une constante.  
  
-   Utilisez les opérateurs de plage (<, \<=, >, > =) avec une expression de chemin d’accès de document et des constantes numériques.  
  
-   L’expression de chemin d’accès de document représente toute expression qui identifie un chemin d’accès constant dans les documents de la collecte de base de données référencée.  
  
 **Expression de chemin d’accès à un document**  
  
 Les expressions de chemin d’accès à un document sont des expressions qu’un chemin d’accès de propriété ou indexeur de tableau évalue sur un document provenant des documents de la collecte de base de données. Ce chemin d’accès peut être utilisé pour identifier l’emplacement des valeurs référencées dans un filtre directement dans les documents de la collection de la base de données.  
  
 Pour qu’une expression soit considérée comme une expression de chemin d’accès de document, elle doit :  
  
1.  Faire référence directement à la racine de la collection.  
  
2.  Référencer la propriété ou l’indexeur de tableau de constantes d’une expression de chemin d’accès de document  
  
3.  Faire référence à un alias, qui représente une expression de chemin d’accès de document.  
  
     **Conventions de syntaxe**  
  
     Le tableau suivant décrit les conventions utilisées pour décrire la syntaxe dans la référence SQL suivante.  
  
    |**Convention**|**Utilisé pour**|  
    |-|-|    
    |MAJUSCULES|Mots-clés non sensibles à la casse.|  
    |minuscules|Mots-clés sensibles à la casse.|  
    |\<nonterminal&gt;|Non terminal, défini séparément.|  
    |\<nonterminal&gt; ::=|Définition de la syntaxe de l’élément nonterminal.|  
    |other_terminal|Terminal (jeton), décrit en détail par des mots.|  
    |identificateur|Identificateur. Autorise uniquement les caractères suivants : a-z A-Z 0-9 _ Le premier caractère ne peut pas être un chiffre.|  
    |"chaîne"|Chaîne entre guillemets. Autorise n’importe quelle chaîne valide. Consultez la description de string_literal.|  
    |'symbole'|Symbole littéral qui fait partie de la syntaxe.|  
    |&#124; (barre verticale)|Alternatives aux éléments de syntaxe. Vous pouvez utiliser seulement un des éléments spécifiés.|  
    |[ ] /(crochets)|Les crochets entourent un ou plusieurs éléments facultatifs.|  
    |[ ,...n ]|Indique que l’élément précédent peut être répété n fois. Les occurrences sont séparées par des virgules.|  
    |[ ...n ]|Indique que l’élément précédent peut être répété n fois. Les occurrences sont séparées par des espaces.|  
  
##  <a name="bk_built_in_functions"></a> Fonctions intégrées  
 Azure Cosmos DB fournit de nombreuses fonctions SQL intégrées. Les catégories de fonctions intégrées sont répertoriées ci-dessous.  
  
|Fonction|Description|  
|--------------|-----------------|  
|[Fonctions mathématiques](#bk_mathematical_functions)|Chaque fonction mathématique effectue un calcul, généralement basé sur les valeurs d'entrée fournies comme arguments, et retourne une valeur numérique.|  
|[Fonctions de vérification du type](#bk_type_checking_functions)|Les fonctions de vérification du type vous permettent de vérifier le type d'une expression donnée dans les requêtes SQL.|  
|[Fonctions de chaîne](#bk_string_functions)|Les fonctions de chaîne effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur de type chaîne, une valeur numérique ou une valeur booléenne.|  
|[Fonctions de tableau](#bk_array_functions)|Les fonctions de tableau effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, une valeur booléenne ou une valeur de tableau.|  
|[Fonctions spatiales](#bk_spatial_functions)|Les fonctions spatiales effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur d’entrée d’objet spatial, une valeur numérique ou une valeur booléenne.|  
  
###  <a name="bk_mathematical_functions"></a> Fonctions mathématiques  
 Les fonctions suivantes effectuent un calcul, généralement basé sur les valeurs d'entrée fournies comme arguments, et retournent une valeur numérique.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Retourne la valeur (positive) absolue de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant montre les résultats de l’utilisation de la fonction ABS sur trois nombres différents.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus.  
  
 **Syntaxe**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant retourne la valeur ACOS de -1.  
  
```  
SELECT ACOS(-1)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Cette fonction est également appelée arcsinus.  
  
 **Syntaxe**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant retourne la valeur ASIN de -1.  
  
```  
SELECT ASIN(-1)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette fonction est également appelée arctangente.  
  
 **Syntaxe**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant renvoie l’ATAN de la valeur spécifiée.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Retourne la valeur principale de l’arc tangente de y/x, exprimée en radians.  
  
 **Syntaxe**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant calcule l’ATN2 pour les composants x et y spécifiés.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant montre des valeurs numériques positives, négatives et nulles avec la fonction CEILING.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant calcule le COS de l’angle spécifié.  
  
```  
SELECT COS(14.78)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant calcule la COT (cotangente) de l’angle spécifié.  
  
```  
SELECT COT(124.1332)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 Retourne l’angle correspondant en degrés d’un angle spécifié en radians.  
  
 **Syntaxe**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant retourne le nombre de degrés d’un angle de PI/2 radians.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant montre des valeurs numériques positives, négatives et nulles avec la fonction FLOOR.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Retourne la valeur exponentielle de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Remarques**  
  
 La constante **e** (2,718281...), est la base des logarithmes naturels.  
  
 L’exposant d’un nombre correspond à la constante **e** élevée à la puissance du nombre. Par exemple EXP(1.0) = e ^ 1,0 = 2,71828182845905 et EXP(10) = e ^ 10 = 22026,4657948067.  
  
 La valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP (LOG (n)) = n. Et le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG (EXP (n)) = n.  
  
 **Exemples**  
  
 L’exemple suivant déclare une variable et renvoie la valeur exponentielle de la variable spécifiée (10).  
  
```  
SELECT EXP(10)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 L’exemple suivant retourne la valeur exponentielle du logarithme naturel de 20 et le logarithme naturel de la valeur exponentielle de 20. Étant donné que ces fonctions sont l’inverse l’une de l’autre, la valeur renvoyée avec arrondi pour les opérations mathématiques à virgule flottante dans les deux cas est 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 Retourne le logarithme naturel de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
-   `base`  
  
     L’argument numérique facultatif qui définit la base du logarithme.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Notes**  
  
 Par défaut, LOG() renvoie le logarithme naturel. Vous pouvez modifier la base du logarithme avec une autre valeur en utilisant le paramètre de base facultatif.  
  
 Le logarithme naturel est le logarithme de base **e**, où **e** est une constante irrationnelle approximativement égale à 2,718281828.  
  
 Le logarithme naturel de la valeur exponentielle d’un nombre est le nombre lui-même : LOG (EXP (n)) = n. Et la valeur exponentielle du logarithme naturel d’un nombre est le nombre lui-même : EXP (LOG (n)) = n.  
  
 **Exemples**  
  
 L’exemple suivant déclare une variable et renvoie la valeur du logarithme de la variable spécifiée (10).  
  
```  
SELECT LOG(10)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 L’exemple suivant calcule le LOG pour l’exposant d’un nombre.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Retourne le logarithme en base 10 de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Remarques**  
  
 Les fonctions LOG10 et POWER sont inversement liées entre elles. Par exemple, 10 ^ LOG10 (n) = n.  
  
 **Exemples**  
  
 L’exemple suivant déclare une variable et renvoie la valeur de LOG10 de la variable spécifiée (100).  
  
```  
SELECT LOG10(100)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Retourne la valeur constante de PI.  
  
 **Syntaxe**  
  
```  
PI ()  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant retourne la valeur de PI.  
  
```  
SELECT PI()  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 Retourne la valeur de l’expression spécifiée élevée à la puissance spécifiée.  
  
 **Syntaxe**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
-   `y`  
  
     La puissance à laquelle élever `numeric_expression`.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant montre l’élévation d’un nombre à la puissance 3 (le cube du nombre).  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Retourne des radians lorsqu’une expression numérique, en degrés, est entrée.  
  
 **Syntaxe**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant prend plusieurs angles comme entrée et retourne les valeurs correspondantes en radians.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Retourne une valeur numérique, arrondie au nombre entier le plus proche.  
  
 **Syntaxe**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant arrondit les nombres positifs et négatifs suivants à l’entier le plus proche.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 Retourne le signe positif (+1), nul (0) ou négatif (-1) de l'expression numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant renvoie les valeurs SIGN des nombres de -2 à 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant calcule le SIN de l’angle spécifié.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Retourne la racine carrée de la valeur numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant renvoie les racines carrées des nombres de 1 à 3.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 Retourne le carré de la valeur numérique spécifiée.  
  
 **Syntaxe**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant renvoie les carrés des nombres de 1 à 3.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Retourne la tangente de l’angle spécifié, en radians, dans l’expression spécifiée.  
  
 **Syntaxe**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant calcule la tangente de PI()/ 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 Retourne une valeur numérique, tronquée au nombre entier le plus proche.  
  
 **Syntaxe**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Arguments**  
  
-   `numeric_expression`  
  
     Est une expression numérique.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant tronque les nombres positifs et négatifs suivants à la valeur entière la plus proche.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Fonctions de vérification du type  
 Les fonctions suivantes prennent en charge la vérification de type par rapport aux valeurs d’entrée, et chacune renvoie une valeur booléenne.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type tableau.  
  
 **Syntaxe**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type booléen.  
  
 **Syntaxe**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété.  
  
 **Syntaxe**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie la présence d’une propriété dans le document JSON spécifié. La première fonction retourne la valeur true, car « a » est présent, mais la seconde retourne false, car « b » est absent.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est de type null.  
  
 **Syntaxe**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_NULL.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type nombre.  
  
 **Syntaxe**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type objet JSON.  
  
 **Syntaxe**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_OBJECT.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est de type primitif (chaîne, booléen, numérique ou null).  
  
 **Syntaxe**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Retourne une valeur booléenne indiquant si l’expression spécifiée est du type chaîne.  
  
 **Syntaxe**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Arguments**  
  
-   `expression`  
  
     Peut être toute expression valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie les objets des types JSON booléen, nombre, chaîne, null, objet, tableau et non défini à l’aide de la fonction IS_STRING.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Fonctions de chaîne  
 Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur de type chaîne, une valeur numérique ou une valeur booléenne.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[UPPER](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne.  
  
 **Syntaxe**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant renvoie la chaîne concaténée des valeurs spécifiées.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde.  
  
 **Syntaxe**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie si « abc » contient « ab » et « d ».  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde.  
  
 **Syntaxe**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant indique si « abc » se termine par « b » et « bc ».  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Retourne la position de départ de la première occurrence de la seconde expression de chaîne dans la première expression de chaîne spécifiée, ou -1 si la chaîne est introuvable.  
  
 **Syntaxe**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant retourne l’index de diverses sous-chaînes à l’intérieur de « abc ».  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Retourne la partie gauche d’une chaîne avec le nombre de caractères spécifié.  
  
 **Syntaxe**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
-   `num_expr`  
  
     Est une expression numérique valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant retourne la partie gauche de « abc » pour différentes valeurs de longueur.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 Retourne le nombre de caractères de l’expression de chaîne spécifiée.  
  
 **Syntaxe**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant retourne la longueur d’une chaîne.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules.  
  
 **Syntaxe**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant explique comment utiliser LOWER dans une requête.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Retourne une expression de chaîne après avoir supprimé les espaces de début.  
  
 **Syntaxe**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment utiliser LTRIM dans une requête.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne.  
  
 **Syntaxe**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment utiliser REPLACE dans une requête.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 Répète une valeur de chaîne un nombre de fois spécifié.  
  
 **Syntaxe**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
-   `num_expr`  
  
     Est une expression numérique valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment utiliser REPLICATE dans une requête.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Retourne l’ordre inverse d’une valeur de chaîne.  
  
 **Syntaxe**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment utiliser REVERSE dans une requête.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 Retourne la partie droite d’une chaîne avec le nombre de caractères spécifié.  
  
 **Syntaxe**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
-   `num_expr`  
  
     Est une expression numérique valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant retourne la partie droite de « abc » pour différentes valeurs de longueur.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Retourne une expression de chaîne après avoir supprimé les espaces de fin.  
  
 **Syntaxe**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment utiliser RTRIM dans une requête.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde.  
  
 **Syntaxe**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant vérifie si la chaîne « abc » commence par « b » et « a ».  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> SUBSTRING  
 Renvoie une partie d’une expression de chaîne commençant à la position de caractère spécifiée (avec base zéro) et se poursuit jusqu'à la longueur spécifiée ou à la fin de la chaîne.  
  
 **Syntaxe**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
-   `num_expr`  
  
     Est une expression numérique valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant retourne la sous-chaîne de « abc » commençant à 1 pour une longueur de 1 caractère.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a> UPPER  
 Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules.  
  
 **Syntaxe**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Arguments**  
  
-   `str_expr`  
  
     Peut être toute expression de chaîne valide.  
  
 **Types de retour**  
  
 Retourne une expression de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant explique comment utiliser UPPER dans une requête  
  
```  
SELECT UPPER("Abc")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Fonctions de tableau  
 Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, une valeur booléenne ou une valeur de tableau  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Retourne un tableau qui est le résultat de la concaténation d’au moins deux valeurs de tableau.  
  
 **Syntaxe**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Arguments**  
  
-   `arr_expr`  
  
     Peut être toute expression de tableau valide.  
  
 **Types de retour**  
  
 Retourne une expression de tableau.  
  
 **Exemples**  
  
 L’exemple suivant montre comment concaténer deux tableaux.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Retourne une valeur booléenne qui indique si le tableau contient la valeur spécifiée. Peut spécifier si la correspondance est totale ou partielle. 

 **Syntaxe**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Arguments**  
  
-   `arr_expr`  
  
     Peut être toute expression de tableau valide.  
  
-   `expr`  
  
     Peut être toute expression valide.  

-   `bool_expr`  
  
     Peut être toute expression booléenne.       
  
 **Types de retour**  
  
 Retourne une valeur booléenne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment vérifier l’appartenance à un tableau avec ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": true, "$2": false}]  
```  

 L’exemple suivant montre comment rechercher une correspondance partielle de JSON dans un tableau avec ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Retourne le nombre d’éléments de l’expression de tableau spécifiée.  
  
 **Syntaxe**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Arguments**  
  
-   `arr_expr`  
  
     Peut être toute expression de tableau valide.  
  
 **Types de retour**  
  
 Renvoie une expression numérique.  
  
 **Exemples**  
  
 L’exemple suivant comment obtenir la longueur d’un tableau avec ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Retourne une partie d’une expression de tableau.
  
 **Syntaxe**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Arguments**  
  
-   `arr_expr`  
  
     Peut être toute expression de tableau valide.  
  
-   `num_expr`  
  
     Est une expression numérique valide.  
  
 **Types de retour**  
  
 Retourne une valeur booléenne.  
  
 **Exemples**  
  
 L’exemple suivant comment obtenir une partie d’un tableau avec ARRAY_SLICE.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Fonctions spatiales  
 Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur d’entrée d’objet spatial, une valeur numérique ou une valeur booléenne.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Retourne la distance entre les deux expressions Point, Polygone ou LineString de GeoJSON.  
  
 **Syntaxe**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
-   `spatial_expr`  
  
     Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
 **Types de retour**  
  
 Retourne une expression numérique contenant la distance. Elle est exprimée en mètres pour le système de référence par défaut.  
  
 **Exemples**  
  
 L’exemple suivant vous montre comment retourner tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l'emplacement spécifié à l'aide de la fonction intégrée ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygone ou LineString) spécifié dans le premier argument se trouve dans le GeoJSON (Point, Polygone ou LineString) du deuxième argument.  
  
 **Syntaxe**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
-   `spatial_expr`  
  
     Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
 
-   `spatial_expr`  
  
     Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
 **Types de retour**  
  
 Retourne une valeur booléenne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment rechercher tous les documents de famille d’un polygone avec ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Retourne une expression booléenne indiquant si l’objet GeoJSON (Point, Polygon ou LineString) spécifié dans le premier argument croise le GeoJSON (Point, Polygon ou LineString) du deuxième argument.  
  
 **Syntaxe**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Arguments**  
  
-   `spatial_expr`  
  
     Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
 
-   `spatial_expr`  
  
     Est toute expression d’objet GeoJSON Point, Polygon ou LineString valide.  
  
 **Types de retour**  
  
 Retourne une valeur booléenne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment rechercher toutes les zones ayant une intersection avec le polygone donné.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Renvoie une valeur booléenne indiquant si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Arguments**  
  
-   `spatial_expr`  
  
     Est toute expression GeoJSON Point, Polygon ou LineString valide.  
  
 **Types de retour**  
  
 Retourne une expression booléenne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment vérifier si un point est valide avec ST_VALID.  
  
 Par exemple, ce point a une valeur de latitude qui n’est pas dans la plage valide de valeurs [-90, 90]. Par conséquent, la requête retourne false.  
  
 La spécification GeoJSON impose que, pour les polygones, la dernière paire de coordonnées fournie soit identique à la première, pour créer une forme fermée. Les points dans un polygone doivent être spécifiés dans le sens antihoraire. Un polygone spécifié dans le sens horaire représente l'inverse de la région qu'il contient.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Renvoie une valeur JSON contenant une valeur booléenne si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide et, dans le cas contraire, le motif sous forme de valeur de chaîne.  
  
 **Syntaxe**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Arguments**  
  
-   `spatial_expr`  
  
     Peut être toute expression GeoJSON Point ou Polygon valide.  
  
 **Types de retour**  
  
 Renvoie une valeur JSON contenant une valeur booléenne si l'expression de points ou de polygones GeoJSON spécifiée est valide et si elle est non valide, le motif sous forme de valeur de chaîne.  
  
 **Exemples**  
  
 L’exemple suivant montre comment vérifier la validité (avec détails) avec ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Voici le jeu de résultats obtenu.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Étapes suivantes  
 [Syntaxe SQL et requête SQL pour Azure Cosmos DB](sql-api-sql-query.md)   
 [Documentation Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
