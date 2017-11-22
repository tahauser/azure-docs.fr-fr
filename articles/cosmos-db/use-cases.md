---
title: "Cas d’utilisation courants et scénarios pour Azure Cosmos DB | Microsoft Docs"
description: "Découvrez les cinq cas d’utilisation principaux pour Azure Cosmos DB : le contenu généré par l’utilisateur, la journalisation des événements, les données de catalogue, les données de préférences de l’utilisateur et l’Internet des objets (IoT)."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: cd10147047a7d5d8b0a15be08fbba24a07adffad
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="common-azure-cosmos-db-use-cases"></a>Cas d’utilisation courants d’Azure Cosmos DB
Cet article présente plusieurs cas d’utilisation courants pour Azure Cosmos DB.  Les recommandations de cet article constituent le point de départ du développement de votre application avec Cosmos DB.   

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes : 

* Quels sont les cas d’utilisation courants pour Azure Cosmos DB ?
* Quels sont les avantages de l’utilisation d’Azure Cosmos DB pour les applications de vente au détail ?
* Quels sont les avantages de l’utilisation d’Azure Cosmos DB en tant que magasin de données pour les systèmes Internet des objets (IoT) ?
* Quels sont les avantages de l’utilisation d’Azure Cosmos DB pour les applications web et mobiles ?

## <a name="introduction"></a>Introduction
[Azure Cosmos DB](../cosmos-db/introduction.md) est le service de base de données de Microsoft distribué à l’échelle mondiale. Il permet de mettre à l’échelle le débit et le stockage de façon indépendante et en toute flexibilité pour le nombre de régions géographiques de votre choix. Azure Cosmos DB est le premier service de base de données distribué à l’échelle mondiale à offrir des [contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/cosmos-db/) complets englobant le débit, la latence, la disponibilité et la cohérence. 

