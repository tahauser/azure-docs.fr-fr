---
title: "Créer un profil Traffic Manager dans Azure| Microsoft Docs"
description: "Cet article décrit comment créer un profil Traffic Manager"
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
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: 1994c8374244b62e65b1a54234775d9a39f72bb3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Cet article décrit comment un profil avec le type de routage **Priorité** peut être créé pour diriger les utilisateurs vers deux points de terminaison Azure Web Apps. Avec le type de routage **Priorité**, tout le trafic est acheminé vers le premier point de terminaison tandis que le deuxième est conservé en tant que sauvegarde. Par conséquent, les utilisateurs peuvent être dirigés vers le deuxième point de terminaison si le premier point de terminaison devient défaillant.

Dans cet article, deux points de terminaison Azure Web Apps créés précédemment sont associés à ce nouveau profil Traffic Manager. Pour en savoir plus sur la création de points de terminaison Azure Web Apps, visitez la [page de documentation Azure Web Apps](https://docs.microsoft.com/azure/app-service/). Vous pouvez ajouter n’importe quel point de terminaison qui a un nom DNS et est accessible via l’Internet public. Nous utilisons des points de terminaison Azure Web Apps comme exemple.

### <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager
1. Dans un navigateur, connectez-vous au [portail Azure](http://portal.azure.com). Si vous ne possédez pas encore de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit d’un mois](https://azure.microsoft.com/free/). 
2. Cliquez sur **Créer une ressource** > **Mise en réseau** > **Profil Traffic Manager** > **Créer**.
4. Dans **Créer un profil Traffic Manager**, procédez comme suit :
    1. Sous **Nom**, entrez un nom pour votre profil. Ce nom doit être unique au sein de la zone trafficmanager.net et résulte dans le nom DNS <name>,trafficmanager.net, qui est utilisé pour accéder à votre profil Traffic Manager.
    2. Sous **Méthode de routage**, sélectionnez la méthode de routage **Priorité**.
    3. Sous **Abonnement**, sélectionnez l’abonnement pour lequel vous souhaitez créer ce profil.
    4. Sous **Groupe de ressources**, créez un groupe de ressources où placer ce profil.
    5. Sous **Emplacement du groupe de ressources**, sélectionnez l’emplacement du groupe de ressources. Ce paramètre fait référence à l’emplacement du groupe de ressources et n’a pas d’impact sur le profil Traffic Manager qui sera déployé globalement.
    6. Cliquez sur **Créer**.
    7. Lorsque le déploiement global de votre profil Traffic Manager est terminé, il est répertorié comme l’une des ressources dans le groupe de ressources respectif.

    ![Créer un profil Traffic Manager](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Ajouter des points de terminaison Traffic Manager

1. Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** que vous avez créé dans la section précédente et cliquez sur le profil Traffic Manager dans les résultats affichés.
2. Dans **Profil Traffic Manager**, à la section **Paramètres**, cliquez sur **Points de terminaison**.
3. Cliquez sur **Add**.
4. Procédez comme suit :
    1. Sous **Type**, cliquez sur **Point de terminaison Azure**.
    2. Entrez un **Nom** pour ce point de terminaison.
    3. Sous **Type de ressource cible**, cliquez sur **App Service**.
    4. Sous **Ressource cible**, cliquez sur **Choisir un service d’application** pour afficher la liste des applications Web sous le même abonnement. Dans **Ressources**, choisissez le service d’application que vous souhaitez ajouter en tant que premier point de terminaison.
    5. Pour **Priorité**, sélectionnez **1**. Ainsi, tout le trafic est dirigé vers ce point de terminaison, s’il est intègre.
    6. Vérifiez que la case **Ajouter comme désactivé** est désélectionnée.
    7. Cliquez sur **OK**
5.  Répétez les étapes 3 et 4 pour le prochain point de terminaison Azure Web Apps. Veillez à ajouter à sa valeur de **Priorité** définie sur **2**.
6.  Lorsque l’ajout de deux points de terminaison est terminé, ceux-ci s’affichent dans **Profil Traffic Manager** avec leur état de surveillance défini sur **En ligne**.

    ![Ajouter un point de terminaison Traffic Manager](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Utiliser le profil Traffic Manager
1.  Dans la barre de recherche du portail, recherchez le nom du **profil Traffic Manager** que vous avez créé dans la section précédente. Dans les résultats affichés, cliquez sur le profil Traffic Manager.
2. Cliquez sur **Overview**.
3. Le **profil Traffic Manager** affiche le nom DNS de votre profil Traffic Manager nouvellement créé. Celui-ci peut être utilisé par tous les clients (par exemple, en y accédant à l’aide d’un navigateur web) pour être acheminés vers le point de terminaison correct, comme déterminé par le type de routage. Dans ce cas, toutes les demandes sont acheminées vers le premier point de terminaison et si Traffic Manager détecte que celui-ci n’est pas intègre, le trafic bascule automatiquement vers le point de terminaison suivant.

## <a name="delete-the-traffic-manager-profile"></a>Supprimer le profil Traffic Manager
Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources et le profil Traffic Manager que vous avez créés. Pour ce faire, sélectionnez le groupe de ressources dans le **profil Traffic Manager**, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [types de routage](traffic-manager-routing-methods.md).
- En savoir plus sur les [types de point de terminaison](traffic-manager-endpoint-types.md).
- En savoir plus sur la [surveillance du point de terminaison](traffic-manager-monitoring.md).



