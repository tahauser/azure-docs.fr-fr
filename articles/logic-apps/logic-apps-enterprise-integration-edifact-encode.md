---
title: Encoder des messages EDIFACT - Azure Logic Apps | Microsoft Docs
description: "Validation EDI et génération du code XML avec l’encodeur EDIFACT Message dans Enterprise Integration Pack pour Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 799d444632b67788520be8a777ec656076022583
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Encoder des messages EDIFACT pour Azure Logic Apps avec Enterprise Integration Pack

Avec le connecteur Encode EDIFACT Message, vous pouvez valider l’EDI et les propriétés spécifiques au partenaire, générer un document XML pour chaque document informatisé et demander un accusé de réception technique ou fonctionnel, ou les deux.
Pour utiliser ce connecteur, vous devez ajouter le connecteur à un déclencheur existant dans votre application logique.

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure. Vous devez disposer d’un compte d’intégration pour pouvoir utiliser le connecteur Encode EDIFACT Message. 
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration
* Un [contrat EDIFACT](logic-apps-enterprise-integration-edifact.md) déjà défini dans votre compte d’intégration

## <a name="encode-edifact-messages"></a>Encoder des messages EDIFACT

1. [Créez une application logique](quickstart-create-first-logic-app-workflow.md).

2. Le connecteur Encode EDIFACT Message ne possède aucun déclencheur, ce qui signifie que vous devez ajouter un déclencheur pour le démarrage de votre application logique, par exemple un déclencheur de requête. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action à votre application logique.

3.  Dans la zone de recherche, entrez « EDIFACT » comme filtre. Sélectionnez **Encode EDIFACT Message par nom de contrat** ou **Encode to EDIFACT message par identités**.
   
    ![recherche EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Si vous n’avez pas encore créé de connexions à votre compte d’intégration, vous êtes invité à le faire à cette étape. Donnez un nom à votre connexion, puis sélectionnez le compte d’intégration auquel vous souhaitez vous connecter.

    ![créer une connexion de compte d’intégration](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Les propriétés marquées d’un astérisque sont obligatoires.

    | Propriété | Détails |
    | --- | --- |
    | Nom de connexion * |Entrez un nom pour votre connexion. |
    | Compte d’intégration * |Entrez un nom pour votre compte d’intégration. Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le même emplacement Azure. |

5.  Lorsque vous avez terminé, les détails de votre connexion doivent apparaître tels qu’indiqués dans l’exemple suivant. Pour terminer la création de votre connexion, sélectionnez l’option **Créer**.

    ![détails de connexion de compte d’intégration](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Votre connexion est maintenant créée.

    ![connexion de compte d’intégration créée](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Encode EDIFACT Message par nom de contrat

Si vous choisissez d’encoder des messages EDIFACT par nom du contrat, ouvrez la liste **Nom de l’accord EDIF**, et entrez ou sélectionnez votre nom de contrat EDIFACT existant. Saisissez le message XML à encoder.

![Indiquez le nom du contrat EDIFACT et le nom du message XML à encoder](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Encode EDIFACT Message par identités

Si vous choisissez d’encoder des messages EDIFAC par identités, indiquez l’identificateur et le qualificateur de l’expéditeur ainsi que l’identificateur et le qualificateur du récepteur tels que configurés dans votre contrat EDIFACT. Sélectionnez le message XML à encoder.

![Renseigner les identités de l’expéditeur et du destinataire, sélectionner le message XML à encoder](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Détails sur l’encodage EDIFACT

Le connecteur Encode EDIFACT effectue les tâches suivantes : 

* Résout le contrat en mettant en correspondance les identificateurs et les qualificateurs de l’expéditeur et du récepteur
* Sérialise l’échange EDI en convertissant les messages codés au format XML en documents informatisés EDI au sein de l’échange.
* Applique les segments d’en-tête et de code de fin du document informatisé
* Génère un numéro de contrôle d’échange, un numéro de contrôle de groupe et un numéro de contrôle de document informatisé pour chaque échange sortant
* Remplace les séparateurs dans les données de charge utile
* Valide l’EDI et les propriétés spécifiques au partenaire
  * Validation de schéma des éléments de données du document informatisé par rapport au schéma de message.
  * Validation EDI effectuée sur les éléments de données du document informatisé.
  * Validation étendue effectuée sur les éléments de données du document informatisé
* Génère un document XML pour chaque document informatisé.
* Demande un accusé de réception fonctionnel et/ou technique (si configuré).
  * En tant qu’accusé de réception technique, le message CONTRL indique la réception d’un échange.
  * En tant qu’accusé de réception fonctionnel, le message CONTRL indique l’acceptation ou le rejet du message, du groupe ou de l’échange reçu, en fournissant une liste des erreurs ou des fonctionnalités non prises en charge

## <a name="view-swagger-file"></a>Afficher le fichier Swagger
Pour afficher les détails Swagger du connecteur EDIFACT, consultez [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack") 

