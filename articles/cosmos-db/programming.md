---
title: "Programmation en JavaScript côté serveur pour Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment utiliser Azure Cosmos DB pour écrire des procédures stockées, des déclencheurs de base de données et des fonctions définies par l’utilisateur en JavaScript. Obtenez notamment des conseils en matière de programmation de base de données."
keywords: "Déclencheurs de base de données, procédure stockée, procédure stockée, programme de base de données, sproc, azure, Microsoft azure"
services: cosmos-db
documentationcenter: 
author: aliuy
manager: jhubbard
editor: mimig
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: andrl
ms.openlocfilehash: d8438d126c1f994e51871e80bb11610ec95b0814
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Programmation Azure Cosmos DB côté serveur : procédures stockées, déclencheurs de base de données et fonctions définies par l’utilisateur

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Découvrez comment l’exécution transactionnelle de JavaScript intégrée au langage d’Azure Cosmos DB permet aux développeurs d’écrire des **procédures stockées**, des **déclencheurs** et des **fonctions définies par l’utilisateur (FDU)** en mode natif dans une version [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) pour JavaScript. Vous pouvez ainsi écrire une logique d’application de programme de base de données qui peut être expédiée et exécutée directement dans les partitions de stockage de base de données. 

Nous vous recommandons de commencer par regarder la vidéo suivante, dans laquelle Andrew Liu présente brièvement le modèle de programmation de base de données côté serveur d’Azure Cosmos DB. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Demo-A-Quick-Intro-to-Azure-DocumentDBs-Server-Side-Javascript/player]
> 
> 

Ensuite, revenez à cet article dans lequel vous découvrirez les réponses aux questions suivantes :  

* Comment écrire une procédure stockée, un déclencheur ou une fonction définie par l'utilisateur à l'aide de JavaScript ?
* Comment Azure Cosmos DB offre-il une garantie ACID ?
* Comment les transactions fonctionnent-elles dans Azure Cosmos DB ?
* Qu'est-ce que les pré-déclencheurs et les post-déclencheurs, et comment procède-t-on pour leur écriture ?
* Comment enregistrer et exécuter une procédure stockée, un déclencheur ou une fonction définie par l'utilisateur sur la base de l'architecture REST avec HTTP ?
* Quels sont les kits SDK Azure Cosmos DB disponibles pour créer et exécuter des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur ?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduction à la programmation de procédures stockées et de fonctions définies par l’utilisateur
Cette approche du *« JavaScript en tant que langage T-SQL actualisé »* libère les développeurs d’applications des complexités liées aux incompatibilités de système de type et aux technologies de mappage de relationnel objet. Elle présente également une série d'avantages intrinsèques pouvant être utilisés pour créer des applications enrichies :  

* **Logique procédurale :** JavaScript en tant que langage de programmation de haut niveau offre une interface riche et familière permettant d’exprimer la logique métier. Vous pouvez effectuer des séquences d'opérations complexes plus proches des données.
* **Transactions atomiques :** Azure Cosmos DB garantit que les opérations de base de données effectuées dans un déclencheur ou une procédure stockée sont atomiques. Cela permet à une application de combiner des applications connexes en un seul lot de façon à ce que toutes réussissent ou qu’aucune ne réussisse. 
* **Performances :** le fait que JSON soit intrinsèquement mappé au système en langage Javascript et qu’il constitue l’unité de base du stockage dans Azure Cosmos DB permet une série d’optimisations telles que la matérialisation différée de documents JSON dans le pool de mémoires tampons et leur transmission au code en cours d’exécution. Il existe d'autres avantages en matière de performances en lien avec l'expédition de la logique métier à la base de données :
  
  * Traitement par lot - Les développeurs peuvent regrouper les opérations telles que les insertions et les envoyer en bloc. Le coût lié à la latence du trafic réseau et la surcharge en matière de stockage pour créer des transactions séparées sont considérablement réduits. 
  * Précompilation : Azure Cosmos DB précompile les procédures stockées, les déclencheurs et les fonctions définies par l’utilisateur pour éviter les frais de compilation JavaScript liés à chaque appel. La surcharge liée à la création du code d'octet pour la logique procédurale est amortie à une valeur minimale.
  * Séquencement - De nombreuses opérations requièrent un effet secondaire (« déclencheur ») qui implique potentiellement d'effectuer une ou plusieurs opérations de stockage secondaires. En dehors de l'atomicité, ceci est plus performant lors du déplacement vers le serveur. 
