---
title: Valider des messages XML - Azure Logic Apps | Microsoft Docs
description: "Validez des messages XML avec des schémas dans Azure Logic Apps et dans des scénarios B2B à l’aide d’Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 9c4b2c1b2fdd9bf70775e5fd4369d1633258ae2a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="validate-xml-for-enterprise-integration"></a>Valider des messages XML pour l’intégration d’entreprise

Souvent, dans les scénarios B2B, les partenaires dans un contrat doivent s’assurer que les messages qu’ils échangent sont valides avant le début du traitement des données. Vous pouvez utiliser le connecteur XML Validation dans Enterprise Integration Pack pour valider les documents par rapport à un schéma prédéfini.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Valider un document avec le connecteur XML Validation

1. Créez une application logique et [liez-la à votre compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le schéma que celui que vous voulez utiliser pour valider les données XML.

2. Ajoutez un déclencheur **Requête - Lors de la réception d’une requête HTTP** à votre application logique.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Pour ajouter l’action **Validation XML** choisissez **Ajouter une action**.

4. Pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser, entrez *xml* dans la zone de recherche. Choisissez **Validation XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Pour spécifier le contenu XML que vous souhaitez valider, sélectionnez **CONTENU**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Sélectionnez la balise body comme contenu à valider.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Pour spécifier le schéma que vous souhaitez utiliser pour la validation de la précédente entrée de *contenu*, choisissez **NOM DU SCHÉMA**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Enregistrez votre travail   

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Vous avez maintenant terminé de configurer votre connecteur de validation. Dans une application réelle, vous souhaiterez peut-être stocker les données validées dans une application métier telle que Salesforce. Pour envoyer la sortie validée à Salesforce, ajoutez une action.

Pour tester votre action de validation, envoyez une demande au point de terminaison HTTP.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")   

