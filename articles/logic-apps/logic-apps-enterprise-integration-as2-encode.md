---
title: Encoder des messages AS2 - Azure Logic Apps | Microsoft Docs
description: "Comment utiliser l’encodeur AS2 dans Enterprise Integration Pack pour Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bccc8f81a3a664e090796ae8a3cbb38c890c0479
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Encodez des messages AS2 pour Azure Logic Apps avec Enterprise Integration Pack

Utilisez le connecteur Encode AS2 Message pour créer des conditions de transmission de messages à la fois sûres et fiables. Ce connecteur fournit des fonctionnalités de signature numérique, de cryptage et d’accusés de réception par vérification des notifications de messages (Message Disposition Notifications, MDN), ce qui renforce par ailleurs la non-répudiation.

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure. Vous devez disposer d’un compte d’intégration pour pouvoir utiliser le connecteur Encode AS2 Message.
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration
* Un [contrat AS2](logic-apps-enterprise-integration-as2.md) déjà défini dans votre compte d’intégration

## <a name="encode-as2-messages"></a>Encoder des messages AS2

1. [Créez une application logique](quickstart-create-first-logic-app-workflow.md).

2. Le connecteur Encode AS2 Message ne possède aucun déclencheur, ce qui signifie que vous devez ajouter un déclencheur pour le démarrage de votre application logique, par exemple un déclencheur de requête. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action à votre application logique.

3.  Dans la zone de recherche, entrez le filtre « AS2 ». Sélectionnez **AS2 - Encode AS2 Message**.
   
    ![Recherchez « AS2 »](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Si vous n’avez pas encore créé de connexions à votre compte d’intégration, vous êtes invité à le faire à cette étape. Donnez un nom à votre connexion, puis sélectionnez le compte d’intégration auquel vous souhaitez vous connecter. 
   
    ![Créer une connexion de compte d’intégration](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Les propriétés marquées d’un astérisque sont obligatoires.

    | Propriété | Détails |
    | --- | --- |
    | Nom de connexion * |Entrez un nom pour votre connexion. |
    | Compte d’intégration * |Entrez un nom pour votre compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure. |

5.  Lorsque vous avez terminé, les détails de votre connexion doivent apparaître tels qu’indiqués dans l’exemple suivant. Pour terminer la création de votre connexion, sélectionnez l’option **Créer**.
   
    ![détails de la connexion d’intégration](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Une fois que votre connexion est créée, comme indiqué dans cet exemple, renseignez les informations des champs **AS2-From**, **AS2-To** (comme configuré dans votre contrat) et **Corps**, qui correspond à la charge utile du message.
   
    ![remplir les champs obligatoires](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Détails sur l’encodeur AS2

Le connecteur Encode AS2 effectue les tâches suivantes : 

* Applique les en-têtes AS2/HTTP
* Signe les messages sortants (si configuré)
* Chiffre les messages sortants (si configuré)
* Compresse le message (si configuré)

## <a name="try-this-sample"></a>Testez cet exemple

Pour déployer une application logique totalement opérationnelle dans le cadre d’un exemple de scénario AS2, consultez le [scénario et le modèle d’application logique AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Afficher le swagger
Consultez les [détails sur Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 

