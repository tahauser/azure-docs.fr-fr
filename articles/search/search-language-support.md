---
title: Recherche Azure multilingue | Microsoft Docs
description: Azure Search prend en charge 56 langages, tirant parti des analyseurs de langue de la technologie Lucene et Natural Language Processing de Microsoft.
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Création d’un index de documents dans plusieurs langues dans Azure Search
> [!div class="op_single_selector"]
>
> * [Portail](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Décupler les performances des analyseurs de langue est aussi facile que de définir une propriété sur un champ de recherche dans la définition d'index. Maintenant, vous pouvez effectuer cette étape dans le portail.

Voici les captures d'écran des panneaux du portail Azure pour Azure Search qui permettent aux utilisateurs de définir un schéma d'index. À partir de ce panneau, les utilisateurs peuvent créer tous les champs et définir la propriété de l'analyseur pour chacun d'eux.

> [!IMPORTANT]
> Vous pouvez uniquement définir un analyseur de langage lors de la définition du champ, comme lors de la création d'un nouvel index ou lorsque vous ajoutez un nouveau champ à un index existant. Veillez à spécifier entièrement tous les attributs, y compris l'analyseur, lors de la création du champ. Vous ne pourrez pas modifier les attributs ou modifier le type d'analyseur une fois vos modifications enregistrées.
>
>

## <a name="define-a-new-field-definition"></a>Définir une nouvelle définition de champ
1. Connectez-vous au [Portail Azure](https://portal.azure.com) et ouvrez le panneau de votre service de recherche.
2. Cliquez sur **Ajouter un index** dans la barre de commandes en haut du tableau de bord de service pour démarrer un nouvel index ou ouvrez un index existant pour définir un analyseur sur des nouveaux champs que vous ajoutez à un index existant.
3. Le panneau Champs s'affiche. Il vous propose des options pour définir le schéma de l'index, y compris l'onglet Analyseur utilisé pour le choix d'un analyseur de langage.
4. Dans le panneau Champs, démarrez une définition de champ en fournissant un nom, le choix du type de données et la définition des attributs pour marquer le champ en tant que texte intégral consultable, récupérables dans les résultats de recherche, utilisable dans des structures de navigation de facette, pouvant être trié, et ainsi de suite.
5. Avant de passer au champ suivant, ouvrez l'onglet **Analyseur** .

![][1]
*Pour sélectionner un analyseur, cliquez sur l’onglet Analyseur du panneau Champs*

## <a name="choose-an-analyzer"></a>Choisir un analyseur
1. Faites défiler pour rechercher le champ que vous définissez.
2. Si vous n'avez pas activé le champ de recherche, cliquez sur la case à cocher pour indiquer qu'il peut **faire l'objet d'une recherche**.
3. Cliquez sur la zone Analyseur pour afficher la liste des analyseurs disponibles.
4. Cliquez sur l'analyseur à utiliser.

![][2]
*Sélectionnez un des analyseurs pris en charge pour chaque champ*

Par défaut, tous les champs pouvant faire l'objet d'une recherche utilisent l' [analyseur Lucene Standard](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) qui est indépendant du langage. Pour afficher la liste complète des analyseurs pris en charge, consultez [Prise en charge linguistique dans Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Une fois que l'analyseur de langage est sélectionné pour un champ, il sera utilisé à chaque demande de recherche et d'indexation pour ce champ. Lorsqu'une requête est émise sur plusieurs champs à l'aide de différents analyseurs, elle sera traitée indépendamment par les analyseurs corrects pour chaque champ.

Plusieurs applications web et mobiles servent les utilisateurs dans le monde entier à l'aide de différentes langues. Il est possible de définir un index pour un scénario comme celui-ci en créant un champ pour chaque langue prise en charge.

![][3]
*Définition d'index avec un champ de description pour chaque langue prise en charge*

Si la langue de l'agent d'émission d'une requête est connue, une demande de recherche peut être étendue à un champ spécifique à l'aide du paramètre de requête **searchFields** . La requête suivante sera émise uniquement sur la description en polonais :

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Vous pouvez interroger votre index à partir du portail en utilisant l **’Explorateur de recherche** pour coller une requête similaire à celle présentée ci-dessus. L’Explorateur de recherche est disponible dans la barre de commandes du panneau de service. Pour plus d’informations, consultez l’article [Interroger votre index Azure Search dans le portail](search-explorer.md) .

Parfois, la langue de l'agent d'émission d'une requête n'est pas connue, auquel cas la requête peut être exécutée simultanément sur tous les champs. Si nécessaire, la préférence de résultats dans une langue donnée peut être définie à l'aide des [profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx). Dans l'exemple ci-dessous, les correspondances trouvées dans la description en anglais auront un score supérieur par rapport aux correspondances en polonais et en français :

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Si vous êtes un développeur .NET, notez que vous pouvez configurer les analyseurs de langage à l'aide du [Kit de développement logiciel (SDK) Azure Search .NET](http://www.nuget.org/packages/Microsoft.Azure.Search). La dernière version prend également en charge les analyseurs de langage Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
