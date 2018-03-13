---
title: "Convertir des données JSON avec des transformations Liquid - Azure Logic Apps | Microsoft Docs"
description: "Créez des transformations ou des mappages pour des transformations JSON avancées à l’aide de Logic Apps et du modèle Liquid."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 4bb003afd757faac675a9af8599a781247717a64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Effectuer des transformations JSON avancées avec un modèle Liquid

Azure Logic Apps prend en charge les transformations JSON de base par le biais d’actions d’opérations de données natives telles que **Compose** ou **Parse JSON**. Pour les transformations JSON avancées, vous pouvez utiliser des modèles Liquid avec vos applications logiques. 
[Liquid](https://shopify.github.io/liquid/) est un langage de modèle open source qui permet de créer des applications web flexibles.
 
Dans cet article, vous allez découvrir comment utiliser un mappage ou un modèle Liquid qui prend en charge des transformations JSON plus complexes (itérations, flux de contrôle, variables, et ainsi de suite). Avant de pouvoir effectuer une transformation Liquid dans votre application logique, vous devez définir le mappage JSON à JSON avec un mappage Liquid et le stocker dans votre compte d’intégration.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) de base


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>Créer un mappage ou un modèle Liquid pour votre compte d’intégration

1. Créez l’exemple de modèle Liquid pour cet exemple. Le modèle Liquid définit comment transformer l’entrée JSON décrite ici :

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > Si votre modèle Liquid utilise des [filtres](https://shopify.github.io/liquid/basics/introduction/#filters), ceux-ci doivent commencer par une majuscule. 

2. Connectez-vous au [Portail Azure](https://portal.azure.com).

3. Dans le menu Azure principal, accédez à **Toutes les ressources**. 

4. Dans la zone de recherche, recherchez et sélectionnez votre compte d’intégration.

   ![Sélectionner le compte d’intégration](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  Sur la vignette du compte d’intégration, sélectionnez **Mappages**.

   ![Sélectionner Mappages](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. Choisissez **Ajouter** et indiquez les informations suivantes pour votre mappage :

   * **Nom** : Nom de votre mappage (« JsontoJsonTemplate » dans cet exemple).
   * **Type de mappage** : Type de votre mappage. Pour une transformation JSON à JSON, vous devez sélectionner **liquid**.
   * **Mappage** : Fichier de modèle ou de mappage Liquid existant à utiliser pour la transformation (« SimpleJsonToJsonTemplate.liquid » dans cet exemple). Pour trouver ce fichier, vous pouvez utiliser le sélecteur de fichiers.

   ![Ajouter un modèle Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Ajouter l’action Liquid pour la transformation JSON

1. [Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Ajoutez le [déclencheur de requête](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) à votre application logique.

3. Choisissez **+ Nouvelle étape > Ajouter une action**. Dans la zone de recherche, entrez *liquid*, puis sélectionnez **Liquid - Transformer de JSON en JSON**.

   ![Rechercher et sélectionner l’action Liquid](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Dans la zone **Contenu**, sélectionnez **Corps** à partir de la liste de contenu dynamique ou de paramètres qu is’affiche.
  
   ![Sélectionner le corps](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Dans la liste **Mappage**, sélectionnez votre modèle Liquid (« JsonToJsonTemplate » dans cet exemple).

   ![Sélectionner le mappage](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Si la liste est vide, votre application logique n’est probablement pas liée à votre compte d’intégration. 
   Pour lier votre application logique au compte d’intégration associé au modèle ou au mappage Liquid, effectuez les étapes suivantes :

   1. Dans le menu de votre application logique, sélectionnez **Paramètres de flux de travail**.
   2. Dans la liste **Sélectionner un compte d’intégration**, sélectionnez votre compte d’intégration et choisissez **Enregistrer**.

   ![Lier une application logique à un compte d’intégration](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Tester votre application logique

Publiez l’entrée JSON sur votre application logique à partir de [Postman](https://www.getpostman.com/postman) ou d’un outil similaire. La sortie JSON transformée à partir de votre application logique ressemble à ceci :
  
![Exemple de sortie](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Autres exemples d’actions Liquid
Liquid n’est pas limité aux transformations JSON. Voici d’autres actions de transformation disponibles qui utilisent Liquid.

* Transformer du JSON en texte
  
  Voici le modèle Liquid utilisé pour cet exemple :
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Voici des exemples d’entrée et de sortie :
  
   ![Exemple de sortie JSON en texte](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformer du XML en JSON
  
  Voici le modèle Liquid utilisé pour cet exemple :
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Voici des exemples d’entrée et de sortie :

   ![Exemple de sortie XML en JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformer du XML en texte
  
  Voici le modèle Liquid utilisé pour cet exemple :

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Voici des exemples d’entrée et de sortie :

   ![Exemple de sortie XML en texte](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>étapes suivantes

* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  
* [En savoir plus sur les mappages](../logic-apps/logic-apps-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")  

