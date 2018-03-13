---
title: "Créer, lier, supprimer ou déplacer un compte d’intégration dans Azure Logic Apps | Microsoft Docs"
description: "Comment créer un compte d’intégration et l’associer à vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>Qu’est-ce qu'un compte d’intégration ?

Un compte d’intégration permet aux applications d’intégration de votre entreprise, plus particulièrement aux applications logiques, d’accéder et de gérer les artefacts B2B, par exemple, les partenaires commerciaux, les contrats, les mappages, les schémas, les certificats et ainsi de suite. Pour fournir cet accès, liez votre compte d’intégration à votre application logique après avoir vérifié que le compte d’intégration et l’application logique ont le *même emplacement Azure*.

## <a name="create-an-integration-account"></a>Créer un compte d’intégration

1. Connectez-vous au [portail Azure](http://portal.azure.com "portail Azure"). 

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « intégration », puis sélectionnez **Comptes d’intégration**.

   ![Créer un compte d’intégration](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. En haut de la page, choisissez **Ajouter**.

   ![Choisir Ajouter](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Nommez votre compte d’intégration et sélectionnez l’abonnement Azure que vous voulez utiliser. Vous pouvez créer un **groupe de ressources** ou sélectionner un groupe de ressources existant. Sélectionnez l’**Emplacement** d’hébergement de votre compte d’intégration et un **Niveau tarifaire**. Une fois ces opérations effectuées, sélectionnez **Créer**.

   ![Indiquez les détails de votre compte d’intégration](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure provisionne votre compte d’intégration à l’emplacement sélectionné en moins d’une minute.

5. Actualisez la page. Votre nouveau compte d’intégration apparaît désormais dans la liste.

   ![Votre nouveau compte d’intégration apparaît](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Ensuite, liez le compte d’intégration que vous venez de créer à votre application logique. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Lier un compte d’intégration à une application logique

Pour que vos applications logiques puissent accéder aux artefacts B2B, tels que les partenaires commerciaux, les contrats, les mappages et les schémas de votre compte d’intégration, liez le compte d’intégration à votre application logique. 

1. Dans le portail Azure, sélectionnez votre application logique, puis vérifiez son emplacement.

   ![Sélectionnez votre application logique, vérifiez l’emplacement](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. Sous **Paramètres**, sélectionnez **Compte d’intégration**.

   ![Sélectionner « Compte d’intégration »](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Dans la liste **Sélectionner un compte d’intégration**, sélectionnez le compte d’intégration que vous souhaitez lier à votre application logique. Pour terminer la liaison, choisissez **Enregistrer**.

   ![Sélectionnez votre compte d’intégration](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Vous obtenez une notification qui indique que votre compte d’intégration est lié à votre application logique et que tous les artefacts de votre compte d’intégration sont désormais accessibles à votre application logique.

   ![Votre application logique est liée à votre compte d’intégration](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Maintenant que votre compte d’intégration est lié à votre application logique, vous pouvez utiliser les connecteurs B2B dans votre application logique. Parmi les connecteurs B2B les plus courants figurent la validation XML, et l’encodage et le décodage de fichiers plats.  

## <a name="delete-your-integration-account"></a>Supprimez votre compte d’intégration

1. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « intégration », puis sélectionnez **Comptes d’intégration**.

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Sélectionnez le compte d’intégration que vous voulez supprimer.

    ![Sélectionner un compte d’intégration à supprimer](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. Dans le menu, choisissez **Supprimer**.

    ![Choisir « Supprimer »](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Confirmez la suppression du compte d’intégration.

## <a name="move-your-integration-account"></a>Déplacer votre compte d’intégration

Pour déplacer un compte d’intégration vers un autre abonnement ou groupe de ressources Azure, procédez comme suit. Après avoir déplacé le compte d’intégration, veillez à mettre à jour tous les scripts pour qu’ils utilisent les nouveaux ID de ressources.

1. Dans le menu principal Azure, sélectionnez **Tous les services**. Dans la zone de recherche, entrez « intégration », puis sélectionnez **Comptes d’intégration**.

   ![Recherche du compte d’intégration](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Sélectionnez le compte d’intégration que vous voulez déplacer. Sous **Paramètres**, choisissez **Propriétés**.

   ![Sélectionner un compte d’intégration à déplacer. Sous Paramètres, choisir Propriétés](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Modifiez le groupe de ressources ou l’abonnement Azure associé à votre compte d’intégration.

   ![Sélectionner Modifier le groupe de ressources ou Modifier l’abonnement](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  

