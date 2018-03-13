---
title: Traiter des messages par lots (groupe ou collection de messages) - Azure Logic Apps | Microsoft Docs
description: "Envoyer et recevoir des messages à traiter par lots dans les applications logiques"
keywords: lot, traitement par lots
author: jonfancey
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/7/2017
ms.author: LADocs; estfan; jonfan
ms.openlocfilehash: de519084a4f172ad984c78727123835eeb9deaef
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="send-receive-and-batch-process-messages-in-logic-apps"></a>Envoyer, recevoir et traiter par lots des messages dans les applications logiques

Pour traiter des groupes de messages, vous pouvez envoyer des éléments de données, ou des messages, vers un *lot*, puis traiter ce lot. Cette méthode est utile lorsque vous souhaitez regrouper les éléments de données d’une certaine manière et les traiter ensemble. 

Vous pouvez créer des applications logiques destinées à recevoir des éléments regroupés dans un lot grâce au déclencheur **Lot**. Vous pouvez créer des applications logiques destinées à envoyer des éléments regroupés dans un lot grâce à **l’opération de traitement par lots**.

Cette rubrique montre comment créer une solution de traitement par lot en effectuant les tâches suivantes : 

* [Créer une application logique destinée à recevoir et collecter des éléments regroupés dans un lot](#batch-receiver). Cette application logique « réceptrice de lots » spécifie les critères relatifs au nom et à la version du lot qui doivent être respectés avant que l’application logique ne publie et traite les éléments. 

* [Créer une application logique qui envoie les éléments vers un lot](#batch-sender). Cette application logique « expéditrice de lots » spécifie où envoyer les éléments, qui doivent se trouver dans une application logique réceptrice de lots existante. Vous pouvez également spécifier une clé unique (telle qu’un numéro de client) pour que le lot cible soit divisé en sous-ensembles, en fonction de cette clé. De cette façon, tous les éléments associés à cette clé sont collectés et traités ensemble. 

## <a name="requirements"></a>Configuration requise

Pour suivre cet exemple, vous avez besoin de ce qui suit :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Un compte de courrier de n’importe quel [fournisseur de messagerie pris en charge par Azure Logic Apps](../connectors/apis-list.md)

<a name="batch-receiver"></a>

## <a name="create-logic-apps-that-receive-messages-as-a-batch"></a>Créer des applications logiques destinées à recevoir des messages regroupés dans un lot

Avant de pouvoir envoyer des messages vers un lot, vous devez d’abord créer une application logique « réceptrice de lots » à l’aide du déclencheur **Lot**. De cette façon, vous pouvez sélectionner cette application logique réceptrice lorsque vous créez l’application logique expéditrice. Pour l’application réceptrice, vous devez spécifier le nom du lot, les critères de déclenchement, ainsi que d’autres paramètres. 

Les applications logiques expéditrices doivent savoir où envoyer les éléments. Les applications logiques réceptrices, quant à elles, n’ont rien à savoir sur les applications expéditrices.

1. Dans le [portail Azure](https://portal.azure.com), créez une application logique et nommez-la « BatchReceiver ». 

2. Dans le Concepteur d'applications logiques, ajoutez le déclencheur **Lot** qui démarre le flux de travail de votre application logique. Dans la zone de recherche, entrez « lot » comme filtre. Sélectionnez le déclencheur **Lot – Traiter les messages par lots**.

   ![Ajout du déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

3. Fournissez un nom pour le lot, puis spécifiez des critères pour son déclenchement, par exemple :

   * **Nom du lot** : nom utilisé pour identifier le lot (« TestBatch » dans cet exemple).
   * **Critères de déclenchement** : critères de déclenchement du lot, liés au nombre de messages, à la planification ou aux deux.
   
     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Nombre de messages** : nombre de messages devant être contenus dans un lot avant le déclenchement du traitement (« 5 » dans cet exemple).

     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Planification** : planification de lancement du traitement du lot, « toutes les cinq minutes » dans cet exemple.

     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/receive-batch-schedule-based.png)


4. Ajoutez une autre action qui envoie un e-mail lorsque le lot est déclenché. Chaque fois que le lot atteint cinq éléments ou dépasse cinq minutes, l’application logique envoie un e-mail.

   1. Sous le déclencheur de lot, sélectionnez **+ Nouvelle étape** > **Ajouter une action**.

   2. Dans la zone de recherche, entrez « e-mail » comme filtre.
   Sélectionnez un connecteur de messagerie en fonction de votre fournisseur de messagerie.
   
      Par exemple, si vous avez un compte professionnel ou scolaire, sélectionnez le connecteur Outlook Office 365. 
      Si vous avez un compte Gmail, sélectionnez le connecteur Gmail.

   3. Sélectionnez cette action pour votre connecteur : **{*fournisseur de messagerie*} - Envoyer un e-mail**

      Par exemple : 

      ![Sélection de l’action « Envoyer un message électronique » pour votre fournisseur de messagerie](./media/logic-apps-batch-process-send-receive-messages/add-send-email-action.png)

5. Si vous n’avez pas déjà créé une connexion pour votre fournisseur de messagerie, entrez votre adresse e-mail et votre mot de passe lorsque vous êtes invité à vous authentifier. En savoir plus sur [l’authentification à l’aide d’une adresse e-mail et d’un mot de passe](../logic-apps/quickstart-create-first-logic-app-workflow.md).

6. Définissez les propriétés de l’action que vous venez d’ajouter.

   * Dans la zone **À**, entrez l’adresse e-mail du destinataire. 
   À des fins de test, vous pouvez utiliser votre propre adresse e-mail.

   * Dans la zone **Objet**, lorsque la liste **Contenu dynamique** s’affiche, sélectionnez le champ **Nom de partition**.

     ![Dans la liste « Contenu dynamique », sélectionnez « Nom de partition »](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Dans une section ultérieure, vous pourrez spécifier une clé de partition unique afin de diviser le lot cible en plusieurs ensembles logiques vers lesquels envoyer des messages. 
     Chaque ensemble est associé à un numéro unique qui est généré par l’application logique expéditrice. 
     Cette fonctionnalité permet d’utiliser un lot comprenant plusieurs sous-ensembles et de définir chaque sous-ensemble avec le nom que vous fournissez.

   * Dans la zone **Corps**, lorsque la liste **Contenu dynamique** s’affiche, sélectionnez le champ **ID de message**.

     ![Pour « Corps », sélectionnez « ID de message »](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

     Étant donné que l’entrée de l’action Envoyer un message électronique se présente sous la forme d’un tableau, le concepteur ajoute automatiquement une boucle **For each** autour de l’action **Envoyer un message électronique**. 
     Cette boucle effectue l’action interne sur chaque élément du lot. 
     Étant donné que le déclencheur de lot est défini sur cinq éléments, vous recevez cinq e-mails chaque fois qu’un lot est déclenché.

7.  Maintenant que vous avez créé l’application logique réceptrice de lots, enregistrez-la.

    ![Enregistrer votre application logique](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

    > [!IMPORTANT]
    > Une partition a une limite de 5 000 messages ou 80 Mo. Si l’une des conditions est remplie, le lot peut être libéré, même si la condition définie par l’utilisateur n’est pas remplie.


<a name="batch-sender"></a>

## <a name="create-logic-apps-that-send-messages-to-a-batch"></a>Créer des applications logiques qui envoient des messages vers un lot

À présent, créez une ou plusieurs applications logiques qui envoient des éléments vers le lot défini par l’application logique réceptrice. Pour l’application expéditrice, vous spécifiez l’application logique réceptrice, le nom du lot, le contenu du message et tout autre paramètre nécessaire. Vous pouvez éventuellement fournir une clé de partition unique pour diviser le lot en sous-ensembles dont le but est de collecter les éléments associés à cette clé.

Les applications logiques expéditrices doivent savoir où envoyer les éléments. Les applications logiques réceptrices, quant à elles, n’ont rien à savoir sur les applications expéditrices.

1. Créez une autre application logique et nommez-la « BatchSender ».

   1. Dans la zone de recherche, entrez « récurrence » comme filtre. 
   Sélectionnez le déclencheur **Planification - Récurrence**

      ![Ajout du déclencheur « Planification - Récurrence »](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-receiver.png)

   2. Définissez la fréquence et l’intervalle d’exécution de l’application logique expéditrice sur une minute.

      ![Définition de la fréquence et de l’intervalle pour le déclencheur Récurrence](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-receiver-details.png)

2. Ajoutez une nouvelle étape pour envoyer des messages vers un lot.

   1. Sous le déclencheur Récurrence, choisissez **+ Nouvelle étape** > **Ajouter une action**.

   2. Dans la zone de recherche, entrez « lot » comme filtre. 

   3. Sélectionnez l’action **Envoyer les messages au lot - Choisir un workflow Logic Apps avec déclencheur de lot**.

      ![Sélection de l’option « Envoyer les messages au lot »](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

   4. Maintenant, sélectionnez l’application logique « BatchReceiver » créée précédemment, qui s’affiche désormais en tant qu’action.

      ![Sélection de l’application logique « BatchReceiver »](./media/logic-apps-batch-process-send-receive-messages/send-batch-select-batch-receiver.png)

      > [!NOTE]
      > La liste affiche également toutes les autres applications logiques qui sont associées à un déclencheur de lot.

3. Définissez les propriétés du lot.

   * **Nom du lot** : nom défini par l’application logique réceptrice, (« TestBatch » dans cet exemple) qui est validé lors de l’exécution.

     > [!IMPORTANT]
     > Veillez à ne pas modifier le nom du lot, car il doit correspondre au nom de lot spécifié par l’application logique réceptrice.
     > Si vous modifiez son nom, l’application logique expéditrice échouera.

   * **Contenu du message** : contenu du message que vous souhaitez envoyer. 
   Pour cet exemple, ajoutez cette expression qui insère la date et l’heure actuelles dans le contenu du message que vous envoyez vers le lot :

     1. Lorsque la liste **Contenu dynamique** s’affiche, choisissez **Expression**. 
     2. Entrez l’expression **utcnow()**, puis choisissez **OK**. 

        ![Dans « Contenu du message », choisissez « Expression ». Entrez « utcnow() ».](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details.png)

4. À présent, définissez une partition pour le lot. Dans l’action « BatchReceiver », choisissez **Afficher les options avancées**.

   * **Nom de partition** : clé de partition unique facultative à utiliser pour diviser le lot cible. Pour cet exemple, ajoutez une expression qui génère un nombre aléatoire compris entre 1 et 5.
   
     1. Lorsque la liste **Contenu dynamique** s’affiche, choisissez **Expression**.
     2. Entrez l’expression **rand(1,6)**.

        ![Définition d’une partition pour le lot cible](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-partition-advanced-options.png)

        Cette fonction **rand** génère un nombre compris entre 1 et 5. 
        Vous divisez donc ce lot en cinq partitions numérotées, définies dynamiquement par cette expression.

   * **ID du message** : identificateur de message facultatif. Lorsqu’il est vide, c’est un GUID généré. 
   Pour cet exemple, laissez cette zone vide.

5. Enregistrez votre application logique. Votre application logique expéditrice doit désormais ressembler à ceci :

   ![Enregistrement de l’application logique](./media/logic-apps-batch-process-send-receive-messages/send-batch-receiver-details-finished.png)

## <a name="test-your-logic-apps"></a>Tester les applications logiques

Pour tester votre solution de traitement par lots, laissez vos applications logiques s’exécuter pendant quelques minutes. Vous allez commencer à recevoir des e-mails par groupes de cinq, tous avec la même clé de partition.

Votre application logique BatchSender s’exécute chaque minute, génère un nombre aléatoire compris entre 1 et 5 et utilise ce numéro généré comme clé de partition pour le lot cible où sont envoyés les messages. Chaque fois que le lot atteint cinq éléments ayant une même clé de partition, l’application logique BatchReceiver est déclenchée et envoie un e-mail.

> [!IMPORTANT]
> Lorsque vous avez terminé vos tests, veillez à désactiver l’application logique BatchSender afin d’arrêter l’envoi de messages et éviter la surcharge de la boîte de réception.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des définitions d’application logique à l’aide de JSON](../logic-apps/logic-apps-author-definitions.md)
* [Créer une application sans serveur dans Visual Studio avec Azure Logic Apps et Azure Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestion des exceptions et journalisation des erreurs pour les applications logiques](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
