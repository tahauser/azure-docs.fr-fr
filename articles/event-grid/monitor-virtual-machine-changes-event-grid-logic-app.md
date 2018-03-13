---
title: "Surveiller les modifications d’une machine virtuelle - Azure Event Grid et Azure Logic Apps | Microsoft Docs"
description: "Vérifier les modifications de configuration dans des machines virtuelles (VM) à l’aide d’Azure Event Grid et Azure Logic Apps"
keywords: "applications logiques, grilles d’événements, machine virtuelle, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 3d99dabe778b9b9234db9fe130ba503cd8b57834
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps

Vous pouvez démarrer un [flux de travail d’application logique](../logic-apps/logic-apps-overview.md) automatisé lorsque des événements spécifiques se produisent dans des ressources Azure ou tierces. Ces ressources peuvent publier ces événements dans une [grille d’événements Azure](../event-grid/overview.md). À son tour, la grille d’événements envoie ces événements aux abonnés qui possèdent des files d’attente, webhooks ou [hubs d’événements](../event-hubs/event-hubs-what-is-event-hubs.md) comme points de terminaison. En tant qu’abonné, votre application logique peut attendre ces événements dans la grille d’événements avant d’exécuter des flux de travail automatisés pour effectuer les tâches, sans qu’il soit nécessaire d’écrire du code.

Par exemple, voici certains événements que les éditeurs peuvent envoyer aux abonnés via le service Azure Event Grid :

* Créer, lire, mettre à jour ou supprimer une ressource. Par exemple, vous pouvez surveiller les modifications susceptibles d’être facturées sur votre abonnement Azure. 
* Ajouter ou retirer une personne d’un abonnement Azure.
* Votre application effectue une action particulière.
* Un nouveau message apparaît dans une file d’attente.

Ce didacticiel crée une application logique qui surveille les modifications apportées à une machine virtuelle et envoie des e-mails à ce sujet. Si vous créez une application logique avec abonnement aux événements d’une ressource Azure, ces événements sont transférés de cette ressource vers l’application logique par le biais d’une grille d’événements. Le didacticiel vous guide tout au long de la création de cette application logique :

