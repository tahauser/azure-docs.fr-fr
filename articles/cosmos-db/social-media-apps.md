---
title: 'Modèle de conception Azure Cosmos DB : applications de réseaux sociaux | Microsoft Docs'
description: Découvrez un modèle de conception destiné aux réseaux sociaux et tirant parti de la souplesse du stockage d’Azure Cosmos DB et d’autres services Azure.
keywords: applications de réseaux sociaux
services: cosmos-db
author: ealsur
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.openlocfilehash: d223187ab1f8338c57e6aa32903ea9b762393ab5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="going-social-with-azure-cosmos-db"></a>Réseaux sociaux avec Azure Cosmos DB
Vivre dans une société massivement interconnectée signifie qu’à un moment donné, vous intégrerez forcément un **réseau social**. Nous utilisons les réseaux sociaux pour rester en contact avec nos amis, nos collègues, notre famille ou parfois pour partager notre passion avec des personnes ayant des intérêts communs.

En tant qu’ingénieurs ou développeurs, vous vous êtes sans doute demandé comment ces réseaux sociaux stockent et interconnectent nos données. Vous avez peut-être aussi décidé de créer ou de concevoir vous-même un nouveau réseau social pour un marché spécifique. C’est à ce moment que la question se pose : comment sont stockées toutes ces données ?

Supposons que nous avons décidé de créer un nouveau réseau social, où nos utilisateurs peuvent publier des articles accompagnés de contenu multimédia tel que des images, des vidéos ou même de la musique. Les utilisateurs peuvent commenter les publications et attribuer des points pour créer des classements. Les utilisateurs peuvent afficher un flux de messages avec lequel ils peuvent interagir sur la page d’accueil du site Web principal. Cela ne semble pas très complexe (au début), mais par souci de simplicité, arrêtons-nous là (nous pourrions nous intéresser aux flux utilisateur personnalisés influencés par les relations, mais cela dépasse l’objectif de cet article).

Alors, comment et où stockons-nous ces données ?

