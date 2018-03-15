---
title: "Unités de requête et estimation du débit - Azure Cosmos DB | Microsoft Docs"
description: "Découvrez les besoins en unités de requête dans Azure Cosmos DB, et comment les spécifier et les estimer."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: d0a3c310-eb63-4e45-8122-b7724095c32f
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: d263c4f5ad14f6692a7c8f6e66429b439a52a84a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="request-units-in-azure-cosmos-db"></a>Unités de requête dans Azure Cosmos DB
Désormais disponible : [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner) Azure Cosmos DB. Pour en savoir plus, consultez [Estimation des besoins de débit](request-units.md#estimating-throughput-needs).

![Calculatrice de débit][5]

## <a name="introduction"></a>Introduction
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) est un service de base de données multimodèle mondialement distribué de Microsoft. Avec Azure Cosmos DB, il n’est pas nécessaire de louer des machines virtuelles, de déployer des logiciels ou de surveiller les bases de données. Azure Cosmos DB est utilisé et surveillé en continu par les excellents ingénieurs Microsoft afin d’offrir une disponibilité, des performances et une protection des données optimales. Vous pouvez accéder à vos données en utilisant les API de votre choix, comme l’[API SQL](documentdb-introduction.md), l’[API MongoDB](mongodb-introduction.md), l’[API Table](table-introduction.md) et Gremlin via l’[API Graph](graph-introduction.md), qui sont toutes prises en charge en mode natif. L’unité de mesure d’Azure Cosmos DB est l’unité de requête (RU). Avec les unités de requête (RU), vous n’avez pas besoin de réserver de capacités en lecture et en écriture, ni de configurer les ressources de processeur, de mémoire et d’E/S par seconde.

Azure Cosmos DB prend en charge un certain nombre d’API avec différentes opérations allant des lectures et des écritures simples aux requêtes de graphe complexes. Toutes les requêtes n’étant pas égales, la quantité normalisée **d’unités de requête** qui leur est affectée est fonction de la quantité de calcul requise pour traiter chaque requête. Le nombre d’unités de requête d’une opération est déterministe. Dans Azure Cosmos DB, vous pouvez suivre le nombre d’unités de requête consommées par une opération via un en-tête de réponse. 

Pour fournir des performances prévisibles, vous devez réserver le débit par unité de 100 RU/seconde. 

Après avoir lu cet article, vous serez en mesure de répondre aux questions suivantes :  

* Que sont les unités de requête et les frais de requête ?
* Comment spécifier la capacité d’unités de requête pour une collection ?
* Comment estimer les besoins en unités de requête de mon application ?
* Que se passe-t-il si je dépasse la capacité d’unités de requête pour une collection ?

Azure Cosmos DB étant une base de données multimodèle, il est important de noter que cet article fait référence à une collection/un document pour l’API Document, à un graphe/nœud pour l’API Graph et à une table/entité pour l’API Table. Cet article fait référence au concept de collection, de graphique ou de table en tant que conteneur, et au concept de document, de nœud ou d’entité en tant qu’élément.

## <a name="request-units-and-request-charges"></a>Unités de requête et frais de requête
Azure Cosmos DB offre des performances élevées et prévisibles en *réservant* des ressources pour répondre aux besoins de débit de votre application.  Étant donné que les schémas d’accès et de charge des applications changent au fil du temps, Azure Cosmos DB vous permet de facilement augmenter ou diminuer la quantité de débit réservé disponible pour votre application.

Avec Azure Cosmos DB, un débit réservé est spécifié en termes de traitement d’unités de requête par seconde. On peut considérer les unités de requête un peu comme une devise de débit : vous *réservez* une quantité d’unités de requête garantie accessible par seconde à votre application.  Chaque opération dans Azure Cosmos DB (écriture d’un document, exécution d’une requête, mise à jour d’un document) consomme des ressources de processeur, de mémoire et d’E/S par seconde.  Autrement dit, chaque opération entraîne des *frais de requête*, exprimés en *unités de requête*.  Comprendre les facteurs qui ont un impact sur les frais d’unités de requête et cerner les demandes de débit de votre application vous permettent d’exécuter votre application de la manière la plus rentable possible. L’Explorateur de données dans le portail Azure est également un outil merveilleux pour tester le cœur d’une requête.

