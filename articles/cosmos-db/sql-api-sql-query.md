---
title: Requêtes SQL pour Azure Cosmos DB | Microsoft Docs
description: Obtenez plus d’informations sur la syntaxe SQL, les concepts de bases de données et les requêtes SQL pour Azure Cosmos DB. SQL peut être utilisé comme un langage de requête JSON dans Azure Cosmos DB.
keywords: syntaxe sql, requête sql, requêtes sql, langage de requête json, concepts de bases de données, requêtes sql et fonctions agrégées
services: cosmos-db
documentationcenter: ''
author: LalithaMV
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: laviswa
ms.openlocfilehash: a79b1a97909a38b4bfba06186db875d0c0c25f03
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="sql-queries-for-azure-cosmos-db"></a>Requêtes SQL pour Azure Cosmos DB

Microsoft Azure Cosmos DB prend en charge l’interrogation de documents à l’aide du langage SQL en tant que langage de requête JSON sur les comptes d’API SQL. Azure Cosmos DB n’utilise pas de schéma. En raison de son engagement dans le modèle de données JSON directement au sein du moteur de base de données, il fournit l'indexation automatique des documents JSON sans nécessiter un schéma explicite ou la création d'index secondaires.

Lors de la conception du langage de requête pour Cosmos DB, nous avions deux objectifs à l’esprit :

* Au lieu d’inventer un langage de requête JSON, nous voulions prendre en charge SQL. SQL est l’un des langages de requête les plus conviviaux et populaires. Le langage SQL de Cosmos DB fournit un modèle de programmation formel pour créer des requêtes élaborées sur les documents JSON.
* Comme une base de données de documents JSON peut exécuter JavaScript directement dans le moteur de base de données, nous avons voulu utiliser le modèle de programmation de JavaScript comme base pour notre langage de requête. L’API SQL est incluse dans le système de type, l’évaluation d’expression et l’appel de fonction de JavaScript. En retour, cela fournit un modèle de programmation naturel pour les projections relationnelles, la navigation hiérarchique entre les documents JSON, les jointures réflexives, les requêtes spatiales et l’appel de fonctions définies par l’utilisateur écrites entièrement en JavaScript, entre autres fonctionnalités. 

