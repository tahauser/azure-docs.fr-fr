---
title: "Prise en main d’Azure CDN | Microsoft Docs"
description: "Cette rubrique montre comment activer le réseau de distribution de contenu (CDN) Azure. Ce didacticiel décrit la création d'un profil CDN et d’un point de terminaison."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: 81a88f6495ca9092ca3b55b8ffb3e41def3b4623
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-cdn"></a>Prise en main d’Azure CDN
Cet article décrit l’activation de Azure CDN en créant un nouveau profil CDN et un point de terminaison.

> [!IMPORTANT]
> Pour obtenir une présentation du CDN et une liste des fonctionnalités, consultez la [Présentation du CDN](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Créer un profil CDN
Un profil CDN est une collection de points de terminaison CDN. Chaque profil peut contenir un ou plusieurs points de terminaison CDN. Vous pouvez utiliser plusieurs profils pour organiser vos points de terminaison CDN par domaine Internet, application web ou d’autres critères.

> [!NOTE]
> Un abonnement Azure comprend des limites par défaut pour les ressources suivantes :
> - Le nombre de profils CDN qui peuvent être créés.
> - Le nombre de points de terminaison qui peuvent être créés dans un profil CDN. 
> - Le nombre de domaines personnalisés qui peuvent être mappés à un point de terminaison.
>
> Pour plus d’informations sur les limites d’abonnement CDN, consultez [Limites de CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> La tarification CDN est appliquée au niveau du profil CDN. Par conséquent, pour utiliser une combinaison de niveaux de tarification Azure CDN, vous devez créer plusieurs profils CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un point de terminaison CDN
**Pour créer un point de terminaison CDN**

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN. Vous l'avez peut-être épinglé au tableau de bord à l'étape précédente. Sinon, vous pouvez le trouver en sélectionnant **Tous les services**, puis **Profils CDN**. Dans le volet **Profils CDN**, sélectionnez le profil auquel vous avez prévu d’ajouter votre point de terminaison. 
   
    Le volet du profil CDN s’affiche.
   
    ![Profil CDN][cdn-profile-settings]

2. Sélectionnez **Point de terminaison**.
   
    ![Bouton Ajouter un point de terminaison][cdn-new-endpoint-button]
   
    Le volet **Ajouter un point de terminaison** s’affiche.
   
    ![Volet Ajouter un point de terminaison][cdn-add-endpoint]

3. Comme **Nom**, entrez un nom unique pour le nouveau point de terminaison CDN. Ce nom sert à accéder à vos ressources en cache au niveau du domaine `<endpointname>.azureedge.net`.

4. Pour **Type d’origine**, sélectionnez un type d’origine. Sélectionnez **Stockage** pour un compte de stockage Azure, **Service cloud** pour un service cloud Azure, **Application web** pour une application web Azure ou **Origine personnalisée** pour toute autre origine de serveur web accessible publiquement (hébergé dans Azure ou ailleurs).
   
    ![Type d'origine CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. Pour **Nom d’hôte d’origine**, sélectionnez ou entrez votre domaine d’origine. La liste déroulante répertorie toutes les origines disponibles du type spécifié à l’étape 4. Si vous avez sélectionné **Origine personnalisée** comme type d’origine, saisissez le domaine de votre origine personnalisée.
    
6. Pour **Chemin d’accès d’origine** , entrez le chemin d’accès aux ressources que vous souhaitez mettre en cache ou laissez cette zone vide pour autoriser la mise en cache de n’importe quelle ressource au niveau du domaine spécifié à l’étape 5.
    
7. Pour **En-tête de l’hôte d’origine**, entrez l’en-tête de l’hôte que le CDN doit envoyer avec chaque requête ou conservez la valeur par défaut.
   
   > [!WARNING]
   > Certains types d’origine, tels que Azure Storage et Web Apps, nécessitent l’en-tête de l’hôte pour correspondre au domaine de l’origine. À moins d’avoir une origine nécessitant un en-tête d’hôte différent de son domaine, vous devriez laisser la valeur par défaut.
   > 
    
8. Pour **Protocole** et **Port d’origine**, spécifiez les protocoles et les ports utilisés pour accéder à vos ressources à l’origine. Vous devez sélectionner au moins un protocole (HTTP ou HTTPS). Utilisez le domaine fourni par le CDN (`<endpointname>.azureedge.net`) pour accéder au contenu HTTPS. 
   
   > [!NOTE]
   > La valeur **port d’origine** ne concerne que le port utilisé par le point de terminaison pour récupérer des informations à partir de l’origine. Le point de terminaison est uniquement disponible pour les clients sur les ports HTTP et HTTPS par défaut (80 et 443), quel que soit la valeur **port d’origine**.  
   > 
   > Les points de terminaison **CDN Azure fournis par Akamai** n’autorisent pas la plage de ports TCP complète pour les ports d’origine. Pour obtenir la liste des ports d’origine non autorisés, consultez l’article [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Ports d’origine autorisés du CDN Azure fourni par Akamai).  
   > 
   > Lorsque vous accédez au contenu CDN avec le protocole HTTPS, vous rencontrerez les inconvénients suivants :
   > 
   > * Utilisez le certificat SSL fourni par le CDN. Les certificats tiers ne sont pas pris en charge.
   > * La prise en charge HTTPS pour les domaines personnalisés Azure CDN est disponible uniquement avec les produits **Azure CDN fournis par Verizon** (Standard ou Premium). Elle n’est pas prise en charge dans les produits **Azure CDN de Akamai**. Pour plus d’informations, consultez [Activer ou désactiver HTTPS sur un domaine personnalisé Azure CDN](cdn-custom-ssl.md).
    
9. Sélectionnez **Ajouter** pour créer un point de terminaison.
   
   Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison pour le profil.
    
   ![Point de terminaison CDN][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Le point de terminaison n’est pas disponible immédiatement, car la propagation de l’enregistrement peut prendre du temps. Pour les profils du **CDN Azure fourni par Akamai** , la propagation s’effectue généralement dans un délai d’une minute. Pour les profils du **CDN Azure fourni par Verizon**, la propagation s’effectue généralement dans un délai de 90 minutes, mais elle peut prendre plus de temps dans certains cas.
    > 
    > Si vous tentez d’utiliser le nom de domaine CDN avant la propagation de la configuration du point de terminaison aux POP, vous recevrez probablement un état de réponse HTTP 404. Si plusieurs heures se sont écoulées depuis la création de votre point de terminaison et que vous recevez toujours un état de réponse 404, consultez [Dépannage des points de terminaison de CDN renvoyant des états 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Voir aussi
* [Contrôle du comportement de mise en cache des demandes avec des chaînes de requête](cdn-query-string.md)
* [Mappage du contenu CDN à un domaine personnalisé](cdn-map-content-to-custom-domain.md)
* [Préchargement d’éléments multimédias sur un point de terminaison CDN Azure](cdn-preload-endpoint.md)
* [Purger un point de terminaison CDN Azure](cdn-purge-endpoint.md)
* [Dépannage des points de terminaison de CDN renvoyant des états 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
