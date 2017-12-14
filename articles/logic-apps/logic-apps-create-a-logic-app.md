---
title: "Automatiser les flux de travail entre des systèmes et des services cloud - Azure Logic Apps| Microsoft Docs"
description: "Créer des applications logiques pour automatiser les flux de travail pour des scénarios d’intégration des systèmes et des Applications d’Entreprise (IAE)"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "flux de travail, applications cloud, services cloud, processus d’entreprise, intégration de systèmes, intégration d’applications d’entreprise, IAE"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatiser votre premier flux de travail pour traiter les données avec une application logique

Pour intégrer des systèmes et services plus rapidement pour votre organisation, vous pouvez automatiser les flux de travail et processus d’entreprise avec [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Ce démarrage rapide explique avec quelle facilité vous pouvez générer et exécuter un flux de travail automatisé en créant une application logique. L’exemple d’application montre comment automatiser un flux de travail qui cherche dans un flux RSS de site web les nouveaux éléments et envoie un e-mail pour chaque élément.

Cet exemple d’application logique envoie un e-mail à l’exemple suivant :

![E-mail envoyé en raison d’un nouvel élément dans le flux RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Et voici le flux de travail d’application logique global que vous créez :

![Vue d’ensemble - Exemple d’application logique](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

Dans ce guide de démarrage rapide, vous apprenez à :

> [!div class="checklist"]
> * Créez une application logique vide.
> * Ajoutez un déclencheur pour démarrer le flux de travail lorsqu’un nouvel élément s’affiche dans le flux RSS.
> * Ajoutez une action pour envoyer un e-mail avec les informations relatives à l’élément du flux RSS.
> * Testez votre flux de travail d’application logique.

Si vous n’avez pas d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

* Un compte de messagerie de n’importe quel fournisseur de messagerie pris en charge par Azure Logic Apps pour envoyer les notifications. Vous pouvez par exemple utiliser Office 365 Outlook, Outlook.com ou Gmail. Pour connaître les autres connecteurs de messagerie pris en charge, [passez en revue la liste des connecteurs](https://docs.microsoft.com/connectors/). Ce démarrage rapide utilise Office 365 Outlook.

  > [!TIP]
  > Si vous disposez d’un [compte Microsoft](https://account.microsoft.com/account) personnel, vous possédez un compte Outlook.com. Sinon, si vous disposez d’un compte Azure professionnel ou scolaire, vous possédez un compte Outlook 365 Office.

* Un lien vers le flux RSS d’un site web. Cet exemple utilise le [flux RSS pour les témoignages du site web Reuters](http://feeds.reuters.com/reuters/topNews) : `http://feeds.reuters.com/reuters/topNews`

Ce démarrage rapide ne requiert pas d’écriture de code, mais Logic Apps prend en charge d’autres scénarios qui utilisent du code, par exemple, l’exécution de votre propre code d’application logique avec [Azure Functions](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Créer une application logique vide 

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). 

2. Dans le menu principal d’Azure, choisissez **Nouveau** > **Intégration d’entreprise** > **Application logique**.

   ![portail Azure, nouveau, intégration d’entreprise, application logique](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Créez votre application logique avec les paramètres spécifiés dans le tableau sous cette image :

   ![Spécifier les détails de l’application logique](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *nom-de-votre-application-logique* | Donnez un nom unique à l’application logique. | 
   | **Abonnement** | *nom-de-votre-abonnement-Azure* | Sélectionnez l’abonnement Azure que vous souhaitez utiliser. | 
   | **Groupe de ressources** | *nom-de-votre-groupe-de-ressources-Azure* | Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) pour cette application logique et pour organiser toutes les ressources associées à cette application. | 
   | **Emplacement** | *région-de-votre-centre-de-données-Azure* | Sélectionnez la région du centre de données où déployer votre application logique, par exemple Ouest des États-Unis. | 
   | **Log Analytics** | Off | Activez la journalisation des diagnostics pour votre application logique, mais pour ce démarrage rapide, conservez le paramètre **Off**. | 
   |||| 

4. Lorsque vous êtes prêt, sélectionnez **Épingler au tableau de bord**. De cette façon, votre application logique apparaît automatiquement sur votre tableau de bord Azure et s’ouvre après le déploiement. Cliquez sur **Créer**.

   > [!NOTE]
   > Si vous ne souhaitez pas épingler votre application logique, vous devez trouver et ouvrir manuellement votre application logique après le déploiement pour pouvoir continuer.

   Une fois qu’Azure a déployé votre application logique, le Concepteur Logic Apps s’ouvre et affiche une page contenant une vidéo de présentation. 
   Sous la vidéo, vous pouvez trouver des modèles courants d’applications logiques. 
   Ce démarrage rapide crée votre application logique à partir de zéro. 

5. Faites défiler la vidéo de présentation et les déclencheurs courants. Sous **Modèles**, choisissez **Application logique vide**.

   ![Choisir un modèle d’application logique vide](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Le Concepteur Logic Apps affiche les connecteurs disponibles et leurs déclencheurs, que vous utilisez pour démarrer les flux de travail de votre application logique.

   ![Déclencheurs d’application logique](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Ajouter un déclencheur pour détecter de nouveaux éléments

Chaque flux de travail d’application logique commence par un [déclencheur](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Le déclencheur s’active lorsqu’un événement spécifique survient ou lorsque de nouvelles données remplissent les conditions que vous avez définies. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique qui démarre et exécute votre flux de travail.

1. Dans la zone de recherche, entrez « rss » comme filtre. Sélectionnez le déclencheur suivant : **RSS - Lors de la publication d’un élément de flux** 

   ![Sélectionnez le déclencheur : « RSS - Lors de la publication d’un élément de flux »](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Indiquez le lien vers le flux RSS que vous souhaitez surveiller. Par exemple, `http://feeds.reuters.com/reuters/topNews`. Définissez l’intervalle et la fréquence de la périodicité. Cet exemple vérifie le flux toutes les cinq minutes.

   ![Définir un déclencheur avec le flux RSS, la fréquence et l’intervalle](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps crée une connexion au flux RSS.

   > [!TIP]
   > Pour simplifier l’affichage dans le concepteur, vous pouvez réduire et masquer les détails d’une forme : cliquez simplement dans la barre de titre de la forme.

3. Enregistrez votre travail. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

   ![Enregistrer votre application logique](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Votre application logique est à présent en ligne mais elle ne fait rien d’autre que vérifier le flux RSS. Par conséquent, ajoutons une action qui répond quand le déclencheur s’active.

## <a name="add-an-action-to-send-email"></a>Ajouter une action pour envoyer un e-mail

À présent que vous avez un déclencheur, ajoutez une [action](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) qui envoie un e-mail lorsqu’un nouvel élément apparaît dans le flux RSS. Votre flux de travail effectue cette action après l’activation du déclencheur.

1. Dans le Concepteur d’application logique, sous le déclencheur, sélectionnez **+ Nouvelle étape** > **Ajouter une action**.

   ![Ajouter une action](./media/logic-apps-create-a-logic-app/add-new-action.png)

   Le concepteur affiche les actions que votre application logique peut effectuer lorsque le déclencheur s’active.

   ![Sélection dans la liste d’actions](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Dans la zone de recherche, entrez «  envoyer e-mail » comme filtre. Recherchez et sélectionnez le connecteur de messagerie électronique que vous souhaitez utiliser. Puis sélectionnez l’action « Envoyer un courrier électronique » pour ce connecteur. Par exemple : 

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez Office 365 Outlook. 
   * Pour les comptes Microsoft personnels, sélectionnez Outlook.com. 
   * Pour les comptes Gmail, sélectionnez Gmail. 

   Ce démarrage rapide utilise Office 365 Outlook. 
   Si vous utilisez un autre fournisseur de messagerie électronique, les étapes restent les mêmes, mais votre interface utilisateur peut s’afficher différemment. 

   ![Sélectionnez l’action : « Office 365 Outlook - Envoyer un e-mail »](./media/logic-apps-create-a-logic-app/actions.png)

3. Lorsque vous êtes invité à saisir des informations d’identification, connectez-vous avec le nom d’utilisateur et le mot de passe de votre compte de messagerie. 

   Logic Apps crée une connexion à votre compte de messagerie.

4. Maintenant, spécifiez les données que vous souhaitez inclure dans l’e-mail. 

   1. Dans la zone **À**, entrez l’adresse e-mail du destinataire. 
   À des fins de test, vous pouvez utiliser votre propre adresse e-mail.

   2. Dans la zone **Objet**, entrez l’objet de l’e-mail. 
   Pour cet exemple, entrez « Nouvel élément RSS : » comme indiqué :

      ![Entrer l’objet du message électronique](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      Lorsque vous cliquez dans la zone d’édition, la **liste Ajouter du contenu dynamique** s’ouvre afin que vous puissiez sélectionner les champs de données disponibles à inclure dans votre action. 
      Si la liste de contenu dynamique ne s’ouvre pas, sous la zone d’édition correspondante, choisissez **Ajouter du contenu dynamique**.

   3. À partir de la liste **Ajouter du contenu dynamique**, sélectionnez **Titre du flux**, qui inclut le titre de l’élément dans l’e-mail.

      ![Entrer l’objet du message électronique](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      Lorsque vous avez terminé, l’objet de l’e-mail ressemble à l’exemple suivant :

      ![Titre du flux ajouté](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Si vous sélectionnez un champ qui contient un tableau, tel que **categories-item**, le concepteur ajoute automatiquement une boucle For each autour de l’action qui référence ce champ. De cette façon, votre application logique peut effectuer cette action sur chaque élément du tableau. 
      > 
      > Pour supprimer la boucle, choisissez le bouton de sélection (**...** ) sur la barre de titre de la boucle, puis **Supprimer**.

   4. Dans la zone **Corps**, entrez le contenu du corps de l’e-mail. 
   Pour cet exemple, entrez ce texte et sélectionnez ces champs :

      ![Ajouter du contenu pour le corps de l’e-mail](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Champ | Description | 
      | ----- | ----------- | 
      | **Titre du flux** | Affiche le titre de l’élément. | 
      | **Flux publié le** | Affiche la date et l’heure de publication de l’élément. | 
      | **Lien du flux principal** | Affiche l’URL de l’élément. | 
      ||| 

      > [!TIP]
      > Pour ajouter des lignes vides dans une zone d’édition, appuyez sur Maj + Entrée. 
      
5. Enregistrez votre travail. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

   ![Application logique terminée](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Exécuter votre flux de travail d’application logique

Pour lancer manuellement votre application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur. Sinon, vous pouvez attendre que votre application logique s’exécute selon la planification que vous avez configurée.

![Exécuter une application logique](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Si le flux RSS a de nouveaux éléments, votre application logique envoie un e-mail pour chaque nouvel élément. Par exemple, voici un exemple d’e-mail Outlook envoyé par cette application logique :

![E-mail envoyé en raison d’un nouvel élément dans le flux RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Si le flux ne comporte aucun nouvel élément, votre application logique ignore l’étape d’envoi de l’e-mail et attend le prochain intervalle de vérification. 

> [!TIP]
> Si vous ne recevez aucun e-mail, vérifiez votre dossier Courrier indésirable. Sinon, si vous ne savez pas si votre application logique s’est correctement exécutée, consultez [Dépanner votre application logique](../logic-apps/logic-apps-diagnosing-failures.md).

Félicitations ! Vous venez de créer et d’exécuter votre première application logique. Ce démarrage rapide vous a montré comment créer facilement et rapidement des flux de travail automatisés pour l’intégration de systèmes et de services.

## <a name="clean-up-resources"></a>Supprimer des ressources

Votre application logique continue son exécution, avec les frais potentiellement associés sur votre abonnement Azure, jusqu’à ce que vous désactiviez ou supprimiez votre application. En outre, lorsque vous créez des connexions pour votre application logique, les connexions sont conservées, même si vous supprimez votre application logique. 

Lorsque vous avez terminé, veillez à désactiver ou supprimer les ressources que vous ne souhaitez pas conserver ou pour lesquelles vous ne souhaitez pas être facturé. Pour supprimer toutes les ressources que vous avez créées pour ce démarrage rapide, supprimez le groupe de ressources Azure que vous avez créé pour cette application logique. 

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

Si vous ne souhaitez conserver aucun élément lié à votre application logique, supprimez le groupe de ressources que vous avez créé pour ce démarrage rapide et toutes les ressources associées. En savoir plus sur [la façon de gérer des groupes de ressources Azure](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. Dans le menu Azure, choisissez **Groupes de ressources**.

2. Recherchez le groupe de ressources que vous souhaitez supprimer. Dans le menu du groupe de ressources, choisissez **Vue d’ensemble**, si cette option n’est pas déjà sélectionnée. 

3. Passez en revue toutes les ressources du groupe de ressources que vous souhaitez supprimer. Lorsque vous êtes prêt, choisissez **Supprimer un groupe de ressources** sur la barre d’outils du groupe de ressources.

### <a name="turn-off-logic-app"></a>Désactiver l’application logique

Pour arrêter l’exécution de votre application logique sans supprimer votre travail, désactivez votre application. 

Dans le menu de votre application logique, **Vue d’ensemble**. Dans la barre d’outils, choisissez **Désactiver**.

  ![Désactiver votre application logique](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

### <a name="delete-logic-app"></a>Supprimer l’application logique

Vous pouvez supprimer simplement votre application logique en conservant toutes les autres ressources associées, telles que les connexions que vous avez créées.

1. Dans le menu de l’application logique, choisissez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Supprimer**. 

   ![Supprimer votre application logique](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

2. Confirmez que vous souhaitez supprimer votre application logique, puis choisissez **Supprimer**.

## <a name="get-support"></a>Obtenir de l'aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des workflows d’application logique à partir de modèles préconfigurés](../logic-apps/logic-apps-create-logic-apps-from-templates.md)