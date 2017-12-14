---
title: "Partage de tableaux de bord de portail Azure à l’aide de RBAC | Microsoft Docs"
description: "Cet article vous explique comment partager un tableau de bord dans le portail Azure en utilisant le contrôle d'accès en fonction du rôle."
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: 33ddfef1e23a6ff0c2cb8e359d408d2c42cbcf3a
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Partager des tableaux de bord Azure à l’aide du contrôle d’accès en fonction du rôle
Après avoir configuré un tableau de bord, vous pouvez le publier et le partager avec d’autres utilisateurs de votre organisation. Vous permettez à d’autres utilisateurs de voir votre tableau de bord au moyen du [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) Azure. Vous affectez un utilisateur ou un groupe d’utilisateurs à un rôle, qui définit si ces utilisateurs peuvent visualiser ou modifier le tableau de bord publié. 

Tous les tableaux de bord publiés sont implémentés en tant que ressources Azure, ce qui signifie qu’ils constituent des éléments gérables dans votre abonnement et qu’ils sont contenus dans un groupe de ressources.  En termes de contrôle d’accès, les tableaux de bord sont traités de la même manière que les autres ressources, telles qu’une machine virtuelle ou un compte de stockage.

> [!TIP]
> Les vignettes individuelles du tableau de bord appliquent leurs propres exigences en matière de contrôle d’accès, compte tenu des ressources qu’elles affichent.  Par conséquent, vous pouvez concevoir un tableau de bord partagé à grande échelle tout en protégeant les données contenues dans chaque vignette.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Présentation du contrôle d’accès relatif aux tableaux de bord
Le contrôle d’accès en fonction du rôle (RBAC) vous permet d’attribuer des utilisateurs à des rôles avec trois niveaux d’étendue distincts :

* abonnement
* resource group
* resource

Les ressources héritent des autorisations que vous affectez à l’abonnement. Le tableau de bord publié est une ressource. Par conséquent, certains de vos utilisateurs disposent peut-être déjà de rôles pour l’abonnement qui fonctionnent également pour le tableau de bord publié. 

Voici un exemple.  Supposons que vous disposiez d’un abonnement Azure et que les différents membres de votre équipe aient reçu les rôles de **propriétaire**, de **contributeur** ou de **lecteur** pour l’abonnement. Les utilisateurs associés au rôle de propriétaire ou de contributeur peuvent répertorier, afficher, créer, modifier ou supprimer des tableaux de bord dans l’abonnement.  Les utilisateurs ayant le rôle de lecteur peuvent répertorier et afficher des tableaux de bord, sans les modifier ou les supprimer.  Les utilisateurs dotés d’un accès de lecteur peuvent apporter des modifications locales à un tableau de bord publié (par exemple, lors de la résolution d’un problème), mais ne peuvent pas republier ces modifications sur le serveur.  Ils pourront effectuer une copie privée du tableau de bord pour leur propre usage.

Toutefois, vous pouvez également affecter des autorisations au groupe de ressources qui contient plusieurs tableaux de bord ou à un tableau de bord spécifique. Par exemple, vous pouvez décider qu’un groupe d’utilisateurs doit disposer d’autorisations limitées dans l’ensemble de l’abonnement, mais un accès plus étendu à un tableau de bord donné. Vous affectez alors ces utilisateurs à un rôle pour ce tableau de bord. 

## <a name="publish-dashboard"></a>Publier un tableau de bord
Supposons que vous ayez terminé de configurer un tableau de bord que vous souhaitez partager avec un groupe d’utilisateurs dans votre abonnement. La procédure ci-après décrit un groupe personnalisé appelé Responsables du stockage, mais vous pouvez nommer votre groupe à votre convenance. Pour plus d’informations sur la création d’un groupe Active Directory et sur l’ajout d’utilisateurs à ce groupe, voir [Gestion des groupes dans Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

1. Dans le tableau de bord, sélectionnez **Partager**.
   
     ![sélectionner Partager](./media/azure-portal-dashboard-share-access/select-share.png)
2. Avant d’affecter l’accès, vous devez publier le tableau de bord. Par défaut, le tableau de bord est publié dans un groupe de ressources nommé **tableaux de bord**. Sélectionnez **Publier**.
   
     ![Publier](./media/azure-portal-dashboard-share-access/publish.png)

Votre tableau de bord est à présent publié. Si les autorisations héritées de l’abonnement vous conviennent, vous n’avez rien d’autre à faire. Les autres utilisateurs de votre organisation seront en mesure d’accéder au tableau de bord et de le modifier en fonction de leur rôle au niveau de l’abonnement. Toutefois, pour ce didacticiel, affectons un groupe d’utilisateurs à un rôle pour ce tableau de bord.

## <a name="assign-access-to-a-dashboard"></a>Affecter l’accès à un tableau de bord
1. Après avoir publié le tableau de bord, sélectionnez **Gérer les utilisateurs**.
   
     ![Gérer les utilisateurs](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Vous obtenez une liste d’utilisateurs existants déjà dotés d’un rôle pour ce tableau de bord. Votre liste d’utilisateurs existants sera différente de celle illustrée ci-dessous. Les affectations sont très probablement héritées de l’abonnement. Pour ajouter un nouvel utilisateur ou un nouveau groupe, sélectionnez **Ajouter**.
   
     ![ajouter un utilisateur](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Sélectionnez le rôle qui représente les autorisations que vous souhaitez accorder. Dans cet exemple, sélectionnez **Contributeur**.
   
     ![sélectionner un rôle](./media/azure-portal-dashboard-share-access/select-role.png)
4. Sélectionnez l’utilisateur ou le groupe à affecter au rôle. Si l’utilisateur ou le groupe que vous recherchez n’apparaissent pas dans la liste, utilisez la zone de recherche. Votre liste de groupes disponibles dépendra des groupes que vous avez créés dans votre service Active Directory.
   
     ![sélectionner un utilisateur](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Lorsque vous avez terminé d’ajouter des utilisateurs ou des groupes, sélectionnez **OK**. 
6. La nouvelle affectation est ajoutée à la liste d’utilisateurs. Notez que son **Accès** présente la valeur **Affecté** plutôt que la valeur **Hérité**.
   
     ![rôles affectés](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir la liste des rôles, voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md).
* Pour plus d’informations sur la gestion des ressources, voir [Gérer les ressources Azure sur le portail](resource-group-portal.md).

