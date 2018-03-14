---
title: "Didacticiel Recherche Azure sur l’indexation, l’interrogation et le filtrage à l’aide du portail | Microsoft Docs"
description: "Dans le portail Azure, utilisez les exemples de données prédéfinis pour générer un index dans Recherche Azure. Explorez la recherche en texte intégral, les filtres, les facettes, la recherche partielle, la recherche géographique, et bien davantage."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/04/2017
ms.author: heidist
ms.openlocfilehash: 528fd6e82079f37c1e9f907e081a1e918abd241e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-query-and-filter-an-azure-search-index-in-the-portal"></a>Créer, interroger et filtrer un index Recherche Azure dans le portail

Dans le portail Azure, partez d’un exemple de jeu de données prédéfini pour générer rapidement un index Recherche Azure à l’aide de l’Assistant **Importer des données**. Explorez la recherche en texte intégral, les filtres, les facettes, la recherche partielle et la recherche géographique avec **l’Explorateur de recherche**.  

Cette présentation dénuée de code est destinée à vous familiariser avec les données prédéfinies pour vous permettre d’écrire immédiatement des requêtes intéressantes. Bien que les outils du portail ne puissent pas se substituer au code, ils se révèlent utiles pour les tâches suivantes :

+ Acquisition d’une expérience pratique en un minimum de temps
+ Création d’un prototype d’index avant l’écriture du code dans l’Assistant **Importer des données**
+ Test des requêtes et de la syntaxe de l’analyseur dans **l’Explorateur de recherche**
+ Visualisation d’un index existant publié dans votre service et examen de ses attributs

**Durée estimée :** 15 minutes environ, mais davantage de temps si une inscription à un compte ou à un service est également requise. 

Vous pouvez également commencer en vous aidant d’une [introduction à la programmation basée sur le code du service Recherche Azure dans .NET](search-howto-dotnet-sdk.md).

## <a name="prerequisites"></a>Prérequis

Ce didacticiel repose sur le principe que vous disposez [d’un abonnement Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) et du [service Recherche Azure](search-create-service-portal.md). 

