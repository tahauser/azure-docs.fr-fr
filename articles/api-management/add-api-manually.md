---
title: "Ajouter une API manuellement à l’aide du portail Azure | Microsoft Docs"
description: Ce didacticiel vous montre comment utiliser le service Gestion des API (APIM) pour ajouter une API manuellement.
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
ms.openlocfilehash: 9426839f88daece1bb688a2079b7854ccaebdc57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="add-an-api-manually"></a>Ajouter une API manuellement 

Les étapes décrites dans cet article montrent comment utiliser le portail Azure pour ajouter une API manuellement à l’instance de Gestion des API (APIM). Un scénario courant lorsque vous souhaitez créer une API vide et la définir manuellement est lorsque vous souhaitez simuler l’API. Pour plus d’informations sur la simulation d’une API, consultez [Mock API responses](mock-api-responses.md) (Simuler des réponses d’API).

Si vous souhaitez importer une API existante, consultez la section [Rubriques connexes](#related-topics).

Dans cet article, nous créons une API vide et spécifions [httpbin.org](http://httpbin.org) (un service de test public) en tant qu’API de serveur principal.

## <a name="prerequisites"></a>Composants requis

Effectuez le démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Création d'une API

1. Sélectionnez **API** sous **Gestion des API**.
2. Dans le menu de gauche, cliquez sur **+ Ajouter l’API**.
3. Sélectionnez **API vide** dans la liste.

    ![API vide](media/add-api-manually/blank-api.png)
4. Entrez des paramètres pour l’API.

    ![Paramètres](media/add-api-manually/settings.png)

    |**Name**|**Valeur**|**Description**|
    |---|---|---|
    |**Nom complet**|« *API vide* » |Ce nom s’affiche dans le portail des développeurs.|
    |**URL du service Web** (facultatif)| « *http://httpbin.org* »| Si vous souhaitez simuler une API, vous pouvez ne rien entrer. <br/>Ici, nous entrons [http://httpbin.org](http://httpbin.org). Il s’agit d’un service de test public. <br/>Si vous souhaitez importer une API qui est mappée à un serveur principal automatiquement, consultez l’une des rubriques dans la section [Rubriques connexes](#related-topics).|
    |**Modèle d’URL**|« *HTTPS* »|Ici, même si le serveur principal a un accès HTTP non sécurisé, nous spécifions un accès APIM HTTPS sécurisé au serveur principal. <br/>Ce type de scénario (HTTPS vers HTTP) est appelé arrêt du protocole HTTPS. Vous pouvez le faire si votre API existe dans un réseau virtuel (dans lequel vous savez que l’accès est sécurisé même si HTTPS n’est pas utilisé). <br/>Vous souhaiterez peut-être utiliser « l’arrêt du protocole HTTPS » pour effectuer un enregistrement sur des cycles de processeur.|
    |**URL suffix** (Suffixe de l’URL)|« *hbin* »| Le suffixe est un nom qui identifie cette API spécifique dans cette instance APIM. Il doit être unique dans cette instance APIM.|
    |**Produits**|« *Illimité* » |Publiez l’API en l’associant à un produit. Si vous souhaitez que l’API soit publiée et mise à la disposition des développeurs, ajoutez-la à un produit. Vous pouvez effectuer cette opération durant la création de l’API ou ultérieurement.<br/><br/>Les produits sont des associations d’une ou de plusieurs API. Vous pouvez inclure un certain nombre d’API et les proposer aux développeurs par le biais du portail des développeurs. <br/>Les développeurs doivent s’abonner à un produit pour obtenir l’accès à l’API. Quand ils s’abonnent à un produit, ils obtiennent une clé d’abonnement qui est valable pour toutes les API dans ce produit. Si vous avez créé l’instance APIM, vous êtes abonné à chaque produit par défaut, car vous êtes déjà administrateur.<br/><br/> Par défaut, chaque instance Gestion des API est fournie avec deux exemples de produits : **Starter** et **Illimité**.| 
5. Sélectionnez **Créer**.

À ce stade, vous ne disposez d’aucune opération dans APIM qui correspond aux opérations dans votre API de serveur principal. Si vous appelez une opération qui est exposée via le serveur principal, mais pas via l’APIM, vous obtenez une erreur **404**. 

>[!NOTE] 
> Par défaut, lorsque vous ajoutez une API, même si elle est connectée à un service de serveur principal, APIM n’exposera pas d’opérations tant que vous ne les mettez pas sur liste verte. Pour mettre une opération de votre service de serveur principal sur liste verte, créez une opération APIM qui correspond à l’opération de serveur principal.
>

## <a name="add-and-test-an-operation"></a>Ajouter et tester une opération

Cette section montre comment ajouter une opération « /get » afin de la mapper à l’opération « http://httpbin.org/get » du serveur principal.

### <a name="add-the-operation"></a>Ajouter l’opération

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Cliquez sur **+ Ajouter une opération**.
3. Dans **URL**, sélectionnez **GET** et entrez « */get* » dans la ressource.
4. Entrez « *FetchData* » pour **Nom complet**.
5. Sélectionnez **Enregistrer**.

### <a name="test-the-operation"></a>Tester l’opération

Testez l’opération dans le portail Azure. Vous pouvez également la tester dans le **portail des développeurs**.

1. Sélectionnez l’onglet **Test**.
2. Sélectionnez **FetchData**.
3. Appuyez sur **Envoyer**.

La réponse que l’opération « http://httpbin.org/get » génère s’affiche. Si vous souhaitez transformer vos opérations, consultez [Transform and protect your API](transform-api.md) (Transformer et protéger votre API).

## <a name="add-and-test-a-parameterized-operation"></a>Ajouter et tester une opération paramétrable

Cette section montre comment ajouter une opération qui accepte un paramètre. Ici, nous mappons l’opération à « http://httpbin.org/status/200 ».

### <a name="add-the-operation"></a>Ajouter l’opération

1. Sélectionnez l’API que vous avez créée à l’étape précédente.
2. Cliquez sur **+ Ajouter une opération**.
3. Dans **URL**, sélectionnez **GET** et entrez « */status/{code}* » dans la ressource. Si vous le souhaitez, vous pouvez fournir des informations associées à ce paramètre. Par exemple, entrez « *Nombre* » pour **TYPE**, « *200* » (par défaut) pour **VALEURS**.
4. Entrez « GetStatus » pour **Nom complet**.
5. Sélectionnez **Enregistrer**.

### <a name="test-the-operation"></a>Tester l’opération 

Testez l’opération dans le portail Azure.  Vous pouvez également la tester dans le **portail des développeurs**.

1. Sélectionnez l’onglet **Test**.
2. Sélectionnez **GetStatus**. Par défaut, la valeur de code est définie sur « *200* ». Vous pouvez la modifier pour tester d’autres valeurs. Par exemple, tapez « *418* ».
3. Appuyez sur **Envoyer**.

    La réponse que l’opération « http://httpbin.org/status/200 » génère s’affiche. Si vous souhaitez transformer vos opérations, consultez [Transform and protect your API](transform-api.md) (Transformer et protéger votre API).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)
