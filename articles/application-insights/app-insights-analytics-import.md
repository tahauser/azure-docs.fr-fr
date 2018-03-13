---
title: "Importer vos données dans Analytics dans Azure Application Insights | Microsoft Docs"
description: "Importez des données statiques à associer à la télémétrie de l’application ou importez un flux de données distinct à interroger avec Analytics."
services: application-insights
keywords: "schéma ouvert, importation de données"
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 963e5cfd929f57b34dcb045df82b64f870e897e2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="import-data-into-analytics"></a>Importer des données dans Analytics

Importez des données tabulaires dans [Analytics](app-insights-analytics.md) pour les associer à la télémétrie [Application Insights](app-insights-overview.md) à partir de votre application ou pour les analyser comme un flux distinct. Analytics est un langage de requête puissant et bien adapté à l’analyse de gros volumes de flux horodatés de télémétrie.

Vous pouvez importer des données dans Analytics à l’aide de votre propre schéma. Il n’est pas nécessaire d’utiliser les schémas Application Insights standard tels qu’une requête ou une trace.

Vous pouvez importer des fichiers JSON ou DSV (valeurs séparées par des délimiteurs : virgule, point-virgule ou tabulation).

L’importation de données dans Analytics peut être utile dans les trois situations suivantes :

* **Association avec la télémétrie de l’application.** Par exemple, vous pouvez importer une table qui mappe des URL de votre site Web à des titres de page plus lisibles. Dans Analytics, vous pouvez créer un rapport graphique de tableau de bord qui affiche les dix pages les plus consultées de votre site Web. Maintenant, il peut afficher les titres des pages plutôt que les URL.
* **Mettre en corrélation la télémétrie de votre application** avec d’autres sources telles que le trafic réseau, les données de serveur ou les fichiers journaux CDN.
* **Appliquer Analytics à un flux de données distinct.** Application Insights Analytics est un outil puissant, qui fonctionne bien avec les flux horodatés partiellement alloués, et même beaucoup mieux que SQL dans la plupart des cas. Si vous avez un flux de ce type issu d’une autre source, vous pouvez l’analyser avec Analytics.

Il est facile d’envoyer des données à votre source de données. 

1. (Une fois) Définissez le schéma de vos données dans une « source de données ».
2. (Régulièrement) Chargez vos données dans le stockage Azure et appelez l’API REST pour nous informer que de nouvelles données sont en attente d’ingestion. Après quelques minutes, les données sont disponibles pour être interrogées dans Analytics.

Vous définissez la fréquence du chargement et la rapidité à laquelle vous souhaitez que vos données soient disponibles pour les requêtes. Nous vous recommandons de charger des données dans des segments plus grands, mais d’une taille inférieure à 1 Go.

