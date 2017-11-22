---
title: "Comment configurer l’authentification et l’autorisation pour qu’une application personnalisée appelle l’API Azure Time Series Insights | Microsoft Docs"
description: "Cet article décrit comment configurer l’authentification et l’autorisation pour une application personnalisée qui appelle l’API Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 7772f6be0c60c1c2e59499d46e3c56975053e551
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentification et autorisation pour l’API Insights Azure Time Series

Cet article explique comment configurer l’authentification et l’autorisation utilisées dans une application personnalisée qui appelle l’API Azure Time Series Insights.

## <a name="service-principal"></a>Principal du service

Cette section explique comment configurer une application pour accéder à l’API Insights Azure Time Series pour le compte de l’application. L’application peut ensuite interroger des données ou publier des données de référence dans l’environnement Time Series Insights avec les informations d’identification de l’application au lieu de celles de l’utilisateur.

Lorsque vous avez une application qui doit accéder à Time Series Insights, vous devez configurer une application Azure Active Directory et affecter des stratégies d’accès aux données dans l’environnement Time Series Insights. Cette approche est préférable à l’exécution de l’application avec vos propres informations d’identification, car :

* Vous pouvez assigner à l’identité de l’application des autorisations différentes de vos propres autorisations. En règle générale, ces autorisations sont strictement limitées à ce que l’application doit faire. Par exemple, vous pouvez autoriser l’application à lire des données uniquement dans un environnement Time Series Insights particulier.
* Il est inutile de modifier les informations d’identification de l’application si vos responsabilités évoluent.
* Vous pouvez utiliser un certificat ou une clé d’application pour automatiser l’authentification lorsque vous exécutez un script sans assistance.

Cet article explique comment effectuer ces étapes via le portail Azure. Elle se concentre sur une application à client unique conçue pour s’exécuter au sein d’une seule organisation. Les applications à client unique sont généralement utilisées pour des applications métier exécutées au sein de votre organisation.

Le flux d’installation se compose de trois étapes principales :

1. Créer une application dans Azure Active Directory.
2. Autoriser cette application à accéder à l’environnement Time Series Insights.
3. Utiliser l’ID et la clé d’application pour acquérir un jeton pour l’audience ou ressource `"https://api.timeseries.azure.com/"`. Le jeton permet ensuite d’appeler l’API Insights Azure Time Series.

Voici les étapes détaillées :

1. Dans le portail Azure, sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

   ![Nouvelle inscription d’application dans Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Donnez un nom à l’application, sélectionnez le type **Application web ou API**, sélectionnez un URI valide pour l’**URL de connexion**, puis cliquez sur **Créer**.

   ![Créer l’application dans Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Sélectionnez votre application nouvellement créée, puis copiez son ID d’application dans votre éditeur de texte favori.

   ![Copier l’ID de l’application](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Sélectionnez **Clés**, entrez le nom de clé, sélectionnez la date d’expiration, puis cliquez sur **Enregistrer**.

   ![sélectionner des clés d’application](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Entrer le nom et la date d’expiration de la clé, puis cliquer sur Enregistrer](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Copiez la clé dans votre éditeur de texte.

   ![Copier la clé de l’application](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Pour l’environnement Time Series Insights, sélectionnez **Stratégies d’accès aux données**, puis cliquez sur **Ajouter**.

   ![Ajouter une nouvelle stratégie d’accès aux données à l’environnement Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. Dans la boîte de dialogue **Sélectionner un utilisateur**, collez le nom de l’application (voir l’étape 2) ou l’ID de l’application (voir l’étape 3).

   ![Rechercher une application dans la boîte de dialogue Sélectionner un utilisateur](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Sélectionnez le rôle (**Lecteur** pour interroger des données, **Contributeur** pour interroger des données et modifier des données de référence), puis cliquez sur **OK**.

   ![Choisir Lecteur ou Contributeur dans la boîte de dialogue Sélectionner un rôle](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Enregistrer la stratégie en cliquant sur **OK**.

10. Pour acquérir un jeton pour le compte de l’application, utilisez l’ID d’application (voir l’étape 3) et la clé de l’application (voir l’étape 5). Le jeton peut ensuite être passé dans l’en-tête `Authorization` lorsque l’application appelle l’API Insights Azure Time Series.

    Si vous utilisez C#, vous pouvez utiliser le code suivant pour acquérir le jeton pour le compte de l’application. Pour obtenir un exemple complet, voir [Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Utilisez l’ID et la clé de l’application dans votre application pour effectuer l’authentification auprès d’Azure Time Series Insights. 

## <a name="next-steps"></a>Étapes suivantes
- Pour un exemple de code qui appelle l’API Time Series Insights, voir [Interroger des données à l’aide de C#](time-series-insights-query-data-csharp.md).
- Pour obtenir des informations de référence sur l’API, consultez le [document de référence sur l’API de requête](/rest/api/time-series-insights/time-series-insights-reference-queryapi).
- [Créer un principal du service dans le portail Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md)
