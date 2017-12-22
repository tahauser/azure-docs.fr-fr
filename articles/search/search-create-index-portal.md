---
title: "Créer un index (portail - Recherche Azure) | Microsoft Docs"
description: "Créez un index à l’aide du portail Azure."
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Créer un index Azure Search à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-an-index.md)
> * [Portail](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Utilisez le concepteur d’index intégré dans le portail Azure pour concevoir un prototype ou créer un [index de recherche](search-what-is-an-index.md) à exécuter sur votre service Recherche Azure. 

## <a name="prerequisites"></a>Composants requis

Cet article repose sur le principe que vous disposez [d’un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et du [service Recherche Azure](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>Rechercher votre service de recherche
1. Connectez-vous à la page du portail Azure et passez en revue les [services de recherche de votre abonnement](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Sélectionnez votre service Recherche Azure.

## <a name="name-the-index"></a>Nommer l’index

1. Cliquez sur le bouton **Ajouter un index** de la barre de commandes en haut de la page.
2. Nommez votre index Azure Search. 
   * Le nom doit commencer par une lettre.
   * N’utilisez que des lettres minuscules, chiffres ou tirets (« - »).
   * Le nom ne doit pas dépasser 60 caractères.

  Le nom de l’index est ensuite intégré dans l’URL de point de terminaison utilisé pour les connexions à l’index, et il est utilisé pour l’envoi de requêtes HTTP dans l’API REST Azure Search.

## <a name="define-the-fields-of-your-index"></a>Définir les champs de l’index

La composition de l’index inclut une *collection de champs* qui définit les données qu’il est possible de rechercher dans votre index. Plus spécifiquement, il spécifie la structure des documents que vous chargez séparément. La collection de champs inclut les champs obligatoires et facultatifs, nommés et typés, avec des attributs d’index déterminant la façon dont le champ peut être utilisé.

1. Dans le panneau **Ajouter un index**, cliquez sur **Champs >** pour ouvrir le panneau de définition de champ. 

2. Acceptez le champ *clé* de type Edm.String généré. Par défaut, le champ est nommé *id*, mais vous pouvez le renommer tant que la chaîne est conforme aux [règles d’affectation de noms](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Chaque index Azure Search doit obligatoirement comporter un champ clé de type chaîne.

3. Ajoutez des champs pour fournir des spécifications complètes sur les documents à charger. Si les documents comportent un *id*, un *nom d’hôtel*, une *adresse*, une *ville* et une *région*, créez un champ correspondant pour chacun de ces éléments dans l’index. Passez en revue les [conseils de conception de la section ci-dessous](#design) pour vous aider à définir les attributs.

4. Si vous le souhaitez, ajoutez tous les champs qui sont utilisés en interne dans les expressions de filtre. Les attributs des champs peuvent être définis pour les exclure des opérations de recherche.

5. Lorsque vous avez terminé, cliquez sur **OK** pour enregistrer et créer l’index.

## <a name="tips-for-adding-fields"></a>Conseils sur l’ajout de champs

La création d’un index dans le portail demande énormément de manipulations clavier. Allégez les différentes étapes en suivant ce processus :

1. Commencez par créer la liste des champs en saisissant leurs noms et en définissant les types de données associés.

2. Utilisez ensuite les cases à cocher au-dessus de chaque attribut pour activer un paramètre sur tous les champs simultanément, puis décochez les quelques cases superflues le cas échéant. Par exemple, il est généralement possible d’effectuer des recherches sur les champs de type chaîne. Par conséquent, vous pouvez cliquer sur **Récupérable**  et **Possibilité de recherche** pour permettre à la fois le renvoi des valeurs du champ dans les résultats de recherche, ainsi qu’une recherche en texte intégral sur ce champ. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Conseils de conception pour la définition des attributs

Même si vous pouvez ajouter de nouveaux champs à tout moment, les définitions de champ existantes sont verrouillées pour toute la durée de vie de l’index. C’est pourquoi les développeurs utilisent généralement le portail pour créer des index simples, tester des idées ou rechercher une définition de paramètre. Il est plus efficace d’effectuer des itérations fréquentes sur la conception d’un index si vous suivez une approche basée sur du code pour reconstruire l’index facilement.

Des analyseurs et générateurs de suggestions sont associés aux champs avant l’enregistrement de l’index. Veillez à cliquer sur chaque onglet pour ajouter des analyseurs de langage ou des générateurs de suggestions à la définition de votre index.

Les champs de type chaîne sont souvent marqués avec les attributs **Possibilité de recherche** et **Récupérable**.

Vous pouvez utiliser les champs **Triable**, **Filtrable** et **À choix multiples** pour affiner les résultats de recherche.

Les attributs d’un champ déterminent son utilisation, par exemple s’il est utilisé dans la recherche en texte intégral, la navigation par facettes, les opérations de tri et ainsi de suite. Le tableau suivant décrit chaque attribut.

|Attribut|Description|  
|---------------|-----------------|  
|**searchable**|Recherche en texte intégral, avec analyse lexicale (césure de mots) lors de l’indexation. Si vous définissez un champ avec possibilité de recherche sur une valeur comme « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Pour en savoir plus, consultez la rubrique [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md).|  
|**filterable**|Référencé dans les requêtes **$filter**. Les champs filtrables de type `Edm.String` ou `Collection(Edm.String)` ne font pas l’objet d’une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ avec la valeur « journée ensoleillée », la requête `$filter=f eq 'sunny'` ne renverra aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. |  
|**sortable**|Le système trie les résultats par score par défaut, mais vous pouvez configurer le tri en fonction des champs des documents. Les champs de type `Collection(Edm.String)` ne sont pas **triables**. |  
|**facetable**|Généralement utilisé dans une présentation des résultats de recherche qui inclut un nombre de correspondances par catégorie (par exemple, les hôtels dans une ville spécifique). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Les champs de type `Edm.String` qui sont **filtrables**, **triables** ou **à choix multiples** ne peuvent pas dépasser 32 Ko de longueur. Pour plus d’informations, consultez l’article [Créer un index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Identificateur unique des documents dans l’index. Un seul champ doit être choisi comme champ clé et il doit être de type `Edm.String`.|  
|**retrievable**|Définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (comme *profit margin*) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu’il soit visible par l’utilisateur final. Il doit être `true` for `key` .|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>Créer l’index des hôtels utilisé dans les exemples de sections d’API

La documentation de l’API Azure Search inclut des exemples de code utilisant un index *hotels* simple. Dans les captures d’écran ci-dessous, vous pouvez voir la définition de l’index, qui inclut l’analyseur de langue française spécifié lors de la définition de l’index, que vous pouvez recréer comme vous entraîner dans le portail.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un index Azure Search, vous pouvez passer à l’étape suivante : [charger des données interrogeables dans l’index](search-what-is-data-import.md).

Sinon, vous pouvez étudier les index de manière plus approfondie. Outre la collection de champs, un index spécifie également des analyseurs, des générateurs de suggestions, des profils de score et des paramètres CORS. Le portail comprend des onglets permettant de définir les éléments les plus courants : les champs, les analyseurs et les générateurs de suggestions. Pour créer ou modifier d’autres éléments, vous pouvez utiliser l’API REST ou le kit de développement logiciel (SDK) .NET.

## <a name="see-also"></a>Voir aussi

 [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md)  
 [API REST du service de recherche](https://docs.microsoft.com/rest/api/searchservice/)[SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