> [!NOTE]
> *Vous avez un grand nombre de sources de données à analyser ?* [*Envisagez d’utiliser* logstash *pour envoyer vos données dans Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Avant de commencer

Ce dont vous avez besoin :

1. Une ressource Application Insights dans Microsoft Azure.

 * Si vous voulez analyser vos données séparément de la télémétrie, [créez une ressource Application Insights](app-insights-create-new-resource.md).
 * Si vous associez ou comparez vos données à la télémétrie d’une application qui est déjà configurée avec Application Insights, vous pouvez utiliser la ressource pour cette application.
 * Accès du propriétaire ou du contributeur à cette ressource.
 
2. stockage Azure. Vous chargez les données dans le stockage Azure et Analytics les récupère. 

 * Nous vous recommandons de créer un compte de stockage dédié pour vos blobs. Si vos blobs sont partagés avec d’autres processus, nos processus prendront plus de temps pour lire vos blobs.


## <a name="define-your-schema"></a>Définir votre schéma

Avant de pouvoir importer des données, vous devez définir une *source de données,* qui spécifie le schéma de vos données.
Vous pouvez avoir jusqu'à 50 sources de données dans une seule ressource Application Insights

1. Démarrez l’Assistant Source de données. Utilisez le bouton Ajouter une nouvelle source de données. Vous pouvez également cliquer sur le bouton Paramètres en haut à droite, puis choisir Sources de données dans le menu déroulant.

    ![Ajouter une source de données](./media/app-insights-analytics-import/add-new-data-source.png)

    Entrez un nom pour votre nouvelle source de données.

2. Définissez le format des fichiers que vous allez charger.

    Vous pouvez définir le format manuellement, ou télécharger un exemple de fichier.

    Si les données sont au format CSV, la première ligne de l’exemple peut être des en-têtes de colonne. Vous pouvez modifier le nom des champs à l’étape suivante.

    L’exemple doit inclure au moins 10 lignes ou enregistrements de données.

    Les noms de colonnes ou de champs doivent avoir des noms alphanumériques (sans espaces ni ponctuation).

    ![Charger un exemple de fichier](./media/app-insights-analytics-import/sample-data-file.png)


3. Examinez le schéma de l’Assistant. S’il a déduit les types à partir d’un exemple, vous devrez probablement modifier les types déduits des colonnes.

    ![Examiner le schéma déduit](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (Facultatif.) Téléchargez une définition de schéma. Consultez le format ci-dessous.

 * Sélectionnez un horodatage. Toutes les données dans Analytics doivent avoir un champ d’horodatage. Il doit être de type `datetime`, mais il ne doit pas forcément être nommé « Horodatage ». Si vos données comportent une colonne contenant une date et une heure au format ISO, choisissez celle-ci en tant que colonne d’horodatage. Sinon, choisissez « à mesure que les données sont arrivées », et le processus d’importation ajoutera un champ d’horodatage.

5. Créez la source de données.

### <a name="schema-definition-file-format"></a>Format de fichier de définition de schéma

Au lieu de modifier le schéma dans l’interface utilisateur, vous pouvez charger la définition de schéma à partir d’un fichier. Le format de définition de schéma est le suivant : 

Format délimité 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

Format JSON 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Chaque colonne est identifiée par l’emplacement, le nom et le type. 

* Emplacement : pour le format de fichier délimité, il s’agit de la position de la valeur mappée. Pour le format JSON, il s’agit du jpath de la clé mappée.
* Nom : nom affiché de la colonne.
* Type : type de données de cette colonne.
 
Si un exemple de données a été utilisé et que le format de fichier est délimité, la définition de schéma doit mapper toutes les colonnes et ajouter de nouvelles colonnes à la fin. 

JSON permet un mappage partiel des données, par conséquent la définition de schéma du format JSON n’a pas besoin de mapper toutes les clés trouvées dans un exemple de données. Il peut également mapper les colonnes qui ne font pas partie de l’exemple de données. 

## <a name="import-data"></a>Importer des données

Pour importer des données, chargez-les dans le stockage Azure, créez une clé d’accès qui leur est associée, puis appelez une API REST.

![Ajouter une source de données](./media/app-insights-analytics-import/analytics-upload-process.png)

Vous pouvez effectuer l’opération suivante manuellement ou configurer un système automatisé pour qu’il le fasse à intervalles réguliers. Vous devez suivre ces étapes pour chaque bloc de données à importer.

1. Chargez les données dans le [stockage Blob Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * La taille maximum des blobs est de 1 Go (sans compression). Les blobs d’une centaine de Mo sont parfaits, du point de vue des performances.
 * Vous pouvez les compresser avec Gzip pour améliorer le temps de chargement et la latence de disponibilité des données pour interrogation. Utilisez l’extension de nom de fichier `.gz`.
 * Il est préférable d’utiliser un compte de stockage distinct à cet effet, afin d’éviter que les appels provenant de différents services ralentissent les performances.
 * Lors de l’envoi de données à des fréquences élevées, toutes les quelques secondes, il est recommandé d’utiliser plusieurs comptes de stockage, pour des raisons de performances.

 
2. [Créez une clé de signature d’accès partagé pour le blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Le délai d’expiration de la clé doit être d’un jour et celle-ci doit fournir un accès en lecture.
3. Effectuez un appel REST pour indiquer à Application Insights que les données sont en attente.

 * Point de terminaison : `https://dc.services.visualstudio.com/v2/track`
 * Méthode HTTP : POST
 * Charge utile :

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Les espaces réservés sont :

* `Blob URI with Shared Access Key` : cet élément est obtenu à partir de la procédure de création d’une clé. Il est spécifique au blob.
* `Schema ID` : l’ID de schéma généré pour votre schéma défini. Les données de ce blob doivent respecter le schéma que vous avez défini pour cette source.
* `DateTime` : l’heure à laquelle la demande est soumise, UTC. Nous acceptons les formats suivants : ISO8601 (comme « 2016-01-01 13:45:01 ») ; RFC822 (« Mer, 14 déc 16 14:57:01 +0000 ») ; RFC850 (« Mercredi, 14-déc-16 14:57:00 UTC ») ; RFC1123 (« Mer, 14 déc 2016 14:57:00 +0000 »).
* `Instrumentation key` de votre ressource Application Insights.

Les données sont disponibles dans Analytics après quelques minutes.

## <a name="error-responses"></a>Réponses d’erreur

* **400 Demande incorrecte** : indique que la charge utile de la demande n’est pas valide. Vérifiez les points suivants :
 * Clé d’instrumentation correcte.
 * Valeur de temps valide. Il s’agit de l’heure actuelle au format UTC.
 * Le format JSON de l’événement est conforme au schéma.
* **403 Interdit** : le blob que vous avez envoyé n’est pas accessible. Assurez-vous que la clé d’accès partagé est valide et n’a pas expiré.
* **404 Introuvable** :
 * Le blob n’existe pas.
 * L’ID source est incorrect.

Des informations plus détaillées sont disponibles dans le message d’erreur de réponse.


## <a name="sample-code"></a>Exemple de code

Ce code utilise le package NuGet [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1).

### <a name="classes"></a>Classes

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Réception de données

Utilisez ce code pour chaque blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Étapes suivantes

* [Présentation du langage de requête Log Analytics](app-insights-analytics-tour.md)
* Si vous utilisez Logstash, utilisez le [plug-in Logstash pour envoyer des données à Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