Le projet Azure Cosmos DB a été lancé en 2011 sous le nom de « Project Florence » pour répondre aux problématiques des développeurs concernant les applications Internet à grande échelle chez Microsoft. Ces problèmes n’étant pas uniquement liés aux applications de Microsoft, nous avons décidé de mettre Azure Cosmos DB à la disposition générale des développeurs externes en 2015 sous la forme [d’Azure DocumentDB](https://azure.microsoft.com/blog/documentdb-moving-to-general-availability/). Le service est très largement utilisé en interne chez Microsoft et est l’un des services à la croissance la plus rapide utilisés en externe par les développeurs Azure. 

Azure Cosmos DB est une base de données multimodèle distribuée à l’échelle mondiale qui est utilisée dans un large éventail d’applications et de cas d’utilisation. Elle constitue un bon choix pour toute application [sans serveur](http://azure.com/serverless) qui a besoin de temps de réponse réduits de l’ordre des millisecondes et qui doit pouvoir évoluer rapidement et à l’échelle mondiale. Elle prend en charge plusieurs modèles de données (clé-valeur, documents, graphiques et en colonnes) et de nombreuses API pour l’accès aux données, notamment les API [MongoDB](mongodb-introduction.md), [DocumentDB (SQL)](documentdb-introduction.md), [Graph (Gremlin)](graph-introduction.md) et [Tables](table-introduction.md) en mode natif et d’une manière extensible. 

Voici certains attributs d’Azure Cosmos DB qui rendent cette base de données parfaitement adaptée aux applications hautes performances destinées à une utilisation mondiale.

* Azure Cosmos DB partitionne vos données en mode natif pour offrir une disponibilité et une évolutivité élevées. Azure Cosmos DB garantit une disponibilité, un débit, une faible latence et une cohérence à 99,99 % pour tous les comptes à région unique et à plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de lecture à 99,999 % pour tous les comptes de base de données à plusieurs régions.
* Azure Cosmos DB dispose d’un stockage SSD avec des temps de réponse de l’ordre des millisecondes à faible latence.
* La prise en charge par Azure Cosmos DB des niveaux de cohérence finale, de préfixe, de session et liée sans état offre un rapport coût-performances faible et une flexibilité totale. Aucun service de base de données n’offre autant de flexibilité qu’Azure Cosmos DB en matière de niveaux de cohérence. 
* Azure Cosmos DB possède un modèle de tarification flexible et adapté aux données qui mesure indépendamment le stockage et le débit.
* Le modèle de débit réservé d’Azure Cosmos DB vous permet de raisonner en termes de nombre de lectures/d’écritures au lieu de processeur/mémoire/IOPS du matériel sous-jacent.
* La conception d’Azure Cosmos DB vous permet de monter en puissance pour des volumes de requêtes énormes, de l’ordre de billions de requêtes par jour.

Ces attributs sont particulièrement utiles dans les applications web, mobiles, de jeu et IoT qui ont besoin de temps de réponse rapides et doivent gérer des quantités énormes de lectures et d’écritures.

## <a name="iot-and-telematics"></a>IoT et télématique
Les cas d'utilisation IoT présentent généralement les mêmes schémas pour la réception, le traitement et le stockage des données.  Tout d'abord, ces systèmes autorisent la réception de paquets de données émanant de capteurs d’appareil dans différents paramètres régionaux. Ensuite, ces systèmes traitent et analysent les données en continu pour générer des informations pertinentes en temps réel. Les données sont alors archivées dans un stockage à froid en vue d’une analyse par batch. Microsoft Azure propose des services enrichis applicables aux cas d’utilisation IoT, dont Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight et PowerBI. 

![Architecture de référence IoT d’Azure Cosmos DB](./media/use-cases/iot.png)

Des paquets de données peuvent être reçus par Azure Event Hubs qui offre un débit d’ingestion de données élevé à faible latence. Les données reçues qui doivent être traitées pour un aperçu en temps réel peuvent être transférées dans Azure Stream Analytics en vue d’une analyse en temps réel. Les données peuvent être chargées dans Azure Cosmos DB pour une interrogation ad hoc. Une fois que les données sont chargées dans Azure Cosmos DB, celles-ci sont prêtes à être interrogées. De plus, les nouvelles données et modifications apportées aux données existantes peuvent être lues sur un flux de modification. Un flux de modification est un journal persistant, constitué exclusivement d’ajouts, qui stocke les modifications apportées aux collections de Cosmos DB dans un ordre séquentiel. La totalité des données ou seulement les modifications qui ont été apportées à ces données peuvent être utilisées dans Azure Cosmos DB comme données de référence pour l’analyse en temps réel. En outre, les données peuvent être davantage affinées et traitées en connectant des données d’Azure Cosmos DB à HDInsight pour les tâches Pig, Hive ou Map/Reduce.  Les données affinées sont ensuite chargées à nouveau dans Azure Cosmos DB pour la création de rapports.   

Pour un exemple de solution IoT utilisant Azure Cosmos DB, EventHubs et Storm, consultez le [référentiel d’exemples de Storm et HDInsight sur GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Pour plus d’informations sur les offres Azure pour IoT, consultez [Création de votre Internet des objets](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Ventes et marketing
Azure Cosmos DB est largement utilisé dans les plateformes d’e-commerce Microsoft qui exécutent le Windows Store et XBox Live. Ce service est également utilisé dans le secteur de la vente au détail, pour le stockage des données de catalogue et l’approvisionnement en événements dans les pipelines de traitement des commandes.

Les scénarios d’utilisation des données de catalogue impliquent le stockage et l’interrogation d’un ensemble d’attributs pour des entités telles que des personnes, des lieux et des produits. Les comptes d’utilisateurs, les catalogues de produits, les registres d’appareil IoT et les systèmes de facturation de matériaux sont des exemples de données de catalogue. Les attributs de ces données peuvent varier et peuvent changer au fil du temps pour répondre aux besoins de l'application.

Prenons l’exemple d'un catalogue de produits pour un fournisseur de pièces détachées de voiture. Chaque pièce peut avoir ses propres attributs en plus des attributs communs qui partagent toutes les pièces. En outre, les attributs pour une pièce spécifique peuvent changer l'année suivante lorsqu'un nouveau modèle est commercialisé. Azure Cosmos DB prend en charge les schémas flexibles et les données hiérarchiques, ce qui en fait une solution idéale pour stocker les données d’un catalogue de produits.

![Architecture de référence d’un catalogue de vente Azure Cosmos DB](./media/use-cases/product-catalog.png)

Azure Cosmos DB est souvent utilisé pour l’approvisionnement en événements, afin d’alimenter les architectures pilotées par événements au moyen de sa fonctionnalité de [flux de modification](change-feed.md). Le flux de modification fournit aux microservices en aval la capacité à lire de façon incrémentielle et fiable insertions et mises à jour (par exemple, les événements de commande) qui sont apportées à un service Azure Cosmos DB. Cette fonctionnalité peut être exploitée pour fournir un magasin d’événements persistant en guise de courtier de messages des événements qui changent d’état, et pour gérer le workflow du traitement des commandes entre de nombreux microservices (pouvant être implémentés comme solution [Azure Functions sans serveur](http://azure.com/serverless)).

![Architecture de référence d’un pipeline de commande en cours Azure Cosmos DB](./media/use-cases/event-sourcing.png)

De plus, les données stockées dans Azure Cosmos DB peuvent être intégrées à HDInsight en vue d’une analyse de données volumineuses par le biais de tâches Apache Spark. Pour plus d’informations sur le connecteur Spark pour Azure Cosmos DB, consultez [Exécuter une tâche Spark avec Azure Cosmos DB et HDInsight](spark-connector.md).

## <a name="gaming"></a>Jeux
Le niveau de base de données est un composant essentiel des applications de jeu. Les jeux modernes traitent des graphiques sur les clients mobiles/console, mais s’appuient sur le cloud pour fournir un contenu personnalisé, comme les statistiques du jeu, l’intégration aux médias sociaux et les tableaux des meilleurs scores. Souvent, les jeux nécessitent des latences de l’ordre de la milliseconde pour les lectures et écritures afin de fournir une bonne qualité de jeu. Une base de données de jeu doit être rapide et pouvoir gérer les hausses importantes du nombre de demandes lors du lancement de nouveaux jeux ou de nouvelles fonctionnalités.

Azure Cosmos DB est utilisé par des jeux tels que [The Walking Dead: No Man’s Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) de [Next Games](http://www.nextgames.com/) et [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB offre les avantages suivants aux développeurs de jeux :

* Azure Cosmos DB permet d’augmenter ou de réduire les performances de manière flexible. Cela permet aux jeux de gérer la mise à jour du profil et des statistiques de dizaines jusqu’à des millions de joueurs simultanés à l’aide d’un seul appel de l’API.
* Azure Cosmos DB prend en charge les lectures et écritures en millisecondes afin d’éviter les retards dans le jeu.
* L’indexation automatique d’Azure Cosmos DB permet le filtrage sur plusieurs propriétés différentes en temps réel, par exemple pour localiser les joueurs avec leur ID de joueur en interne, ou leur ID GameCenter, Facebook, Google, ou exécuter une requête basée sur l’appartenance du joueur à un groupe. Cela est possible sans générer d’indexation complexe, ni infrastructure de partitionnement.
* Les fonctionnalités sociales, y compris les messages dans le jeu, l’appartenance des joueurs à des groupes, les défis terminés, les tableaux des meilleurs scores, et les graphiques sociaux sont plus faciles à implémenter avec un schéma flexible.
* Azure Cosmos DB comme plateforme en tant que service (PaaS) gérée requiert une gestion et une configuration minimales pour offrir une itération rapide et réduire le délai de mise sur le marché.

![Architecture de référence de gaming Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Applications web et mobiles
Azure Cosmos DB est couramment utilisé dans les applications web et mobiles, et ce service est bien adapté à la modélisation des interactions sociales, à l’intégration avec les services tiers et à la création d’expériences personnalisées riches. Les Kits de développement logiciel (SDK) Cosmos DB permettent de générer des applications iOS et Android enrichies utilisant [l’infrastructure Xamarin](mobile-apps-with-xamarin.md) qui est connue.  

### <a name="social-applications"></a>Applications des réseaux sociaux
Un cas d’utilisation courant pour Azure Cosmos DB consiste à stocker et à interroger du contenu généré par l’utilisateur pour des applications web, mobiles et de réseaux sociaux. Les sessions de conversation, les tweets, les billets de blog, les évaluations et les commentaires sont des exemples de contenus générés par l’utilisateur. Souvent, les contenus générés par les utilisateurs dans les applications des réseaux sociaux combinent du texte au format libre, des propriétés, des balises et des relations qui ne sont pas contraintes par une structure rigide. Les contenus tels que les discussions, les commentaires et les publications peuvent être stockés dans Cosmos DB sans nécessiter des transformations ou un objet complexe de couches de mappage relationnelles.  Des propriétés de données peuvent être ajoutées ou modifiées facilement pour répondre aux besoins lorsque les développeurs effectuent une itération dans le code d'application, favorisant ainsi un développement rapide.  

Les applications qui s’intègrent dans des réseaux sociaux tiers doivent répondre à l’évolution des schémas de ces réseaux. Puisque les données sont automatiquement indexées par défaut dans Cosmos DB, les données sont prêtes à être interrogées à tout moment. Par conséquent, ces applications ont la possibilité de récupérer les projections selon leurs besoins respectifs.

La plupart des applications des réseaux sociaux s'exécutent à l'échelle mondiale et peuvent présenter des modèles d'utilisation imprévisibles. La flexibilité dans la mise à l'échelle des données est essentielle car la couche Application se met à l’échelle pour correspondre à la demande.  Vous pouvez monter en charge en ajoutant des partitions de données supplémentaires sous un compte Cosmos DB.  De plus, vous pouvez également créer des comptes Cosmos DB supplémentaires dans plusieurs régions. Pour la disponibilité régionale du service Cosmos DB, consultez [Régions Azure](https://azure.microsoft.com/regions/#services).

![Architecture de référence des applications web Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personnalisation
Aujourd’hui, la plupart des applications offrent des affichages et des expériences complexes. Elles sont généralement dynamiques et répondent aux préférences et aux humeurs de l’utilisateur ainsi qu’aux besoins de personnalisation. Par conséquent, les applications doivent pouvoir extraire efficacement des paramètres personnalisés pour restituer rapidement les éléments de l’interface utilisateur et les expériences. 

Le format JSON, pris en charge par Cosmos DB, est un format efficace pour représenter des données de disposition de l’interface utilisateur afin de la rendre légère, mais aussi pour qu’elle soit facilement interprétée par JavaScript. Cosmos DB offre des niveaux de cohérence paramétrables qui permettent des lectures rapides avec des écritures à faible latence. Ainsi, le stockage des données de disposition de l’interface utilisateur, y compris les paramètres personnalisés en tant que documents JSON dans Cosmos DB, est un moyen efficace pour obtenir ces données sur tout le réseau.

![Architecture de référence des applications web Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir Azure Cosmos DB, suivez nos [démarrages rapides](create-documentdb-dotnet.md) qui vous guident dans la création d’un compte et la prise en main de Cosmos DB. 

Si vous souhaitez en savoir plus sur les clients qui utilisent Cosmos DB, consultez les témoignages de clients suivants :

* [Jet.com](https://jet.com). Une jeune entreprise ambitieuse d’e-commerce qui s’exécute sur le cloud Microsoft et utilise Cosmos DB à l’échelle mondiale.
* [Asos.com](http://www.asos.com/). Asos.com est une boutique de mode et beauté en ligne britannique. Ciblant principalement les jeunes adultes, Asos vend plus de 850 marques, ainsi que sa propre sélection de vêtements et accessoires.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation est un constructeur automobile japonais. Toyota a utilisé Cosmos DB dans le cadre d’une application IoT globale.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix développe une solution d’authentification unique à l’aide d’Azure Service Fabric et d’Azure Cosmos DB.
* [TEXA](https://customers.microsoft.com/story/texaspa) La solution IoT révolutionnaire de TEXA destinée aux propriétaires de véhicule permet d’économiser du temps, de l’argent, de l’essence, et même de sauver des vies.
* [Domino's Pizza](https://www.dominos.com). Domino's Pizza Inc. est une chaîne de pizzerias américaine.
* [Johnson Controls](http://www.johnsoncontrols.com). Johnson Controls est un leader technologique mondial qui propose ses solutions à une vaste clientèle de tous secteurs dans plus de 150 pays.
* [Microsoft Windows, Universal Store, Azure IoT Hub, Xbox Live et autres services Internet](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Découvrez comment Microsoft crée des services hautement évolutifs à l’aide d’Azure Cosmos DB.
* [Équipe Microsoft en charge des données et des analyses](https://customers.microsoft.com/story/microsoftdataandanalytics). L’équipe Microsoft en charge des données et des analyses collecte un nombre impressionnant de données au niveau mondial avec Azure Cosmos DB.
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha utilise Azure Cosmos DB pour se connecter aux clients et aux entreprises en Inde.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit prend de l’altitude avec Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio passe d’AWS à Azure Cosmos DB pour exploiter des données sociales à grande échelle.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Le jeu The Walking Dead: No Man's Land atteint la première place avec le soutien d’Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Comment Halo 5 a mis en œuvre l’expérience de jeu de société avec Azure Cosmos DB.
* [Galerie Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galerie Cortana Analytics : un site communautaire évolutif basé sur Azure Cosmos DB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). L’intégrateur principal offre aux entreprises multinationales un aperçu global en quelques minutes avec les technologies de cloud flexibles.
* [News République](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Rendre les informations plus intelligentes pour offrir un contenu plus pertinent aux citoyens engagés. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Pour une harmonisation à travers le globe, les grandes marques se tournent vers SGS. Et SGS se tourne vers Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Le leader mondial Telenor utilise le cloud pour évoluer à la vitesse d'une startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Le magasin du futur fonctionne grâce à la recherche rapide et au flux de données simplifié.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Plateforme logicielle basée sur Azure, qui élimine les barrières entre les entreprises et les clients
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka Smart Fridge améliore la gestion des vaccins, ce qui permet de protéger davantage de personnes contre les maladies
* [Orange Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Cette application dédiée à l’alimentation satisfait encore plus la vue, et le goût.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Le Real Madrid permet à 450 millions de fans du monde entier de ressentir les émotions du stade grâce à Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). Avec les services Azure, il est très amusant d’acheter une voiture sur TUKU
