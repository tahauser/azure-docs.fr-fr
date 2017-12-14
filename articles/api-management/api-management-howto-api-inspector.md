---
title: "Déboguer vos API à l’aide du suivi des demandes dans la Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour apprendre à inspecter les étapes du traitement des demandes dans la Gestion des API Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7b9bec7927169b9d820c095a7d11705264e7dcfe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="debug-your-apis-using-request-tracing"></a>Déboguer vos API à l’aide du suivi des demandes

Ce didacticiel explique comment inspecter le traitement des demandes pour mieux déboguer et résoudre les problèmes liés à votre API. 

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Suivre un appel

![Inspecteur d’API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Prérequis

+ Suivez le guide de démarrage rapide suivant : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).
+ Suivez également le didacticiel suivant : [Importer et publier votre première API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="trace-a-call"></a>Suivre un appel

1. Sélectionnez **API**.
2. Cliquez sur **API de conférence de démonstration** dans votre liste d’API.
3. Sélectionnez l’opération **GetSpeakers**.
4. Basculez vers l’onglet **Test**.
5. Veillez à inclure un en-tête HTTP nommé **Ocp-Apim-Trace** dont la valeur est définie sur **true**.
6. Cliquez sur **Envoyer** pour effectuer un appel d’API. 
7. Attendez la fin de l’appel. 
8. Accédez à l’onglet **Trace** dans la **console d’API**. Vous pouvez cliquer sur les liens suivants pour accéder aux informations de suivi détaillées : **Entrant**, **Principal**, **Sortant**.

    La section **Entrant** contient la demande d’origine reçue par le service Gestion des API de la part de l’appelant et toutes les stratégies appliquées à la demande, y compris les stratégies rate-limit et set-header que nous avons ajoutées à l’étape 2.

    La section **Principal** affiche les demandes envoyées par le service Gestion des API au backend d’API et la réponse qu’il a reçue.
    
    La section **Sortant** présente toutes les stratégies appliquées à la réponse avant son envoi à l’appelant.
 
    > [!TIP]
    > Chaque étape indique également le temps écoulé depuis la réception de la demande par le service Gestion des API.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Suivre un appel

Passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Utiliser des révisions](api-management-get-started-revise-api.md)
