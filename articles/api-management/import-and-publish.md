---
title: "Importer et publier votre première API dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment importer et publier votre première API avec la Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: ffe5ee95c66eee7dccd25a1afd2fe639cbc273f5
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="import-and-publish-your-first-api"></a>Importer et publier votre première API 

Ce didacticiel explique comment importer une API de serveur principal de « spécification OpenAPI » se trouvant à l’adresse http://conferenceapi.azurewebsites.net?format=json. Cette API de serveur principal est fournie par Microsoft et hébergée sur Azure. 

Une fois que l’API de serveur principal est importée dans la Gestion des API (APIM), l’API APIM devient une façade pour l’API de serveur principal. Au moment où vous importez l’API de serveur principal, la source d’API et l’API APIM sont identiques. APIM vous permet de personnaliser la façade en fonction de vos besoins sans toucher l’API de serveur principal. Pour plus d’informations, consultez [Transformer et protéger votre API](transform-api.md). 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Importer votre première API
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

![Nouvelle API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>configuration requise

Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API de serveur principal

Cette section montre comment importer et publier une API de serveur principal à la spécification OpenAPI.
 
1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **Spécification OpenAPI** dans la liste.

    ![Création d'une API](./media/api-management-get-started/create-api.png)

    Vous pouvez définir les valeurs de l’API lors de la création ou ultérieurement en accédant à l’onglet **Paramètres**.  

    |Paramètre|Valeur|Description|
    |---|---|---|
    |**Spécification OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Indique le service implémentant l’API. La gestion des API transmet les demandes à cette adresse.|
    |**Nom complet**|*API de conférence de démonstration*|Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json. <br/>Ce nom s’affiche dans le portail des développeurs.|
    |**Name**|*demo-conference-api*|Fournit un nom unique pour l’API. <br/>Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json.|
    |**Description**|Fournit une description facultative de l’API.|Si vous appuyez sur la touche de tabulation après avoir entré l’URL du service, APIM renseigne ce champ en fonction de ce qui se trouve dans le fichier json.|
    |**Suffixe de l’URL de l’API**|*conference*|Le suffixe est ajouté à l’URL de base du service Gestion des API. Gestion des API distingue les API selon leur suffixe. Celui-ci doit donc être unique pour chaque API d'un éditeur donné.|
    |**Modèle d’URL**|*HTTPS*|Détermine les protocoles qui peuvent être utilisés pour accéder à l’API. |
    |**Produits**|*Illimité*| Publiez l’API en l’associant à un produit. Pour éventuellement ajouter cette nouvelle API à un produit, tapez le nom du produit. Cette étape peut être répétée plusieurs fois pour ajouter l'API à plusieurs produits.<br/>Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.<br/> Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits : **Starter** et **Illimité**. |
3. Sélectionnez **Créer**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Tester la nouvelle API APIM dans le portail Azure

Les opérations peuvent être directement appelées depuis le portail Azure, qui permet d’afficher et de tester les opérations d’une API.  
1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.  ![Tester l’API](./media/api-management-get-started/test-api.png)
3. Cliquez sur **GetSpeakers**.
    La page affiche les champs des paramètres de requête, mais ici, nous n’en avons pas. La page affiche également des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. La clé est renseignée automatiquement.
4. Appuyez sur **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

## <a name="call-operation"></a>Appel d’une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations depuis le **portail des développeurs** pour tester les API. 

1. Sélectionnez l’API que vous avez créée à l’étape « Importer et publier une API de serveur principal ».
2. Appuyez sur **Portail des développeurs**.

    ![Test dans le portail des développeurs](./media/api-management-get-started/developer-portal.png)

    Le site « Portail des développeurs » s’ouvre.
3. Sélectionnez **l’API**.
4. Sélectionnez **API de conférence de démonstration**.
5. Cliquez sur **GetSpeakers**.
    
    La page affiche les champs des paramètres de requête, mais ici, nous n’en avons pas. La page affiche également des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance APIM, la clé est renseignée automatiquement, car vous êtes déjà administrateur.
6. Appuyez sur **Essayer**.
7. Appuyez sur **Envoyer**.
    
    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Importer votre première API
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Créer et publier un produit](api-management-howto-add-products.md)
