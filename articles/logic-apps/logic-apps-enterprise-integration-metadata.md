---
title: "Gestion des métadonnées d’artefact de compte d’intégration - Azure Logic Apps | Microsoft Docs"
description: "Ajout ou suppression de métadonnées d’artefact à partir de comptes d’intégration pour Azure Logic Apps"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 5eebae624ea024f2ff8c1fa4764027c05220a15f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Gestion de métadonnées d’artefact dans des comptes d’intégration pour des applications logiques

Vous pouvez définir des métadonnées personnalisées pour les artefacts dans les comptes d’intégration et récupérer ces métadonnées pendant l’exécution pour votre application logique. Par exemple, vous pouvez spécifier des métadonnées pour les artefacts tels que des partenaires, des contrats, des schémas et mappages - tous stockent les métadonnées à l’aide de paires clé-valeur. Actuellement, les artefacts ne peuvent pas créer de métadonnées via l’interface utilisateur, mais vous pouvez utiliser l’API REST pour créer des métadonnées. Pour ajouter des métadonnées lorsque vous créez ou sélectionnez un partenaire, un accord ou un schéma dans le portail Azure, choisissez **Modifier**. Pour récupérer des métadonnées d’artefact dans des applications logiques, vous pouvez utiliser la fonctionnalité de Recherche d’artefact de compte d’intégration.

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Ajoute de métadonnées à des artefacts dans des comptes d’intégration

1. Créez un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md).

2. Ajoutez un artefact à votre compte d’intégration, par exemple, un [partenaire](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), un [contrat](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) ou un [schéma](logic-apps-enterprise-integration-schemas.md).

3.  Sélectionnez l’artefact, choisissez **Modifier** et entrez les informations des métadonnées.

    ![Saisie des métadonnées](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Récupération des métadonnées à partir d’artefacts pour les applications logiques

1. Créez une [application logique](quickstart-create-first-logic-app-workflow.md).

2. Créez un [lien de votre application logique vers votre compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. Dans le Concepteur d’application logique, ajoutez un déclencheur comme *Request* ou *HTTP* à votre application logique.

4.  Choisissez **Étape suivante** > **Ajouter une action**. Recherchez *Intégration*, puis recherchez et sélectionnez **Compte d’intégration - Recherche d’artefact de compte d’intégration**.

    ![Sélectionner Recherche d’artefact de compte d’intégration](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Sélectionnez le **type d’artefact** et indiquez son **nom**.

    ![Sélectionner le type d’artefact et spécifiez son nom](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exemple : récupération des métadonnées du partenaire

Les métadonnées du partenaire ont ces détails `routingUrl` :

![Rechercher des métadonnées de « routingURL » de partenaire](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Dans votre application logique, ajoutez votre déclencheur, une action **Compte d’intégration - Recherche d’artefact de compte d’intégration** pour votre partenaire et une requête **HTTP**.

    ![Ajouter un déclencheur, une recherche d’artefact et « HTTP » à votre application logique](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Pour récupérer l’URI, passez en mode Code pour votre application logique. La définition de votre application logique doit ressembler à cet exemple :

    ![Recherche](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>étapes suivantes
* [En savoir plus sur les contrats](logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  
