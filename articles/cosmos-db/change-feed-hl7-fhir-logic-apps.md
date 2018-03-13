---
title: "Flux de modification pour les ressources HL7 FHIR - Azure Cosmos DB | Microsoft Docs"
description: "Découvrez comment définir les notifications de modification pour les dossiers médicaux HL7 FHIR à l’aide d’Azure Logic Apps, Azure Cosmos DB et Service Bus."
keywords: hl7 fhir
services: cosmos-db
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
ms.openlocfilehash: e5fa40dc128412e3b5b39c9fbdb751c797ca0cc7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-azure-cosmos-db"></a>Notification aux patients des modifications des dossiers médicaux HL7 FHIR à l’aide de Logic Apps et Azure Cosmos DB

Howard Edidin, récompensé dans le cadre du programme Azure MVP, a récemment été contacté par une structure qui souhaitait ajouter une nouvelle fonctionnalité à son portail patient. L’objectif était d’envoyer des notifications de mise à jour aux patients, le cas échéant, et de leur permettre de s’abonner à ces mises à jour. 

Cet article examine la solution de notification du flux de modification, mise en œuvre pour cet établissement de santé à l’aide d’Azure Cosmos DB, Logic Apps et Service Bus. 

## <a name="project-requirements"></a>Configuration requise du projet
- Les fournisseurs ont transmis les documents d’architecture documentaires cliniques consolidés (C-CDA) HL7 au format XML. Les documents C-CDA englobent tout type de contenus cliniques, tels que les antécédents familiaux et les dossiers d’immunisation, ainsi que les fichiers administratifs, de workflow et financiers. 
- Les documents C-CDA sont convertis en [ressources HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) au format JSON.
- Les documents de ressource FHIR modifiés sont transmis par voie électronique au format JSON.

## <a name="solution-workflow"></a>Workflow de la solution 

À un niveau élevé, le projet nécessitait les étapes suivantes de workflow : 
1. Conversion des documents C-CDA en ressources FHIR.
2. Exécution de l’interrogation de déclenchement récurrent pour les ressources FHIR modifiées. 
2. Appel d’une application personnalisée, FhirNotificationApi, afin d’établir la connexion à Azure Cosmos DB et de rechercher des documents nouveaux ou modifiés.
3. Enregistrez la réponse dans la file d’attente Service Bus.
4. Vérifiez l’absence de nouveaux messages dans la file d’attente Service Bus.
5. Envoi de notifications d’e-mail aux patients.

## <a name="solution-architecture"></a>Architecture de solution
Cette solution nécessite que 3 applications logiques respectent les conditions préalables ci-dessus et exécutent le workflow de la solution. Les 3 applications logiques sont les suivantes :
1. **Application de mappage HL7 FHIR** : reçoit le document HL7 C-CDA, le transforme en ressource FHIR et l’enregistre sur Azure Cosmos DB.
2. **Application des dossiers électroniques** : interroge le référentiel Azure Cosmos DB FHIR et enregistre la réponse dans la file d’attente Service Bus. Cette application logique utilise une [application API](#api-app) pour récupérer les documents nouveaux et modifiés.
3. **Application de notification du processus** : envoie une notification par courrier électronique avec les documents de ressource FHIR dans le corps.

![Les 3 applications logiques utilisées dans cette solution de soins de santé HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>Services Azure utilisés dans la solution

#### <a name="azure-cosmos-db-sql-api"></a>API SQL Azure Cosmos DB
Azure Cosmos DB est le référentiel dédié aux ressources FHIR, tel que représenté dans la figure suivante.

![Compte Azure Cosmos DB utilisé dans ce didacticiel de soins de santé HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/account.png)

#### <a name="logic-apps"></a>Logic Apps
Logic Apps gère le processus du workflow. Les captures d’écran suivantes représentent les applications logiques créées pour cette solution. 


1. **Application de mappage HL7 FHIR** : reçoit le document HL7 C-CDA et le transforme en ressource FHIR à l’aide de l’instance Enterprise Integration Pack (EIP) pour Logic Apps. EIP prend en charge le mappage du document C-CDA vers les ressources FHIR.

    ![L’application logique utilisée pour recevoir les dossiers médicaux HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-json-transform.png)


2. **Application des dossiers électroniques** : interroge le référentiel Azure Cosmos DB FHIR et enregistre la réponse dans la file d’attente Service Bus. Vous trouverez le code de l’application GetNewOrModifiedFHIRDocuments ci-dessous.

    ![Application logique utilisée pour interroger Azure Cosmos DB](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-api-app.png)

3. **Application de notification du processus** : envoie une notification par courrier électronique avec les documents de ressource FHIR dans le corps.

    ![L’application logique qui transmet au patient l’e-mail avec la ressource HL7 FHIR dans le corps](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Service Bus
La figure suivante représente la file d’attente des patients. La valeur de la propriété Tag est utilisée pour l’objet de l’e-mail.

![La file d’attente Service Bus utilisée dans le didacticiel HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>application API
Une application API se connecte à Azure Cosmos DB et recherche des documents FHIR nouveaux ou modifiés, par type de ressource. Cette application dispose d’un contrôleur, **FhirNotificationApi** avec une opération, **GetNewOrModifiedFhirDocuments**. Voir la [source de l’application API](#api-app-source).

Nous utilisons la classe [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) de l’API .NET SQL Azure Cosmos DB. Pour plus d’informations, consultez l’[article sur le flux de modification](change-feed.md). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Opération GetNewOrModifiedFhirDocuments

**Entrées**
- DatabaseId
- CollectionId
- Nom du type de ressource HL7 FHIR
- Valeur booléenne : Start from Beginning(démarrer à partir du début)
- Int : nombre de documents renvoyés

**Sorties**
- Réussite : code d’état 200, Réponse : liste des documents (tableau JSON)
- Échec : code d’état 404, Réponse : « No Documents found for ’*resource name’* Resource Type » (Aucun document trouvé pour le type de ressource)

<a id="api-app-source"></a>

**Source pour l’application API**

```csharp

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///     or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();  
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Test de l’élément FhirNotificationApi 

L’image suivante illustre la manière dont l’élément swagger a été utilisé pour tester l’élément [FhirNotificationApi](#api-app-source).

![Le fichier Swagger utilisé pour tester l’application API](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Tableau de bord du portail Azure

L’image suivante représente l’ensemble des services Azure dédiés à cette solution exécutée dans le portail Azure.

![Portail Azure représentant l’ensemble des services utilisés dans ce didacticiel HL7 FHIR](./media/change-feed-hl7-fhir-logic-apps/hl7-fhir-portal.png)


## <a name="summary"></a>Résumé

- Vous avez appris qu’Azure Cosmos DB dispose d’une prise en charge native des notifications relatives aux documents nouveaux ou modifiés, et vous vous êtes rendu compte de sa simplicité d’utilisation. 
- En vous appuyant sur Logic Apps, vous pouvez créer des workflows sans écrire aucun code.
- Vous valorisez les files d’attente Azure Service Bus pour gérer la distribution des documents HL7 FHIR.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Cosmos DB, consultez la [page d’accueil Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Pour plus d’informations sur Logic Apps, voir [Logic Apps](https://azure.microsoft.com/services/logic-apps/).


