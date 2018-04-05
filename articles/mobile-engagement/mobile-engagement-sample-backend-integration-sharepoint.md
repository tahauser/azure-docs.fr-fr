---
title: Azure Mobile Engagement - Intégration au serveur principal
description: Connexion d’Azure Mobile Engagement avec un serveur principal SharePoint pour créer des campagnes à partir de SharePoint
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cf530ca6dce32050487f367aa80b10bf7f20b7e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---api-integration"></a>Azure Mobile Engagement - Intégration dans une API
> [!IMPORTANT]
> Azure Mobile Engagement est hors service depuis le 31/03/2018. Cette page sera supprimée prochainement.
> 

Dans un système marketing automatisé, la création et l'activation des campagnes marketing peuvent également être automatiques. À cette fin, Azure Mobile Engagement permet également de créer ces campagnes marketing automatisées à l'aide d’API. 

En général, les clients utilisent l'interface frontale de Mobile Engagement pour créer des annonces/sondages et autres dans le cadre de leurs campagnes marketing. Toutefois, à mesure que les campagnes marketing mûrissent, il est nécessaire d'exploiter les données enfermées dans les systèmes principaux (comme n'importe quel système de  CRM ou CMS comme SharePoint) afin qu'un pipeline entièrement automatisé puisse être créé pour générer des campagnes de façon dynamique dans Mobile Engagement en se basant sur les données contenues dans les systèmes principaux. 

![][5]

Ce didacticiel se base sur un scénario dans lequel un utilisateur SharePoint remplit une liste SharePoint de données marketing, dont les éléments sont récupérés par un processus automatisé qui se connecte au système Mobile Engagement à l'aide des API REST disponibles afin de créer une campagne marketing à partir des données SharePoint. 

> [!IMPORTANT]
> En général, vous pouvez utiliser cet exemple comme point de départ pour comprendre comment appeler les API REST Mobile Engagement, car il détaille les deux principaux aspects de l'appel des API : l’authentification et le passage des paramètres. 
> 
> 

## <a name="sharepoint-integration"></a>Intégration de SharePoint
1. L'exemple de liste SharePoint ressemble à ceci. **Titre**, **Catégorie**, **Titre de notification**, **Message** et **URL** sont utilisés pour créer l’annonce. Une colonne appelée **IsProcessed** est utilisée par l’exemple de processus d’automatisation sous la forme d’un programme de console. Vous pouvez exécuter ce programme de console comme un WebJob Azure de façon à le planifier. Vous pouvez également utiliser directement le flux de travail SharePoint pour programmer la création et l'activation de l'annonce lorsqu'un élément est inséré dans la liste SharePoint. Dans cet exemple, nous utilisons le programme de console qui parcourt les éléments de la liste SharePoint et crée une annonce dans Azure Mobile Engagement pour chacun d’eux avant de définir l’indicateur **IsProcessed** sur true lors de la réussite de la création de l’annonce.
   
    ![][1]
2. Nous utilisons le code de l'exemple *Authentification à distance dans SharePoint Online à l’aide du modèle objet client* [ici](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) pour s'authentifier avec la liste SharePoint.
3. Une fois authentifiés, nous parcourons les éléments de la liste pour trouver les éléments nouvellement créés (pour lesquels **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Intégration à Mobile Engagement
1. Lorsque nous trouvons un élément qui nécessite un traitement, nous extrayons les informations requises pour créer une annonce à partir de l'élément de liste et nous appelons `CreateAzMECampaign` pour le créer, puis `ActivateAzMECampaign` pour l'activer. Il s'agit essentiellement d’appels d'API REST vers le serveur principal de Mobile Engagement. 
2. Les API REST Mobile Engagement requièrent un **en-tête d’autorisation HTTP de schéma d’authentification de base** composé des éléments `ApplicationId` et `ApiKey` obtenus à partir du portail Azure. Assurez-vous d’utiliser la clé de la section **clés api** et *non* celle de la section **clés sdk**. 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Pour créer la campagne d’annonces, reportez-vous à la [documentation](https://msdn.microsoft.com/library/azure/mt683750.aspx). Vous devez vous assurer de spécifier la campagne `kind` comme *annonce* et [charge](https://msdn.microsoft.com/library/azure/mt683751.aspx) et de la passer comme FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. Une fois l'annonce créée, vous devriez voir apparaître une chose similaire sur le portail Mobile Engagement (notez que State=Draft et Activated = N/A).
   
    ![][3]
5. `CreateAzMECampaign` crée une campagne d’annonces et retourne son ID à l'appelant. `ActivateAzMECampaign` requiert cet ID comme paramètre pour activer la campagne. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. Une fois l'annonce activée, vous devriez voir apparaître une chose similaire sur le portail Mobile Engagement :
   
    ![][4]
7. Dès que la campagne est activée, tous les appareils répondant aux critères de la campagne reçoivent des notifications. 
8. Vous remarquerez également que l'élément de liste marqué avec IsProcessed = false reçoit la valeur True une fois la campagne d'annonce créée.  

Cet exemple permet de créer une campagne d’annonces simple en spécifiant principalement les propriétés obligatoires. Vous pouvez la personnaliser à votre guise à partir du portail en utilisant les informations indiquées [ici](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



