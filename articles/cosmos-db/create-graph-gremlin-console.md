---
title: "Didacticiel Azure Cosmos DB : Créer, interroger et parcourir la console Apache TinkerPops Gremlin | Microsoft Docs"
description: "Démarrage rapide d’Azure Cosmos DB pour créer des vertex, des bords et des requêtes à l’aide de l’API Graph Azure Cosmos DB."
services: cosmos-db
author: luisbosquez
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: terminal
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lbosq
ms.openlocfilehash: 2729ad97b49e7284022adae06c5b5f006647849c
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="azure-cosmos-db-create-query-and-traverse-a-graph-in-the-gremlin-console"></a>Azure Cosmos DB : Créer, interroger et parcourir la console Gremlin

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce guide de démarrage rapide explique comment créer, à l’aide du portail Azure, un compte Azure Cosmos DB, une base de données, ainsi qu’un graphique (conteneur) et comment utiliser ensuite la [console Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) à partir de [Apache TinkerPop](http://tinkerpop.apache.org) pour travailler avec les données API Graph. Dans ce didacticiel, vous créez et interrogez des vertex et des bords, mettez à jour une propriété de vertex, interrogez des vertex, parcourez le graphique et supprimez un vertex.

![Azure Cosmos DB à partir de la console Apache Gremlin](./media/create-graph-gremlin-console/gremlin-console.png)

La console Gremlin est basée sur Groovy/Java et s’exécute sous Linux, Mac et Windows. Vous pouvez la télécharger à partir du [site Apache TinkerPop](http://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>configuration requise

Vous devez posséder un abonnement Azure pour créer un compte Azure Cosmos DB pour ce démarrage rapide.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Vous devez également installer la [console Gremlin](http://tinkerpop.apache.org/). Utilisez la version 3.2.5 ou supérieure.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Ajout d’un graphique

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a id="ConnectAppService"></a>Connexion à votre App Service
1. Avant de démarrer la console Gremlin, créez ou modifiez le fichier config remote-secure.yaml dans le répertoire `apache-tinkerpop-gremlin-console-3.2.5/conf`.
2. Renseignez vos configurations *hôte*, *port*, *nom d’utilisateur*, *mot de passe*, *connectionPool* et *sérialiseur* :

    Paramètre|Valeur suggérée|Description
    ---|---|---
    hôtes|[***.gremlin.cosmosdb.azure.com] ou [***.graphs.azure.com] pour les comptes créés avant le 20 décembre 2017|Reportez-vous à la capture d’écran ci-dessous. Il s’agit de la valeur URI Gremlin sur la page Vue d’ensemble du portail Azure, entre crochets, avec la fin : 443/ supprimée.<br><br>Cette valeur peut également être récupérée à partir de l’onglet Clés, à l’aide de la valeur URI, en supprimant https://, en changeant documents par graphs ou gremlin.cosmosdb et en supprimant la fin : 443/.
    port|443|Définissez la valeur sur 443.
    username|*Votre nom d’utilisateur*|Ressource sous la forme `/dbs/<db>/colls/<coll>`, où `<db>` est le nom de votre base de données et `<coll>` le nom de votre collection.
    password|*Votre clé primaire*| Reportez-vous à la deuxième capture d’écran ci-dessous. Il s’agit de votre clé primaire, que vous pouvez récupérer à partir de la page Clés du portail Azure, dans la zone Clé primaire. Utilisez le bouton Copier sur le côté gauche de la zone pour copier la valeur.
    connectionPool|{enableSsl: true}|Votre paramètre de pool de connexions pour SSL.
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: {serializeResultToString: true }}|Définissez le paramètre sur cette valeur et supprimez tous les sauts de ligne `\n` quand vous collez la valeur.

    Pour la valeur hôtes, copiez la valeur **URI Gremlin** à partir de la page **Vue d’ensemble** : ![Afficher et copier la valeur URI Gremlin sur la page Vue d’ensemble dans le portail Azure](./media/create-graph-gremlin-console/gremlin-uri.png)

    Pour la valeur Mot de passe, copiez la **clé primaire** dans la page **Clés** : ![Afficher et copier votre clé primaire dans le portail Azure, la page Clés](./media/create-graph-gremlin-console/keys.png)

Votre fichier remote-secure.yaml doit ressembler à ceci :

```
hosts: [your_database_server.gremlin.cosmosdb.azure.com]
port: 443
username: /dbs/your_database_account/colls/your_collection
password: your_primary_key
connectionPool: {
  enableSsl: true
}
serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0, config: { serializeResultToString: true }}
```

3. Dans votre terminal, exécutez `bin/gremlin.bat` ou `bin/gremlin.sh` pour démarrer la [console Gremlin](http://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).
4. Dans votre terminal, exécutez `:remote connect tinkerpop.server conf/remote-secure.yaml` pour vous connecter à votre service d’application.

    > [!TIP]
    > Si vous recevez l’erreur `No appenders could be found for logger`, vérifiez que vous avez mis à jour la valeur de sérialiseur dans le fichier remote-secure.yaml comme décrit à l’étape 2. 

Parfait ! L’installation étant terminée, exécutons quelques commandes de console.

Essayons une simple commande count(). Lorsque l’invite de commandes s’affiche, saisissez ce qui suit dans la console :
```
:> g.V().count()
```

> [!TIP]
> Avez-vous remarqué le signe `:>` qui précède le texte `g.V().count()` ? 
>
> il indique la partie de la commande que vous devez saisir. Ce signe est important lorsque vous utilisez la console Gremlin avec Azure Cosmos DB.  
>
> Si vous n’indiquez pas ce préfixe `:>`, la console doit exécuter la commande en local, le plus souvent sur un graphique en mémoire.
> L’utilisation du signe `:>` indique à la console qu’elle doit exécuter une commande à distance, sur Cosmos DB (l’émulateur localhost ou une instance >Azure) dans le cas présent.


## <a name="create-vertices-and-edges"></a>Création de vertex et de bords

Commençons par ajouter des vertex pour cinq personnes : *Thomas*, *Mary Kay*, *Robin*, *Ben* et *Jack*.

Entrée (Thomas) :

```
:> g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1)
```

Sortie :

```
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```
Entrée (Mary Kay) :

```
:> g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2)

```

Sortie :

```
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Entrée (Robin) :

```
:> g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3)
```

Sortie :

```
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Entrée (Ben) :

```
:> g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4)

```

Sortie :

```
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Entrée (Jack) :

```
:> g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5)
```

Sortie :

```
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Ensuite, ajoutons des bords pour les relations entre ces personnes.

Entrée (Thomas -> Mary Kay) :

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Sortie :

```
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrée (Thomas -> Robin) :

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Sortie :

```
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Entrée (Robin -> Ben) :

```
:> g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Sortie :

```
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Mise à jour d’un vertex

Nous allons mettre à jour le vertex *Thomas* avec un nouvel âge : *45*.

Entrée :
```
:> g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Sortie :

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Interrogation de votre graphique

À présent, exécutons différentes requêtes sur votre graphique.

Tout d’abord, essayons une requête avec un filtre pour retourner uniquement les personnes de plus de 40 ans.

Entrée (requête de filtre) :

```
:> g.V().hasLabel('person').has('age', gt(40))
```

Sortie :

```
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Ensuite, projetons le premier nom parmi les personnes de plus de 40 ans.

Entrée (filtre + requête de projection) :

```
:> g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Sortie :

```
==>Thomas
```

## <a name="traverse-your-graph"></a>Traversée du graphique

Parcourons le graphique pour retourner tous les amis de Thomas.

Entrée (amis de Thomas) :

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Sortie : 

```
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Ensuite, nous allons obtenir la couche suivante de vertex. Parcourons le graphique pour retourner tous les amis des amis de Thomas.

Entrée (amis des amis de Thomas) :

```
:> g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Sortie :

```
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Suppression d’un vertex

Nous allons à présent supprimer un vertex de la base de données des graphiques.

Entrée (suppression du vertex de Jack) :

```
:> g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Effacement de tout le contenu de votre graphique

Enfin, nous allons supprimer tous les vertex et bords de la base de données.

Entrée :

```
:> g.E().drop()
:> g.V().drop()
```

Félicitations ! Vous avez terminé ce tutoriel Azure Cosmos DB : API Graph !

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante :  

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB, à créer un graphique à l’aide de l’Explorateur de données, à créer des vertex et des bords et à parcourir votre graphique à l’aide de la console Gremlin. Vous pouvez maintenant générer des requêtes plus complexes et implémenter une logique de traversée de graphique puissante, à l’aide de Gremlin. 

> [!div class="nextstepaction"]
> [Interroger à l’aide de Gremlin](tutorial-query-graph.md)
