---
title: "Configurer la méthode de routage du trafic géographique à l’aide d’Azure Traffic Manager | Microsoft Docs"
description: "Cet article explique comment configurer la méthode de routage du trafic géographique à l’aide d’Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 7b49e2a4eef5a966f1ef2aa283a3089bb5b73734
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Configurer la méthode de routage du trafic géographique à l’aide de Traffic Manager

La méthode de routage du trafic géographique vous permet de diriger le trafic vers des points de terminaison spécifiques en fonction de l’emplacement géographique d’où proviennent les demandes. Ce didacticiel vous montre comment créer un profil Traffic Manager avec cette méthode de routage et configurer les points de terminaison pour recevoir le trafic à partir de zones géographiques spécifiques.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

1. Dans un navigateur, connectez-vous au [portail Azure](http://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez [vous inscrire pour bénéficier d’un essai gratuit d’un mois](https://azure.microsoft.com/free/).
2. Cliquez sur **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager** > **Créer**.
4. Dans **Créer un profil Traffic Manager** :
    1. Entrez un nom pour votre profil. Ce nom doit être unique dans la zone trafficmanager.net. Pour accéder à votre profil Traffic Manager, vous utilisez le nom DNS <profilename>.trafficmanager.net.
    2. Sélectionnez la méthode de routage **géographique**.
    3. Sélectionnez l’abonnement pour lequel vous souhaitez créer ce profil.
    4. Utilisez un groupe de ressources existant ou créez-en un où placer ce profil. Si vous choisissez de créer un nouveau groupe de ressources, utilisez la liste déroulante **Emplacement du groupe de ressources** pour spécifier l’emplacement du groupe de ressources. Ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’impact sur le profil Traffic Manager qui est déployé globalement.
    5. Après avoir cliqué sur **Créer**, votre profil Traffic Manager est créé et déployé globalement.

![Créer un profil Traffic Manager](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Ajouter des points de terminaison

1. Dans la barre de recherche du portail, recherchez le nom du profil Traffic Manager que vous avez créé et cliquez sur le résultat lorsqu’il s’affiche.
2. Dans le volet Traffic Manager, accédez à **Paramètres** -> **Points de terminaison**.
3. Cliquez sur **Ajouter** pour afficher le volet **Ajouter un point de terminaison**.
3. Cliquez sur **Ajouter** et dans le volet **Ajouter un point de terminaison** qui s’affiche, procédez comme suit :
4. Sélectionnez **Type** en fonction du type de point de terminaison que vous ajoutez. Pour les profils de routage géographique utilisés en production, nous vous recommandons d’utiliser des types de point de terminaison imbriqués contenant un profil enfant avec plus d’un point de terminaison. Pour plus d’informations, consultez [FAQ sur les méthodes de routage du trafic géographique](traffic-manager-FAQs.md).
5. Entrez un **Nom** pour ce point de terminaison.
6. Certains champs de cette page varient selon le type de point de terminaison que vous ajoutez :
    1. Si vous ajoutez un point de terminaison Azure, sélectionnez le **type de ressource cible** et la **cible** en fonction de la ressource vers laquelle vous souhaitez diriger le trafic
    2. Si vous ajoutez un point de terminaison **externe**, entrez le **nom de domaine complet (FQDN)** pour votre point de terminaison.
    3. Si vous ajoutez un **point de terminaison imbriqué**, sélectionnez la **ressource cible** qui correspond au profil enfant à utiliser et spécifiez le **nombre minimal de points de terminaison enfants**.
7. Dans la section du mappage géographique, utilisez la liste déroulante pour ajouter les régions à partir desquelles le trafic doit être envoyé à ce point de terminaison. Vous devez ajouter au moins une région et vous pouvez avoir plusieurs régions mappées.
8. Répétez cette procédure pour tous les points de terminaison à ajouter sous ce profil

![Ajouter un point de terminaison Traffic Manager](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utiliser le profil Traffic Manager
1.  Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** que vous avez créé dans la section précédente et cliquez sur le profil Traffic Manager dans les résultats affichés.
2. Cliquez sur **Overview**.
3. Le **profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé. Celui-ci peut être utilisé par tous les clients (par exemple, en y accédant à l’aide d’un navigateur web) pour être acheminés vers le point de terminaison correct, comme déterminé par le type de routage.  Dans le cas du routage géographique, Traffic Manager examine l’adresse IP source de la demande entrante et détermine la région d’où elle provient. Si cette région est mappée à un point de terminaison, le trafic est acheminé vers cet emplacement. Si cette région n’est pas mappée à un point de terminaison, Traffic Manager renvoie une réponse NODATA.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en davantage sur la [méthode de routage du trafic géographique](traffic-manager-routing-methods.md#geographic).
- Découvrez comment [tester les paramètres Traffic Manager](traffic-manager-testing-settings.md).
