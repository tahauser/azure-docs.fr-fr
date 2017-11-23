---
title: "Convertir les données XML avec des transformations - Azure Logic Apps | Microsoft Docs"
description: "Créer des transformations ou des mappages pour convertir les données XML entre les différents formats dans les applications logiques à l’aide du Kit de développement logiciel (SDK) d’intégration d’entreprise"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: f09819a1bfd380cd826a478471e673b6d5ff9ee7
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="enterprise-integration-with-xml-transforms"></a>Intégration d’entreprise avec les transformations XML
## <a name="overview"></a>Vue d'ensemble
Le connecteur Enterprise Integration Transform convertit les données d’un format vers un autre format. Vous pouvez, par exemple, avoir un message entrant qui contient la date du jour au format AnnéeMoisJour. Vous pouvez utiliser une transformation pour remettre en forme la date au format MoisJourAnnée.

## <a name="what-does-a-transform-do"></a>Que fait une transformation ?
Une transformation, également appelée mappage, est composée d’un schéma XML source (l’entrée) et d’un schéma XML cible (la sortie). Vous pouvez utiliser différentes fonctions intégrées pour aider à manipuler ou à contrôler les données, y compris les manipulations de chaînes, les affectations conditionnelles, les expressions arithmétiques, les formateurs d'heure et de date et même les constructions de bouclage.

## <a name="how-to-create-a-transform"></a>Création d’une transformation
Vous pouvez créer une transformation/un mappage à l’aide du [Kit de développement logiciel (SDK) Enterprise Integration](https://aka.ms/vsmapsandschemas)de Visual Studio. Lorsque vous avez terminé de créer et de tester la transformation, vous la téléchargez dans votre compte d’intégration. 

## <a name="how-to-use-a-transform"></a>Utilisation d’une transformation
Après avoir chargé la transformation / le mappage dans votre compte d’intégration, vous pouvez l’utiliser pour créer une application logique. L’application logique exécute vos transformations à chaque fois qu’elle est déclenchée (et qu’il existe du contenu d’entrée qui doit être transformé).

**Voici les étapes à suivre pour utiliser une transformation**:

### <a name="prerequisites"></a>Composants requis

* Créer un compte d’intégration et y ajouter un mappage  

Maintenant que vous avez exécuté la configuration requise, il est temps de créer votre application logique :  

1. Créez une application logique et [liez-la à votre compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le mappage.
2. Ajouter un déclenchement de **Requête** à votre application logique  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Ajoutez l’action **Transformer XML** en sélectionnant d’abord **Ajouter une action**   
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Entrez le mot *transform* dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. Sélectionnez l’action **Transformer XML**   
6. Ajoutez le **CONTENU** XML à transformer. Vous pouvez utiliser toute donnée XML que vous recevez dans la requête HTTP en tant que **CONTENU**. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique.

   > [!NOTE]
   > Vérifiez que le contenu de **Transformer XML** est au format XML. Si le contenu n’est pas au format XML ou s’il est codé en base 64, vous devez spécifier une expression qui traite le contenu. Vous pouvez par exemple utiliser des [fonctions](logic-apps-workflow-definition-language.md#functions) comme ```@base64ToBinary``` pour le décodage du contenu ou ```@xml``` pour le traitement du contenu au format XML.
 

7. Sélectionnez le nom du **MAPPAGE** que vous souhaitez utiliser pour effectuer la transformation. Le mappage doit déjà exister dans votre compte d’intégration. Lors d’une étape précédente, vous avez déjà attribué à votre application logique l’accès à votre compte d’intégration qui contient le mappage.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Enregistrez votre travail   
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

À ce stade, vous avez terminé de configurer votre mappage. Dans une application réelle, vous souhaiterez peut-être stocker les données transformées dans une application métier, comme SalesForce. Vous pouvez facilement ajouter une action pour envoyer la sortie de la transformation à SalesForce. 

Vous pouvez maintenant tester votre transformation en effectuant une demande au point de terminaison HTTP.  

## <a name="features-and-use-cases"></a>Fonctionnalités et cas d’usage
* La transformation créée dans un mappage peut être simple, par exemple la copie d'un nom et de l'adresse d'un document vers un autre. Vous pouvez aussi créer des transformations plus complexes à l'aide des opérations de mappage prêtes à l'emploi.  
* Plusieurs fonctions ou opérations de mappage sont disponibles, y compris des chaînes, des fonctions de date et d'heure, et ainsi de suite.  
* Vous pouvez effectuer une copie de données directe entre les schémas. Dans le Mappeur inclus dans le Kit de développement logiciel (SDK), il suffit de dessiner une ligne qui relie les éléments dans le schéma source à leurs équivalents dans le schéma de destination.  
* Lors de la création d’un mappage, sa représentation graphique est affichée, notamment toutes les relations et les liens que vous créez.
* Utilisez la fonctionnalité Tester le mappage pour ajouter un exemple de message XML. Avec un simple clic, vous pouvez tester le mappage que vous avez créé et afficher la sortie générée.  
* Téléchargement de mappages existants  
* Comprend la prise en charge du format XML.

## <a name="learn-more"></a>En savoir plus
* [En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")  
* [En savoir plus sur les mappages](../logic-apps/logic-apps-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")  

