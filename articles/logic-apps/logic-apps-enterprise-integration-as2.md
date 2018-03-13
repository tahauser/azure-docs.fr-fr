---
title: "Messages AS2 pour l’intégration d’entreprise B2B - Azure Logic Apps | Microsoft Docs"
description: "Échangez des messages AS2 dans le cadre d’une intégration d’entreprise B2B avec Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c9b7e1a9-4791-474c-855f-988bd7bf4b7f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 8c6dbc507cd1b79fe9424071bc3eb8bf24331a16
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="exchange-as2-messages-for-enterprise-integration-with-logic-apps"></a>Échangez des messages AS2 dans le cadre d’une intégration d’entreprise avec Logic Apps

Avant de pouvoir échanger des messages AS2 pour Azure Logic Apps, vous devez créer un contrat AS2 et le stocker dans votre compte d’intégration. Voici les étapes de création d’un contrat AS2.

## <a name="before-you-start"></a>Avant de commencer

Voici les éléments dont vous avez besoin :

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) déjà défini et associé à votre abonnement Azure
* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) déjà définis dans votre compte d’intégration et configurés avec le qualificateur AS2 sous **Identités d’entreprise**

> [!NOTE]
> Lorsque vous créez un contrat, le contenu du fichier de contrat doit correspondre au type de contrat.    

Une fois que vous avez [créé un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md) et [ajouté des partenaires](logic-apps-enterprise-integration-partners.md), vous pouvez créer un contrat AS2 en procédant comme suit.

## <a name="create-an-as2-agreement"></a>Créer un contrat AS2

