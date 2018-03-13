---
title: "Décoder des messages AS2 - Azure Logic Apps | Microsoft Docs"
description: "Comment utiliser le décodeur AS2 dans Enterprise Integration Pack pour Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 4acae9f1837069c494985ff1456979490485f609
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Décoder des messages AS2 pour Azure Logic Apps avec Enterprise Integration Pack 

Utilisez le connecteur Decode AS2 Message pour créer des conditions de transmission de messages à la fois sûres et fiables. Ce connecteur fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN).

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure. Vous devez disposer d’un compte d’intégration pour pouvoir utiliser le connecteur Decode AS2 Message.
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration
* Un [contrat AS2](logic-apps-enterprise-integration-as2.md) déjà défini dans votre compte d’intégration

## <a name="decode-as2-messages"></a>Décoder des messages AS2

1. [Créez une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Le connecteur Decode AS2 Message ne possède aucun déclencheur, ce qui signifie que vous devez ajouter un déclencheur pour le démarrage de votre application logique, par exemple un déclencheur de requête. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action à votre application logique.

3.  Dans la zone de recherche, entrez le filtre « AS2 ». Sélectionnez **AS2 - Decode AS2 Message**.
   
    ![Recherchez « AS2 »](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Si vous n’avez pas encore créé de connexions à votre compte d’intégration, vous êtes invité à le faire à cette étape. Donnez un nom à votre connexion, puis sélectionnez le compte d’intégration auquel vous souhaitez vous connecter.
   
    ![Créer une connexion d’intégration](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Les propriétés marquées d’un astérisque sont obligatoires.

    | Propriété | Détails |
    | --- | --- |
    | Nom de connexion * |Entrez un nom pour votre connexion. |
    | Compte d’intégration * |Entrez un nom pour votre compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure. |

5.  Lorsque vous avez terminé, les détails de votre connexion doivent apparaître tels qu’indiqués dans l’exemple suivant. Pour terminer la création de votre connexion, sélectionnez l’option **Créer**.

    ![détails de la connexion d’intégration](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Une fois votre connexion créée, comme indiqué dans cet exemple, sélectionnez le **corps** et les **en-têtes** des sorties de requête.
   
    ![connexion d’intégration créée](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Par exemple : 

    ![Sélectionnez le corps et les en-têtes à partir des sorties de requête.](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Détails sur le décodeur AS2

Le connecteur Decode AS2 effectue les tâches suivantes : 

* Traite les en-têtes AS2/HTTP
* Vérifie la signature (si configuré)
* Déchiffre les messages (si configuré)
* Compresse le message (si configuré)
* Rapproche un MDN reçu avec le message sortant d’origine
* Met à jour et met en corrélation les enregistrements dans la base de données de non-répudiation
* Écrit les enregistrements pour le rapport d’état AS2
* Les contenus de charge utile de sortie sont codés en base64
* Détermine si un MDN est requis et si le MDN doit être synchrone ou asynchrone d’après la configuration dans l’accord AS2
* Génère un MDN synchrone ou asynchrone (basé sur les configurations de l’accord)
* Définit les propriétés et les jetons de corrélation sur le MDN

## <a name="try-this-sample"></a>Testez cet exemple

Pour déployer une application logique totalement opérationnelle dans le cadre d’un exemple de scénario AS2, consultez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Afficher le swagger
Consultez les [détails sur Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md) 

