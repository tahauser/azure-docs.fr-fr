---
title: "Utilisation d’un service web de gestion des modèles Azure Machine Learning | Microsoft Docs"
description: "Ce document décrit la procédure et les concepts relatifs à l’utilisation de services web déployés à l’aide de la gestion des modèles dans Azure Machine Learning."
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 64141afe421ace44fe71c04f8a2fba48144633c9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="consuming-web-services"></a>Utilisation de services web
Après avoir déployé un modèle en tant que service web en temps réel, vous pouvez lui envoyer des données et obtenir les prédictions de diverses plateformes et applications. Le service web en temps réel expose une API REST pour obtenir les prédictions. Vous pouvez envoyer des données au service web dans un format à une ou plusieurs lignes pour obtenir une ou plusieurs prédictions à la fois.

Avec le [service web Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy), une application externe communique de manière synchrone avec un modèle prédictif en adressant un appel HTTP POST à l’URL du service. Pour appeler un service web, l’application cliente doit spécifier la clé d’API qui est créée au moment où vous déployez une prédiction et placer les données de la demande dans le corps de la demande POST.

Notez que les clés d’API sont disponibles uniquement dans le mode de déploiement de cluster. Les services web locaux n’ont pas de clés.

## <a name="service-deployment-options"></a>Options de déploiement de service
Les services web Azure Machine Learning peuvent être déployés sur des clusters cloud pour des scénarios de production et de test et sur des stations de travail locales utilisant le moteur Docker. Dans les deux cas, la fonctionnalité du modèle prédictif reste la même. Le déploiement sur cluster offre une solution scalable et performante basée sur Azure Container Services, alors que le déploiement local peut être utilisé pour le débogage. 

L’interface CLI et l’API d’Azure Machine Learning proposent des commandes pratiques qui permettent de créer et de gérer des environnements Compute pour les déploiements de services à l’aide de l’option ```az ml env```. 

## <a name="list-deployed-services-and-images"></a>Afficher la liste des services et des images déployés
Vous pouvez afficher la liste des services et des images Docker actuellement déployés en utilisant la commande CLI ```az ml service list realtime -o table```. Notez que cette commande fonctionne toujours dans le contexte de l’environnement Compute actif et n’affiche pas les services déployés dans un environnement qui n’est pas défini comme étant actif. Pour définir l’environnement, utilisez ```az ml env set```. 

## <a name="get-service-information"></a>Obtenir des informations sur le service
Une fois que le service web a été déployé avec succès, servez-vous de la commande suivante pour obtenir l’URL du service et les autres détails pour appeler le point de terminaison du service. 

```
az ml service usage realtime -i <service name>
```

Cette commande imprime l’URL du service, les en-têtes de demande nécessaires, l’URL Swagger et les exemples de données en vue d’appeler le service si le schéma d’API du service a été fourni au moment du déploiement.

Vous pouvez tester le service directement à partir de l’interface CLI sans composer de requête HTTP, en entrant l’exemple de commande CLI avec les données d’entrée :

```
az ml service run realtime -i <service name> -d "Your input data"
```

## <a name="get-the-service-api-key"></a>Obtenir la clé d’API du service
Pour obtenir la clé du service web, utilisez la commande suivante :

```
az ml service keys realtime -i <web service id>
```
Au moment de créer la requête HTTP, utilisez la clé figurant dans l’en-tête d’autorisation : « Authorization » : « Bearer<key> »

## <a name="get-the-service-swagger-description"></a>Obtenir la description Swagger du service
Si le schéma d’API du service a été fourni, le point de terminaison du service expose un document Swagger à l’adresse ```http://<ip>/api/v1/service/<service name>/swagger.json```. Le document Swagger peut servir à générer automatiquement le client du service et à explorer les données d’entrée attendues et d’autres détails sur le service.

## <a name="get-service-logs"></a>Obtenir les journaux du service
Pour analyser le comportement du service et diagnostiquer les problèmes éventuels, il existe plusieurs façons de récupérer les journaux du service :
- La commande d’interface CLI ```az ml service logs realtime -i <service id>```. Cette commande fonctionne à la fois en mode cluster et en mode local.
- Si la journalisation du service a été activée au moment du déploiement, les journaux du service sont aussi envoyés à AppInsight. La commande CLI ```az ml service usage realtime -i <service id>``` affiche l’URL d’AppInsight. Notez que les journaux AppInsight peuvent être retardés de 2 à 5 minutes.
- Les journaux de cluster peuvent être consultés via la console Kubernetes qui est connectée quand vous définissez l’environnement de cluster actif avec ```az ml env set```.
- Les journaux Docker locaux sont disponibles via les journaux du moteur Docker quand le service s’exécute localement.

## <a name="call-the-service-using-c"></a>Appeler le service en utilisant C#
Utilisez l’URL du service pour envoyer une demande à partir d’une application console C#. 

1. Dans Visual Studio, créez une application console : 
    * Dans le menu, cliquez sur Fichier -> Nouveau -> Projet.
    * Sous Visual Studio C#, cliquez sur Windows Class Desktop, puis sélectionnez Application console.
2. Entrez _MyFirstService_ en guise de nom de projet, puis cliquez sur OK.
3. Dans Références du projet, définissez les références _System.Net_ et _System.Net.Http_.
4. Cliquez sur Outils -> Gestionnaire de package NuGet -> Console du Gestionnaire de package, puis installez le package Microsoft.AspNet.WebApi.Client.
5. Ouvrez le fichier Program.cs et remplacez le code par le code suivant :
6. Mettez à jour les paramètres _SERVICE_URL_ et _API_KEY_ avec les informations de votre service web.
7. Exécutez le projet.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MyFirstService
{
    class Program
    {
        // Web Service URL (update it with your service url)
        private const string SERVICE_URL = "http://<service ip address>:80/api/v1/service/<service name>/score";
        private const string API_KEY = "your service key";

        static void Main(string[] args)
        {
            Program.PostRequest();
            Console.ReadLine();
        }

        private static void PostRequest()
        {
            HttpClient client = new HttpClient();
            client.BaseAddress = new Uri(SERVICE_URL);
            //For local web service, comment out this line.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", API_KEY);

            var inputJson = new List<RequestPayload>();
            RequestPayload payload = new RequestPayload();
            List<InputDf> inputDf = new List<InputDf>();
            inputDf.Add(new InputDf()
            {
                feature1 = value1,
                feature2 = value2,
            });
            payload.Input_df_list = inputDf;
            inputJson.Add(payload);

            try
            {
                var request = new HttpRequestMessage(HttpMethod.Post, string.Empty);
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;

                Console.Write(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
        public class InputDf
        {
            public double feature1 { get; set; }
            public double feature2 { get; set; }
        }
        public class RequestPayload
        {
            [JsonProperty("input_df")]
            public List<InputDf> Input_df_list { get; set; }
        }
    }
}
```

## <a name="call-the-web-service-using-python"></a>Appeler le service web avec Python
Utilisez Python pour envoyer une demande à votre service web en temps réel. 

1. Copiez l’exemple de code suivant dans un nouveau fichier Python.
2. Mettez à jour les paramètres data, url et api_key. Pour les services web locaux, supprimez l’en-tête « Authorization ».
3. Exécutez le code. 

```python
import requests
import json

data = "{\"input_df\": [{\"feature1\": value1, \"feature2\": value2}]}"
body = str.encode(json.dumps(data))

url = 'http://<service ip address>:80/api/v1/service/<service name>/score'
api_key = 'your service key' 
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

resp = requests.post(url, data, headers=headers)
resp.text
```