Si vous ne voulez pas effectuer une mise en service immédiatement, vous pouvez visionner une démonstration de 6 minutes de la procédure décrite dans ce didacticiel. Pour cela, accédez à la [vidéo de présentation du service Recherche Azure](https://channel9.msdn.com/Events/Connect/2016/138), à partir de la 3e minute environ.

## <a name="find-your-service"></a>Recherche de votre service
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur le tableau de bord des services de votre service Azure Search. Si vous n’avez pas épinglé la vignette du service à votre tableau de bord, vous pouvez trouver votre service en procédant comme suit : 
   
   * Dans la barre de lancement, cliquez sur **Tous les services** dans le volet de navigation de gauche.
   * Dans la zone de recherche, tapez *recherche* pour obtenir la liste des services de recherche associés à votre abonnement. Votre service doit apparaître dans la liste. 

## <a name="check-for-space"></a>Vérifier l’espace disponible
De nombreux clients commencent avec le service gratuit. Cette version est limitée à trois index, trois sources de données et trois indexeurs. Avant de commencer, assurez-vous de disposer d’assez d’espace pour stocker des éléments supplémentaires. Ce didacticiel crée une occurrence de chaque objet. 

> [!TIP] 
> Les vignettes figurant sur le tableau de bord des services indiquent le nombre d’index, d’indexeurs et de sources de données dont vous disposez déjà. La vignette Indexeurs affiche des indicateurs de réussite et d’échec. Cliquez sur cette vignette pour visualiser le nombre d’indexeurs. 
>
> ![Vignettes Indexeurs et Sources de données][1]
>

## <a name="create-index"></a> Créer un index et charger des données
Les requêtes de recherche se répètent sur un *index* contenant les données de recherche, les métadonnées et les constructions utilisées pour l’optimisation de certains comportements de recherche.

Pour conserver cette tâche sur le portail, nous utilisons un exemple de jeu de données intégré qui est analysable à l’aide d’un indexeur par le biais de l’Assistant **Importer des données**. 

#### <a name="step-1-start-the-import-data-wizard"></a>Étape 1 : Démarrer l’Assistant Importer des données
1. Dans votre tableau de bord de service Azure Search, cliquez sur **Importer des données** dans la barre de commandes pour lancer un assistant qui crée et remplit un index.
   
    ![Commande Importer des données][2]

2. Dans l’Assistant, cliquez sur **Source de données** > **Exemples** > **realestate-us-sample**. Cette source de données est préconfigurée avec un nom, un type et des informations de connexion. Une fois créée, elle devient une « source de données existante » qui peut être réutilisée dans d’autres opérations d’importation.

    ![Sélection d’un exemple de jeu de données][9]

3. Cliquez sur **OK** pour utiliser cette source.

#### <a name="step-2-define-the-index"></a>Étape 2 : définir l’index
La création d’un index est généralement manuelle et basée sur du code, mais l’Assistant peut générer un index pour toutes les sources de données qu’il peut analyser. Un index requiert au minimum un nom et une collection de champs, l’un de ces champs étant désigné comme clé du document pour identifier chaque document de manière unique.

Les champs comportent des types de données et des attributs. Les cases à cocher figurant dans la partie supérieure sont des *attributs d’index* qui contrôlent le mode d’utilisation du champ. 

* **Récupérable** signifie que le champ s’affiche dans la liste des résultats de recherche. En décochant cette case, vous pouvez marquer des champs individuels comme hors limites pour les résultats de recherche, par exemple lorsqu’un champ est utilisé uniquement dans les expressions de filtre. 
* Les options **Filtrable**, **Triable** et **À choix multiples** déterminent si le champ peut être utilisé dans un filtre, un tri ou une structure de navigation à facettes. 
* **Possibilité de recherche** signifie que le champ est inclus dans la recherche en texte intégral. Les chaînes sont utilisables dans une recherche. Les champs numériques et booléens sont souvent marqués comme ne pouvant pas faire l’objet d’une recherche. 

Par défaut, l’Assistant analyse la source de données pour y rechercher des identificateurs uniques comme base pour le champ de clé. Les chaînes sont dotées d’attributs les définissant comme récupérables et utilisables dans une recherche. Les entiers présentent des attributs les définissant comme récupérables, filtrables, triables et à choix multiples.

  ![Index généré pour la source realestate][3]

Cliquez sur **OK** pour créer l’index.

#### <a name="step-3-define-the-indexer"></a>Étape 3 : définir l’indexeur
Toujours dans l’Assistant **Importer des données**, cliquez sur **Indexeur** > **Nom**, puis tapez un nom pour l’indexeur. 

Cet objet définit un processus exécutable. Vous pouvez le configurer en planification récurrente, mais pour l’instant, utilisez l’option par défaut qui exécute l’indexeur une seule fois dès que vous cliquez sur **OK**.  

  ![Indexeur de la source realestate][8]

## <a name="check-progress"></a>Vérification de la progression
Pour surveiller le processus d’importation des données, revenez au tableau de bord des services, faites défiler la page vers le bas, puis double-cliquez sur la vignette **Indexeurs** pour ouvrir la liste des indexeurs. L’indexeur que vous venez de créer devrait apparaître dans la liste, avec un état indiquant que l’opération est en cours ou qu’elle a réussi, ainsi que le nombre de documents indexés.

   ![Message de progression de l’indexeur][4]

## <a name="query-index"></a> Interroger l’index
Vous disposez maintenant d’un index de recherche prêt à être interrogé. **Explorateur de recherche** est un outil de requête intégré au portail. Il comporte une zone de recherche qui vous permet de vérifier si les résultats de la recherche répondent à vos attentes. 

> [!TIP]
> La procédure qui suit fait l’objet d’une démonstration à 06 min 08 s dans la [vidéo de présentation du service Recherche Azure](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Cliquez sur **Explorateur de recherche** dans la barre de commandes.

   ![Commande Explorateur de recherche][5]

2. Dans la barre de commandes, cliquez sur **Modifier l’index** pour basculer vers la source de données *realestate-us-sample*.

   ![Commandes d’index et d’API][6]

3. Dans la barre de commandes, cliquez sur **Définir l’API de version** pour découvrir les API REST disponibles. Les versions d’évaluation des API vous permettent d’accéder à de nouvelles fonctionnalités non encore mises à la disposition du grand public. Pour les requêtes ci-après, utilisez la version mise à la disposition générale (2016-09-01), sauf spécification contraire. 

    > [!NOTE]
    > [L’API REST du service Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/search-documents) et la [bibliothèque .NET](search-howto-dotnet-sdk.md#core-scenarios) sont totalement équivalents, mais **l’Explorateur de recherche** ne gère que les appels REST. Il accepte à la fois la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) et la [syntaxe de l’analyseur de requêtes complètes Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), ainsi que tous les paramètres de recherche disponibles dans les opérations [Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).
    > 

4. Dans la barre de recherche, entrez les chaînes de recherche ci-dessous, puis cliquez sur **Rechercher**.

  ![Exemple de requête de recherche][7]

**`search=seattle`**

+ Le paramètre **recherche** permet d’entrer une recherche par mot clé pour une recherche en texte intégral et, dans ce cas précis, renvoie les entrées du Comté de King, dans l’État de Washington, dont le document contient le terme *Seattle* dans l’un des champs utilisables dans une recherche. 

+ **L’Explorateur de recherche** renvoie les résultats au format JSON, qui est particulièrement détaillé et difficile à lire si les documents présentent une structure dense. Selon les documents dont vous disposez, vous devrez peut-être écrire du code destiné à manipuler les résultats de la recherche pour en extraire les éléments importants. 

+ Les documents sont composés de tous les champs désignés comme récupérables dans l’index. Pour visualiser les attributs d’index dans le portail, cliquez sur *realestate-us-sample* dans la vignette **Index**.

**`search=seattle&$count=true&$top=100`**

+ Le symbole **&** permet d’ajouter des paramètres de recherche, qui peuvent être spécifiés dans n’importe quel ordre. 

+  Le paramètre **$count=true** récupère une valeur indiquant le nombre total de documents renvoyés. Vous pouvez vérifier les requêtes de filtre en surveillant les modifications signalées par **$count=true**. 

+ La chaîne **$top=100** renvoie les 100 documents les mieux classés du nombre total de documents. Par défaut, le service Recherche Azure renvoie les 50 meilleures correspondances. Vous pouvez augmenter ou diminuer ce nombre par le biais du paramètre **$top**.


## <a name="filter-query"></a>Filtrer la requête

Les filtres sont inclus dans les demandes de recherche lorsque vous ajoutez le paramètre **$filter**. 

**`search=seattle&$filter=beds gt 3`**

+ Le paramètre **$filter** renvoie les résultats correspondant aux critères que vous avez spécifiés. Dans ce cas précis, la recherche renvoie les entrées présentant un nombre de chambres supérieur à 3. 

+ La syntaxe de filtre est une construction OData. Pour plus d’informations, consultez l’article [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData).

## <a name="facet-query"></a> « Facetter » la requête

Les filtres de facettes sont inclus dans les demandes de recherche. Vous pouvez utiliser le paramètre de facette pour retourner un nombre agrégé des documents qui correspondent à la valeur de facette que vous fournissez. 

**`search=*&facet=city&$top=2`**

+ **search=*** est une recherche vide. Les recherches vides portent sur tous les éléments. L’un des motifs possibles de l’exécution d’une requête vide est l’application de filtres ou de facettes au jeu complet de documents. Par exemple, vous souhaitez obtenir une structure de navigation par facettes constituée de toutes les villes dans l’index.

+  **facet** renvoie une structure de navigation que vous pouvez transmettre à un contrôle d’interface utilisateur. Il renvoie des catégories ainsi qu’un nombre. Dans ce cas précis, les catégories reposent sur le nombre de villes. Le service Recherche Azure ne propose aucune fonction d’agrégation, mais vous pouvez bénéficier d’une fonction quasiment comparable par le biais du paramètre `facet`, qui renvoie un nombre de documents dans chaque catégorie.

+ **$top=2** renvoie deux documents, illustrant ainsi la possibilité d’utiliser `top` pour réduire ou augmenter les résultats.

**`search=seattle&facet=beds`**

+ Cette requête définit une facette correspondant au nombre de chambres dans une recherche de texte portant sur *Seattle*. Le terme *beds* peut être spécifié en tant que facette, car ce champ est désigné comme récupérable, filtrable et à choix multiples dans l’index, et les valeurs qu’il contient (valeurs numériques de 1 à 5) sont adaptées à un classement des entrées en différents groupes (entrées comportant 3 chambres, entrées comportant 4 chambres, etc.). 

+ Seuls les champs filtrables peuvent être désignés comme étant à facettes. Les résultats ne peuvent renvoyer que les champs récupérables.

## <a name="highlight-query"></a> Ajouter la mise en surbrillance

La mise en surbrillance des correspondances fait référence au formatage du texte qui correspond au mot clé, lorsque des correspondances sont trouvées dans un champ spécifique. Si votre terme de recherche est profondément enfoui dans une description, vous pouvez définir une mise en surbrillance des correspondances pour le localiser plus facilement. 

**`search=granite countertops&highlight=description`**

+ Dans cet exemple, l’expression mise en forme *granite countertops* est plus facile à repérer dans le champ de description.

**`search=mice&highlight=description`**

+ La recherche en texte intégral recherche les formes d’un mot qui présentent une sémantique similaire. Dans ce cas précis, les résultats de la recherche présentent le texte « mouse » en surbrillance en réponse à une recherche portant sur le mot clé « mice ». Les résultats peuvent afficher différentes formes du même mot grâce à l’exécution d’une analyse linguistique. 

+ Le service Recherche Azure prend en charge 56 analyseurs Lucene et Microsoft. Par défaut, le service Recherche Azure utilise l’analyseur Lucene standard. 

## <a name="fuzzy-search"></a> Utiliser la recherche partielle

Dans une recherche classique, aucune correspondance n’est renvoyée pour les mots mal orthographiés, comme *samamish* pour le plateau Samammish dans la région de Seattle. Pour gérer les fautes d’orthographe, vous pouvez utiliser une recherche partielle, décrite dans l’exemple suivant.

**`search=samamish`**

+ Cet exemple orthographie mal une localité de la région de Seattle.

**`search=samamish~&queryType=full`**

+ La recherche partielle est activée lorsque vous spécifiez le symbole **~** et que vous utilisez l’analyseur de requêtes complètes, qui interprète et traite correctement la syntaxe **~**. 

+ La recherche partielle est disponible lorsque vous optez pour l’analyseur de requêtes complètes en définissant **queryType=full**. Pour plus d’informations sur les scénarios de requête autorisés par l’analyseur de requêtes complètes, consultez l’article [Lucene query syntax in Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (Syntaxe de requête Lucene dans le service Recherche Azure).

+ Lorsque l’élément **queryType** n’est pas spécifié, l’analyseur de requêtes simples par défaut est utilisé. L’analyseur de requêtes simples fonctionne plus rapidement, mais si vous avez besoin d’utiliser des recherches partielles, des expressions régulières, des recherches de proximité ou d’autres types de requêtes avancées, vous devrez recourir à la syntaxe complète. 

## <a name="geo-search"></a> Essayez la recherche géospatiale

La recherche géographique est prise en charge par le biais du [type de données edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) sur un champ contenant des coordonnées. La recherche géographique est un type de filtre, spécifié dans l’article [Filter OData syntax](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) (Syntaxe d’expression de filtre OData). 

**`search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`**

+ L’exemple de requête ci-dessus filtre tous les résultats sur la base de données positionnelles et renvoie les résultats situés à moins de 5 kilomètres d’un point donné (spécifié sous la forme de coordonnées de latitude et de longitude). L’ajout du paramètre **$count** vous permet de connaître le nombre de résultats renvoyés lorsque vous modifiez la distance ou les coordonnées. 

+ La recherche géographique est utile si votre application de recherche dispose d’une fonctionnalité « rechercher à proximité » ou qu’elle utilise la navigation dans les cartes. Toutefois, cette fonction de recherche n’est pas disponible en texte intégral. Si vos utilisateurs ont besoin de rechercher des villes ou des pays par leur nom, ajoutez des champs contenant les noms de ville ou de pays, en plus des coordonnées.

## <a name="next-steps"></a>étapes suivantes

+ Modifiez certains des objets que vous venez de créer. Après avoir exécuté une fois l’assistant, vous pouvez revenir en arrière et afficher ou modifier des composants individuels : l’index, l’indexeur ou la source de données. Certaines modifications, telles que le type de données du champ, ne sont pas autorisées dans l’index, mais la plupart des paramètres et des propriétés sont modifiables.

  Pour afficher des composants individuellement, cliquez sur la mosaïque **Index**, **Indexeur** ou **Sources de données** sur votre tableau de bord pour afficher une liste des objets existants. Pour plus d’informations sur les modifications d’index qui ne nécessitent aucune régénération, consultez l’article [Update Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/update-index) (Mettre à jour un index (API REST du service Recherche Azure)).

+ Essayez les différents outils et étapes avec d’autres sources de données. L’exemple de jeu de données `realestate-us-sample` provient d’un service Azure SQL Database que le service Recherche Azure peut analyser. Outre Azure SQL Database, le service Recherche Azure peut analyser et déduire un index à partir de structures de données plates de Stockage Table Azure, de Stockage Blob, de SQL Server sur une machine virtuelle Azure et d’Azure Cosmos DB. Toutes ces sources de données sont prises en charge dans l’Assistant. Dans le code, vous pouvez facilement remplir un index à l’aide d’un *indexeur*.

+ Toutes les autres sources de données non analysées par un indexeur sont prises en charge par le biais d’un modèle de transmission de type Push, où votre code transmet à votre index des ensembles de lignes nouveaux et modifiés au format JSON. Pour plus d’informations, consultez l’article [Add, update, or delete documents in Azure Search](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) (Ajouter, mettre à jour ou supprimer des documents dans le service Recherche Azure).

Pour plus d’informations sur les autres fonctionnalités mentionnées dans cet article, sélectionnez les liens suivants :

* [Présentation des indexeurs](search-indexer-overview.md)
* [Création d’Index (comprend une explication détaillée sur les attributs d’index)](https://docs.microsoft.com/rest/api/searchservice/create-index)
* [Explorateur de recherche](search-explorer.md)
* [Rechercher des Documents (comprend des exemples de syntaxe de requête)](https://docs.microsoft.com/rest/api/searchservice/search-documents)


<!--Image references-->
[1]: ./media/search-get-started-portal/tiles-indexers-datasources2.png
[2]: ./media/search-get-started-portal/import-data-cmd2.png
[3]: ./media/search-get-started-portal/realestateindex2.png
[4]: ./media/search-get-started-portal/indexers-inprogress2.png
[5]: ./media/search-get-started-portal/search-explorer-cmd2.png
[6]: ./media/search-get-started-portal/search-explorer-changeindex-se2.png
[7]: ./media/search-get-started-portal/search-explorer-query2.png
[8]: ./media/search-get-started-portal/realestate-indexer2.png
[9]: ./media/search-get-started-portal/import-datasource-sample2.png