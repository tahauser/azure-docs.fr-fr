---
title: "Importer une API SOAP à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment importer l’API SOAP avec Gestion des API."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 0a013aa63e8b04748e1b64126795189a5d189fa1
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="import-soap-api"></a>Importer une API SOAP

Cet article explique comment importer une représentation XML standard d’une API SOAP. L’article explique également comment tester l’API APIM.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une API SOAP
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Composants requis

Effectuez le démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API de serveur principal

1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **WSDL** dans la liste **Ajouter une nouvelle API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Dans **Spécification WSDL**, entrez l’URL correspondant à votre API SOAP.
4. La case d’option **Requête SOAP directe** est sélectionnée par défaut. Avec cette sélection, l’API va être exposée en tant que SOAP. Le consommateur doit utiliser des règles SOAP. Si vous souhaitez « convertir l’API pour REST », suivez les étapes de [Import a SOAP API and convert to REST](restify-soap-api.md) (Importer une API SOAP et la convertir pour REST).

    ![Requête directe](./media/import-soap-api/pass-through.png)
5. Appuyez sur la touche de tabulation.

    Les champs suivants sont automatiquement remplis avec les informations de l’API SOAP : Nom complet, Nom, Description.
6. Ajoutez un suffixe d’URL d’API. Le suffixe est un nom qui identifie cette API spécifique dans cette instance APIM. Il doit être unique dans cette instance APIM.
9. Publiez l’API en l’associant à un produit. Dans ce cas, le produit « *Illimité* » est utilisé.  Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez effectuer cette opération durant la création de l’API ou ultérieurement.

    Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs par le biais du portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API dans ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.

    Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

    * **Starter**
    * **Illimité**   
10. Sélectionnez **Créer**.

### <a name="test-the-new-apim-api-in-the-administrative-portal"></a>Tester la nouvelle API APIM dans le portail d’administration

Les opérations peuvent être directement appelées depuis le portail d’administration, qui permet d’afficher et de tester les opérations d’une API.  

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.
3. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance APIM, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 
1. Appuyez sur **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

### <a name="call-operation"></a>Appel d’une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations depuis le **portail des développeurs** pour tester les API. 

1. Sélectionnez l’API que vous avez créée à l’étape « Importer et publier une API de serveur principal ».
2. Appuyez sur **Portail des développeurs**.

    Le site « Portail des développeurs » s’ouvre.
3. Sélectionnez **l’API** que vous avez créée.
4. Cliquez sur l’opération que vous souhaitez tester.
5. Appuyez sur **Essayer**.
6. Appuyez sur **Envoyer**.
    
    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)