Beaucoup d’entre vous ont sans doute une expérience d’utilisation des bases de données SQL ou disposent au moins de connaissances de base sur la [modélisation relationnelle des données](https://en.wikipedia.org/wiki/Relational_model) et vous pourriez être tentés de commencer à concevoir quelque chose comme ceci :

![Diagramme illustrant un modèle relationnel relatif](./media/social-media-apps/social-media-apps-sql.png) 

Une structure de données tout à fait normalisée et agréable... qui n’est pas extensible. 

Ne vous méprenez pas, j’utilise des bases de données SQL depuis très longtemps, elles sont parfaites, mais comme tout modèle, pratique et plateforme logicielle, elles ne sont pas adaptées à tous les scénarios.

Pourquoi SQL n’est-il pas le meilleur choix dans ce scénario ? Examinons la structure d’une publication unique. Si je voulais afficher celle-ci dans un site web ou une application, je devrais faire une requête avec… 8 jointures de tables... Pour une seule publication ! À présent, imaginez un flux de publications qui se chargent et s’affichent de façon dynamique, et vous verrez peut-être où je veux en venir.

Nous pourrions, bien sûr, utiliser une instance SQL volumineuse avec suffisamment de puissance pour résoudre des milliers de requêtes avec ces nombreuses jointures pour présenter notre contenu, mais pourquoi procéder ainsi alors qu’une solution plus simple existe ?

## <a name="the-nosql-road"></a>L’utilisation de NoSQL
Cet article vous explique comment modéliser les données de votre plateforme sociale avec la base de données NoSQL [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) à moindre frais tout en exploitant d’autres fonctionnalités Azure Cosmos DB, telles que [l’API Graph Gremlin](../cosmos-db/graph-introduction.md). Avec une approche [NoSQL](https://en.wikipedia.org/wiki/NoSQL), le stockage des données au format JSON et l’application de la [dénormalisation](https://en.wikipedia.org/wiki/Denormalization), notre publication si compliquée auparavant peut être transformée en un seul [document](https://en.wikipedia.org/wiki/Document-oriented_database) :


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Et il peut être obtenu avec une seule requête, sans jointure. Cette approche est beaucoup plus simple et directe. Elle nécessite moins de ressources pour un meilleur résultat, ce qui est parfait du point de vue du budget.

Azure Cosmos DB s’assure que toutes les propriétés sont indexées avec son indexation automatique, qui peut même être [personnalisée](indexing-policies.md). L’approche sans schéma nous permet de stocker des documents avec des structures différentes et dynamiques. Peut-être que demain nous voudrons avoir des publications avec une liste de catégories ou des hashtags associés. Cosmos DB gérera les nouveaux documents avec les attributs ajoutés sans aucun travail supplémentaire de notre part.

Les commentaires sur une publication peuvent être traités comme n’importe quelle autre publication avec une propriété parente (cela simplifie le mappage d’objets). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Et toutes les interactions sociales peuvent être stockées sur un objet distinct en tant que compteurs :

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

La création de flux consiste simplement à créer des documents qui peuvent contenir une liste des ID des publications avec un ordre de pertinence donné :

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Nous pourrions avoir un flux « récent » avec les publications classées par date de création, un flux « populaire » regroupant les publications ayant obtenu le plus grand nombre de J’aime dans les dernières 24 heures, nous pourrions même implémenter un flux personnalisé pour chaque utilisateur basé sur la logique, comme ses abonnés et ses centres d’intérêt, et cela sera toujours considéré comme une liste de publications. Le plus compliqué est de créer ces listes, mais les performances de lecture ne sont pas affectées. Une fois que nous avons obtenu l’une de ces listes, nous émettons une requête unique à Cosmos DB avec l’[opérateur IN](sql-api-sql-query.md#WhereClause) pour obtenir des pages de publications simultanément.

Les flux de commentaires peuvent être créés à l’aide des processus d’arrière-plan d’[Azure App Services](https://azure.microsoft.com/services/app-service/) : [Webjobs](../app-service/web-sites-create-web-jobs.md). Lorsqu’une publication est créée, le traitement en arrière-plan peut être déclenché à l’aide de [files d’attente](../storage/queues/storage-dotnet-how-to-use-queues.md) de [Stockage Azure](https://azure.microsoft.com/services/storage/) et de tâches webjobs déclenchées avec le [Kit de développement logiciel (SDK) Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki), implémentant la propagation ultérieure dans les flux basée sur notre propre logique personnalisée. 

Les points et les J’aime attribués à une publication peuvent être traités de manière différée à l’aide de cette même technique pour créer un environnement cohérent.

Cela est plus compliqué pour les abonnés. Cosmos DB a une taille de document limite, donc la lecture ou l’écriture de documents volumineux peuvent avoir une incidence sur la scalabilité de votre application. Vous pouvez envisager de stocker les abonnés dans un document avec cette structure :

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Cela peut fonctionner pour un utilisateur avec quelques milliers d’abonnés, mais si des célébrités rejoignent nos rangs, cette opération donnera lieu à un document volumineux et atteindra forcément la limite de taille du document.

Pour résoudre ce problème, nous pouvons utiliser une approche mixte. Dans le cadre du document Statistiques de l’utilisateur, nous pouvons stocker le nombre d’abonnés :

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Et le graphe réel d’abonnés peut être stocké à l’aide de [l’API Graph Gremlin](../cosmos-db/graph-introduction.md) d’Azure Cosmos DB pour créer des [sommets](http://mathworld.wolfram.com/GraphVertex.html) pour chaque utilisateur et des [arêtes](http://mathworld.wolfram.com/GraphEdge.html) qui gèrent les relations de type « A-suit-B ». L’API Graph vous permet non seulement d’obtenir les abonnés d’un utilisateur donné, mais également de créer des requêtes plus complexes pour même suggérer des personnes en commun. Si nous ajoutons au graphe les catégories de contenus que les personnes aiment ou apprécient, nous pouvons commencer à tisser des expériences qui incluent la détection de contenu intelligente, la suggestion de contenu aimé par les personnes que nous suivons ou la recherche de personnes avec lesquelles nous pourrions avoir beaucoup en commun.

Le document sur les statistiques utilisateur peut toujours servir à créer des cartes dans l’interface utilisateur ou des préversions de profil rapides.

## <a name="the-ladder-pattern-and-data-duplication"></a>Duplication des données et modèle « Échelle »
Comme vous l’avez peut-être remarqué dans le document JSON qui fait référence à une publication, il existe plusieurs occurrences d’un utilisateur. Et vous auriez raison. Cela signifie que les informations qui représentent un utilisateur peuvent être présentes à plusieurs endroits, en raison de la dénormalisation.

Pour obtenir des requêtes plus rapides, nous engendrons la duplication des données. Le problème de cet effet secondaire est que si les données d’un utilisateur changent, nous devrons trouver toutes les activités auxquelles il a participé et les mettre toutes à jour. Cela ne semble pas très pratique, n’est-ce pas ?

Nous allons le résoudre en identifiant les attributs clés d’un utilisateur que nous affichons dans notre application pour chaque activité. Si nous affichons visuellement une publication dans notre application et n’affichons que le nom et la photo du créateur, pourquoi stocker toutes les données de l’utilisateur dans l’attribut « createdBy » ? Si, pour chaque commentaire, nous affichons uniquement la photo de l’utilisateur, nous n’avons pas vraiment besoin des autres informations le concernant. C’est là que ce que j’appelle le « modèle Échelle » entre en jeu.

Prenons comme exemple les informations utilisateur :

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

En examinant ces informations, nous pouvons rapidement détecter les informations essentielles et celles qui ne le sont pas, créant ainsi une « échelle » :

![Diagramme d’un modèle d’échelle](./media/social-media-apps/social-media-apps-ladder.png)

L’étape la plus petite est appelée UserChunk, l’information minimale qui identifie un utilisateur et qui est utilisée pour la duplication des données. En limitant la taille des données dupliquées uniquement aux informations que nous allons « afficher », nous réduisons la possibilité de mises à jour massives.

L’étape intermédiaire est appelée Utilisateur. Ce sont les données complètes qui seront employées pour les requêtes qui dépendent le plus des performances sur Cosmos DB, les données les plus consultées et les plus essentielles. Elles incluent les informations représentées par un UserChunk.

L’étape la plus importante est appelée Utilisateur étendu. Elle inclut toutes les informations utilisateur essentielles, ainsi que d’autres données qui ne nécessitent pas vraiment d’être lues rapidement ou dont l’utilisation est finale (par exemple, le processus de connexion). Ces données peuvent être stockées en dehors de Cosmos DB, dans Azure SQL Database ou des tables Stockage Azure.

Pourquoi fractionner l’utilisateur et même stocker ces informations dans des endroits différents ? Parce que du point de vue du niveau de performance, plus les documents sont volumineux, plus les requêtes sont coûteuses. Maintenez les documents à une taille minimale, avec les informations correctes pour exécuter toutes vos requêtes dépendantes des performances pour votre réseau social, et stockez les informations supplémentaires pour des scénarios éventuels comme les modifications de profil complètes, les connexions, et même l’exploration des données pour l’analyse de l’utilisation et les initiatives de Big Data. Peu importe que la collecte de données pour l’analyse soit plus lente parce qu’elle est exécutée sur Azure SQL Database, ce qui nous importe c’est que nos utilisateurs bénéficient d’une expérience rapide et compacte. Un utilisateur, stocké sur Cosmos DB, ressemblerait à ceci :

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Et une publication ressemblerait à ce qui suit :

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Et en cas de modification où l’un des attributs du segment est affecté, il est facile de trouver les documents affectés à l’aide de requêtes qui pointent vers les attributs indexés (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”), puis de mettre les segments à jour.

## <a name="the-search-box"></a>La zone de recherche
Heureusement, les utilisateurs génèrent beaucoup de contenu. Et nous devrions être en mesure de fournir la possibilité de rechercher et de trouver du contenu qui n’est peut-être pas directement dans leur flux de contenu, parce qu’ils ne suivent pas les créateurs ou qu’ils essayent simplement de trouver une publication créée il y a 6 mois.

Heureusement, et grâce à l’utilisation d’Azure Cosmos DB, nous pouvons facilement implémenter un moteur de recherche à l’aide de [Recherche Azure](https://azure.microsoft.com/services/search/) en quelques minutes et sans taper une seule ligne de code (à part le processus de recherche et l’interface utilisateur, bien entendu).

Pourquoi est-ce si facile ?

Le service Recherche Azure implémente des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx). Ce sont des processus d’arrière-plan intégrés dans vos référentiels de données et qui ajoutent, mettent à jour ou suppriment automatiquement vos objets dans les index. Ils prennent en charge les [indexeurs Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), les [indexeurs d’objets blob Azure](../search/search-howto-indexing-azure-blob-storage.md) et les [indexeurs Azure Cosmos DB](../search/search-howto-index-documentdb.md). Le passage des informations de Cosmos DB à Recherche Azure est simple, car ils stockent tous les deux les informations au format JSON. Nous devons simplement [créer notre index](../search/search-create-index-portal.md) et mapper les attributs de nos documents à indexer et voilà, en quelques minutes (selon la taille des données), tout notre contenu sera disponible à la recherche, grâce à la meilleure solution Search-as-a-Service de l’infrastructure cloud. 

Pour plus d’informations sur Recherche Azure, vous pouvez consulter le document [Hitchhiker’s Guide to Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Les connaissances sous-jacentes
Après avoir stocké ce contenu dont la taille augmente chaque jour, nous pourrions penser : que puis-je faire avec toutes ces informations de mes utilisateurs ?

La réponse est simple : exploitez-les.

Mais, que pouvons-nous apprendre ? Quelques exemples simples incluent l’ [analyse des sentiments](https://en.wikipedia.org/wiki/Sentiment_analysis), les recommandations de contenu basées sur les préférences d’un utilisateur ou même un modérateur de contenu automatique qui garantit que tout le contenu publié par notre réseau social est adapté à toute la famille.

Maintenant que j’ai votre attention, vous pensez sans doute qu’il vous faut un doctorat en sciences mathématiques pour extraire ces modèles et ces informations de fichiers et de bases de données simples, mais vous avez tort.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), composant de [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), est un service cloud entièrement géré qui vous permet de créer des workflows à l’aide d’algorithmes dans une simple interface de type glisser-déposer, de coder vos propres algorithmes en [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) ou d’utiliser certaines des API déjà créées et prêtes à l’utilisation, comme : [Analyse de texte](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Modérateur de contenu](https://www.microsoft.com/moderator) ou [Recommandations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Pour réaliser l’un de ces scénarios d’apprentissage, nous pouvons utiliser [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) pour ingérer les informations de différentes sources et utiliser [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) pour traiter les informations et générer une sortie qui peut être traitée par Azure Machine Learning.

Une autre possibilité consiste à utiliser [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) pour analyser le contenu de nos utilisateurs ; non seulement nous les comprenons mieux (en analysant ce qu’ils écrivent avec l’[API d’analyse de texte](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), mais nous pouvons également détecter le contenu indésirable ou réservé aux adultes et agir en conséquence avec l’[API Vision par ordinateur](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services inclut un grand nombre de solutions prêtes à l’emploi ne nécessitant aucune connaissance Machine Learning.

## <a name="a-planet-scale-social-experience"></a>Une expérience sociale à l’échelle de la planète
Il existe un dernier sujet tout aussi important à traiter : **l’évolutivité**. Lors de la conception d’une architecture, il est essentiel que chaque composant puisse évoluer de manière autonome, soit parce que nous devons traiter plus de données ou que nous voulons une plus grande couverture géographique (voire les deux). Heureusement, Cosmos DB offre une **solution clé en main** pour gérer une tâche aussi complexe.

Cosmos DB prend en charge nativement le [partitionnement dynamique](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) en créant automatiquement des partitions basées sur une **clé de partition** donnée (définie comme l’un des attributs dans vos documents). La définition de la clé de partition correcte doit être effectuée au moment de la conception et en tenant compte des [meilleures pratiques](../cosmos-db/partition-data.md#designing-for-partitioning) disponibles ; dans le cas d’une expérience sociale, votre stratégie de partitionnement doit être alignée avec la manière dont vous interrogez (des lectures dans la même partition sont souhaitables) et écrivez (évitez les « zones réactives » en répartissant les écritures sur plusieurs partitions). Options disponibles : partitions basées sur une clé temporaire (jour/mois/semaine), par catégorie de contenu, par région géographique, par utilisateur ; tout cela dépend de la façon dont vous allez interroger les données et les présenter dans votre expérience sociale. 

Cosmos DB exécute vos requêtes (y compris les [agrégats](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) de façon transparente sur toutes vos partitions, et vous n’avez pas besoin d’ajouter de logique à mesure que vos données augmentent.

Avec le temps, le trafic et la consommation des ressources augmenteront (mesurés en unité de requête ou [RU](request-units.md)). Vous lirez et écrirez plus fréquemment à mesure que votre base d’utilisateurs se développe et ces utilisateurs créeront et liront plus de contenus ; la possibilité de **mise à l’échelle de votre débit** est donc indispensable. Augmenter vos RU est très facile : nous pouvons le faire en quelques clics sur le portail Azure ou en [émettant des commandes via l’API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Mise à l’échelle et définition d’une clé de partition](./media/social-media-apps/social-media-apps-scaling.png)

Imaginez que les choses continuent de s’améliorer et que des utilisateurs d’une autre région, d’un autre pays ou d’un autre continent découvrent votre plateforme et l’utilisent ? Quelle bonne surprise !

Mais vous constatez rapidement que leur expérience avec votre plate-forme n’est pas optimale car ils sont si éloignés de votre région opérationnelle que la latence est catastrophique et risquerait de les dissuader d’utiliser votre plate-forme. Mais il existe un moyen facile de **développer votre visibilité globale** !

Cosmos DB vous permet de [répliquer vos données globalement](../cosmos-db/tutorial-global-distribution-sql-api.md) et de manière transparente en quelques clics, mais aussi de choisir automatiquement parmi les régions disponibles à partir de votre [code client](../cosmos-db/tutorial-global-distribution-sql-api.md). Cela signifie également que vous pouvez avoir [plusieurs régions de basculement](regional-failover.md). 

Lorsque vous répliquez vos données globalement, vous devez vous assurer que vos clients peuvent en tirer parti. Si vous utilisez un front-end web ou accédez aux API à partir de clients mobiles, vous pouvez déployer [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) et cloner votre Azure App Service dans toutes les régions de votre choix en utilisant une configuration des performances pour prendre en charge votre couverture étendue globale. Quand vos clients accèdent à votre serveur frontal ou API, ils sont redirigés vers l’instance App Service la plus proche qui, à son tour, se connecte au réplica Cosmos DB local.

![Ajout d’une couverture globale à votre plate-forme sociale](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusion
Cet article tente de vous éclairer sur les alternatives de création de réseaux sociaux sur Azure avec des services à faible coût et offrant de bons résultats, avec l’utilisation d’une solution de stockage à plusieurs niveaux et une distribution des données appelée « Échelle ».

![Diagramme de l’interaction entre les services Azure pour les réseaux sociaux](./media/social-media-apps/social-media-apps-azure-solution.png)

En réalité, il n’existe aucune solution parfaite pour ce type de scénario. C’est la synergie créée par la combinaison d’excellents services qui nous permet de concevoir des expériences exceptionnelles  : la rapidité et la liberté d’Azure Cosmos DB pour une application sociale intéressante, l’intelligence d’une solution de recherche de premier ordre comme Recherche Azure, la flexibilité d’Azure App Services pour héberger non pas des applications indépendantes du langage, mais des processus d’arrière-plan puissants, ainsi que les outils de stockage Azure et Azure SQL Database extensibles pour le stockage de grandes quantités de données et la puissance d’analyse d’Azure Machine Learning pour créer les connaissances et l’intelligence décisionnelle capables de fournir des commentaires à nos processus et de nous aider à présenter le bon contenu aux bons utilisateurs.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les cas d’usage de Cosmos DB, consultez [Cas d’utilisation courants et scénarios pour Cosmos DB](use-cases.md).