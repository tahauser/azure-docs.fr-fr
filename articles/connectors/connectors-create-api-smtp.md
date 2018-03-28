---
title: Connecteur SMTP dans Azure Logic Apps | Microsoft Docs
description: Créez des applications logiques avec Azure App Service. Connectez-vous à SMTP pour envoyer un e-mail.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9bf7c9b7c3e775ab03b071d13d792f4b2d8fb3e3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-smtp-connector"></a>Prise en main du connecteur SMTP
Connectez-vous à SMTP pour envoyer un e-mail.

Pour utiliser [n’importe quel connecteur](apis-list.md), vous devez commencer par créer une application logique. Vous pouvez démarrer maintenant en [créant une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Se connecter à SMTP
Pour que votre application logique puisse accéder à un service, vous devez commencer par créer une *connexion* à celui-ci. Une [connexion](connectors-overview.md) permet d’assurer la connectivité entre une application logique et un autre service. Par exemple, pour vous connecter à SMTP, vous devez préalablement disposer d’une *connexion* SMTP. Pour créer une connexion, entrez les informations d’identification que vous utilisez généralement pour accéder au service auquel vous vous connectez. Ainsi, dans l’exemple SMTP, entrez les informations d’identification telles que votre nom de connexion, l’adresse du serveur SMTP et les informations de connexion utilisateur pour créer la connexion à SMTP.  

### <a name="create-a-connection-to-smtp"></a>Créer une connexion à SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Utiliser un déclencheur SMTP
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Dans cet exemple, SMTP ne dispose pas d’un déclencheur qui lui est propre. Par exemple, utilisez le déclencheur **Salesforce - quand un objet est créé**. Ce déclencheur s’active lorsqu’un objet est créé dans Salesforce. Dans notre exemple, il est configuré de sorte que chaque fois qu’un nouveau prospect est créé dans Salesforce, une action *envoyer un e-mail* s’exécute par le biais du connecteur SMTP avec une notification signalant la création du nouveau prospect.

1. Saisissez *salesforce* dans la zone de recherche sur le concepteur d’applications logiques, puis sélectionnez le déclencheur **Salesforce - Création d’un objet** .  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. Le contrôle **Lors de la création d’un objet** s’affiche.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Sélectionnez le **Type d’objet** puis sélectionnez *Prospect* à partir de la liste d’objets. Lors de cette étape, vous créez un déclencheur qui informe votre application logique de la création d’un nouveau prospect dans Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Le déclencheur a été créé.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Utiliser une action SMTP
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Une fois le déclencheur ajouté, procédez comme suit pour ajouter une action SMTP qui se produira chaque fois qu’un nouveau prospect sera créé dans Salesforce.

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action à exécuter lorsqu’un prospect est créé.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Sélectionnez **Ajouter une action**. Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Entrez *smtp* pour rechercher les actions associées à SMTP.  
4. Sélectionnez **SMTP - Envoyer un message électronique** comme action à exécuter lorsque le prospect est créé. Le bloc de contrôles de l’action s’affiche. Vous devez établir votre connexion SMTP dans le bloc du concepteur, si ce n’est pas déjà fait.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Entrez les informations de messagerie de votre choix dans le bloc **SMTP - Envoyer un message électronique**.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Enregistrez votre travail afin d’activer votre workflow.  

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).