* **Encapsulation :** Les procédures stockées peuvent être utilisées pour regrouper la logique métier à un endroit. Ceci présente deux avantages :
  * Une couche d'abstraction est ajoutée aux données brutes, ce qui permet aux architectes de données de faire évoluer leurs applications indépendamment des données. Ceci est particulièrement avantageux lorsque les données ne présentent pas de schéma, en raison des hypothèses fragiles devant être intégrées à l'application si elles doivent gérer des données directement.  
  * Cette abstraction permet aux entreprises d'assurer la sécurité de leurs données en simplifiant l'accès à partir des scripts.  

La création et l’exécution de déclencheurs de base de données, de procédures stockées et d’opérateurs de requêtes personnalisés sont prises en charge par le biais du [portail Azure](https://portal.azure.com), de [l’API REST](/rest/api/documentdb/), [d’Azure Document DB Studio](https://github.com/mingaliu/DocumentDBStudio/releases) et de [SDK clients](sql-api-sdk-dotnet.md) sur de nombreuses plateformes, dont .NET, Node.js et JavaScript.

Ce didacticiel utilise le [kit SDK Node.js avec Q Promises](http://azure.github.io/azure-documentdb-node-q/) pour illustrer la syntaxe et l’utilisation des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur.   

## <a name="stored-procedures"></a>Procédures stockées
### <a name="example-write-a-simple-stored-procedure"></a>Exemple : Écriture d’une simple procédure stockée
Commençons par une simple procédure stockée qui renvoie une réponse « Hello World ».

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Les procédures stockées sont enregistrées par collection, et elles peuvent s'appliquer à tout document et toute pièce jointe figurant dans cette collection. L'extrait de code suivant indique comment enregistrer la procédure stockée helloWorld avec une collection. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Une fois que la procédure stockée est enregistrée, nous pouvons l'exécuter sur la base de la collection et renvoyer les résultats au client. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


L’objet context donne accès à toutes les opérations pouvant être effectuées dans le stockage Azure Cosmos DB, ainsi que l’accès aux objets request et response. En l'occurrence, nous avons utilisé l'objet response pour définir le corps de la réponse renvoyée au client. Pour plus d’informations, consultez la [documentation du kit SDK du serveur JavaScript Azure Cosmos DB](http://azure.github.io/azure-documentdb-js-server/).  

Extrapolons à partir de cet exemple et ajoutons à la procédure stockée d'autres fonctionnalités liées à la base de données. Les procédures stockées peuvent créer, mettre à jour, lire, interroger et supprimer des documents et des pièces jointes au sein de la collection.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exemple : Écriture d’une procédure stockée pour créer un document
L’extrait de code suivant indique comment utiliser l’objet context pour interagir avec les ressources Azure Cosmos DB.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Cette procédure stockée prend en entrée documentToCreate, le corps d'un document à créer dans la collection actuelle. Toutes ces opérations sont asynchrones et dépendent de rappels de fonction JavaScript. La fonction de rappel présente deux paramètres, un pour l'objet d'erreur en cas d'échec de l'opération et un pour l'objet créé. À l'intérieur du rappel, les utilisateurs peuvent gérer l'exception ou générer une erreur. Si aucun rappel n’est fourni et qu’une erreur se produit, le runtime d’Azure Cosmos DB génère une erreur.   

Dans l'exemple ci-dessous, la fonction de rappel génère une erreur si l'opération a échoué. Dans le cas contraire, elle définit l'ID du document créé en tant que corps de la réponse au client. Voici la façon dont cette procédure stockée est exécutée avec des paramètres d'entrée.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


Notez que cette procédure stockée peut être modifiée pour accepter en entrée un tableau de corps de document et pour les créer dans la même exécution de procédure stockée au lieu de plusieurs demandes du réseau visant à les créer chacune séparément. Ceci permet de mettre en œuvre une importation en bloc efficace pour Azure Cosmos DB (un point que nous aborderons plus tard dans ce didacticiel).   

L'exemple décrit ci-dessus a illustré la façon d'utiliser des procédures stockées. Nous verrons les déclencheurs et les fonctions définies par l'utilisateur plus loin dans ce didacticiel.

## <a name="database-program-transactions"></a>Transactions de programme de base de données
Une transaction dans une base de données classique peut être définie comme étant une séquence d'opérations effectuées en tant qu'unité de travail logique unique. Chaque transaction offre des **garanties ACID**. ACID est un acronyme bien connu qui est l’abréviation de quatre propriétés : Atomicité, Cohérence, Isolation et Durabilité.  

En bref, l'atomicité permet de s'assurer que tout le travail effectué au sein d'une transaction est traité en tant que simple unité validée dans son intégralité ou aucunement. La cohérence permet de s'assurer que les données sont toujours dans un état interne correct d'une transaction à l'autre. L'isolation, quant à elle, permet de garantir qu'aucune transaction n'interfère avec les autres. Généralement, la plupart des systèmes commerciaux fournissent plusieurs niveaux d'isolation pouvant être utilisés en fonction des besoins des applications. Enfin, la durabilité permet de s'assurer que toute modification validée dans la base de données sera toujours présente.   

Dans Azure Cosmos DB, JavaScript est hébergé dans le même espace mémoire que la base de données. Par conséquent, les demandes effectuées au sein de procédures stockées et de déclencheurs s'exécutent dans la même étendue qu'une session de base de données. Cela permet à Azure Cosmos DB de garantir ACID pour toutes les opérations qui font partie d’une procédure stockée ou d’un déclencheur. Envisageons la définition de procédure stockée suivante :

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Cette procédure stockée utilise des transactions au sein d'une application de jeu pour échanger des éléments entre deux joueurs en une seule opération. Elle essaie de lire deux documents, correspondant chacun aux ID de joueur transmis en tant qu'arguments. Si les deux documents de joueur sont trouvés, la procédure stockée les met à jour en intervertissant leurs éléments. Si des erreurs se produisent en chemin, elle génère une exception JavaScript qui annule implicitement la transaction.

Si la collection de la procédure stockée est enregistrée sur une collection à partition unique, la transaction est étendue à tous les documents au sein de la collection. Si la collection est partitionnée, les procédures stockées sont exécutées dans l’étendue de transaction d’une clé de partition unique. Chaque exécution de procédure stockée doit alors inclure une valeur de clé de partition correspondant à l’étendue sous laquelle la transaction doit être exécutée. Pour plus d’informations, consultez [Partitionnement dans Azure Cosmos DB](partition-data.md).

### <a name="commit-and-rollback"></a>Validation et restauration
Les transactions sont intégrées de façon approfondie et native dans le modèle de programmation JavaScript d’Azure Cosmos DB. Dans une fonction JavaScript, toutes les opérations sont automatiquement encapsulées dans une transaction unique. Si le code JavaScript s'exécute sans erreur, les opérations dans la base de données sont validées. En effet, les instructions BEGIN TRANSACTION et COMMIT TRANSACTION des bases de données relationnelles sont implicites dans Azure Cosmos DB.  

Si une exception est propagée à partir du script, le runtime JavaScript d’Azure Cosmos DB annule toute la transaction. Comme le montre l’exemple précédent, la génération d’une exception équivaut à une instruction ROLLBACK TRANSACTION dans Azure Cosmos DB.

### <a name="data-consistency"></a>Cohérence des données
Les procédures stockées et les déclencheurs sont toujours exécutés dans le réplica principal du conteneur Azure Cosmos DB. Cela permet de s'assurer que les lectures à partir des procédures stockées offrent une cohérence forte. Les requêtes utilisant des fonctions définies par l'utilisateur peuvent être exécutées dans le réplica principal ou n'importe quel réplica secondaire, mais nous veillons à répondre au niveau de cohérence demandé en choisissant le réplica approprié.

## <a name="bounded-execution"></a>Exécution limitée
Toutes les opérations Azure Cosmos DB doivent s’effectuer avant l’expiration de la demande spécifiée par le serveur. Cette contrainte s'applique également aux fonctions JavaScript (procédures stockées, déclencheurs et fonctions définies par l'utilisateur). Si une opération n'est pas terminée dans ce délai imparti, la transaction est annulée. Les fonctions JavaScript doivent s'exécuter dans ce délai ou mettre en œuvre un modèle basé sur la continuation pour traiter par lots/reprendre l'exécution.  

Afin de simplifier le développement de procédures stockées et de déclencheurs pour gérer les limites de temps, toutes les fonctions sous l'objet de collection (pour la création, la lecture, le remplacement et la suppression de documents et de pièces jointes) renvoient une valeur booléenne qui indique si l'opération arrivera à son terme. Si cette valeur est false, cela indique que la limite de temps est sur le point d'arriver à échéance et que la procédure doit clôturer l'exécution.  Les opérations mises en file d'attente avant la première opération de stockage non acceptée sont assurées de s'exécuter si la procédure stockée s'exécute à temps et ne place pas d'autres demandes dans la file d'attente.  

Les fonctions JavaScript sont également liées lors de la consommation de ressources. Azure Cosmos DB réserve le débit par collection en fonction de la taille configurée d’un compte de base de données. Le débit est exprimé en unités normalisées de processeur, de mémoire et de consommation d'E/S, appelées unités de demande. Les fonctions JavaScript peuvent potentiellement utiliser un nombre élevé d'unités de demande en peu de temps, et la limite de débit peut être restreinte si la limite de la collection est atteinte. Les procédures stockées gourmandes en ressources peuvent également être mises en quarantaine pour garantir la disponibilité des opérations de base de données primitives.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exemple : importation de données en bloc dans un programme de base de données
Ci-dessous se trouve un exemple de procédure stockée qui a été écrite pour importer des documents en bloc dans une collection. Notez la façon dont la procédure stockée gère l'exécution liée en vérifiant la valeur de retour booléenne à partir de createDocument, puis utilise le nombre de documents insérés dans chaque appel de la procédure stockée pour effectuer le suivi de la progression et la reprendre d'un lot à un autre.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a> Déclencheurs de base de données
### <a name="database-pre-triggers"></a>Pré-déclencheurs de base de données
Azure Cosmos DB fournit des déclencheurs qui sont exécutés ou déclenchés par une opération sur un document. Par exemple, vous pouvez spécifier un pré-déclencheur lorsque vous créez un document ; ce pré-déclencheur s'exécutera avant la création du document Voici un exemple de la façon dont les pré-déclencheurs peuvent être utilisés pour valider les propriétés d'un document en cours de création.

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Ainsi que le code d'enregistrement côté client Node.js correspondant pour le déclencheur :

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Les pré-déclencheurs ne peuvent pas avoir de paramètres en entrée. L'objet request peut être utilisé pour manipuler le message de demande associé à l'opération. Ici, le pré-déclencheur est exécuté avec la création d'un document et le corps du message de demande contient le document à créer au format JSON.   

Lorsque les déclencheurs sont enregistrés, les utilisateurs peuvent spécifier les opérations avec lesquelles il peut s'exécuter. Ce déclencheur a été créé avec TriggerOperation.Create, ce qui signifie que le code suivant n'est pas autorisé.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Post-déclencheurs de base de données
Les post-déclencheurs, comme les pré-déclencheurs, sont associés à une opération dans un document et n'acceptent pas de paramètres en entrée. Ils s'exécutent **après** la fin de l'opération et ils ont accès au message de réponse qui est envoyé au client.   

L'exemple suivant montre les post-déclencheurs en action :

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Le déclencheur peut être enregistré comme indiqué dans l'exemple suivant.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Ce déclencheur interroge le document de métadonnées et le met à jour avec des informations relatives au document qui vient d'être créé.  

Un élément important à noter est l’exécution **transactionnelle** des déclencheurs dans Azure Cosmos DB. Ce post-déclencheur s'exécute dans le cadre de la même transaction que la création du document initial. Par conséquent, si nous générons une exception à partir du post-déclencheur (supposons que nous ne soyons pas en mesure de mettre à jour le document de métadonnées), la transaction entière échoue et est annulée. Aucun document n'est créé et une exception est renvoyée.  

## <a id="udf"></a>Fonctions définies par l’utilisateur
Les fonctions définies par l’utilisateur permettent d’étendre la grammaire du langage de requête SQL dans Azure Cosmos DB et de mettre en œuvre une logique métier personnalisée. Elles peuvent uniquement être appelées à partir de requêtes. Elles n'ont pas accès à l'objet de contexte et sont destinées à être utilisées en tant que JavaScript en calcul seul. Par conséquent, elles peuvent être exécutées sur des réplicas secondaires du service Azure Cosmos DB.  

L'exemple suivant crée une fonction définie par l'utilisateur pour calculer les impôts sur la base des taux de différentes tranches de revenu, puis utilise celle-ci au sein d'une requête pour trouver toutes les personnes ayant payé des impôts supérieurs à 20 000 $.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


La fonction définie par l'utilisateur peut ensuite être utilisée dans des requêtes comme dans l'exemple suivant :

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>API de requête intégrée au langage JavaScript
En plus de l’émission de requêtes à l’aide de la grammaire SQL d’Azure Cosmos DB, le SDK côté serveur vous permet d’effectuer des requêtes optimisées à l’aide d’une interface JavaScript fluide sans aucune connaissance de SQL. L’API de requête JavaScript permet de créer des requêtes par programme en transmettant des fonctions de prédicat dans des appels de fonction chaînables, avec une syntaxe connue des types prédéfinis de Array ECMAScript5 et des bibliothèques JavaScript courantes, telles que lodash. Les requêtes sont analysées par le runtime JavaScript pour être exécutées efficacement à l’aide d’index Azure Cosmos DB.

> [!NOTE]
> `__` (trait de soulignement double) est un alias pour `getContext().getCollection()`.
> <br/>
> En d’autres termes, vous pouvez utiliser `__` ou `getContext().getCollection()` pour accéder à l’API de requête JavaScript.
> 
> 

Les fonctions prises en charge sont les suivantes :

<ul>
<li>
<b>chain() ... .value([callback] [, options])</b>
<ul>
<li>
Commence un appel chaîné qui doit se terminer par value().
</li>
</ul>
</li>
<li>
<b>filter(predicateFunction [, options] [, callback])</b>
<ul>
<li>
Filtre l’entrée à l’aide d’une fonction de prédicat qui renvoie true/false afin de filtrer les documents d’entrée dans le jeu résultant. Ce comportement est semblable à celui d’une clause WHERE dans SQL.
</li>
</ul>
</li>
<li>
<b>map(transformationFunction [, options] [, callback])</b>
<ul>
<li>
Applique une projection à partir d’une fonction de transformation qui mappe chaque élément d’entrée à une valeur ou un objet JavaScript. Ce comportement est semblable à celui d’une clause SELECT dans SQL.
</li>
</ul>
</li>
<li>
<b>pluck([propertyName] [, options] [, callback])</b>
<ul>
<li>
Ceci est un raccourci pour un mappage qui extrait la valeur d’une propriété unique de chaque élément d’entrée.
</li>
</ul>
</li>
<li>
<b>flatten([isShallow] [, options] [, callback])</b>
<ul>
<li>
Combine et aplatit les tableaux à partir de chaque élément d’entrée en un seul tableau. Ce comportement est semblable à SelectMany dans LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy([predicate] [, options] [, callback])</b>
<ul>
<li>
Produit un nouvel ensemble de documents en les triant dans le flux du document d’entrée dans l’ordre croissant à l’aide du prédicat donné. Ce comportement est semblable à celui d’une clause ORDER BY dans SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending([predicate] [, options] [, callback])</b>
<ul>
<li>
Produit un nouvel ensemble de documents en les triant dans le flux du document d’entrée dans l’ordre décroissant à l’aide du prédicat donné. Ce comportement est semblable à celui d’une clause ORDER BY x DESC dans SQL.
</li>
</ul>
</li>
</ul>


Quand elles sont incluses dans les fonctions de prédicat et/ou de sélecteur, les constructions JavaScript suivantes sont automatiquement optimisées pour s’exécuter directement sur les index Azure Cosmos DB :

* Opérateurs simples : = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Littéraux, y compris le littéral d’objet : {}
* var, return

Les constructions JavaScript suivantes ne sont pas optimisées pour les index Azure Cosmos DB :

* Flux de contrôle (par exemple, if, for, while)
* Appels de fonction

Pour plus d’informations, voir [JSDocs côté serveur](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exemple : Écrire une procédure stockée à l’aide de l’API de requête JavaScript
L’exemple de code suivant illustre comment l’API de requête JavaScript peut être utilisée dans le contexte d’une procédure stockée. La procédure stockée insère un document donné par un paramètre d’entrée et met à jour les métadonnées de document, à l’aide de la méthode `__.filter()` avec minSize, maxSize et totalSize basées sur la propriété de taille du document d’entrée.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>Aide-mémoire SQL vers Javascript
Le tableau suivant présente différentes requêtes SQL et les requêtes JavaScript correspondantes.

Comme pour les requêtes SQL, les clés de propriété de document (par exemple, `doc.id`) respectent la casse.

|SQL| API de requête JavaScript|Description ci-dessous|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|1|
|SELECT docs.id, docs.message AS msg, docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|SELECT *<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|3|
|SELECT *<br>FROM docs<br>WHERE ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags &amp;&amp; x.Tags.indexOf(123) &gt; -1;<br>});|4|
|SELECT docs.id, docs.message AS msg<br>FROM docs<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5.|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;return doc.Tags &amp;&amp; Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6.|

Les descriptions suivantes expliquent chaque requête du tableau ci-dessus.
1. Renvoie tous les documents (paginés avec jeton de continuation) tels quels.
2. Projette l’ID, le message (alias msg) et l’action de tous les documents.
3. Requêtes pour les documents avec le prédicat : id = "X998_Y998".
4. Requêtes pour les documents comportant une propriété Tags, et Tags est un tableau contenant la valeur 123.
5. Interroge les documents avec un prédicat, id = "X998_Y998", puis projette l’ID et le message (alias msg).
6. Filtre les documents comportant une propriété de tableau, Tags, trie les documents résultants par la propriété système _ts timestamp, puis projette et aplatit le tableau Tags.


## <a name="runtime-support"></a>Prise en charge du runtime
[L’API côté serveur JavaScript](http://azure.github.io/azure-documentdb-js-server/) Azure Cosmos DB offre la prise en charge de la plupart des fonctionnalités de langage JavaScript répondant à la norme [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Sécurité
Les déclencheurs et les procédures stockées JavaScript sont exécutés dans le bac à sable (sandbox) de façon à ce que les effets d'un script ne soient divulgués à un autre sans passer par l'isolement de transaction de capture instantanée au niveau de la base de données. Les environnements d'exécution sont regroupés mais leur contexte est nettoyé après chaque exécution. Par conséquent, ils sont assurés d'être préservés de tout effet secondaire inattendu les uns des autres.

### <a name="pre-compilation"></a>Précompilation
Les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur sont précompilés de façon implicite en format de code d'octet afin d'éviter les frais de compilation à chaque appel de script. Cela permet de s'assurer que les appels de procédures stockées sont rapides et présentent un encombrement réduit.

## <a name="client-sdk-support"></a>Prise en charge du kit SDK client
En plus de l’API [Node.js](sql-api-sdk-node.md) Azure Cosmos DB, Azure Cosmos DB propose [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](http://azure.github.io/azure-documentdb-js/) et les [SDK Python](sql-api-sdk-python.md) pour l’API SQL. Les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur peuvent être créés et exécutés au moyen de l'un de ces kits SDK également. Voici un exemple de la façon de créer et d'exécuter une procédure stockée au moyen du client .NET. Notez la façon dont les types .NET sont transmis dans la procédure stockée au format JSON et lus.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);


Cet exemple illustre l’utilisation de [l’API .NET SQL .](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) pour créer un prédéclencheur et un document dans lequel le déclencheur est activé. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Enfin, l’exemple suivant illustre la création d’une fonction définie par l’utilisateur et son utilisation dans une [requête SQL](sql-api-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>API REST
Toutes les opérations Azure Cosmos DB peuvent être effectuées sur la base de l’architecture REST. Les procédures stockées, les déclencheurs et les fonctions définies par l'utilisateur peuvent être enregistrés dans une collection au moyen de HTTP POST. Voici un exemple de la façon d'enregistrer une procédure stockée :

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


La procédure stockée est enregistrée en exécutant une requête POST sur la base de l’URI dbs/testdb/colls/testColl/sprocs avec le corps contenant la procédure stockée à créer. Les déclencheurs et les fonctions définies par l'utilisateur peuvent être inscrits de la même façon en émettant une demande POST sur /triggers et /udfs respectivement.
Cette procédure stockée peut ensuite être exécutée en émettant une demande POST sur son lien de ressource :

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Ici, la valeur entrée pour la procédure stockée est transmise dans le corps de la requête. Notez que la valeur entrée est transmise en tant que tableau JSON de paramètres d'entrée. La procédure stockée prend la première entrée en tant que document correspondant à un corps de réponse. La réponse reçue se présente comme suit :

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Contrairement aux procédures stockées, les déclencheurs ne peuvent pas être exécutés directement. À la place, ils sont exécutés au sein d'une opération dans un document. Nous pouvons spécifier les déclencheurs à exécuter avec une demande au moyen d'en-têtes HTTP. Voici une demande de création de document.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Ici, le pré-déclencheur devant s'exécuter avec la demande est spécifié dans l'en-tête x-ms-documentdb-pre-trigger-include. De même, tous les post-déclencheurs sont fournis dans l'en-tête x-ms-documentdb-post-trigger-include. Notez que les pré- et post-déclencheurs peuvent tous deux être spécifiés pour une demande donnée.

## <a name="sample-code"></a>Exemple de code
Vous trouverez d’autres exemples de code côté serveur (notamment [bulk-delete](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js) et [update](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) dans notre [référentiel GitHub](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Vous souhaitez partager votre remarquable procédure stockée ? Envoyez-nous une requête d’extraction ! 

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur, vous pouvez les charger et les afficher dans le portail Azure à l’aide de l’Explorateur de données.

Pour en savoir plus sur la programmation Azure Cosmos DB côté serveur, vous pouvez également trouver utiles les références et les ressources suivantes :

* [Kits SDK Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Studio DocumentDB](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Extensibilité de la base de données sécurisée et portable](http://dl.acm.org/citation.cfm?id=276339) 
* [Architecture de base de données orientée services](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Hébergement du Runtime .NET dans Microsoft SQL Server](http://dl.acm.org/citation.cfm?id=1007669)

