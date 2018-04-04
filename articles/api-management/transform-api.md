---
title: Transformer et protéger votre API avec Gestion des API Azure | Microsoft Docs
description: Découvrez comment protéger votre API avec les quotas et les stratégies (limite de débit) de limitation.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: fb56b8489b086b724df9f3c9179f2c3265cd05a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="transform-and-protect-your-api"></a>Transformer et protéger votre API 

Le didacticiel vous montre comment transformer votre API de manière à ce qu’elle ne divulgue pas d’informations confidentielles du serveur principal. Par exemple, il se peut que vous vouliez masquer certaines informations relatives à la pile de technologies exécutée sur le serveur principal. De la même manière, vous pourriez avoir intérêt à protéger les URL d’origine qui apparaissent dans le corps de la réponse HTTP de l’API, en les redirigeant vers la passerelle APIM.

Ce didacticiel vous montre par ailleurs combien il est facile d’ajouter une protection à votre API principale en configurant les limite de débit avec la Gestion des API Azure. Par exemple, vous pouvez limiter le nombre d’appels dirigés vers l’API, ceci pour éviter toute surutilisation de celle-ci par les développeurs. Pour plus d’informations, consultez la section [Stratégies dans Gestion des API Azure](api-management-policies.md)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Transformer une API pour supprimer des en-têtes de réponse
> * Remplacer les URL d’origine dans le corps de la réponse de l’API par les URL de la passerelle APIM
> * Protéger une API en ajoutant une stratégie de limite de débit (limitation)
> * Tester les transformations

![Stratégies](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Prérequis


+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Effectuez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Transformer une API pour supprimer des en-têtes de réponse

Cette section vous montre comment masquer les en-têtes HTTP que vous ne souhaiter pas divulguer à vos utilisateurs. Dans cet exemple, les en-têtes suivants ont été supprimés de la réponse HTTP :

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>Tester la réponse d’origine

Pour consulter la réponse d’origine :

1. Sélectionnez l’onglet **API**.
2. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
3. Sélectionnez l’opération **GetSpeakers**.
4. Cliquez sur l’onglet **Test**, sur la partie supérieure de l’écran.
5. Appuyez sur le bouton **Envoyer**, sur la partie inférieure de l’écran. 

    Comme vous pouvez le voir, la réponse se présente ainsi :

    ![Stratégies](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Définir la stratégie de transformation

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Sélectionnez **Toutes les opérations**.
5. Sélectionnez l’onglet **Conception** en haut de l’écran.
6. Dans la fenêtre **Traitement sortant**, cliquez sur le triangle (à côté du crayon).
7. Sélectionnez **Éditeur de Code**.
    
     ![Modifier la stratégie](./media/set-edit-policies/set-edit-policies01.png)
9. Placez le curseur à l’intérieur de l’élément **&lt;sortant&gt;**.
10. Dans la fenêtre de droite, sous **Stratégies de transformation**, cliquez deux fois sur **+ Set HTTP header** (afin d’insérer deux extraits de stratégie).

    ![Stratégies](./media/transform-api/transform-api.png)
11. Modifiez votre code **<outbound>** afin qu’il ressemble à ceci :

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Remplacer les URL d’origine dans le corps de la réponse de l’API par les URL de la passerelle APIM

Cette section vous explique comment protéger vos URL d’origine qui apparaissent dans le corps de la réponse HTTP de l’API, en les redirigeant vers la passerelle APIM.

### <a name="test-the-original-response"></a>Tester la réponse d’origine

Pour consulter la réponse d’origine :

1. Sélectionnez l’onglet **API**.
2. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
3. Sélectionnez l’opération **GetSpeakers**.
4. Cliquez sur l’onglet **Test**, sur la partie supérieure de l’écran.
5. Appuyez sur le bouton **Envoyer**, sur la partie inférieure de l’écran. 

    Comme vous pouvez le voir, la réponse se présente ainsi :

    ![Stratégies](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Définir la stratégie de transformation

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Sélectionnez **Toutes les opérations**.
5. Sélectionnez l’onglet **Conception** en haut de l’écran.
6. Dans la fenêtre **Traitement sortant**, cliquez sur le triangle (à côté du crayon).
7. Sélectionnez **Éditeur de Code**.
8. Placez le curseur à l’intérieur de l’élément **&lt;sortant&gt;**.
9. Dans la fenêtre de droite, sous **Stratégies de transformation**, cliquez sur **+ Find and replace string in body**.
10. Modifiez votre code **<find-and-replace** (dans l’élément **<outbound>**) afin de remplacer votre URL par une instance correspondant à celle de la passerelle APIM. Par exemple : 

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Protéger une API en ajoutant une stratégie de limite de débit (limitation)

Cette section vous montre comment ajouter une protection pour votre API principale, en configurant les limites de débit. Par exemple, vous pouvez limiter le nombre d’appels dirigés vers l’API, ceci pour éviter toute surutilisation de celle-ci par les développeurs. Dans cet exemple, la limite est fixée à 3 appels par intervalle de 15 secondes, pour chaque identifiant d’abonnement. Après 15 secondes, un développeur peut de nouveau tenter d’appeler l’API.

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Sélectionnez **Toutes les opérations**.
5. Sélectionnez l’onglet **Conception** en haut de l’écran.
6. Dans la fenêtre **Traitement entrant**, cliquez sur le triangle (à côté du crayon).
7. Sélectionnez **Éditeur de Code**.
8. Placez le curseur à l’intérieur de l’élément **&lt;sortant&gt;**.
9. Dans la fenêtre de droite, sous **Accès aux stratégies de restriction**, cliquez sur **+ Limit call rate per key**.
10. Remplacez votre code **<rate-limit-by-key** (dans l’élément **<inbound>**) par le code suivant :

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Tester les transformations
        
À ce stade, votre code de stratégie ressemble à ceci :

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Le reste de cette section est dédié au test des transformations de stratégies définies dans cet article.

### <a name="test-the-stripped-response-headers"></a>Tester les en-têtes de réponse supprimés

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Cliquez sur l’opération **GetSpeakers**.
5. Sélectionnez l’onglet **Test**.
6. Appuyez sur **Envoyer**.

    Comme vous pouvez le voir, les en-têtes ont été supprimés :

    ![Stratégies](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Tester l’URL remplacé

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Cliquez sur l’opération **GetSpeakers**.
5. Sélectionnez l’onglet **Test**.
6. Appuyez sur **Envoyer**.

    Comme vous pouvez le constater, l’URL a été remplacée.

    ![Stratégies](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Tester la limite de débit (limitation)

1. Accédez à votre instance APIM.
2. Sélectionnez l’onglet **API**.
3. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
4. Cliquez sur l’opération **GetSpeakers**.
5. Sélectionnez l’onglet **Test**.
6. Appuyez trois fois consécutives sur **Envoyer**.

    Une fois que vous avez transmis 3 fois la requête, vous recevez une réponse **429 Trop de demandes**.
7. Attendez environ 15 secondes, puis appuyez de nouveau sur **Envoyer**. Cette fois, vous devriez obtenir une réponse **200 OK**.

    ![Limitation](./media/transform-api/test-throttling.png)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Transformer une API pour supprimer des en-têtes de réponse
> * Remplacer les URL d’origine dans le corps de la réponse de l’API par les URL de la passerelle APIM
> * Protéger une API en ajoutant une stratégie de limite de débit (limitation)
> * Tester les transformations

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Surveiller votre API](api-management-howto-use-azure-monitor.md)
