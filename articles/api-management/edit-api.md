---
title: Modifier une API avec le portail Azure | Microsoft Docs
description: Ce didacticiel vous montre comment utiliser le service Gestion des API (APIM) pour modifier une API.
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
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 362c36181da706e3fe0a27cc5ab262271c2a1e57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="edit-an-api"></a>Modifier une API

Les étapes de ce didacticiel vous montrent comment utiliser le service Gestion des API (APIM) pour modifier une API. 

+ Vous pouvez le faire en ajoutant, supprimant et renommant des opérations dans l’instance APIM. 
+ Vous pouvez modifier le Swagger de votre API.

## <a name="prerequisites"></a>Composants requis

+ [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
+ [Importer et publier votre première API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Modifier une API dans APIM

![Modifier une API](./media/edit-api/edit-api001.png)

1. Cliquez sur l’onglet **API**.
2. Sélectionnez l’une des API que vous avez importées précédemment.
3. Sélectionnez l’onglet **Conception**.
4. Sélectionnez une opération à modifier.
5. Pour renommer l’opération, sélectionnez un **crayon** dans la fenêtre **FrontEnd**.

## <a name="update-the-swagger"></a>Mettre à jour le Swagger

Vous pouvez mettre à jour votre API de service de serveur principal à partir du portail Azure en suivant ces étapes :

1. Sélectionnez **Toutes les opérations**.
2. Cliquez sur le crayon dans la fenêtre **FrontEnd**.

    ![Modifier une API](./media/edit-api/edit-api002.png)

    Le Swagger de votre API s’affiche.

    ![Modifier une API](./media/edit-api/edit-api003.png)

3. Mettez à jour le Swagger.
4. Appuyez sur **Enregistrer**.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [APIM policy samples](policy-samples.md) (Exemples de stratégie du service Gestion d’API)
> [Transform and protect your API](transform-api.md) (Transformer et protéger votre API)