---
title: Utilisation de messages XML dans vos flux de travail - Azure Logic Apps | Microsoft Docs
description: Traitement, validation, transformation et enrichissement de messages XML dans les applications logiques et les scénarios d’entreprise à entreprise à l’aide de Enterprise Integration Pack
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 495cb44219ee9d8ca20ea17c373545668839f4c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Validation et transformation XML, codage et décodage de fichiers plats et enrichissement des fonctionnalités des messages dans les applications logiques

Les applications logiques vous permettent de traiter les messages XML envoyés et reçus. Cette fonctionnalité est incluse dans Enterprise Integration Pack. Pour les utilisateurs de BizTalk Server, Enterprise Integration Pack offre des capacités similaires pour transformer et valider des messages, utiliser des fichiers plats et même XPath pour enrichir ou extraire des propriétés spécifiques d’un message. 

Pour les utilisateurs qui découvrent cet environnement, ces fonctionnalités étendent les possibilités de traitement des messages au sein de votre flux de travail. Par exemple, dans un scénario d’entreprise à entreprise où vous travaillez avec des schémas XML spécifiques, vous pouvez utiliser Enterprise Integration Pack pour améliorer la façon dont votre entreprise traite ces messages. 

Enterprise Integration Pack inclut les éléments suivants : 

* [Validation XML](logic-apps-enterprise-integration-xml-validation.md "En savoir plus sur la validation de messages XML") : permet de valider un message XML entrant ou sortant par rapport à un schéma spécifique.
* [Transformation XML](../logic-apps/logic-apps-enterprise-integration-transform.md "En savoir plus sur les transformations et les mappages de message XML") : permet de convertir un message XML basé sur vos exigences ou celles d’un partenaire.
* [Encodage et décodage de fichier plat](logic-apps-enterprise-integration-flatfile.md "En savoir plus sur l’encodage/décodage de fichier plat") : permet de coder ou décoder un fichier plat. Par exemple, SAP accepte et envoie des fichiers IDOC dans un format de fichier plat. De nombreuses plates-formes d’intégration créent des messages XML, y compris Logic Apps. Vous pouvez donc créer une application logique qui utilise l’encodeur de fichier plat afin de « convertir » des fichiers XML en fichiers plats. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) : permet d’enrichir un message et d’extraire des propriétés spécifiques du message. Les propriétés extraites peuvent ensuite servir à acheminer le message vers une destination ou un point de terminaison intermédiaire.

## <a name="try-it-out"></a>Faites un essai
[Déployez votre propre application logique entièrement fonctionnelle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (exemple GitHub) à l’aide des fonctionnalités XML d’Azure Logic Apps.

## <a name="learn-more"></a>En savoir plus
[En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")
