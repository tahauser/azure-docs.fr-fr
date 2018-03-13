---
title: "Création de partenaires pour les messages d’entreprise à entreprise (B2B) - Azure Logic Apps | Microsoft Docs"
description: "Découvrez comment ajouter des partenaires à votre compte d’intégration avec Enterprise Integration Pack et Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89066ba062c2b243136a03a52144fd99ae87eddc
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Ajout ou mise à jour des partenaires dans les contrats d’entreprise à entreprise de votre flux de travail

Les partenaires sont des entités qui participent aux transactions d’entreprise à entreprise (B2B) et qui échangent des messages entre eux. Avant de pouvoir créer des partenaires qui vous représentent et qui représentent une autre organisation dans le cadre de ces transactions, vous devez partager des informations qui identifient et valident les messages envoyés par chacun. Après avoir discuté de ces détails et quand vous serez prêt à initier cette relation commerciale, vous pouvez créer des partenaires dans votre compte d’intégration pour vous représenter, ainsi que l’organisation.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>Quels rôles jouent les partenaires dans votre compte d’intégration ?

Pour définir les détails relatifs aux messages échangés entre les partenaires, vous pouvez créer des contrats entre ces partenaires. Toutefois, avant de pouvoir créer un contrat, vous devez avoir ajouté au moins deux partenaires à votre compte d’intégration. Votre organisation doit faire partie du contrat, en tant que **partenaire hôte**. L’autre partenaire, ou **partenaire invité**, représente l’organisation qui échange des messages avec votre organisation. Le partenaire invité peut être une autre entreprise ou même un service au sein de votre organisation.

Après avoir ajouté ces partenaires, vous pouvez créer un contrat.

Les paramètres de réception et d’envoi sont basés du point de vue du partenaire hébergé. Par exemple, les paramètres de réception dans un contrat déterminent la façon dont le partenaire hébergé reçoit les messages envoyés à partir d’un partenaire invité. De même, les paramètres d’envoi du contrat indiquent la façon dont le partenaire hébergé envoie des messages au partenaire invité.

## <a name="create-partner"></a>Créer un partenaire

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Entrez « intégration » dans la zone de recherche, puis sélectionnez **Comptes d’intégration**.

   ![Chercher le compte d’intégration](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

   ![Sélectionner le compte d’intégration](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. Choisissez la mosaïque **Partenaires**.

   ![Choisir les « Partenaires »](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Sous **Partenaires**, choisissez **Ajouter**.

   ![Choisir « Ajouter »](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Entrez un nom pour votre partenaire, puis sélectionnez un **qualificateur**. Entrez une **valeur** pour identifier les documents que vos applications reçoivent. Une fois que vous avez terminé, sélectionnez **OK**.

   ![Ajouter des détails sur le partenaire](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Choisissez à nouveau la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   Votre nouveau partenaire s’affiche désormais. 

   ![Voir le nouveau partenaire](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>Modifier un partenaire

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Choisissez la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/edit.png)

2. Sous **Partenaires**, sélectionnez le partenaire à modifier.

   ![Sélectionner le partenaire à supprimer](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. Sous **Mettre à jour le partenaire**, apportez les modifications nécessaires.
Une fois que vous avez terminé, sélectionnez **Enregistrer**. 

   ![Effectuer et enregistrer vos modifications](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   Pour annuler vos modifications, sélectionnez **Ignorer**.

## <a name="delete-partner"></a>Supprimer un partenaire

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez votre compte d’intégration. Choisissez la mosaïque **Partenaires**.

   ![Choisir la mosaïque « Partenaires »](./media/logic-apps-enterprise-integration-partners/delete.png)

2. Sous **Partenaires**, sélectionnez le partenaire à supprimer.
Choisissez **Supprimer**.

   ![Supprimer un partenaire](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  