Nous vous recommandons de commencer par visionner la vidéo suivante, dans laquelle Aravind Ramachandran explique les unités de requête et les performances prévisibles avec Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Predictable-Performance-with-DocumentDB/player]
> 
> 

## <a name="specifying-request-unit-capacity-in-azure-cosmos-db"></a>Spécification de la capacité d’unités de requête dans Azure Cosmos DB
Lorsque vous démarrez une nouvelle collection, table ou un nouveau graphique, vous spécifiez le nombre d’unités de requête (RU par seconde) à réserver. En fonction du débit configuré, Azure Cosmos DB alloue des partitions physiques pour héberger votre collection et répartit/rééquilibre les données sur les partitions à mesure qu’elles se développent.

Les conteneurs Azure Cosmos DB peuvent être créés fixes ou illimités. Les conteneurs de taille fixe ont une limite maximale de 10 Go et de 10 000 RU/s de débit. Pour créer un conteneur illimité, vous devez spécifier un débit minimal de 1 000 RU/s et une [clé de partition](partition-data.md). Dans la mesure où vos données doivent parfois être réparties sur plusieurs partitions, vous devez choisir une clé de partition ayant une cardinalité élevée (de plusieurs centaines à plusieurs millions de valeurs distinctes). En sélectionnant une clé de partition avec de nombreuses valeurs distinctes, vous garantissez que votre collection/table/graphique et vos requêtes peuvent être mis à l’échelle de façon uniforme par Azure Cosmos DB. 

> [!NOTE]
> Une clé de partition est une limite logique et non physique. Il n’est donc pas nécessaire de limiter le nombre de valeurs de clé de partition distinctes. En fait, il vaut mieux avoir trop de valeurs de clé de partition distinctes que pas assez, car Azure Cosmos DB offre plus d’options d’équilibrage de charge.

L’extrait de code suivant permet de créer une collection avec 3 000 unités de requête par seconde à l’aide du SDK .NET :

```csharp
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000 });
```