Nous pensons que ces capacités sont la clé pour réduire la friction entre l'application et la base de données et sont cruciales pour la productivité des développeurs.

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Andrew Liu, Azure Cosmos DB Program Manager, présente les fonctionnalités d’interrogation d’Azure Cosmos DB et fait la démonstration de [Query Playground](http://www.documentdb.com/sql/demo) en ligne, où vous pouvez essayer Azure Cosmos DB et exécuter des requêtes SQL sur notre jeu de données, comme indiqué dans la vidéo.

> [!VIDEO https://www.youtube.com/embed/1LqUQRpHfFI]
>
>

Cette vidéo de suivi illustre des techniques d’interrogation avancées supplémentaires :

> [!VIDEO https://www.youtube.com/embed/kASU9NOIR90]
>
>

Revenez ensuite à cet article, qui commence par un didacticiel sur les requêtes SQL destiné à vous montrer quelques documents JSON et commandes SQL simples.

## <a id="GettingStarted"></a>Prise en main des commandes du langage SQL dans Cosmos DB
Pour voir comment le langage SQL de Cosmos DB fonctionne, nous allons commencer par quelques documents JSON simples sur lesquels nous allons appliquer certaines requêtes simples. Prenez ces deux documents JSON relatifs à deux familles. Avec Cosmos DB, nous n’avons pas besoin de créer de schéma ou d’index secondaire de façon explicite. Nous devons simplement insérer les documents JSON dans une collection Cosmos DB et ensuite les interroger. Nous avons ici un document JSON simple pour la famille Andersen, les parents, les enfants (et leurs animaux), l’adresse et les informations d’inscription. Le document se compose de chaînes, de nombres, d'opérateurs booléens, de tableaux et de propriétés imbriquées. 

**Document**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Voici un second document comportant une différence subtile : `givenName` et `familyName` sont utilisés au lieu de `firstName` et `lastName`.

**Document**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

À présent, appliquons quelques requêtes sur ces données pour comprendre certains aspects clés du langage de requête SQL de Azure Cosmos DB. Par exemple, la requête suivante renvoie les documents dans lesquels le champ ID correspond à `AndersenFamily`. Comme il s’agit d’un `SELECT *`, le résultat de la requête est le document JSON complet :

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


À présent, imaginez que nous ayons besoin de remettre en forme le résultat JSON. Cette requête projette un nouvel objet JSON avec 2 champs sélectionnés, Name et City, où le nom de la ville de l'adresse est identique à celui de l'État. Dans ce cas, « NY, NY » correspond.

**Requête**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Résultats**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La requête suivante retourne tous les noms donnés des enfants de la famille dont l’ID correspond à `WakefieldFamily` classés par ville de résidence.

**Requête**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Résultats**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Nous aimerions attirer votre attention sur quelques aspects importants du langage de requête Cosmos DB à travers les exemples que nous venons de voir :  

* Comme l’API SQL fonctionne avec les valeurs JSON, il traite des entités d'arborescence au lieu des lignes et des colonnes. C’est pourquoi ce langage vous permet de faire référence aux nœuds de l’arborescence à n’importe quel niveau arbitraire, comme `Node1.Node2.Node3…..Nodem`, tout comme le SQL relationnel se rattachant à la référence en deux parties de `<table>.<column>`.   
* Le langage SQL fonctionne avec des données sans schéma. C'est pourquoi le système de type doit être lié de façon dynamique. La même expression peut engendrer différents types sur différents documents. Le résultat d'une requête est une valeur JSON valide, mais n'est pas forcément un schéma fixe.  
* Cosmos DB prend uniquement en charge les documents JSON stricts. Cela signifie que le système de type et les expressions peuvent uniquement traiter des types JSON. Reportez-vous à la [spécification JSON](http://www.json.org/) pour en savoir plus.  
* Une collection Cosmos DB est un conteneur sans schéma pour vos documents JSON. Les relations des entités de données dans et entre les documents d'une collection sont capturées de façon implicite par le contenant et non par les relations de clé primaire et de clé étrangère. Cet aspect est important dans le cadre des liaisons entre documents (ce sujet est abordé plus loin dans cet article).

## <a id="Indexing"></a> Indexation Cosmos DB
Avant d’aborder la syntaxe SQL, nous allons présenter la conception de l’indexation dans Azure Cosmos DB. 

L'objectif des index de base de données est de servir les requêtes dans leurs différents formulaires et formes tout en consommant un minimum de ressources (comme le temps processeur ou les E/S) et en fournissant un bon débit et une faible latence. Souvent, le choix des index adéquats pour l'interrogation d'une base de données requiert une planification et une expérimentation importantes. Cette approche constitue un défi pour les bases de données sans schéma, où les données ne sont pas conformes à un schéma strict et évoluent rapidement. 

C’est pourquoi, lorsque nous avons conçu le sous-système d’indexation de Cosmos DB, nous avons fixé les objectifs suivants :

* Indexer les documents sans requérir un schéma : Le sous-système d’indexation ne requiert pas d’informations de schéma ou n’établit pas d’hypothèse sur les schémas des documents. 
* Prendre en charge des requêtes hiérarchiques et relationnelles enrichies et efficaces : l’index prend en charge le langage de requête Cosmos DB de manière efficace, notamment la prise en charge des projections hiérarchiques et relationnelles.
* Prendre en charge des requêtes cohérentes en dépit de volumes soutenus d’écritures : Dans le cas des charges de travail à débits d’écriture élevés avec des requêtes cohérentes, l’index est mis à jour de manière incrémentielle, efficacement et en ligne, en dépit de volumes soutenus d’écritures. La mise à jour d'index cohérente est cruciale pour servir les requêtes en respectant le niveau de cohérence défini par l'utilisateur pour le service du document.
* Prendre en charge l’infrastructure multilocataire : Étant donné le modèle basé sur la réservation pour la gouvernance des ressources sur les locataires, les mises à jour d’index sont effectuées dans le budget des ressources système (processeur, mémoire, opérations d’E/S par seconde) allouées par réplica. 
* Efficacité du stockage : Pour des raisons économiques, la surcharge de stockage sur disque de l’index est limitée et prévisible. C’est très important, car Cosmos DB permet au développeur de trouver des compromis en fonction des coûts entre la surcharge d’index et les performances des requêtes.  

Reportez-vous aux [exemples Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) sur MSDN pour obtenir des exemples montrant comment configurer la stratégie d’indexation d’une collection. Nous allons à présent détailler davantage la syntaxe SQL d’Azure Cosmos DB.

## <a id="Basics"></a>Principes de base d’une requête SQL Azure Cosmos DB
Chaque requête se compose d'une clause SELECT et de clauses FROM et WHERE facultatives conformes aux normes ANSI-SQL. Généralement, pour chaque requête, la source de la clause FROM est énumérée. Puis le filtre de la clause WHERE est appliqué sur la source pour extraire un sous-ensemble de documents JSON. Finalement, la clause SELECT est utilisée pour projeter les valeurs JSON demandées dans la liste sélectionnée.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>Clause FROM
La clause `FROM <from_specification>` est facultative, sauf si la source est filtrée ou projetée plus loin dans la requête. L'objectif de cette clause est de spécifier la source des données à partir de laquelle la requête doit fonctionner. Généralement, l'intégralité de la collection est la source, mais parfois, il peut s'agir plutôt d'un sous-ensemble de la collection. 

Une requête telle que `SELECT * FROM Families` indique que l’intégralité de la collection Families est la source de l’énumération. Un identificateur ROOT spécial peut être utilisé pour représenter la collection au lieu d'utiliser le nom de la collection. La liste suivante contient les règles appliquées par requête :

* La collection peut être un alias, tel que `SELECT f.id FROM Families AS f` ou simplement `SELECT f.id FROM Families f`. Ici, `f` équivaut à `Families`. `AS` est un mot clé facultatif pour appliquer un alias à l’identificateur.
* Une fois l’alias appliqué, vous ne pouvez plus lier la source d’origine. Par exemple, `SELECT Families.id FROM Families f` est syntaxiquement incorrect dans la mesure où l’identificateur « Families » ne peut plus être résolu.
* Toutes les propriétés qui doivent être référencées doivent être entièrement qualifiées. Si le schéma strict n'est pas respecté, ceci est renforcé pour éviter toute liaison ambiguë. `SELECT id FROM Families f` est donc syntaxiquement incorrect, car la propriété `id` n’est pas liée.

### <a name="subdocuments"></a>Sous-documents
Vous pouvez également réduire la source à un sous-ensemble. Par exemple, en cas d’énumération de la seule sous-arborescence de chaque document, le sous-dossier racine peut alors devenir la source, comme indiqué dans l’exemple suivant :

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Bien que l’exemple ci-dessus utilise un tableau comme source, un objet peut également servir de source, comme dans l’exemple suivant : toute valeur JSON valide (non indéfinie) qui se trouve dans la source est prise en compte pour une inclusion dans le résultat de la requête. Si certaines familles n’ont pas de valeur `address.state`, elles sont exclues des résultats de la requête.

**Requête**

    SELECT * 
    FROM Families.address.state

**Résultats**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Clause WHERE
La clause WHERE (**`WHERE <filter_condition>`**) est facultative. Elle indique les conditions que doivent respecter les documents JSON fournis par la source pour être inclus dans le résultat. Chaque document JSON doit évaluer les conditions indiquées sur « true » pour être inclus dans le résultat. La clause WHERE est utilisée par la couche d'index pour déterminer le sous-ensemble le plus petit absolu de documents sources pouvant appartenir au résultat. 

La requête suivante demande des documents qui contiennent une propriété name dont la valeur est `AndersenFamily`. Tous les documents n’ayant pas la propriété name ou ceux dont la valeur de la propriété name ne correspond pas à `AndersenFamily` sont exclus. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


L'exemple précédent illustrait une simple requête d'égalité. L’API SQL prend également en charge plusieurs expressions scalaires. Les plus répandues sont les expressions binaires et unaires. Les références de propriété de l'objet JSON source sont également des expressions valides. 

Les opérateurs binaires suivants sont actuellement pris en charge et peuvent être utilisés dans les requêtes, comme indiqué dans les exemples suivants :  

<table>
<tr>
<td>Opérateurs arithmétiques</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Opérateurs au niveau du bit</td>    
<td>|, &, ^, <<, >>, >>> (décalage vers la droite avec remplissage de zéros)</td>
</tr>
<tr>
<td>Opérateurs logiques</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>Opérateurs de comparaison</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Chaîne</td>    
<td>|| (concaténer)</td>
</tr>
</table>  


Examinons certaines requêtes utilisant des opérateurs binaires.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Les opérateurs unaires +,-, ~ et NOT sont également pris en charge et peuvent être utilisés dans des requêtes comme illustré dans l'exemple suivant :

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



En plus des opérateurs binaires et unaires, les références de propriétés sont également autorisées. Par exemple, `SELECT * FROM Families f WHERE f.isRegistered` retourne le document JSON qui contient la propriété `isRegistered` dont la valeur est égale à la valeur `true` JSON. Toutes les autres valeurs (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>`, etc.) entraînent l'exclusion du document source du résultat. 

### <a name="equality-and-comparison-operators"></a>Opérateurs d'égalité et de comparaison
Le tableau suivant répertorie les résultats des comparaisons d'égalité dans l’API SQL entre deux types JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolean</strong>
         </td>
         <td valign="top">
            <strong>Number</strong>
         </td>
         <td valign="top">
            <strong>String</strong>
         </td>
         <td valign="top">
            <strong>Object</strong>
         </td>
         <td valign="top">
            <strong>Array</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolean<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Number<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>String<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Object<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Array<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Pour les autres opérateurs de comparaison tels que >, >=,! =, < et <=, les règles suivantes s'appliquent :   

* Une comparaison entre des types entraîne le résultat Undefined.
* Une comparaison entre deux objets ou deux tableaux entraîne le résultat Undefined.   

Si le résultat de l'expression scalaire dans le filtre est Undefined, le document correspondant ne doit pas être inclus dans le résultat, car Undefined n'équivaut pas logiquement à « true ».

### <a name="between-keyword"></a>Mot clé BETWEEN
Vous pouvez également utiliser le mot clé BETWEEN pour exprimer des requêtes sur des plages de valeurs, comme dans SQL ANSI. Vous pouvez utiliser BETWEEN sur des chaînes ou des nombres.

Par exemple, cette requête retourne tous les documents de la famille dans lesquels la note du premier enfant est comprise entre 1 et 5 (tous deux inclus). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Contrairement à SQL ANSI, vous pouvez également utiliser la clause BETWEEN dans la clause FROM, comme dans l'exemple suivant.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Pour accélérer le temps d'exécution de requête, pensez à créer une stratégie d'indexation qui utilise un type d'index de plage sur tous les chemins d'accès/propriétés numériques qui sont filtrés dans la clause BETWEEN. 

La principale différence entre l’utilisation de BETWEEN dans l’API SQL et ANSI SQL est que vous pouvez exprimer des requêtes de plage sur des propriétés de types mixtes. Vous pourriez par exemple faire en sorte que « grade » soit un nombre (5) dans certains documents et des chaînes dans d’autres (« grade4 »). Dans ces cas-là, comme dans JavaScript, une comparaison entre deux types différents a comme résultat « undefined » et le document est ignoré.

### <a name="logical-and-or-and-not-operators"></a>Opérateurs logiques (AND, OR et NOT)
Les opérateurs logiques interviennent sur des valeurs booléennes. Les tables de vérité logiques de ces opérateurs sont présentées dans les tableaux suivants.

| Ou | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

### <a name="in-keyword"></a>Mot clé IN
Le mot clé IN permet de vérifier si une valeur spécifiée correspond à une valeur dans une liste. Par exemple, cette requête renvoie tous les documents de famille dont l’ID est « WakefieldFamily » ou « AndersenFamily ». 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Cet exemple renvoie tous les documents dans lesquels l’état est l’une des valeurs spécifiées.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Opérateurs Ternary (?) et Coalesce (??)
Vous pouvez utiliser les opérateurs Ternary et Coalesce pour créer des expressions conditionnelles, un peu comme dans des langages de programmation courants tels que C# et JavaScript. 

L'opérateur Ternary (?) peut être très pratique lors de la construction de nouvelles propriétés JSON à la volée. Par exemple, vous pouvez maintenant écrire des requêtes pour classer les niveaux de classe dans un format lisible tel que Débutant/Intermédiaire/Avancé, comme illustré ci-dessous.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Vous pouvez également imbriquer les appels à l'opérateur, comme dans la requête ci-dessous.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Comme avec d’autres opérateurs de requête, si les propriétés référencées dans l’expression conditionnelle sont manquantes dans un document, ou si les types comparés sont différents, ces documents sont exclus dans les résultats de requête.

Vous pouvez utiliser l’opérateur Coalesce (?) pour vérifier la présence d’une propriété (c’est-à-dire vérifier si elle est définie) dans un document. Cela est utile lors de l'interrogation de données semi-structurées ou de types différents. Par exemple, cette requête retourne « lastName » s'il est présent ou « surname » dans le cas contraire.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Accesseur de propriété entre guillemets
Vous pouvez également accéder aux propriétés à l’aide de l’opérateur de propriété entre guillemets `[]`. Par exemple, `SELECT c.grade` and `SELECT c["grade"]` sont équivalentes. Cette syntaxe est utile si vous devez placer dans une séquence d’échappement une propriété qui contient des espaces, des caractères spéciaux, ou qui partage le même nom qu’un mot clé SQL ou un mot réservé.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Clause SELECT
La clause SELECT (**`SELECT <select_list>`**) est obligatoire et indique les valeurs récupérées à partir de la requête, comme dans ANSI-SQL. Le sous-ensemble filtré au début des documents source est transmis à la phase de projection, où les valeurs JSON spécifiées sont récupérées et un nouvel objet JSON est construit, pour chaque entrée qui lui est transmise. 

L'exemple ci-dessous illustre une requête SELECT classique. 

**Requête**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propriétés imbriquées
Dans l’exemple suivant, nous allons projeter les deux propriétés imbriquées `f.address.state` et `f.address.city`.

**Requête**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


La projection prend également en charge les expressions JSON, comme le montre l’exemple suivant :

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Observons le rôle de `$1` ici. La clause `SELECT` doit créer un objet JSON et, comme aucune clé n’est fournie, nous utilisons des noms de variable d’argument implicites commençant par `$1`. Par exemple, cette requête renvoie 2 variables d’argument implicites, étiquetées `$1` and `$2`.

**Requête**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Alias
À présent, nous allons développer l'exemple précédent en appliquant des alias de valeurs explicites. AS est le mot clé utilisé pour l'application d'alias. Cela est facultatif, comme indiqué lors de la projection de la seconde valeur en tant que `NameInfo`. 

Si une requête a deux propriétés portant le même nom, l'alias doit être utilisé pour renommer l'une ou l'autre des propriétés, pour éviter toute ambiguïté dans le résultat projeté.

**Requête**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expressions scalaires
Outre les références de propriété, la clause SELECT prend également en charge les expressions scalaires telles que les constantes, les expressions arithmétiques, les expressions logiques, etc. Par exemple, voici une requête « Hello World » simple.

**Requête**

    SELECT "Hello World"

**Résultats**

    [{
      "$1": "Hello World"
    }]


Voici un exemple plus complexe utilisant une expression scalaire.

**Requête**

    SELECT ((2 + 11 % 7)-2)/3    

**Résultats**

    [{
      "$1": 1.33333
    }]


Dans l'exemple suivant, le résultat de l'expression scalaire est un booléen.

**Requête**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Résultats**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Création d'objet et de tableau
Une autre fonctionnalité clé de l’API SQL est la possibilité de créer un tableau ou un objet. Dans l'exemple précédent, notez que nous avons créé un objet JSON. De même, on peut également construire des tableaux comme indiqué dans les exemples suivants :

**Requête**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Résultats**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>Mot clé VALUE
Le mot clé **VALUE** fournit une méthode pour renvoyer une valeur JSON. Par exemple, la requête indiquée ci-dessous renvoie le scalaire `"Hello World"` au lieu de `{$1: "Hello World"}`.

**Requête**

    SELECT VALUE "Hello World"

**Résultats**

    [
      "Hello World"
    ]


La requête suivante renvoie la valeur JSON sans l’étiquette `"address"` dans les résultats.

**Requête**

    SELECT VALUE f.address
    FROM Families f    

**Résultats**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

L'exemple suivant développe ceci pour expliquer comment renvoyer des valeurs JSON primitives, comme le niveau feuille de l'arborescence JSON. 

**Requête**

    SELECT VALUE f.address.state
    FROM Families f    

**Résultats**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>Opérateur *
L'opérateur spécial (*) est pris en charge pour projeter le document tel quel. Une fois utilisé, il doit être le seul champ projeté. Si une requête comme `SELECT * FROM Families f` est valide, `SELECT VALUE * FROM Families f ` et `SELECT *, f.id FROM Families f ` ne le sont pas.

**Requête**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>Opérateur TOP
Le mot clé TOP peut être utilisé pour limiter le nombre de valeurs provenant d'une requête. Lorsque TOP est utilisé conjointement avec la clause ORDER BY, le jeu de résultats est limité aux N premières valeurs ordonnées ; sinon, il retourne les N premiers résultats dans un ordre non défini. En tant que meilleure pratique, dans une instruction SELECT, utilisez toujours une clause ORDER BY avec la clause TOP. Il s'agit de la seule façon d'indiquer de manière prévisible les lignes qui sont affectées par TOP. 

**Requête**

    SELECT TOP 1 * 
    FROM Families f 

**Résultats**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

L’opérateur TOP peut être utilisé avec une valeur constante (comme indiqué ci-dessus) ou avec une valeur variable à l'aide de requêtes paramétrables. Pour plus d'informations, consultez les requêtes paramétrables ci-dessous.

### <a id="Aggregates"></a>Fonctions d’agrégation
Vous pouvez également effectuer des agrégations dans la clause `SELECT`. Les fonctions d’agrégation effectuent un calcul sur un ensemble de valeurs et renvoient une valeur unique. Par exemple, la requête suivante renvoie le nombre de documents de famille que contient la collection.

**Requête**

    SELECT COUNT(1) 
    FROM Families f 

**Résultats**

    [{
        "$1": 2
    }]

Vous pouvez également renvoyer la valeur scalaire de l’agrégation à l’aide du mot clé `VALUE`. Par exemple, la requête suivante renvoie le nombre de valeurs sous forme de nombre unique :

**Requête**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Résultats**

    [ 2 ]

Vous pouvez également effectuer des agrégations en appliquant des filtres simultanément. Par exemple, la requête suivante renvoie le nombre de documents avec une adresse dans l’état de Washington.

**Requête**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Résultats**

    [ 1 ]

Le tableau suivant présente la liste des fonctions d’agrégation prises en charge dans l’API SQL. `SUM` et `AVG` s’appliquent à des valeurs numériques, tandis que `COUNT`, `MIN`, et `MAX` peuvent être effectuées sur des nombres, des chaînes, des booléens et des valeurs Null. 

| Usage | Description |
|-------|-------------|
| COUNT | Renvoie le nombre d’éléments que contient l’expression. |
| SUM   | Renvoie la somme de toutes les valeurs de l’expression. |
| MIN   | Renvoie la valeur minimale de l’expression. |
| MAX   | Renvoie la valeur maximale de l’expression. |
| MOY   | Renvoie la moyenne des valeurs de l’expression. |

Il est également possible d’effectuer des agrégations sur les résultats d’une itération de tableau. Pour en savoir plus, consultez la section relative à [l’itération de tableaux dans les requêtes](#Iteration).

> [!NOTE]
> Lorsque vous utilisez l’Explorateur de données du portail Azure, notez que les requêtes d’agrégation peuvent renvoyer les résultats partiellement agrégés sur une page de requête. Les kits de développement logiciel (SDK) génèrent une valeur cumulée unique sur toutes les pages. 
> 
> Pour effectuer des requêtes d’agrégation à l’aide de code, vous avez besoin du SDK .NET 1.12.0, du SDK .NET Core 1.1.0 ou du SDK Java 1.9.5 ou version ultérieure.    
>

## <a id="OrderByClause"></a>Clause ORDER BY
Comme dans ANSI-SQL, vous pouvez désormais inclure une clause Order By facultative lors d’une interrogation. La clause peut inclure un argument ASC/DESC facultatif pour spécifier l'ordre dans lequel les résultats doivent être récupérés.

Par exemple, voici une requête qui récupère les familles dans l'ordre de la ville de résidence.

**Requête**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Résultats**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

Et voici une requête qui récupère les familles suivant l'ordre de la date de création, qui est stockée comme un nombre représentant l'époque d’époque, c'est-à-dire le temps écoulé depuis le 1er janvier 1970 en secondes.

**Requête**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Résultats**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Concepts avancés de base de données et requêtes SQL

### <a id="Iteration"></a>Itération
Une nouvelle construction a été ajoutée via le mot clé **IN** de l’API SQL pour prendre en charge l’itération sur les tableaux JSON. La source FROM fournit une prise en charge pour l'itération. Commençons par l'exemple suivant :

**Requête**

    SELECT * 
    FROM Families.children

**Résultats**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

À présent, intéressons-nous à une autre requête, qui effectue une itération sur les enfants de la collection. Notez la différence dans le tableau de sortie. Cet exemple fractionne `children` et regroupe les résultats en un seul tableau.  

**Requête**

    SELECT * 
    FROM c IN Families.children

**Résultats**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Cette utilisation peut être généralisée pour filtrer chaque entrée du tableau, comme dans l’exemple suivant :

**Requête**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Résultats**  

    [{
      "givenName": "Lisa"
    }]

Vous pouvez également effectuer une agrégation sur le résultat de l’itération de tableau. Par exemple, la requête suivante compte le nombre d’enfants parmi toutes les familles.

**Requête**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Résultats**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Jointures
Dans une base de données relationnelle, il est important de joindre les tables. Ceci est la conséquence logique de la conception de schémas normalisés. Au contraire, l’API SQL traite les modèles de données dénormalisés de documents sans schéma. Il s'agit de l'équivalent logique d'une « jointure réflexive ».

La syntaxe prise en charge par le langage est la suivante : <from_source1> JOIN <from_source2> JOIN… JOIN <from_sourceN>. D’une façon générale, ceci renvoie un ensemble de **N**-tuples (un tuple avec **N** valeurs). Les valeurs de chaque tuple sont produites par l'itération de tous les alias de la collection sur leurs ensembles respectifs. En d'autres termes, il s'agit d'un produit croisé complet des ensembles participants à la jointure.

Les exemples suivants illustrent le fonctionnement de la clause JOIN. Dans l'exemple suivant, le résultat est vide, car le produit croisé de chaque document de la source et d'un ensemble vide est vide.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Résultats**  

    [{
    }]


Dans l’exemple suivant, la jointure concerne la racine du document et la sous-racine `children`. Il s'agit d'un produit croisé entre deux objets JSON. Le fait que les enfants soient compris dans un tableau n'est pas valide dans le JOIN, car nous traitons une seule racine qui est le tableau des enfants. Nous n'obtenons donc que deux résultats, car le produit croisé de chaque document avec le tableau renvoie exactement un seul document.

**Requête**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


L'exemple suivant illustre une jointure plus conventionnelle :

**Requête**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Résultats**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



La première chose à noter est que l’élément `from_source` de la clause **JOIN** est un itérateur. Dans ce cas, le flux est le suivant :  

* Développez chaque élément enfant **c** dans le tableau.
* Appliquez le produit croisé de la racine du document **f** avec chaque élément enfant **c** aplati à la première étape.
* Enfin, projetez seule la propriété du nom **f** de l’objet racine. 

Le premier document (`AndersenFamily`) contient un seul élément enfant. Le jeu de résultats contient donc un seul objet correspondant à ce document. Le second document (`WakefieldFamily`) contient deux enfants. Le produit croisé produit donc un objet distinct pour chaque enfant, résultant en deux objets, un pour chaque enfant correspondant à ce document. Les champs racine de ces deux documents sont identiques, comme on peut l’attendre d’un produit croisé.

La véritable utilité de la syntaxe JOIN est de former des tuples à partir du produit croisé dans une forme qui serait autrement difficile à projeter. En outre, comme nous pouvons le voir dans l’exemple ci-dessous, vous pouvez filtrer la combinaison d’un tuple permettant à l’utilisateur de choisir une condition respectée par l’ensemble des tuples.

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Résultats**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Cet exemple est une extension naturelle du précédent, et effectue une double jointure. Le produit croisé peut donc être affiché comme le pseudo-code ci-dessous :

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` a un enfant qui a un animal. Le produit croisé renvoie une ligne (1\*1\*1) à partir de cette famille. Cependant, WakefieldFamily a deux enfants, mais seul l'un d'eux, « Jesse », a des animaux. Or, Jesse a deux animaux. Le produit croisé renvoie donc 1\*1\*2 = 2 lignes à partir de cette famille.

L’exemple suivant ajoute un filtre supplémentaire sur `pet`. Ceci exclut tous les tuples où le nom de l'animal n'est pas « Shadow ». Notez que nous pouvons développer des tuples à partir de tableaux, filtrer n'importe quel élément du tuple et projeter n'importe quelle combinaison d'éléments. 

**Requête**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Résultats**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Intégration JavaScript
Azure Cosmos DB fournit un modèle de programmation pour l’exécution de la logique d’application JavaScript directement sur les collections en termes de procédures stockées et de déclencheurs. Ceci permet pour les deux :

* La possibilité d’effectuer des opérations CRUD transactionnelles hautes performances et d’interroger les documents d’une collection grâce à l’intégration approfondie de l’exécution JavaScript directement dans le moteur de base de données. 
* Une modélisation naturelle du flux de contrôle, de l'étendue des variables, de l'attribution et de l'intégration des primitives de gestion d'exception avec des transactions de base de données. Pour plus de détails sur la prise en charge Azure Cosmos DB dans le cadre de l’intégration JavaScript, veuillez consulter la documentation sur la programmation côté serveur de JavaScript.

### <a id="UserDefinedFunctions"></a>Fonctions définies par l’utilisateur
En plus des types déjà définis dans cet article, l’API SQL prend en charge les fonctions définies par l'utilisateur. En particulier, les fonctions définies par l'utilisateur scalaires sont prises en charge pour que les développeurs puissent transmettre de nombreux arguments ou aucun, puis renvoyer un seul argument en retour. La légalité des valeurs JSON de chacun de ces arguments est vérifiée.  

La syntaxe du langage SQL est étendue pour prendre en charge la logique d’application personnalisée à l’aide de ces fonctions définies par l’utilisateur. Ces dernières peuvent être enregistrées avec l’API SQL, puis référencées dans le cadre d’une requête SQL. En fait, les fonctions définies par l'utilisateur sont conçues avec soin pour pouvoir être appelées par des requêtes. En conséquence, les fonctions définies par l'utilisateur ne peuvent pas accéder à l'objet de contexte que possèdent les autres types JavaScript (procédures stockées, déclencheurs). Comme les requêtes s'exécutent en lecture seule, elles peuvent démarrer sur des réplicas principaux ou secondaires. Par conséquent, les fonctions définies par l'utilisateur sont conçues pour être exécutées sur des réplicas secondaires, contrairement à d'autres types JavaScript.

Voici un exemple de méthode d’enregistrement de fonction définie par l’utilisateur sur la base de données Cosmos DB, plus précisément sous une collection de documents.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

L’exemple ci-dessus crée une fonction définie par l’utilisateur dont le nom est `REGEX_MATCH`. Elle accepte les deux valeurs de chaîne JSON `input` and `pattern` , et vérifie si la première correspond au modèle spécifié dans la seconde à l’aide de la fonction string.match() de JavaScript.

Nous pouvons maintenant utiliser cette fonction définie par l'utilisateur dans une requête, dans une projection. Les fonctions définies par l’utilisateur doivent être qualifiées par le préfixe respectant la casse « udf.» quand elles sont appelées à partir de requêtes. 

> [!NOTE]
> Jusqu’au 17 mars 2015, Cosmos DB prenait en charge les appels de fonctions définies par l’utilisateur sans préfixe « udf ». comme SELECT REGEX_MATCH(). Ce modèle d'appel est maintenant déconseillé.  
> 
> 

**Requête**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Résultats**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Vous pouvez également utiliser les fonctions définies par l'utilisateur (et qualifiées par le préfixe « udf. Préfixe :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Résultats**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Fondamentalement, les fonctions définies par l'utilisateur sont des expressions scalaires valides et peuvent être utilisées dans des projections et des filtres. 

Pour développer la puissance des fonctions définies par l'utilisateur, étudions un autre exemple de logique conditionnelle :

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


L'exemple ci-dessous utilise les fonctions définies par l'utilisateur.

**Requête**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Résultats**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Les exemples suivants illustrent l'aptitude des fonctions définies par l'utilisateur à intégrer la puissance du langage JavaScript dans l’API SQL afin de fournir une interface programmable enrichie pour mener à bien des procédures complexes et employer une logique conditionnelle en exploitant les capacités intégrées de l'exécution JavaScript.

L’API SQL fournit les arguments aux fonctions définies par l'utilisateur pour chaque document de la source lors de l'étape actuelle (clause WHERE ou SELECT) du traitement des fonctions définies par l'utilisateur. Le résultat est intégré au pipeline d'exécution générale en toute transparence. Si les propriétés de référence des paramètres des fonctions définies par l'utilisateur ne sont pas disponibles dans la valeur JSON, les paramètres sont considérés comme non définis et l'appel des fonctions définies par l'utilisateur est entièrement ignoré. De même, si le résultat des fonctions définies par l'utilisateur est indéfini, il n'est pas inclus dans le résultat. 

En résumé, les fonctions définies par l'utilisateur sont des outils efficaces pour mener à bien des logiques métier complexes dans le cadre d'une requête.

### <a name="operator-evaluation"></a>Évaluation d'opérateur
Cosmos DB, en sa qualité de base de données JSON, peut établir des correspondances entre les opérateurs JavaScript et sa sémantique d’évaluation. Même si Cosmos DB tente de préserver la sémantique JavaScript dans le cadre de la prise en charge JSON, l’opération d’évaluation dévie dans certains cas.

Dans l’API SQL, contrairement au langage SQL classique, les types de valeur sont souvent inconnus jusqu’à ce que les valeurs soient extraites de la base de données. Afin d'exécuter les requêtes de manière efficace, la plupart des opérateurs ont des exigences de type strictes. 

L’API SQL n'effectue pas de conversions implicites, contrairement à JavaScript. Par exemple, une requête comme `SELECT * FROM Person p WHERE p.Age = 21` correspond à des documents qui contiennent une propriété Age dont la valeur est 21. Tout autre document dont la propriété Age correspond à la chaîne « 21 » ou à l'une de ses multiples variantes telles que « 021 », « 21.0 », « 0021 », « 00021 », etc. ne sera pas mis en correspondance. Ce comportement contraste avec celui de JavaScript où les valeurs de chaîne sont implicitement converties en nombres (à partir de l’opérateur, par exemple :==). Ce choix est crucial pour une correspondance d'index efficace dans l’API SQL. 

## <a name="parameterized-sql-queries"></a>Requêtes SQL paramétrables
Cosmos DB prend en charge les requêtes avec des paramètres, exprimées avec la notation @ classique. SQL paramétré fournit une gestion et un échappement robustes de l'entrée utilisateur et empêche l'exposition accidentelle des données par l'intermédiaire de l'injection SQL. 

Par exemple, vous pouvez écrire une requête qui prend le nom et l'état de l'adresse comme paramètres, puis l'exécuter pour différentes valeurs de nom et d'état d'adresse en fonction de l'entrée utilisateur.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Cette demande peut ensuite être envoyée à Cosmos DB comme requête JSON paramétrable, tel qu’indiqué ci-dessous.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

L'argument de l’opérateur TOP peut être défini à l'aide de requêtes paramétrables, comme indiqué ci-dessous.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Les valeurs de paramètres peuvent être n'importe quel format JSON valide (chaînes, nombres, valeurs booléennes, null, même des tableaux ou des valeurs JSON imbriquées). De plus, comme Cosmos DB est sans schéma, les paramètres ne sont pas validés par rapport à un type.

## <a id="BuiltinFunctions"></a>Fonctions intégrées
Cosmos DB prend également en charge plusieurs fonctions intégrées pour des opérations courantes. Ces fonctions s’utilisent dans les requêtes comme les fonctions définies par l’utilisateur.

| Groupe de fonctions          | Opérations                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Fonctions mathématiques  | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN et TAN |
| Fonctions de vérification du type | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED et IS_PRIMITIVE                                                           |
| Fonctions de chaîne        | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING et UPPER       |
| Fonctions de tableau         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH et ARRAY_SLICE                                                                                         |
| Fonctions spatiales       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID et ST_ISVALIDDETAILED                                                                           | 

Si vous utilisez actuellement une fonction définie par l’utilisateur pour laquelle une fonction intégrée est désormais disponible, remplacez-la par la fonction intégrée correspondante, car celle-ci s’exécutera plus rapidement et sera plus performante. 

### <a name="mathematical-functions"></a>Fonctions mathématiques
Chaque fonction mathématique effectue un calcul, basé sur les valeurs d’entrée fournies comme arguments, et renvoie une valeur numérique. Ce tableau répertorie les fonctions mathématiques intégrées qui sont prises en charge.


| Usage | Description |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Retourne la valeur (positive) absolue de l'expression numérique spécifiée. |
| [CEILING (num_expr)](#bk_ceiling) | Retourne le plus petit nombre entier qui est supérieur ou égal à l'expression numérique spécifiée. |
| [FLOOR (num_expr)](#bk_floor) | Retourne le plus grand nombre entier qui est inférieur ou égal à l'expression numérique spécifiée. |
| [EXP (num_expr)](#bk_exp) | Retourne l'exposant de l'expression numérique spécifiée. |
| [LOG (num_expr [,base])](#bk_log) | Retourne le logarithme népérien de l'expression numérique spécifiée, ou le logarithme dans la base spécifiée. |
| [LOG10 (num_expr)](#bk_log10) | Retourne le logarithme en base 10 de l'expression numérique spécifiée. |
| [ROUND (num_expr)](#bk_round) | Retourne une valeur numérique, arrondie au nombre entier le plus proche. |
| [TRUNC (num_expr)](#bk_trunc) | Retourne une valeur numérique, tronquée au nombre entier le plus proche. |
| [SQRT (num_expr)](#bk_sqrt) | Retourne la racine carrée de l'expression numérique spécifiée. |
| [SQUARE (num_expr)](#bk_square) | Retourne le carré de l'expression numérique spécifiée. |
| [POWER (num_expr, num_expr)](#bk_power) | Renvoie un nombre spécifié élevé à la puissance spécifiée. |
| [SIGN (num_expr)](#bk_sign) | Retourne la valeur du signe (-1, 0, 1) de l'expression numérique spécifiée. |
| [ACOS (num_expr)](#bk_acos) | Retourne l’angle, en radians, dont le cosinus est l’expression numérique spécifiée ; également appelée arccosinus. |
| [ASIN (num_expr)](#bk_asin) | Retourne l’angle, en radians, dont le sinus est l’expression numérique spécifiée. Cette fonction est également appelée arcsinus. |
| [ATAN (num_expr)](#bk_atan) | Retourne l’angle, en radians, dont la tangente est l’expression numérique spécifiée. Cette fonction est également appelée arctangente. |
| [ATN2 (num_expr)](#bk_atn2) | Retourne l’angle, en radians, entre l’axe des abscisses positives et le rayon depuis l’origine vers le point (y, x), où x et y sont les valeurs des deux expressions flottantes spécifiées. |
| [COS (num_expr)](#bk_cos) | Retourne le cosinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée. |
| [COT (num_expr)](#bk_cot) | Retourne la cotangente trigonométrique de l’angle spécifié, en radians, dans l’expression numérique spécifiée. |
| [DEGREES (num_expr)](#bk_degrees) | Retourne l’angle correspondant en degrés d’un angle spécifié en radians. |
| [PI ()](#bk_pi) | Retourne la valeur constante de PI. |
| [RADIANS (num_expr)](#bk_radians) | Retourne des radians lorsqu’une expression numérique, en degrés, est entrée. |
| [SIN (num_expr)](#bk_sin) | Retourne le sinus trigonométrique de l’angle spécifié, en radians, dans l’expression spécifiée. |
| [TAN (num_expr)](#bk_tan) | Retourne la tangente de l’expression d’entrée, dans l’expression spécifiée. |

Par exemple, vous pouvez désormais exécuter des requêtes similaires à celle-ci :

**Requête**

    SELECT VALUE ABS(-4)

**Résultats**

    [4]

La principale différence entre les fonctions de Cosmos DB et le langage SQL ANSI est que les fonctions sont conçues pour s’exécuter avec des données sans schéma et des données avec un schéma mixte. Par exemple, si vous avez un document pour lequel la propriété Size est manquante ou a une valeur non numérique (de type « inconnu »), le document est ignoré, et aucune erreur n'est retournée.

### <a name="type-checking-functions"></a>Fonctions de vérification du type
Les fonctions de vérification du type vous permettent de vérifier le type d'une expression donnée dans les requêtes SQL. Elles peuvent être utilisées pour déterminer instantanément le type variable ou inconnu des propriétés dans les documents. Le tableau répertorie les fonctions de vérification du type intégrées qui sont prises en charge.

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type tableau.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type booléen.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type numérique.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type objet JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type chaîne.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si une valeur a été attribuée à la propriété.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Retourne une valeur booléenne indiquant si la valeur est du type chaîne, nombre, valeur booléenne ou Null.</td>
</tr>

</table>

Avec ces fonctions, vous pouvez désormais exécuter des requêtes similaires à celle-ci :

**Requête**

    SELECT VALUE IS_NUMBER(-4)

**Résultats**

    [true]

### <a name="string-functions"></a>Fonctions de chaîne
Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de chaîne et retournent une valeur de type chaîne, une valeur numérique ou une valeur booléenne. Voici un tableau des fonctions de chaîne intégrées :

| Usage | Description |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Retourne le nombre de caractères de l’expression de chaîne spécifiée. |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Retourne une chaîne qui est le résultat de la concaténation d’au moins deux valeurs de chaîne. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Retourne une partie d’une expression de chaîne. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Retourne une valeur booléenne indiquant si la première expression de chaîne commence par la seconde |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Retourne une valeur booléenne indiquant si la première expression de chaîne se termine par la seconde. |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Retourne une valeur booléenne indiquant si la première expression de chaîne contient la seconde. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Retourne la position de départ de la première occurrence de la seconde expression de chaîne dans la première expression de chaîne spécifiée, ou -1 si la chaîne est introuvable. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Retourne la partie gauche d’une chaîne avec le nombre de caractères spécifié. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Retourne la partie droite d’une chaîne avec le nombre de caractères spécifié. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Retourne une expression de chaîne après avoir supprimé les espaces de début. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Retourne une expression de chaîne après avoir tronqué tous les espaces de fin. |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Retourne une expression de chaîne après la conversion des caractères majuscules en caractères minuscules. |
| [UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Retourne une expression de chaîne après la conversion des caractères minuscules en caractères majuscules. |
| [REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Remplace toutes les occurrences d’une valeur de chaîne spécifiée par une autre valeur de chaîne. |
| [REPLICATE (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Répète une valeur de chaîne un nombre de fois spécifié. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Retourne l’ordre inverse d’une valeur de chaîne. |

Avec ces fonctions, vous pouvez désormais exécuter des requêtes similaires aux suivantes. Par exemple, vous pouvez retourner le nom de famille en majuscules comme suit :

**Requête**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Résultats**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Vous pouvez également concaténer des chaînes, comme dans cet exemple :

**Requête**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Résultats**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Les fonctions de chaîne peuvent également être utilisées dans la clause WHERE pour filtrer les résultats, comme dans l’exemple suivant :

**Requête**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Résultats**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Fonctions de tableau
Les fonctions scalaires suivantes effectuent une opération sur une valeur d’entrée de tableau et retournent une valeur numérique, une valeur booléenne ou une valeur de tableau. Voici un tableau des fonctions de tableau intégrées :

| Usage | Description |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Retourne le nombre d’éléments de l’expression de tableau spécifiée. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Retourne un tableau qui est le résultat de la concaténation d’au moins deux valeurs de tableau. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Retourne une valeur booléenne qui indique si le tableau contient la valeur spécifiée. Peut spécifier si la correspondance est totale ou partielle. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Retourne une partie d’une expression de tableau. |

Les fonctions de tableau permettent de manipuler des tableaux dans JSON. Par exemple, voici une requête qui retourne tous les documents dans lesquels l’un des parents est « Robin Wakefield ». 

**Requête**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Vous pouvez spécifier un fragment partiel pour faire correspondre des éléments dans le tableau. La requête suivante recherche tous les parents avec le `givenName` `Robin`.

**Requête**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]


Voici un autre exemple dans lequel la fonction ARRAY_LENGTH est utilisée pour obtenir le nombre d’enfants par famille.

**Requête**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Résultats**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Fonctions spatiales
Cosmos DB prend en charge les fonctions intégrées Open Geospatial Consortium (OGC) suivantes pour les requêtes géospatiales. 

<table>
<tr>
  <td><strong>Utilisation</strong></td>
  <td><strong>Description</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Renvoie la distance entre les deux expressions Point, Polygone ou LineString de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Renvoie une expression booléenne indiquant si le premier objet GeoJSON (Point, Polygone ou LineString) se trouve dans le second objet GeoJSON (Point, Polygone ou LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Retourne une expression booléenne indiquant si les deux objets GeoJSON spécifiés (Point, Polygone ou LineString) se croisent.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Renvoie une valeur booléenne indiquant si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Renvoie une valeur JSON contenant une valeur booléenne si l’expression Point, Polygone ou LineString GeoJSON spécifiée est valide et, dans le cas contraire, le motif sous forme de valeur de chaîne.</td>
</tr>
</table>

Les fonctions spatiales peuvent être utilisées pour effectuer des requêtes de proximité par rapport aux données spatiales. Par exemple, voici une requête qui retourne tous les documents de famille se trouvant dans un rayon de 30 kilomètres de l'emplacement spécifié à l'aide de la fonction intégrée ST_DISTANCE. 

**Requête**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Résultats**

    [{
      "id": "WakefieldFamily"
    }]

Pour plus d’informations sur la prise en charge géospatiale dans Cosmos DB, consultez [Working with geospatial data in Azure Cosmos DB (Utilisation de données géospatiales dans Azure Cosmos DB)](geospatial.md). Cela inclut les fonctions spatiales et la syntaxe SQL pour Cosmos DB. Maintenant, examinons le fonctionnement de l’interrogation LINQ et voyons comment elle interagit avec la syntaxe que nous avons vue jusqu’à présent.

## <a id="Linq"></a>API LINQ to SQL
LINQ est un modèle de programmation .NET qui exprime un calcul en tant que requête sur des flux d'objets. Cosmos DB fournit une bibliothèque côté client pour interagir avec LINQ en facilitant la conversion entre les objets JSON et .NET, ainsi qu’un mappage à partir d’un sous-ensemble de requêtes LINQ vers des requêtes Cosmos DB. 

L’image suivante illustre l’architecture de prise en charge des requêtes LINQ à l’aide de Cosmos DB.  En utilisant le client Cosmos DB, les développeurs peuvent créer un objet **IQueryable** dirigeant les requêtes vers le fournisseur de requête de Cosmos DB, qui traduit alors les requêtes LINQ en requêtes Cosmos DB. Ces requêtes sont ensuite transmises au serveur Cosmos DB pour récupérer un ensemble de résultats au format JSON. Les résultats renvoyés sont désérialisés en un flux d'objets .NET, côté client.

![Architecture de prise en charge des requêtes LINQ avec l’API SQL (syntaxe SQL, langage de requête JSON, concepts de bases de données et requêtes SQL)][1]

### <a name="net-and-json-mapping"></a>Mappage .NET et JSON
Le mappage entre les objets .NET et les documents JSON est naturel : chaque champ de membre de données est mappé vers un objet JSON, où le nom du champ est mappé vers la partie « clé » de l'objet tandis que la partie « valeur » est mappée de façon récursive vers la partie de valeur de l'objet. Prenons pour exemple : L’objet Family créé est mappé vers le document JSON, comme indiqué ci-dessous. À l'inverse, le document JSON est mappé vers un objet .NET.

**Classe C#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>Conversion LINQ en SQL
Le fournisseur de requêtes de Cosmos DB effectue le meilleur mappage possible entre une requête LINQ et une requête SQL Cosmos DB. Dans la description suivante, nous partons du principe que le lecteur connaît les principes de base de LINQ.

D'abord, pour le système de type, nous prenons en charge tous les types JSON primitifs : numérique, booléen, chaîne et Null. Seuls ces types JSON sont pris en charge. Les expressions scalaires suivantes sont prises en charge.

* Valeurs constantes : elles incluent des valeurs constantes des types de données primitifs au moment de l’évaluation de la requête.
* Expressions d'index de propriété/tableau : ces expressions font référence à la propriété d'un objet ou d'un élément de tableau.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n étant un entier
* Expressions arithmétiques : elles incluent les expressions arithmétiques communes sur les valeurs numériques et booléennes. Pour obtenir une liste complète, reportez-vous à la spécification SQL.
  
     2 * family.children[0].grade;    x + y;
* Expressions de comparaison de chaîne : elles incluent les comparaisons de valeurs de chaînes à certaines valeurs de chaînes constantes.  
  
     mother.familyName == "Smith";    child.givenName == s; //s étant une chaîne
* Expressions de création d'objet/tableau : ces expressions renvoient un objet de type de valeur composée ou de type anonyme ou un tableau de tels objets. Ces valeurs peuvent être imbriquées.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //type anonyme avec deux champs              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Liste des opérateurs LINQ pris en charge
Voici une liste des opérateurs LINQ pris en charge dans le fournisseur LINQ inclus avec le kit SDK .NET SQL.

* **Select**: les projections sont traduites en SQL SELECT, y compris la construction d'objets
* **Where** : les filtres sont traduits en SQL WHERE et prennent en charge la traduction entre &amp;&amp; , || et ! vers les opérateurs SQL
* **SelectMany**: autorise le déroulement de tableaux vers la clause SQL JOIN. Peut être utilisé pour associer/imbriquer des expressions afin de filtrer les éléments de tableau
* **OrderBy et OrderByDescending** : se traduit par ORDER BY dans l’ordre croissant/décroissant
* Les opérateurs **Count**, **Sum**, **Min**, **Max** et **Average** pour l’agrégation, et leurs équivalents asynchrones **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** et **AverageAsync**.
* **CompareTo**: se traduit par des comparaisons de plages. Généralement utilisés pour les chaînes car ils ne sont pas comparables dans .NET
* **Take**: se traduit par SQL TOP pour limiter les résultats provenant d'une requête
* **Math Functions**: prend en charge la traduction de .NET’s Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan et Truncate vers les fonctions SQL intégrées équivalentes.
* **String Functions**: prend en charge la traduction de .NET’s Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString et ToUpper vers les fonctions SQL intégrées équivalentes.
* **Array Functions**: prend en charge la traduction à partir de .NET’s Concat, Contains et Count pour les fonctions SQL intégrées équivalentes.
* **Geospatial Extension Functions**: prend en charge la traduction des méthodes stub Distance, Within, IsValid et IsValidDetailed vers les fonctions SQL intégrées équivalentes.
* **User Defined Function Extension Function** : prend en charge la traduction de la méthode stub UserDefinedFunctionProvider.Invoke vers la fonction définie par l’utilisateur correspondante.
* **Miscellaneous**: prend en charge la traduction des opérateurs conditionnels et coalesce. Peut traduire Contains en chaîne CONTAINS, ARRAY_CONTAINS ou SQL IN, selon le contexte.

### <a name="sql-query-operators"></a>Opérateurs de requête SQL
Voici quelques exemples illustrant comment certains opérateurs de requête LINQ standard sont traduits en requêtes Cosmos DB.

#### <a name="select-operator"></a>Opérateur Select
La syntaxe est `input.Select(x => f(x))`, où `f` est une expression scalaire.

**Expression Lambda LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expression Lambda LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expression Lambda LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Opérateur SelectMany
La syntaxe est `input.SelectMany(x => f(x))`, où `f` est une expression scalaire qui retourne un type de collection.

**Expression Lambda LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Opérateur Where
La syntaxe est `input.Where(x => f(x))`, où `f` est une expression scalaire qui retourne une valeur booléenne.

**Expression Lambda LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expression Lambda LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Requêtes SQL composites
Les opérateurs suivants peuvent être composés pour former des requêtes plus puissantes. Étant donné que Cosmos DB prend en charge les collections imbriquées, la composition peut être concaténée ou imbriquée.

#### <a name="concatenation"></a>Concaténation
La syntaxe est `input(.|.SelectMany())(.Select()|.Where())*`. Une requête concaténée peut commencer par une requête `SelectMany` facultative suivie de plusieurs opérateurs `Select` ou `Where`.

**Expression Lambda LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expression Lambda LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expression Lambda LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expression Lambda LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Imbrication
La syntaxe est `input.SelectMany(x=>x.Q())`, où Q est un opérateur `Select`, `SelectMany` ou `Where`.

Dans une requête imbriquée, la requête interne est appliquée à chaque élément de la collection externe. La requête interne peut faire référence aux champs des éléments dans la collection externe, comme des jointures réflexives : cette fonctionnalité est importante.

**Expression Lambda LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expression Lambda LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expression Lambda LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Exécution de requêtes SQL
Cosmos DB expose les ressources par le biais d’une API REST qui peut être appelée par n’importe quel langage capable de créer des requêtes HTTP/HTTPS. Par ailleurs, Cosmos DB propose des bibliothèques de programmation pour plusieurs langages populaires comme .NET, Node.js, JavaScript et Python. L'API REST et les différentes bibliothèques prennent toutes en charge l'interrogation via SQL. Le kit SDK .NET prend en charge l'interrogation LINQ en plus du SQL.

Les exemples suivants montrent comment créer une requête et la soumettre à un compte de base de données Cosmos DB.

### <a id="RestAPI"></a>API REST
Cosmos DB fournit un modèle de programmation RESTful ouvert sur HTTP. Vous pouvez approvisionner vos comptes de bases de données en utilisant un abonnement Azure. Le modèle de ressource de Cosmos DB se compose d’un ensemble de ressources sous un compte de base de données, toutes adressables par le biais d’un URI stable et logique. Dans ce document, de tels ensembles de ressources sont désignés sous le nom de « flux ». Un compte de base de données se compose d'un ensemble de bases de données. Chacune d'elles contient plusieurs collections et chaque collection contient des documents, des fonctions définies par l'utilisateur et d'autres types de ressources.

Le modèle d’interaction de base avec ces ressources consiste à utiliser des verbes HTTP, tels que GET, PUT, POST et DELETE, avec leur interprétation standard. Le verbe POST permet de créer une ressource, d’exécuter une procédure stockée ou d’émettre une requête Cosmos DB. Les requêtes sont toujours des opérations en lecture seule sans effets secondaires.

Les exemples suivants illustrent l’utilisation d’une action POST pour une requête API SQL appliquée sur une collection contenant les deux exemples de documents utilisés jusqu’à présent. La requête contient un simple filtre sur la propriété de nom JSON. Notez l’utilisation des en-têtes `x-ms-documentdb-isquery` et Content-Type: `application/query+json` pour indiquer que l’opération est une requête.

**Requête**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Le deuxième exemple illustre une requête plus complexe qui renvoie plusieurs résultats à partir de la jointure.

**Requête**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Résultats**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Si les résultats d’une requête ne tiennent pas sur une seule page, l’API REST retourne un jeton de liaison via l’en-tête de réponse `x-ms-continuation-token` . Les clients peuvent paginer les résultats en incluant l'en-tête dans les résultats suivants. Vous pouvez aussi contrôler le nombre de résultats par page via l'en-tête de nombre `x-ms-max-item-count` . Si la requête spécifiée inclut une fonction d’agrégation telle que `COUNT`, la page de requête peut renvoyer une valeur partiellement agrégée sur la page de résultats. Les clients doivent effectuer une agrégation de deuxième niveau sur ces résultats pour produire les résultats finaux, par exemple effectuer la somme des nombres renvoyés dans les pages individuelles pour renvoyer le nombre total.

Pour gérer la stratégie de cohérence des données des requêtes, utilisez l’en-tête `x-ms-consistency-level` comme pour toutes les requêtes d’API REST. Pour maintenir la cohérence par session, vous devez aussi appliquer l’écho sur le dernier en-tête de cookie `x-ms-session-token` dans la demande de requête. La stratégie d’indexation de la collection interrogée peut également influencer la cohérence des résultats de la requête. Avec les paramètres de stratégie d’indexation par défaut, les collections de l’index sont toujours actualisées par rapport aux contenus du document, et les résultats de la requête correspondent à la cohérence choisie pour les données. Si la stratégie d'indexation est passée en différé, les requêtes peuvent renvoyer des résultats obsolètes. Pour en savoir plus, voir [Niveaux de cohérence des données paramétrables dans Azure Cosmos DB][consistency-levels].

Si la stratégie d’indexation configurée pour la collection ne peut pas prendre en charge la requête spécifiée, le serveur Azure Cosmos DB renvoie le code d’état 400 « Demande incorrecte ». Ce code est renvoyé pour les requêtes de plage par rapport aux chemins d'accès configurés pour les recherches (d'égalité) de hachage et pour les chemins d'accès explicitement exclus de l'indexation. L’en-tête `x-ms-documentdb-query-enable-scan` peut être spécifié pour permettre à la requête d’effectuer une analyse quand un index n’est pas disponible.

Vous pouvez obtenir les métriques détaillées sur l’exécution des requêtes en définissant l’en-tête `x-ms-documentdb-populatequerymetrics` sur `True`. Pour en savoir plus, consultez la section relative aux [métriques de requête SQL pour Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>Kit SDK C# (.NET)
Le kit SDK .NET prend en charge l'interrogation LINQ et SQL. L'exemple suivant illustre l'exécution d'une simple requête de filtre présentée précédemment dans ce document.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Cet exemple compare l'égalité de deux propriétés dans chaque document et utilise des projections anonymes. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Le prochain exemple illustre des jointures, exprimées via l'opérateur LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



Le client .NET effectue une itération automatique à travers l'ensemble des pages des résultats de la requête dans les blocs foreach, comme indiqué précédemment. Les options de requête présentées dans la section sur l’API REST sont également disponibles dans le kit SDK .NET à l’aide des classes `FeedOptions` et `FeedResponse` dans la méthode CreateDocumentQuery. Vous pouvez aussi contrôler le nombre de pages en utilisant le paramètre `MaxItemCount` . 

Vous pouvez également contrôler explicitement la pagination en créant `IDocumentQueryable` à l’aide de l’objet `IQueryable`, puis en lisant les valeurs ` ResponseContinuationToken` et en les retransférant en tant que `RequestContinuationToken` dans `FeedOptions`. `EnableScanInQuery` peut être défini pour autoriser les analyses lorsque la stratégie d’indexation configurée ne peut pas prendre en charge la requête. Dans le cas des collections partitionnées, vous pouvez utiliser `PartitionKey` pour exécuter la requête sur une seule partition (bien que Cosmos DB puisse extraire automatiquement cet élément du texte de la requête) et `EnableCrossPartitionQuery` pour exécuter des requêtes qu’il peut être nécessaire d’exécuter sur plusieurs partitions. 

Reportez-vous aux [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) pour obtenir plus d’exemples de requête. 

### <a id="JavaScriptServerSideApi"></a>API JavaScript côté serveur
Cosmos DB fournit un modèle de programmation pour l’exécution de la logique d’application JavaScript directement sur les collections par le biais de procédures stockées et de déclencheurs. La logique JavaScript enregistrée au niveau d'une collection peut alors émettre des opérations de base de données sur les opérations des documents d'une collection donnée. Ces opérations sont encapsulées dans les transactions ACID ambiantes.

L’exemple suivant illustre l’utilisation de queryDocuments dans l’API JavaScript côté serveur pour créer des requêtes depuis l’intérieur des procédures stockées et des déclencheurs.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Références
1. [Présentation d’Azure Cosmos DB][introduction]
2. [Spécification SQL Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Exemples .NET Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net)
4. [Niveaux de cohérence de base Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Spécification Javascript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Techniques d’évaluation de requête pour les bases de données volumineuses [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins : Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
