---
title: "Importer une application logique en tant qu’API avec le portail Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment utiliser le service Gestion des API (APIM) pour importer une application logique en tant qu’API."
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
ms.openlocfilehash: 96ac8ce81087717f05ae6480a8f875079139b7b6
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="import-a-logic-app-as-an-api"></a>Importer une application logique en tant qu’API

Cet article explique comment importer une application logique en tant qu’API. L’article explique également comment tester l’API APIM.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Importer une application logique en tant qu’API
> * Tester l’API dans le portail Azure
> * Tester l’API dans le portail des développeurs

## <a name="prerequisites"></a>Prérequis

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Assurez-vous que votre abonnement contient une application logique. Pour plus d’informations, consultez [Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importer et publier une API backend

1. Sélectionnez **API** sous **Gestion des API**.
2. Sélectionnez **Application logique** dans la liste **Ajouter une nouvelle API**.

    ![Application logique](./media/import-logic-app-as-api/logic-app-api.png)
3. Appuyez sur **Parcourir** pour afficher la liste des applications logiques de votre abonnement.
4. Sélectionnez l’application. APIM recherche le swagger associé à l’application sélectionnée, l’extrait et l’importe. 
5. Ajoutez un suffixe d’URL d’API. Le suffixe est un nom qui identifie cette API spécifique dans cette instance APIM. Il doit être unique dans cette instance APIM.
6. Publiez l’API en l’associant à un produit. Dans ce cas, le produit « *Illimité* » est utilisé.  Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez effectuer cette opération durant la création de l’API ou ultérieurement.

    Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs dans le portail des développeurs. Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API de ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.

    Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits :

    * **Starter**
    * **Illimité**   
7. Sélectionnez **Créer**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Tester la nouvelle API APIM dans le portail Azure

Les opérations peuvent être directement appelées depuis le portail Azure, qui permet d’afficher et de tester les opérations d’une API.  

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Appuyez sur l’onglet **Test**.
3. Sélectionnez une opération.

    La page affiche des champs pour les paramètres de requête et des champs pour les en-têtes. L’un des en-têtes est « Ocp-Apim-Subscription-Key », pour la clé d’abonnement du produit qui est associé à cette API. Si vous avez créé l’instance APIM, la clé est renseignée automatiquement, car vous êtes déjà administrateur. 
1. Appuyez sur **Envoyer**.

    Le serveur principal répond avec **200 OK** et certaines données.

## <a name="call-operation"></a>Appel d’une opération à partir du portail des développeurs

Vous pouvez également appeler des opérations depuis le **portail des développeurs** pour tester les API. 

1. Sélectionnez l’API que vous avez créée à l’étape « Importer et publier une API de serveur principal ».
2. Appuyez sur **Portail des développeurs**.

    Le site « Portail des développeurs » s’ouvre.
3. Sélectionnez l’**API** que vous avez créée.
4. Cliquez sur l’opération que vous souhaitez tester.
5. Appuyez sur **Essayer**.
6. Appuyez sur **Envoyer**.
    
    Après l’appel d’une opération, le portail des développeurs affiche le **statut de réponse**, les **en-têtes de réponse**, et tout **contenu de la réponse**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
> Chaque application logique a une opération **manual-invoke**. Si vous souhaitez que votre API soit composée de plusieurs applications logiques afin d’éviter les conflits, vous devez renommer la fonction.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)