1.  Connectez-vous au [portail Azure](http://portal.azure.com "portail Azure").  

2. Dans le menu principal Azure, sélectionnez **Tous les services**. Entrez « intégration » dans la zone de recherche, puis sélectionnez **Comptes d’intégration**.

   ![Chercher votre compte d’intégration](./media/logic-apps-enterprise-integration-as2/overview-1.png)

   > [!TIP]
   > Si vous ne voyez pas l’option **Tous les services**, vous devez commencer par développer le menu. En haut du menu réduit, sélectionnez **Afficher les étiquettes de texte**.

3. Sous **Comptes d’intégration**, sélectionnez le compte d’intégration dans lequel vous souhaitez créer le contrat.

   ![Sélectionnez le compte d’intégration dans lequel vous souhaitez créer le contrat](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Choisissez la mosaïque **Contrats**. Si vous ne voyez pas la mosaïque Contrats, commencez par ajouter la mosaïque.

    ![Choisissez la mosaïque « Contrats »](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. Sous **Contrats**, choisissez **Ajouter**.

    ![Choisir « Ajouter »](./media/logic-apps-enterprise-integration-as2/agreement-2.png)

6. Sous **Ajouter**, entrez le **nom** de votre contrat. Pour le **type de contrat**, sélectionnez **AS2**. Sélectionnez le **partenaire hôte**, **l’identité de l’hôte**, le **partenaire invité** et **l’identité de l’invité** pour votre contrat.

    ![Renseigner les détails relatifs au contrat](./media/logic-apps-enterprise-integration-as2/agreement-3.png)  

    | Propriété | Description |
    | --- | --- |
    | NOM |Nom du contrat. |
    | Type de contrat | Doit être AS2 |
    | Partenaire hôte |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire hôte représente l’organisation qui configure le contrat. |
    | Identité de l’hôte |Identificateur du partenaire hôte. |
    | Partenaire invité |Un contrat nécessite un partenaire hôte et un partenaire invité. Le partenaire invité représente l’organisation qui travaille avec le partenaire hôte. |
    | identité de l’invité |Identificateur du partenaire invité. |
    | Paramètres de réception |Ces propriétés s’appliquent à tous les messages reçus par un contrat. |
    | Paramètres d’envoi |Ces propriétés s’appliquent à tous les messages envoyés par un contrat. |

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configuration du traitement des messages reçus

Maintenant que vous avez défini les propriétés du contrat, vous pouvez configurer la manière dont ce contrat identifie et traite les messages entrants reçus par votre partenaire par l’intermédiaire de ce contrat.

1.  Sous **Ajouter**, sélectionnez **Paramètres de réception**.
Configurez ces propriétés selon le contrat conclu avec le partenaire qui échange des messages avec vous. Pour obtenir les descriptions des propriétés, consultez le tableau de cette section.

    ![Configurer les « Paramètres de réception »](./media/logic-apps-enterprise-integration-as2/agreement-4.png)

2. Si vous le souhaitez, vous pouvez remplacer les propriétés des messages entrants en cochant la case **Remplacer les propriétés du message**.

3. Sélectionnez **Le message doit être signé** pour demander la signature de tous les messages entrants. Dans la liste **Certificat**, sélectionnez un [certificat public de partenaire invité](../logic-apps/logic-apps-enterprise-integration-certificates.md) existant pour valider la signature dans les messages. Sinon, créez le certificat si vous n’en possédez pas.

4.  Sélectionnez **Le message doit être chiffré** pour exiger que tous les messages entrants soient chiffrés. Dans la liste **Certificat**, sélectionnez un [certificat privé de partenaire hôte](../logic-apps/logic-apps-enterprise-integration-certificates.md) existant pour déchiffrer les messages entrants. Sinon, créez le certificat si vous n’en possédez pas.

5. Pour exiger que les messages soit compressés, sélectionnez **Le message doit être compressé**.

6. Pour envoyer une notification de réception du message (MDN) synchrone pour les messages reçus, sélectionnez **Envoyer un MDN**.

7. Pour envoyer un MDN signé pour les messages reçus, sélectionnez **Envoyer un MDN signé**.

8. Pour envoyer un MDN asynchrone pour les messages reçus, sélectionnez **Envoyer un MDN asynchrone**.

9. Une fois que vous avez terminé, cliquez sur **OK** pour enregistrer vos paramètres.

Votre contrat est maintenant prêt à traiter les messages entrants qui sont conformes aux paramètres que vous avez sélectionnés.

| Propriété | Description |
| --- | --- |
| Override message properties |Indique que les propriétés dans les messages reçus peuvent être remplacées. |
| Le message doit être signé |Exige que les messages soient signés numériquement. Configurez le certificat public du partenaire invité pour la vérification de signature.  |
| Le message doit être chiffré |Exige que les messages soient chiffrés. Les messages non chiffrés sont rejetés. Configurez le certificat privé du partenaire hôte pour déchiffrer les messages.  |
| Le message doit être compressé |Exige que les messages soient compressés. Les messages non compressés sont rejetés. |
| Texte du MDN |La notification de réception du message (MDN) par défaut à envoyer à l’expéditeur du message. |
| Send MDN (Envoyer MDN) |Exige que le MDN soit envoyé. |
| Send signed MDN (Envoyer MDN signé) |Exige que le MDN soit signé. |
| MIC Algorithm (Algorithme MIC) |Sélectionnez l’algorithme à utiliser pour la signature du message. |
| Send asynchronous MDN (Envoyer MDN asynchrone) | Exige que les messages soient envoyés de manière asynchrone. |
| URL | Spécifiez l’URL vers laquelle envoyer les MDN. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configuration de l’envoi des messages

Vous pouvez configurer la manière dont votre contrat identifie et traite les messages sortants que vous envoyez à vos partenaires par l’intermédiaire de ce contrat.

1.  Sous **Ajouter**, sélectionnez **Paramètres d’envoi**.
Configurez ces propriétés selon le contrat conclu avec le partenaire qui échange des messages avec vous. Pour obtenir les descriptions des propriétés, consultez le tableau de cette section.

    ![Définissez les propriétés de « Paramètres d’envoi »](./media/logic-apps-enterprise-integration-as2/agreement-51.png)

2. Sélectionnez l’option **Activer la signature des messages** pour envoyer des messages signés à votre partenaire. Pour signer les messages, sélectionnez *l’algorithme MIC de certificat privé du partenaire hôte* dans la liste **Algorithme MIC**. Dans la liste **Certificat**, sélectionnez un [certificat privé de partenaire hôte](../logic-apps/logic-apps-enterprise-integration-certificates.md).

3. Sélectionnez l’option **Activer le chiffrement des messages** pour envoyer des messages chiffrés au partenaire. Pour chiffrer les messages, sélectionnez *l’algorithme de certificat public du partenaire invité* dans la liste **Algorithme de chiffrement**.
Dans la liste **Certificat**, sélectionnez un [certificat public de partenaire invité](../logic-apps/logic-apps-enterprise-integration-certificates.md).

4. Pour compresser les messages, sélectionnez **Activer la compression des messages**.

5. Sélectionnez l’option **Dérouler les en-têtes HTTP** pour dérouler l’en-tête de type de contenu HTTP sur une seule ligne.

6. Pour recevoir des MDN synchrones pour les messages envoyés, sélectionnez **Exiger le MDN**.

7. Pour recevoir des MDN signés pour les messages envoyés, sélectionnez **Exiger le MDN signé**.

8. Pour recevoir des MDN asynchrones pour les messages envoyés, sélectionnez **Exiger le MDN asynchrone**. Si vous sélectionnez cette option, saisissez l’URL à laquelle envoyer les MDN.

9. Pour exiger la non-répudiation de réception, sélectionnez **Activer NRR**.  

10. Pour spécifier le format d’algorithme à utiliser dans le MIC ou pour la signature dans les en-têtes sortants des messages AS2 ou des MDN, sélectionnez **Format de l’algorithme SHA2**.  

11. Une fois que vous avez terminé, cliquez sur **OK** pour enregistrer vos paramètres.

Votre contrat est maintenant prêt à traiter les messages sortants qui sont conformes aux paramètres que vous avez sélectionnés.

| Propriété | Description |
| --- | --- |
| Enable message signing (Activer la signature des messages) |Exige que tous les messages envoyés à partir du contrat soient signés. |
| MIC Algorithm (Algorithme MIC) |L’algorithme à utiliser pour la signature du message. Configurez l’algorithme MIC de certificat privé du partenaire hôte pour signer les messages. |
| Certificat |Sélectionnez le certificat à utiliser pour la signature des messages. Configurez le certificat privé du partenaire hôte pour signer les messages. |
| Enable message encryption (Activer le chiffrement du message) |Exige que tous les messages envoyés à partir du contrat soient chiffrés. Configure l’algorithme de certificat public du partenaire invité pour chiffrer les messages. |
| Algorithme de chiffrement |L’algorithme de chiffrement à utiliser pour le chiffrement des messages. Configure le certificat public du partenaire invité pour chiffrer les messages. |
| Certificat |Le certificat à utiliser pour chiffrer les messages. Configure le certificat privé du partenaire invité pour chiffrer les messages. |
| Enable message compression (Activer la compression des messages) |Exige que tous les messages envoyés à partir du contrat soient compressés. |
| Dérouler les en-têtes HTTP |Déroule l’en-tête de type de contenu HTTP sur une seule ligne. |
| Exiger le MDN |Exige l’envoi d’un MDN pour tous les messages envoyés à partir du contrat. |
| Exiger le MDN signé |Exige que tous les MDN envoyés dans le cadre de ce contrat soient signés. |
| Exiger le MDN asynchrone |Exige l’envoi d’un MDN asynchrone à ce contrat. |
| URL |Spécifiez l’URL vers laquelle envoyer les MDN. |
| Enable NRR (Activer NRR) |Exige la non-répudiation de réception (NRR), un attribut de communication qui fournit une preuve de réception des données. |
| Format de l’algorithme SHA2 |Sélectionnez le format d’algorithme à utiliser dans le MIC ou pour la signature dans les en-têtes sortants des messages AS2 ou des MDN. |

## <a name="find-your-created-agreement"></a>Comment retrouver le contrat que vous avez créé

1. Après avoir défini toutes les propriétés de votre contrat, sur la page **Ajouter**, cliquez sur **OK** pour terminer la création de votre contrat et revenir à votre compte d’intégration.

    Le contrat que vous venez d’ajouter s’affiche dans votre liste **Contrats**.

2. Vous pouvez également afficher vos contrats dans la vue d’ensemble de votre compte d’intégration. Dans le menu de votre compte d’intégration, choisissez **Vue d’ensemble**, puis sélectionnez la mosaïque **Contrats**. 

   ![Sélectionner la mosaïque « Contrats » pour afficher tous les contrats](./media/logic-apps-enterprise-integration-as2/agreement-6.png)

## <a name="view-the-swagger"></a>Afficher le swagger
Consultez les [détails sur Swagger](/connectors/as2/). 

## <a name="next-steps"></a>étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Découvrez Enterprise Integration Pack")  