![Vue d’ensemble - surveiller une machine virtuelle avec une grille d’événements et une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * créer une application logique qui surveille les événements d’une grille d’événements ;
> * ajouter une condition qui recherche spécifiquement les modifications apportées à la machine virtuelle ;
> * envoyer un e-mail en cas de modification de la machine virtuelle.

## <a name="prerequisites"></a>configuration requise

* Un compte de messagerie sur [n’importe quel fournisseur de messagerie pris en charge par Azure Logic Apps](../connectors/apis-list.md), par exemple Outlook Office 365, Outlook.com ou Gmail, pour envoyer les notifications. Ce didacticiel utilise Outlook Office 365.

* Une [machine virtuelle](https://azure.microsoft.com/services/virtual-machines). Si ce n’est pas déjà fait, créez une machine virtuelle en suivant le [didacticiel Créer une machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/). Pour que la machine virtuelle publie des événements, [aucun autre élément n’est nécessaire](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Créer une application logique qui surveille les événements d’une grille d’événements

Tout d’abord, créez une application logique et ajoutez un déclencheur Event Grid, qui surveille le groupe de ressources de votre machine virtuelle. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Dans le coin supérieur gauche du menu principal Azure, choisissez **Créer une ressource** > **Enterprise Integration** > **Application logique**.

   ![Créer une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Créez votre application logique avec les paramètres spécifiés dans le tableau suivant :

   ![Spécifier les détails de l’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Name** | *{nom-de-votre-application-logique}* | Donnez un nom unique à l’application logique. | 
   | **Abonnement** | *{votre-abonnement-Azure}* | Sélectionnez le même abonnement Azure pour tous les services de ce didacticiel. | 
   | **Groupe de ressources** | *{votre-groupe-de-ressources-Azure}* | Sélectionnez le même groupe de ressources Azure pour tous les services de ce didacticiel. | 
   | **Lieu** | *{votre-région-Azure}* | Sélectionnez la même région pour tous les services de ce didacticiel. | 
   | | | 

4. Lorsque vous êtes prêt, sélectionnez **Épingler au tableau de bord**, puis **Créer**.

   Vous venez de créer une ressource Azure pour votre application logique. 
   Une fois qu’Azure a déployé votre application logique, le Concepteur d’applications logiques vous propose des modèles courants pour faciliter vos premiers pas.

   > [!NOTE] 
   > Lorsque vous sélectionnez **Épingler au tableau de bord**, votre application logique s’ouvre automatiquement dans le Concepteur d’applications logiques. Sinon, vous pouvez manuellement rechercher et ouvrir votre application logique.

5. Déployez maintenant un modèle d’application logique. Sous **Modèles**, choisissez **Application logique vide**, afin de développer votre application logique à partir de zéro.

   ![Choisir le modèle d’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Le Concepteur d’application logique vous montre à présent des [*connecteurs*](../connectors/apis-list.md) et des [*déclencheurs*](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui vous permettent de démarrer votre application logique, ainsi que des actions que vous pouvez ajouter après un déclencheur pour effectuer des tâches. Un déclencheur est un événement qui crée une instance d’application logique et démarre le flux de l’application logique. 
   Votre application logique a besoin d’un déclencheur comme premier élément.

6. Dans la zone de recherche, entrez « grille d’événements » comme filtre. Sélectionner le déclencheur : **Azure Event Grid - On a resource event**

   ![Sélectionner le déclencheur : « Azure Event Grid - On a resource event »](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Lorsque vous y êtes invité, connectez-vous à Azure Event Grid avec vos informations d’identification Azure.

   ![Se connecter avec des informations d’identification Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Si vous êtes connecté avec un compte Microsoft personnel, par exemple @outlook.com ou @hotmail.com, le déclencheur Event Grid risque de ne pas apparaître correctement. Il existe une solution de contournement qui consiste à choisir [Se connecter avec le principal du service](../azure-resource-manager/resource-group-create-service-principal-portal.md), ou à s’authentifier en tant que membre de l’instance Azure Active Directory associée à l’abonnement Azure, par exemple, *nom-utilisateur*@emailoutlook.onmicrosoft.com.

8. Maintenant, abonnez votre application logique aux événements de l’éditeur. Renseignez les détails de votre abonnement aux événements, comme l’indique le tableau suivant :

   ![Spécifier les détails de l’abonnement aux événements](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **Abonnement** | *{abonnement-Azure-de-la-machine-virtuelle}* | Sélectionnez l’abonnement Azure de l’éditeur d’événements. Dans le cadre de ce didacticiel, sélectionnez l’abonnement Azure de votre machine virtuelle. | 
   | **Type de ressource** | Microsoft.Resources.resourceGroups | Sélectionnez le type de ressource de l’éditeur d’événements. Dans le cadre de ce didacticiel, sélectionnez la valeur spécifiée, afin que votre application logique ne surveille que les groupes de ressources. | 
   | **Nom de la ressource** | *{nom-du-groupe-de-ressources-de-la-machine-virtuelle}* | Sélectionnez le nom de la ressource de l’éditeur. Pour ce didacticiel, sélectionnez le nom du groupe de ressources pour votre machine virtuelle. | 
   | Pour les paramètres facultatifs, choisissez **Afficher les options avancées**. | *{voir les descriptions}* | * **Filtre de préfixe** : pour ce didacticiel, laissez ce paramètre vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de préfixe en tant que filtre, par exemple, un chemin d’accès et un paramètre pour une ressource spécifique. <p>* **Filtre de suffixe** : pour ce didacticiel, laissez ce paramètre vide. Le comportement par défaut s’applique à toutes les valeurs. Vous pouvez cependant spécifier une chaîne de suffixe en tant que filtre, par exemple, une extension de nom de fichier, si vous ne souhaitez utiliser que des types de fichiers spécifiques.<p>* **Nom de l’abonnement** : indiquez un nom unique pour votre abonnement aux événements. |
   | | | 

   Une fois que vous avez terminé, votre déclencheur Event Grid peut se présenter ainsi :
   
   ![Détails d’un exemple de déclencheur de grille d’événements](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. Pour réduire et masquer les détails d’une action dans votre logique d’application, choisissez la barre de titre de l’action.

   ![Enregistrer votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Lorsque vous enregistrez votre application logique avec un déclencheur Event Grid, Azure crée automatiquement un abonnement aux événements de votre application logique pour la ressource que vous avez sélectionnée. Par conséquent, lorsque la ressource publie un événement dans la grille d’événements, cette grille d’événements transmet automatiquement l’événement à votre application logique. Cet événement déclenche votre application logique, puis crée et lance une instance du flux de travail que vous définissez dans les étapes suivantes.

Votre application logique est désormais en ligne ; elle écoute les événements de la grille d’événements, mais ne fait rien tant que vous n’avez pas ajouté d’actions au flux de travail. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Ajouter une condition qui recherche les modifications apportées à la machine virtuelle

Pour exécuter le flux de travail de votre application logique uniquement quand un événement spécifique se produit, ajoutez une condition qui recherche les opérations d’écriture sur la machine virtuelle. Lorsque cette condition est true, votre application logique vous envoie par courrier électronique des informations sur la machine virtuelle mise à jour.

1. Dans le Concepteur d’application logique, sous le déclencheur de votre grille d’événements, sélectionnez **Nouvelle étape** > **Ajouter une condition**.

   ![Ajouter une condition à votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   Le Concepteur d’application logique ajoute une condition vide à votre flux de travail, y compris les chemins d’accès des actions à suivre selon que la condition est true ou false.

   ![Condition vide](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Dans la zone **Condition**, choisissez **Modifier en mode avancé**.
Entrez cette expression :

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Votre condition ressemble maintenant à cet exemple :

   ![Condition vide](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Cette expression vérifie si l’événement `body` contient un objet `data` dont la propriété `operationName` correspond à l’opération `Microsoft.Compute/virtualMachines/write`. 
   En savoir plus sur le [schéma d’un événement Event Grid](../event-grid/event-schema.md).

3. Pour fournir une description de la condition, choisissez le bouton en forme **d’ellipse** (**...** ) sur la forme de la condition, puis sélectionnez **Renommer**.

   > [!NOTE] 
   > Les exemples suivants de ce didacticiel décrivent également les étapes du flux de travail de l’application logique.

4. Choisissez maintenant **Modifier en mode de base** afin que l’expression se résolve automatiquement ainsi :

   ![Condition d’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Enregistrez votre application logique.

## <a name="send-email-when-your-virtual-machine-changes"></a>Envoyer un courrier électronique lorsque votre machine virtuelle change

Ajoutez maintenant une [ *action* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) pour recevoir un message électronique lorsque la condition spécifiée est true.

1. Dans la zone **If true** de la condition, choisissez **Ajouter une action**.

   ![Ajouter une action lorsque la condition est true](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Dans la zone de recherche, entrez « e-mail » comme filtre. Selon votre fournisseur de messagerie, recherchez et sélectionnez le connecteur correspondant. Puis sélectionnez l’action « Envoyer un courrier électronique » pour votre connecteur. Par exemple :  

   * Pour un compte professionnel ou scolaire Azure, sélectionnez le connecteur Outlook Office 365. 
   * Pour des comptes personnels Microsoft, sélectionnez le connecteur Outlook.com. 
   * Pour des comptes Gmail, sélectionnez le connecteur Gmail. 

   Nous continuerons d’utiliser le connecteur Outlook Office 365. 
   Si vous utilisez un autre fournisseur, les étapes restent les mêmes, mais votre interface utilisateur peut s’afficher différemment. 

   ![Sélectionner l’action « Envoyer un courrier électronique »](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Si vous ne disposez pas déjà d’une connexion pour votre fournisseur de messagerie, connectez-vous à votre compte de messagerie lorsque vous êtes invité vous identifier.

4. Renseignez les détails de l’e-mail comme l’indique le tableau suivant :

   ![Action E-mail vide](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Pour sélectionner certains des champs disponibles dans votre flux de travail, cliquez sur une zone d’édition afin d’ouvrir la liste **Contenu dynamique**, ou choisissez **Ajouter du contenu dynamique**. Pour voir davantage de champs, choisissez **Plus** pour chacune des sections de la liste. Pour fermer la liste **Contenu dynamique**, choisissez **Ajouter du contenu dynamique**.

   | Paramètre | Valeur suggérée | Description | 
   | ------- | --------------- | ----------- | 
   | **To** | *{adresse-e-mail-du-destinataire}* |Entrez l’adresse e-mail du destinataire. À des fins de test, vous pouvez utiliser votre propre adresse e-mail. | 
   | **Objet** | Mise à jour de la ressource : **Subject**| Entrez le contenu de l’objet de l’e-mail. Dans le cadre de ce didacticiel, entrez le texte suggéré et sélectionnez le champ **Subject** de l’événement. Ici, l’objet de votre e-mail comprend le nom de la ressource mise à jour (machine virtuelle). | 
   | **Corps** | Groupe de ressources : **Topic** <p>Type d’événement : **Event Type**<p>ID d’événement : **ID**<p>Heure : **Event Time** | Entrez le contenu du corps de l’e-mail. Dans le cadre de ce didacticiel, entrez le texte suggéré et sélectionnez les champs **Topic**, **Event Type**, **ID** et **Event Time** de façon à inclure dans votre e-mail le nom du groupe de ressources, le type d’événement, son horodatage et son ID pour la mise à jour. <p>Pour ajouter des lignes vides à votre contenu, appuyez sur Maj + Entrée. | 
   | | | 

   > [!NOTE] 
   > Si vous sélectionnez un champ qui représente un tableau, le concepteur ajoute automatiquement une boucle **For each** autour de l’action qui référence ce tableau. De cette façon, votre application logique effectue cette action sur chaque élément du tableau.

   Votre action d’e-mail peut maintenant se présenter ainsi :

   ![Sélectionner les sorties à inclure dans le courrier électronique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Et votre application logique terminée peut se présenter ainsi :

   ![Application logique terminée](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Enregistrez votre application logique. Pour réduire et masquer les détails de chaque action dans votre logique d’application, choisissez la barre de titre de l’action.

   ![Enregistrer votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Votre application logique est désormais en ligne, mais elle attend que votre machine virtuelle soit modifiée avant de faire quoi que ce soit. 
   Pour tester maintenant votre application logique, passez à la section suivante.

## <a name="test-your-logic-app-workflow"></a>Tester le flux de travail d’une application logique

1. Pour vérifier que votre application logique reçoit des événements spécifiés, mettez à jour votre machine virtuelle. 

   Par exemple, vous pouvez redimensionner votre machine virtuelle dans le portail Azure ou [redimensionner votre machine virtuelle avec Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Après quelques instants, vous devriez recevoir un courrier électronique. Par exemple : 

   ![Courrier électronique à propos de la mise à jour de la machine virtuelle](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Pour consulter l’historique des exécutions et des déclenchements de votre application logique, choisissez **Vue d’ensemble** dans le menu de votre application logique. Pour afficher plus de détails sur une exécution, sélectionnez la ligne correspondante.

   ![Historique d’exécutions de l’application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Pour afficher les entrées et les sorties de chaque étape, développez celle que vous souhaitez vérifier. Ces informations peuvent vous aider à diagnostiquer et déboguer les problèmes de votre application logique.
 
   ![Détails de l’historique des exécutions d’une application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Félicitations, vous avez créé et exécuté une application logique qui surveille les événements liés aux ressources grâce à une grille d’événements et vous envoie un e-mail lorsque ces événements se produisent. Vous avez également appris comment créer facilement des flux de travaux qui automatisent les processus et intégrer des systèmes et des services cloud.

Vous pouvez surveiller les autres modifications de configuration avec des grilles d’événements et des applications logiques. Par exemple :

* Une machine virtuelle se voit attribuer des droits de contrôle d’accès en fonction du rôle (RBAC).
* Des modifications sont apportées à un groupe de sécurité réseau (NSG) sur une carte d’interface réseau (NIC).
* Des disques d’une machine virtuelle ont été ajoutés ou supprimés.
* Une adresse IP publique est affectée à la carte d’interface réseau (NIC) d’une machine virtuelle.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce didacticiel utilise des ressources et effectue des actions qui peuvent entraîner des frais sur votre abonnement Azure. Par conséquent, lorsque vous aurez terminé de suivre ce didacticiel et d’effectuer les tests, veillez à désactiver ou à supprimer les ressources pour lesquelles vous ne souhaitez pas être facturé.

* Pour arrêter l’exécution de votre application logique sans supprimer votre travail, désactivez votre application. Dans le menu de votre application logique, **Vue d’ensemble**. Dans la barre d’outils, choisissez **Désactiver**.

  ![Désactiver votre application logique](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Si vous ne voyez pas le menu de l’application logique, essayez de revenir au tableau de bord Azure et de rouvrir votre application logique.

* Pour supprimer définitivement votre application logique, choisissez **Vue d’ensemble** dans le menu de l’application logique. Dans la barre d’outils, choisissez **Supprimer**. Confirmez que vous souhaitez supprimer votre application logique, puis choisissez **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

* [Créer et acheminer des événements personnalisés avec Event Grid](../event-grid/custom-event-quickstart.md)