Azure Cosmos DB fonctionne sur un modèle de réservation du débit. Autrement dit, vous êtes facturé pour la quantité de débit *réservée*, quelle que soit la quantité activement *utilisée*. À mesure que les modèles d’utilisation, de données et de charge de votre application évoluent, vous pouvez facilement augmenter ou réduire la quantité de RU réservées par le biais des kits de développement logiciel (SDK) Azure Cosmos DB ou à l’aide du [portail Azure](https://portal.azure.com).

Chaque collection/table/graphique est mappé sur une ressource `Offer` dans Azure Cosmos DB, qui contient des métadonnées sur le débit configuré. Vous pouvez modifier le débit alloué pour un conteneur en recherchant la ressource de l’offre correspondante, et en mettant à jour la valeur de débit. L’extrait de code suivant permet de changer le débit d’une collection en 5 000 unités de requête par seconde à l’aide du SDK .NET :

```csharp
// Fetch the resource to be updated
Offer offer = client.CreateOfferQuery()
                .Where(r => r.ResourceLink == collection.SelfLink)    
                .AsEnumerable()
                .SingleOrDefault();

// Set the throughput to 5000 request units per second
offer = new OfferV2(offer, 5000);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offer);
```

La modification du débit n’a aucun impact sur la disponibilité de votre conteneur. Le nouveau débit réservé est généralement effectif quelques secondes après son application.

## <a name="throughput-isolation-in-globally-distributed-databases"></a>Isolement de débit dans des bases de données distribués à l’échelle mondiale

Une fois votre base de données répliquée dans plusieurs régions, Azure Cosmos DB fournit une isolation de débit pour garantir que l’utilisation de RU dans une région n’a aucune incidence sur l’utilisation de RU dans une autre région. Par exemple, si vous écrivez des données dans une région et lisez des données à partir d’une autre région, les RU permettant d’effectuer l’opération d’écriture dans la région A ne lèsent pas les RU utilisées pour l’opération de lecture dans la région B. Les RU ne sont pas fractionnées sur les différentes régions où vous avez effectué le déploiement. Chaque région dans laquelle la base de données est répliquée comporte la quantité totale de RU configurées. Pour en savoir plus sur la réplication à l’échelle mondiale, voir [Comment distribuer des données mondialement avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="request-unit-considerations"></a>Considérations relatives aux unités de requête
Quand vous évaluez le nombre d’unités de requête à réserver pour votre conteneur Azure Cosmos DB, vous devez impérativement tenir compte des variables suivantes :

* **Taille de l’élément**. Plus la taille est grande, plus le nombre d’unités consommées pour lire ou écrire des données augmente.
* **Nombre de propriétés de l’élément**. En supposant que toutes les propriétés sont indexées par défaut, le nombre d’unités consommées pour écrire un document/nœud/une entité augmente parallèlement au nombre de propriétés.
* **Cohérence des données**. Quand vous utilisez les niveaux de cohérence des données Fort ou Obsolescence limitée, des unités supplémentaires sont consommées pour lire les éléments.
* **Propriétés indexées**. Une stratégie d’indexation sur chaque conteneur détermine quelles propriétés sont indexées par défaut. Vous pouvez réduire la consommation d’unités de requête en limitant le nombre de propriétés indexées ou en activant l’indexation différée.
* **Indexation des documents**. Par défaut, chaque élément est indexé automatiquement. Vous consommez moins d’unités de requête si vous choisissez de ne pas indexer certains de vos éléments.
* **Modèles de requête**. La complexité d’une requête a un impact sur le nombre d’unités de requête consommées pour une opération. Le nombre de prédicats, la nature des prédicats, les projections, le nombre de fonctions définies par l’utilisateur et la taille du jeu de données source ont tous une influence sur le coût des opérations de requête.
* **Utilisation des scripts**.  Comme avec les requêtes, les procédures stockées et les déclencheurs consomment plus ou moins d’unités de requête en fonction de la complexité des opérations effectuées. Pendant le développement de votre application, inspectez l'en-tête des frais de requêtes pour mieux comprendre de quelle façon chaque opération consomme la capacité des unités de requête.

## <a name="estimating-throughput-needs"></a>Estimation des besoins de débit
Une unité de requête est une mesure normalisée du coût de traitement de la requête. Une unité de requête représente la capacité de traitement nécessaire pour lire (par le biais d’un lien réflexif ou d’un ID) un seul élément de 1 Ko composé de 10 valeurs de propriété uniques (à l’exclusion des propriétés système). Une demande de création (insertion), de remplacement ou de suppression du même élément nécessite un plus grand traitement de la part du service et consomme donc plus d’unités de requête.   

> [!NOTE]
> La ligne de base d’une unité de requête pour un élément de 1 Ko correspond à une opération GET simple par le lien réflexif ou l’ID de l’élément.
> 
> 

Par exemple, voici un tableau qui indique le nombre d’unités de requête à fournir pour trois tailles d’élément (1 Ko, 4 Ko et 64 Ko) et à deux niveaux de performances (500 lectures par seconde + 100 écritures par seconde et 500 lectures par seconde + 500 écritures par seconde). La cohérence des données a été configurée au niveau de la session et la stratégie d’indexation a été définie sur None.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Taille de l’élément</strong></p></td>
            <td valign="top"><p><strong>Lectures par seconde</strong></p></td>
            <td valign="top"><p><strong>Écritures par seconde</strong></p></td>
            <td valign="top"><p><strong>Unités de demande</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1) + (500 * 5) = 3 000 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1 350 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4 150 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9 800 UR/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 Ko</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29 000 UR/s</p></td>
        </tr>
    </tbody>
</table>

### <a name="use-the-request-unit-calculator"></a>Utiliser la calculatrice d’unités de demande
Pour aider les clients à affiner leurs estimations de débit, il existe une [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner) sur le web qui permet d’estimer les besoins d’unité de requête pour les opérations courantes, notamment :

* Créations (écritures) d’éléments
* Lectures d’éléments
* Suppressions d’éléments
* Mises à jour d’éléments

L’outil inclut également la prise en charge de l’estimation des besoins de stockage de données en fonction des exemples d’éléments que vous fournissez.

L’utilisation de l’outil est simple :

1. Chargez un ou plusieurs éléments représentatifs.
   
    ![Charger les éléments dans la calculatrice d’unités de requête][2]
2. Pour estimer les besoins de stockage de données, entrez le nombre total d’éléments que vous pensez stocker.
3. Entrez le nombre d’opérations de création, lecture, mise à jour et suppression d’éléments dont vous avez besoin (par seconde). Pour estimer les frais d’unités de requête des opérations de mise à jour d’éléments, chargez une copie de l’exemple d’élément de l’étape 1 comprenant des mises à jour de champs types.  Par exemple, si les mises à jour d’éléments comprennent généralement la modification de deux propriétés nommées lastLogi et userVisits, copiez l’exemple d’élément, mettez à jour les valeurs de ces deux propriétés, puis chargez l’élément copié.
   
    ![Entrez les exigences de débit dans la calculatrice d’unités de demande][3]
4. Cliquez sur Calculer et examinez les résultats.
   
    ![Résultats de la calculatrice d’unités de demande][4]

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, chargez un exemple de chaque *type* d’élément standard dans l’outil, puis calculez les résultats.
> 
> 

### <a name="use-the-azure-cosmos-db-request-charge-response-header"></a>Utiliser l’en-tête de réponse de frais de requête Azure Cosmos DB
Chaque réponse du service Azure Cosmos DB inclut un en-tête personnalisé (`x-ms-request-charge`) qui contient le nombre d’unités de requête consommées pour la requête. L’en-tête est également accessible via les kits SDK Azure Cosmos DB. Dans le Kit de développement logiciel (SDK) .NET, RequestCharge est une propriété de l’objet ResourceResponse.  Pour les requêtes, l’Explorateur de données Azure Cosmos DB dans le portail Azure fournit des informations sur les frais de requête pour les requêtes exécutées.

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un élément représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.  Veillez à mesurer et à inclure également les requêtes courantes et l’utilisation des scripts Azure Cosmos DB.

> [!NOTE]
> Si vous avez des types d’élément qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* d’élément standard.
> 
> 

Par exemple : 

1. Enregistrez les frais d’unités de requête de création (insertion) d’un élément standard. 
2. Enregistrez les frais d’unités de requête de lecture d’un élément standard.
3. Enregistrez les frais d’unités de requête de mise à jour d’un élément standard.
4. Enregistrez les frais d’unités de requête des requêtes d’élément standard courantes.
5. Enregistrer les frais d’unités de requête des scripts personnalisés (procédures stockées, déclencheurs, fonctions définies par l’utilisateur) utilisés par l’application.
6. Calculer les unités de requête nécessaires étant donné l’estimation du nombre d’exécutions d’opérations prévues chaque seconde.

## <a id="GetLastRequestStatistics"></a>Utiliser la commande GetLastRequestStatistics de l’API pour MongoDB
L’API MongoDB prend en charge une commande personnalisée, *getLastRequestStatistics*, pour récupérer les frais de requête des opérations spécifiées.

Par exemple, dans l’interpréteur de commandes Mongo, exécutez l’opération dont vous souhaitez vérifier les frais de demande.
```
> db.sample.find()
```

Ensuite, exécutez la commande *getLastRequestStatistics*.
```
> db.runCommand({getLastRequestStatistics: 1})
{
    "_t": "GetRequestStatisticsResponse",
    "ok": 1,
    "CommandName": "OP_QUERY",
    "RequestCharge": 2.48,
    "RequestDurationInMilliSeconds" : 4.0048
}
```

Ainsi, une méthode permettant d’estimer la quantité de débit réservé requis par votre application consiste à enregistrer les frais d’unité de requête associés à l’exécution des opérations courantes sur un élément représentatif utilisé par votre application, puis à évaluer le nombre d’opérations que vous prévoyez d’effectuer chaque seconde.

> [!NOTE]
> Si vous avez des types d’éléments qui varient considérablement en termes de taille et de nombre de propriétés indexées, enregistrez les frais d’unités de requête d’opérations applicables associés à chaque *type* d’élément standard.
> 
> 

## <a name="use-mongodb-api-portal-metrics"></a>Utiliser les mesures du portail de l’API MongoDB
La méthode la plus simple pour obtenir une estimation correcte des frais d’unité de requête pour votre base de données d’API MongoDB consiste à utiliser les mesures du [portail Azure](https://portal.azure.com). Grâce aux graphiques *Nombre de demandes* et *Frais de demande*, vous pouvez obtenir une estimation du nombre d’unités de requête consommées par chaque opération et par les opérations les unes par rapport aux autres.

![Mesures du portail de l’API MongoDB][6]

## <a name="a-request-unit-estimation-example"></a>Exemple d’estimation d’unités de requête
Prenez le document suivant d’environ 1 Ko :

```json
{
 "id": "08259",
  "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
  "tags": [
    {
      "name": "cereals ready-to-eat"
    },
    {
      "name": "kellogg"
    },
    {
      "name": "kellogg's crispix"
    }
  ],
  "version": 1,
  "commonName": "Includes USDA Commodity B855",
  "manufacturerName": "Kellogg, Co.",
  "isFromSurvey": false,
  "foodGroup": "Breakfast Cereals",
  "nutrients": [
    {
      "id": "262",
      "description": "Caffeine",
      "nutritionValue": 0,
      "units": "mg"
    },
    {
      "id": "307",
      "description": "Sodium, Na",
      "nutritionValue": 611,
      "units": "mg"
    },
    {
      "id": "309",
      "description": "Zinc, Zn",
      "nutritionValue": 5.2,
      "units": "mg"
    }
  ],
  "servings": [
    {
      "amount": 1,
      "description": "cup (1 NLEA serving)",
      "weightInGrams": 29
    }
  ]
}
```

> [!NOTE]
> Les documents étant minimisés dans Azure Cosmos DB, la taille du document ci-dessus calculée par le système est légèrement inférieure à 1 Ko.
> 
> 

Le tableau suivant montre les frais d’unités de requête approximatifs pour les opérations courantes sur cet élément (les frais d’unités de requête approximatifs partent du principe que le niveau de cohérence du compte a comme valeur « Session » et que tous les éléments sont indexés automatiquement) :

| Opération | Frais d’unités de requête |
| --- | --- |
| Créer un élément |~15 unités de requête |
| Lire un élément |~1 unité de requête |
| Interroger un élément par id |~2,5 unités de requête |

En outre, ce tableau montre les frais d’unités de requête approximatifs pour les requêtes courantes utilisées dans l’application :

| Requête | Frais d’unités de requête | Nombre d’éléments renvoyés |
| --- | --- | --- |
| Sélectionner un aliment par id |~2,5 unités de requête |1 |
| Sélectionner un aliment par fabricant |~7 unités de requête |7 |
| Sélectionner par groupe d’aliments et trier par poids |~70 unités de requête |100 |
| Sélectionner les 10 premiers aliments dans un groupe d’aliments |~10 unités de requête |10 |

> [!NOTE]
> Les frais de RU varient en fonction du nombre d’éléments renvoyés.
> 
> 

Avec ces informations, vous pouvez estimer les besoins en unités de requête pour cette application étant donné le nombre d’opérations et de requêtes attendues par seconde :

| Opération/Requête | Nombre estimé par seconde | Unités de requête nécessaires |
| --- | --- | --- |
| Créer un élément |10 |150 |
| Lire un élément |100 |100 |
| Sélectionner un aliment par fabricant |25 |175 |
| Sélectionner par groupe d’aliments |10 |700 |
| Sélectionner les 10 premiers |15 |150 Total |

Dans ce cas, vous estimez le besoin de débit moyen à 1 275 unités de requête par seconde.  En arrondissant à la centaine la plus proche, vous devez approvisionner 1 300 unités de requête par seconde pour la collection de cette application.

## <a id="RequestRateTooLarge"></a> Dépassement des limites de débit réservé dans Azure Cosmos DB
Souvenez-vous que la consommation d’unités de requête est évaluée en fonction d’un taux par seconde, si le budget est vide. Pour les applications qui dépassent le taux d’unités de requête configuré pour un conteneur, les requêtes pour cette collection sont limitées jusqu’à ce que le taux tombe sous le niveau réservé. En cas de limitation, le serveur met fin à la requête de manière préventive avec RequestRateTooLargeException (code d’état HTTP 429) et il retourne l’en-tête x-ms-retry-after-ms indiquant la durée, en millisecondes, pendant laquelle l’utilisateur doit attendre avant de réessayer.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Si vous utilisez des requêtes LINQ et le SDK .NET Client, la plupart du temps vous ne devez jamais traiter cette exception, car la version actuelle du SDK .NET Client intercepte implicitement cette réponse, respecte l’en-tête retry-after spécifié par le serveur, et réessaie d’effectuer la demande. La tentative suivante réussira toujours, sauf si plusieurs clients accèdent simultanément à votre compte.

Si vous avez plusieurs clients qui opèrent en même temps au-delà du taux de requêtes, le comportement par défaut peut ne pas suffire, et le client générera dans l’application une exception DocumentClientException avec le code d’état 429. Dans ce cas, vous pourriez traiter le comportement et la logique de nouvelles tentatives dans les routines de gestion d’erreurs de votre application ou accroître le débit réservé pour le conteneur.

## <a id="RequestRateTooLargeAPIforMongoDB"></a> Dépassement des limites de débit réservé dans l’API MongoDB
Les applications qui dépassent le nombre d’unités de requête configuré pour une collection seront limitées jusqu’à ce que le taux tombe sous le niveau réservé. En cas de limitation, le serveur principal interrompra la demande de manière préventive avec un code d’erreur *16500* indiquant un *trop grand nombre de demandes*. Par défaut, l’API MongoDB réessaie automatiquement jusqu’à 10 fois avant de renvoyer un code d’erreur indiquant un *trop grand nombre de demandes*. Si vous recevez de nombreux codes d’erreur indiquant un *trop grand nombre de demandes*, vous pouvez ajouter le comportement de nouvelles tentatives dans les routines de gestion d’erreurs de votre application ou [accroître le débit réservé pour la collection](set-throughput.md).

## <a name="next-steps"></a>étapes suivantes
Pour en savoir plus sur le débit réservé avec les bases de données Azure Cosmos DB, explorez ces ressources :

* [Tarification de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/)
* [Partitionnement, clés de partition et mise à l’échelle dans Azure Cosmos DB](partition-data.md)

Pour en savoir plus sur Azure Cosmos DB, consultez la [documentation](https://azure.microsoft.com/documentation/services/cosmos-db/) relative à Azure Cosmos DB. 

Pour commencer avec le test des performances et de la mise à l’échelle avec Azure Cosmos DB, consultez [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).

[2]: ./media/request-units/RUEstimatorUpload.png
[3]: ./media/request-units/RUEstimatorDocuments.png
[4]: ./media/request-units/RUEstimatorResults.png
[5]: ./media/request-units/RUCalculator2.png
[6]: ./media/request-units/api-for-mongodb-metrics.png
