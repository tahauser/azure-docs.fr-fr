---
title: "Générer des flux de travail pour traiter les e-mails et les pièces jointes : Azure Logic Apps | Microsoft Docs"
description: "Ce didacticiel montre comment créer des flux de travail automatisés pour le traitement des e-mails et des pièces jointes avec Azure Logic Apps, Stockage Azure et Azure Functions"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: 8c327599585e67ccc6ebdf849d3e9cf9b95e7398
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="process-emails-and-attachments-with-a-logic-app"></a>Traiter les e-mails et les pièces jointes à l’aide d’une application logique

Azure Logic Apps vous aide à automatiser les flux de travail et à intégrer des données dans les services Azure et Microsoft, d’autres applications SaaS (software-as-a-service) et des systèmes locaux. Ce didacticiel montre comment créer une [application logique](../logic-apps/logic-apps-overview.md) qui gère les e-mails entrants et les éventuelles pièces jointes. Cette application logique traite ce contenu, enregistre le contenu dans Stockage Azure et envoie des notifications de révision de ce contenu. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurez les applications [Stockage Azure](../storage/common/storage-introduction.md) et Explorateur Stockage pour vérifier les e-mails et pièces jointes enregistrés.
> * Créez une [fonction Azure](../azure-functions/functions-overview.md) qui supprime le code HTML des e-mails. Ce didacticiel inclut le code que vous pouvez utiliser pour cette fonction.
> * Créez une application logique vide.
> * Ajoutez un déclencheur qui surveille les e-mails et recherche des pièces jointes.
> * Ajoutez une condition qui vérifie si les e-mails contiennent des pièces jointes.
> * Ajoutez une action qui appelle la fonction Azure lorsqu’un e-mail comporte des pièces jointes.
> * Ajoutez une action qui crée des objets blob de stockage pour les e-mails et les pièces jointes.
> * Ajoutez une action qui envoie des notifications par e-mail.

Lorsque vous avez terminé, votre application logique ressemble à ce flux de travail à un niveau élevé :

![Application logique terminée de niveau élevé](./media/tutorial-process-email-attachments-workflow/overview.png)

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">créez un compte Azure gratuit</a> avant de commencer. 

## <a name="prerequisites"></a>configuration requise

* Un compte de messagerie d’un fournisseur de messagerie pris en charge par Azure Logic Apps, par exemple Office 365 Outlook, Outlook.com ou Gmail. Pour les autres fournisseurs, [passez en revue la liste des connecteurs ici](https://docs.microsoft.com/connectors/).

  Cette application logique utilise un compte Office 365 Outlook. 
  Si vous utilisez un autre compte de messagerie, les étapes générales sont identiques, mais l’affichage de l’interface utilisateur peut être légèrement différent.

* Téléchargez et installez <a href="http://storageexplorer.com/" target="_blank">l’Explorateur Stockage Microsoft Azure gratuit</a>. Cet outil vous permet de vérifier que votre conteneur de stockage est correctement configuré.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au <a href="https://portal.azure.com" target="_blank">portail Azure</a> avec les informations d’identification de votre compte Azure.

## <a name="set-up-storage-to-save-attachments"></a>Configurer le stockage pour y enregistrer les pièces jointes

Vous pouvez enregistrer les e-mails entrants et les pièces jointes en tant qu’objets blob dans un [conteneur de stockage Azure](../storage/common/storage-introduction.md). 

1. Avant de créer un conteneur de stockage, [créez un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) avec les paramètres suivants :

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Name** | attachmentstorageacct | Nom de votre compte de stockage | 
   | **Modèle de déploiement** | Resource manager | [Modèle de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) pour la gestion du déploiement des ressources. | 
   | **Type de compte** | Usage général | [Type de compte de stockage](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Performances** | standard | Ce paramètre spécifie les types de données pris en charge et les médias de stockage des données. Voir [Types de compte de stockage](../storage/common/storage-introduction.md#types-of-storage-accounts). | 
   | **Réplication** | Stockage localement redondant (LRS) | Ce paramètre spécifie comment vos données sont copiées, stockées, gérées et synchronisées. Voir [Réplication](../storage/common/storage-introduction.md#replication). | 
   | **Transfert sécurisé requis** | Désactivé | Ce paramètre spécifie la sécurité requise pour les demandes de connexions. Voir [Exiger un transfert sécurisé dans Stockage Azure](../storage/common/storage-require-secure-transfer.md). | 
   | **Abonnement** | <*your-Azure-subscription-name*> | Nom de votre abonnement Azure. | 
   | **Groupe de ressources** | LA-Tutorial-RG | Nom du [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) utilisé pour organiser et gérer les ressources connexes. <p>**Remarque :** un groupe de ressources existe dans une région spécifique. Même si les éléments de ce didacticiel ne sont pas forcément disponibles dans toutes les régions, essayez d’utiliser la même région dans la mesure du possible. | 
   | **Lieu** | Est des États-Unis 2 | Région dans laquelle stocker les informations sur votre compte de stockage. | 
   | **Configurer des réseaux virtuels** | Désactivé | Pour ce didacticiel, maintenez le paramètre **Désactivé**. | 
   |||| 

   Vous pouvez également utiliser [Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) ou [Azure CLI](../storage/common/storage-quickstart-create-storage-account-cli.md).
  
2. Une fois qu’Azure a déployé votre compte de stockage, obtenez la clé d’accès de votre compte de stockage :

   1. Dans le menu de votre compte de stockage, sous **Paramètres**, choisissez **Clés d’accès**. 
   2. Recherchez **key1** sous **Clés par défaut** ainsi que le nom de votre compte de stockage.

      ![Copier et enregistrer un nom de compte de stockage et une clé](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Vous pouvez également utiliser [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) ou [Azure CLI](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az_storage_account_keys_list). 

3. Créez un conteneur de stockage pour vos pièces jointes.
   
   1. Dans le menu de votre compte de stockage, dans le volet **Vue d’ensemble**, choisissez **Objets blob** sous **Services**, puis **+ Conteneur**.

   2. Entrez « pièces jointes » comme nom de votre conteneur. Sous **Niveau d’accès public**, sélectionnez **Conteneur (accès en lecture anonyme pour les conteneurs et les objets blob)**, puis choisissez **OK**.

   Vous pouvez également utiliser [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) ou [Azure CLI](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az_storage_container_create). 
   Lorsque vous avez terminé, vous pouvez trouver votre conteneur de stockage dans votre compte de stockage ici dans le portail Azure :

   ![Conteneur de stockage terminé](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

À présent, connectez l’Explorateur Stockage à votre compte de stockage.

## <a name="set-up-storage-explorer"></a>Configurer l’Explorateur Stockage

Connectez l’Explorateur Stockage à votre compte de stockage afin de pouvoir vérifier que votre application logique enregistre correctement les pièces jointes en tant qu’objets blob dans votre conteneur de stockage.

1. Ouvrez l’Explorateur Stockage Microsoft Azure. Lorsque l’Explorateur Stockage vous invite à vous connecter à Stockage Azure, choisissez **Use a storage account name and key (Utiliser un nom de compte de stockage et une clé)** > **Suivant**.
Si aucune invite ne s’affiche, choisissez **Ajouter un compte** dans la barre d’outils de l’Explorateur.

2. Sous **Attach using Name and Key (Attacher à l’aide d’un nom et d’une clé)**, entrez le nom de votre compte de stockage et la clé d’accès que vous avez enregistrés précédemment. Choisissez **Suivant** > **Connecter**.

3. Vérifiez que le compte de stockage et le conteneur s’affichent correctement dans l’Explorateur Stockage :

   1. Sous **Explorateur**, développez **(Local and Attached) (Local et attaché)** > 
   **Comptes de stockage** > **attachmentstorageaccount** > 
   **Conteneurs d’objets blob**.

   2. Vérifiez que le conteneur « pièces jointes » est désormais affiché. 
   Par exemple : 

      ![Explorateur Stockage : vérifier le conteneur de stockage](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Créez une [fonction Azure](../azure-functions/functions-overview.md) qui supprime le code HTML des e-mails entrants.

## <a name="create-a-function-to-clean-html"></a>Créer une fonction pour nettoyer le code HTML

Utilisez l’extrait de code fourni par ces étapes pour créer une fonction Azure qui supprime le code HTML dans chaque e-mail entrant. De cette façon, le contenu des e-mails est plus net et plus facile à traiter. Vous pouvez ensuite appeler cette fonction à partir de votre application logique.

1. Avant de pouvoir créer une fonction, [créez une application de fonction](../azure-functions/functions-create-function-app-portal.md) avec les paramètres suivants :

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Nom de l’application** | CleanTextFunctionApp | Nom global unique et descriptif de votre application de fonction. | 
   | **Abonnement** | <*your-Azure-subscription-name*> | Abonnement Azure que vous avez utilisé précédemment. | 
   | **Groupe de ressources** | LA-Tutorial-RG | Groupe de ressources Azure que vous avez utilisé précédemment. | 
   | **Plan d’hébergement** | Plan de consommation | Ce paramètre détermine l’affectation et la mise à l’échelle des ressources, telles que la puissance de calcul, pour l’exécution de votre application de fonction. Voir [Comparaison des plans d’hébergement](../azure-functions/functions-scale.md). | 
   | **Lieu** | Est des États-Unis 2 | Région que vous avez utilisée précédemment. | 
   | **Stockage** | cleantextfunctionstorageacct | Créez un compte de stockage pour votre application de fonction. Utilisez uniquement des lettres minuscules et des chiffres. <p>**Remarque :** ce compte de stockage contient vos applications de fonction et diffère du compte de stockage créé précédemment pour les pièces jointes. | 
   | **Application Insights** | Off | Active la surveillance des applications avec [Application Insights](../application-insights/app-insights-overview.md), mais pour ce didacticiel, conservez le paramètre **Désactivé**. | 
   |||| 

   Si votre application de fonction ne s’ouvre pas automatiquement après le déploiement, recherchez-la dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>. Dans le menu Azure principal, choisissez **App Services**, puis sélectionnez votre application de fonction.

   ![Application de fonction créée](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Si **App Services** n’apparaît pas dans le menu Azure, il est recommandé d’accéder à **Autres services**. Dans la zone de recherche, recherchez et sélectionnez **Applications de fonction**. Pour plus d’informations, voir l’article portant sur la [création d’une fonction](../azure-functions/functions-create-first-azure-function.md).

   Vous pouvez également utiliser [Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md), ou [PowerShell et des modèles Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

2. Sous **Applications de fonction**, développez **CleanTextFunctionApp**, puis sélectionnez **Fonctions**. Dans la barre d’outils des fonctions, choisissez **+ Nouvelle fonction**.

   ![Créer une fonction](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. Sous **Choisir un modèle ci-dessous ou accéder au démarrage rapide**, sélectionnez le modèle de fonction **HttpTrigger - C#**.

   ![Sélectionner un modèle de fonction](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

4. Sous **Nommez votre fonction**, entrez ```RemoveHTMLFunction```. Sous **Déclencheur HTTP** > **Niveau d’autorisation**, conservez la valeur **Fonction** par défaut, puis choisissez **Créer**.

   ![Nommer votre fonction](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Dans l’éditeur ouvert, remplacez le code du modèle par ce code, ce qui supprime le code HTML et retourne les résultats à l’appelant :

   ``` CSharp
   using System.Net;
   using System.Text.RegularExpressions;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await req.Content.ReadAsStringAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return req.CreateResponse(HttpStatusCode.OK, new { updatedBody });

   }
   ```

6. Une fois ces opérations effectuées, sélectionnez **Enregistrer**. Pour tester votre fonction, choisissez **Test** sous l’icône de flèche (**<**) située sur le côté droit de l’éditeur. 

   ![Ouvrir le volet Test](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. Dans le volet **Test**, sous **Corps de la requête**, entrez la ligne suivante, puis choisissez **Exécuter**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Tester votre fonction](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   La fenêtre **Sortie** affiche ce résultat de la fonction :

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Après avoir vérifié le bon fonctionnement de votre fonction, créez votre application logique. Même si ce didacticiel montre comment créer une fonction qui supprime le code HTML des e-mails, Logic Apps possède également un connecteur **HTML to Text (HTML à texte)**.

## <a name="create-your-logic-app"></a>Créer votre application logique

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration Entreprise** > **Application logique**.

   ![Créer une application logique](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. Sous **Créer une application logique**, indiquez les informations suivantes sur votre application logique comme illustré et décrit. Lorsque c’est fait, cliquez sur **Épingler au tableau de bord** > **Créer**.

   ![Spécifier les informations de l’application logique](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Name** | LA-ProcessAttachment | Nom de l’application logique. | 
   | **Abonnement** | <*your-Azure-subscription-name*> | Abonnement Azure que vous avez utilisé précédemment. | 
   | **Groupe de ressources** | LA-Tutorial-RG | Groupe de ressources Azure que vous avez utilisé précédemment. |
   | **Lieu** | Est des États-Unis 2 | Région que vous avez utilisée précédemment. | 
   | **Log Analytics** | Off | Pour ce didacticiel, maintenez le paramètre **Désactivé**. | 
   |||| 

3. Une fois qu’Azure a déployé votre application, le Concepteur d’applications logiques s’ouvre et affiche une page contenant une vidéo de présentation et des modèles d’applications logiques courantes. Sous **Modèles**, choisissez **Application logique vide**.

   ![Choisir un modèle d’application logique vide](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Ajoutez maintenant un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) qui écoute les e-mails entrants comportant des pièces jointes. Chaque application logique doit commencer par un déclencheur, qui est activé lorsqu’un événement spécifique se produit ou lorsque de nouvelles données respectent une condition particulière. Pour plus d’informations, voir [Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Surveiller les e-mails entrants

1. Dans le concepteur, entrez « à l’arrivée de l’e-mail » dans la zone de recherche. Sélectionnez ce déclencheur pour votre fournisseur de messagerie : **<*votre-fournisseur-de-messagerie*> - à l’arrivée d’un nouvel e-mail**, par exemple :

   ![Sélectionner ce déclencheur pour le fournisseur de messagerie : « À la réception d’un e-mail »](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez Office 365 Outlook. 
   * Pour les comptes Microsoft personnels, sélectionnez Outlook.com. 

2. Si vous êtes invité à entrer vos informations d’identification, connectez-vous à votre compte de messagerie afin que Logic Apps puisse se connecter à votre compte de messagerie.

3. Indiquez maintenant les critères que le déclencheur utilise pour filtrer les nouveaux e-mails.

   1. Spécifiez le dossier, un intervalle et une fréquence de vérification des e-mails.

      ![Spécifier le dossier, l’intervalle et la fréquence de vérification des e-mails](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Paramètre | Valeur | Description | 
      | ------- | ----- | ----------- | 
      | **Folder** | Inbox | Dossier d’e-mail à vérifier | 
      | **Intervalle** | 1 | Nombre d’intervalles d’attente entre les vérifications. | 
      | **Fréquence** | Minute | Unité de temps de chaque intervalle entre les vérifications. | 
      |  |  |  | 
  
   2. Choisissez **Afficher les options avancées** et spécifiez les paramètres suivants :

      | Paramètre | Valeur | Description | 
      | ------- | ----- | ----------- | 
      | **Contient une pièce jointe** | OUI | Récupère uniquement les e-mails comportant des pièces jointes. <p>**Remarque :** le déclencheur ne supprime pas les e-mails de votre compte. Il vérifie uniquement les nouveaux messages et ne traite que les e-mails qui correspondent au filtre Objet. | 
      | **Inclure des pièces jointes** | OUI | Récupérez les pièces jointes en tant qu’entrée de votre flux de travail au lieu de les rechercher simplement. | 
      | **Filtre Objet** | ```Business Analyst 2 #423501``` | Texte à rechercher dans l’objet de l’e-mail. | 
      |  |  |  | 

4. Pour masquer les informations du déclencheur pour le moment, cliquez dans sa barre de titre.

   ![Réduire la forme pour masquer les informations](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**.

   Votre application logique est à présent active, mais elle vérifie uniquement vos e-mails. 
   Ajoutez une condition qui spécifie les critères pour poursuivre le flux de travail.

## <a name="check-for-attachments"></a>Rechercher des pièces jointes

1. Sous le déclencheur, choisissez **+ Nouvelle étape** > **Ajouter une condition**.

   Lorsque la forme de condition s’affiche, par défaut, la liste des paramètres ou la liste de contenu dynamique apparaît et affiche tous les paramètres de l’étape précédente que vous pouvez inclure en tant qu’entrées du flux de travail. 
   La largeur de la fenêtre du navigateur détermine la liste qui s’affiche.

2. Renommez la condition en utilisant une meilleure description.

   1. Dans la barre de titre de la condition, choisissez le bouton représentant des **points de suspension** (**...**) > **Renommer**.

      Par exemple, si le navigateur est défini sur l’affichage étroit :

      ![Renommer la condition](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

      Si votre navigateur est en affichage large et que la liste de contenu dynamique bloque l’accès au bouton points de suspension, fermez la liste en choisissant **Ajouter du contenu dynamique** dans la condition. 
      
      ![Fermer la liste de contenu dynamique](./media/tutorial-process-email-attachments-workflow/close-dynamic-content-list.png)

   2. Renommez votre condition à l’aide de cette description : ```If email has attachments and key subject phrase```

3. Décrivez la condition en fournissant une expression. 

   1. Dans la forme de condition, choisissez **Modifier en mode Avancé**.

      ![Modifier la condition en mode Avancé](./media/tutorial-process-email-attachments-workflow/edit-advanced-mode.png)

   2. Dans la zone de texte, entrez cette expression :

      ```@equals(triggerBody()?['HasAttachment'], bool('true'))```

      Cette expression compare la valeur de la propriété **HasAttachment** du corps du déclencheur (e-mail dans ce didacticiel) avec l’objet booléen ```True```. 
      Si les deux valeurs sont égales, l’e-mail comporte au moins une pièce jointe, la condition est transmise et le flux de travail se poursuit.

      Votre condition ressemble maintenant à cet exemple :

      ![Expression de condition](./media/tutorial-process-email-attachments-workflow/condition-expression.png)

   3. Choisissez **Modifier en mode De base**. Votre expression est résolue maintenant comme indiqué ici :

      ![Expression résolue](./media/tutorial-process-email-attachments-workflow/condition-expression-resolved.png)

      > [!NOTE]
      > Pour créer manuellement une expression, vous devez travailler en mode De base et ouvrir la liste dynamique afin de pouvoir utiliser le Générateur d’expressions. Sous **Expression**, vous pouvez sélectionner des fonctions. Sous **Contenu dynamique**, vous pouvez sélectionner les champs des paramètres à utiliser dans ces fonctions.
      > Ce didacticiel montre ultérieurement comment créer manuellement des expressions.

4. Enregistrez votre application logique.

### <a name="test-your-condition"></a>Tester votre condition

Vérifiez si la condition fonctionne correctement :

1. Si votre application logique n’est pas déjà en cours d’exécution, choisissez **Exécuter** dans la barre d’outils du concepteur.

   Cette étape démarre manuellement votre application logique sans avoir à attendre la transmission de l’intervalle spécifié. 
   Toutefois, rien ne se produit tant que l’e-mail de test n’est pas arrivé dans votre boîte de réception. 

2. Envoyez-vous un e-mail qui respecte les critères suivants :

   * L’objet de votre e-mail contient le texte que vous avez spécifié dans le **Filtre Objet** du déclencheur : ```Business Analyst 2 #423501```

   * Votre e-mail comporte une pièce jointe. 
   Pour le moment, créez simplement un fichier texte vide et joignez-le à votre e-mail.

   Lorsque l’e-mail arrive, votre application logique recherche des pièces jointes et le texte de l’objet spécifié.
   Si la condition est transmise, le déclencheur est activé et provoque la création d’une instance d’application logique et le démarrage du flux de travail par le moteur Logic Apps. 

3. Pour vérifier que le déclencheur est activé et que l’application logique est exécutée avec succès, choisissez **Vue d’ensemble** dans le menu de l’application logique.

   ![Vérifier l’historique du déclencheur et l’historique des exécutions](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Si votre application logique n’a pas été activée ni exécutée en dépit d’un déclencheur réussi, voir [Résoudre les problèmes et diagnostiquer les échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

À présent, définissez les actions à entreprendre pour la branche **Si true**. Pour enregistrer l’e-mail ainsi que les pièces jointes, supprimez le code HTML dans le corps de l’e-mail, puis créez des objets blob dans le conteneur de stockage correspondant à l’e-mail et aux pièces jointes.

> [!NOTE]
> Votre application logique n’a rien à faire pour la branche **Si false** lorsqu’un e-mail ne contient pas de pièces jointes. À titre d’exercice supplémentaire à l’issue de ce didacticiel, vous pouvez ajouter une action appropriée que vous souhaitez entreprendre pour la branche **Si false**.

## <a name="call-the-removehtmlfunction"></a>Appeler la fonction RemoveHTMLFunction

1. Dans le menu de l’application logique, choisissez **Concepteur d’application logique**. Dans la branche **Si true**, choisissez **Ajouter une action**.

2. Recherchez « azure functions », puis sélectionnez cette action : **Azure Functions : choisir une fonction Azure**.

   ![Sélectionner une action pour « Azure Functions : choisir une fonction Azure »](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Sélectionnez votre application de fonction créée précédemment : **CleanTextFunctionApp**.

   ![Sélectionner votre application de fonction Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. À présent, sélectionnez votre fonction : **RemoveHTMLFunction**.

   ![Sélectionner votre application de fonction](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Renommez la forme de votre fonction avec cette description : ```Call RemoveHTMLFunction to clean email body```. 

6. Dans la forme de fonction, tapez l’entrée correspondant à votre fonction à traiter. Spécifiez le corps de l’e-mail comme indiqué et décrit ici :

   ![Spécifier le corps de la requête pour la fonction attendue](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   1. Sous **Corps de la requête**, entrez ce texte : 
   
      ```{ "emailBody": ``` 

      Tant que vous n’avez pas terminé la saisie dans les étapes suivantes, une erreur relative à un code JSON non valide s’affiche.
      Lorsque vous avez testé précédemment cette fonction, l’entrée spécifiée pour cette fonction utilisait JavaScript Objet Notation (JSON). 
      Le corps de la requête doit donc utiliser également le même format. 

   2. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez le champ **Corps** sous **À la réception d’un e-mail**.
   Après le champ **Corps**, ajoutez l’accolade fermante : ```}```.

      ![Spécifier le corps de la requête à transmettre à la fonction](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing2.png)

      Dans la définition de l’application logique, cette entrée apparaît au format suivant :

      ```{ "emailBody": "@triggerBody()?['Body']" }```

7. Enregistrez votre application logique.

Ajoutez maintenant une action qui crée un objet blob dans votre conteneur de stockage pour enregistrer le corps de l’e-mail.

## <a name="create-blob-for-email-body"></a>Créer un objet blob pour le corps de l’e-mail

1. Sous la forme de la fonction Azure, choisissez **Ajouter une action**. 

2. Sous **Choisir une action**, recherchez « objet blob », puis sélectionnez cette action : **Stockage Blob Azure : créer un objet blob**.

   ![Ajouter une action pour créer un objet blob pour le corps de l’e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Si vous n’êtes pas connecté à un compte de stockage Azure, créez une connexion à votre compte de stockage avec ces paramètres comme indiqué et décrit ici. Lorsque vous êtes prêt, choisissez **Créer**.

   ![Créer une connexion au compte de stockage](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Nom de connexion** | AttachmentStorageConnection | Nom descriptif de la connexion. | 
   | **Compte de stockage** | attachmentstorageacct | Nom du compte de stockage que vous avez créé précédemment pour enregistrer des pièces jointes. | 
   |||| 

4. Renommez l’action **Créer un objet blob** avec cette description : ```Create blob for email body```.

5. Dans l’action **Créer un objet blob**, indiquez ces informations, puis sélectionnez ces paramètres pour créer l’objet blob comme indiqué et décrit :

   ![Fournir des informations d’objet blob pour le corps de l’e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Chemin d’accès du dossier** | /pièces jointes | Chemin d’accès et nom du conteneur que vous avez créé précédemment. Vous pouvez également parcourir l’arborescence et sélectionner un conteneur. | 
   | **Nom de l’objet blob** | Champ **De** | Transmettez le nom de l’expéditeur de l’e-mail en tant que nom d’objet blob. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez le champ **De** sous **À la réception d’un e-mail**. | 
   | **Contenu de l’objet blob** | Champ **Contenu** | Transmettez le corps de l’e-mail sans code HTML en tant que contenu d’objet blob. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez **Corps** sous **Call RemoveHTMLFunction to clean email body (Appeler RemoveHTMLFunction pour nettoyer le corps de l’e-mail)**. |
   |||| 

6. Enregistrez votre application logique. 

### <a name="check-attachment-handling"></a>Vérifier le traitement des pièces jointes

Vérifiez à présent si votre application logique gère les e-mails comme vous l’avez spécifié :

1. Si votre application logique n’est pas déjà en cours d’exécution, choisissez **Exécuter** dans la barre d’outils du concepteur.

2. Envoyez-vous un e-mail qui respecte les critères suivants :

   * L’objet de votre e-mail contient le texte que vous avez spécifié dans le **Filtre Objet** du déclencheur : ```Business Analyst 2 #423501```

   * Votre e-mail comporte au moins une pièce jointe. 
   Pour le moment, créez simplement un fichier texte vide et joignez-le à votre e-mail.

   * Le corps de votre e-mail comporte des éléments de test, par exemple : 

     ```
     Testing my logic app
     ```

   Si votre application logique n’a pas été activée ni exécutée en dépit d’un déclencheur réussi, voir [Résoudre les problèmes et diagnostiquer les échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

3. Vérifiez que votre application logique a enregistré l’e-mail dans le conteneur de stockage approprié. 

   1. Dans l’Explorateur Stockage, développez **(Local and Attached) (local et attaché)** > 
   **Comptes de stockage** > **attachmentstorageacct (Externe)** > 
   **Conteneurs d’objets blob** > **pièces jointes**.

   2. Vérifiez le conteneur **pièces jointes** de l’e-mail. 

      À ce stade, seul l’e-mail s’affiche dans le conteneur, car l’application logique ne traite pas encore les pièces jointes.

      ![Rechercher l’e-mail enregistré dans l’Explorateur Stockage](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Lorsque vous avez terminé, supprimez l’e-mail dans l’Explorateur Stockage.

4. Si vous le souhaitez, pour tester la branche **Si false** qui n’effectue aucune action pour le moment, vous pouvez envoyer un e-mail qui ne réponde pas aux critères.

Ajoutez une boucle pour traiter toutes les pièces jointes.

## <a name="process-attachments"></a>Traiter des pièces jointes

Cette application logique utilise une boucle **For Each** pour traiter chaque pièce jointe de l’e-mail.

1. Sous la forme **Créer un objet blob pour le corps de l’e-mail**, choisissez **… Plus**, puis sélectionnez cette commande : **Add a for each (Ajouter une boucle For Each)**.

   ![Ajouter une boucle « For Each »](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

2. Renommez votre boucle à l’aide de cette description : ```For each email attachment```.

3. Indiquez les données de la boucle à traiter. Cliquez dans la zone **Sélectionnez un résultat à partir des étapes précédentes**. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez **Pièces jointes**. 

   ![Sélectionner « Pièces jointes »](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Le champ **Pièces jointes** transmet un tableau qui contient toutes les pièces jointes incluses dans un e-mail. 
   La boucle **For Each** répète des actions sur chaque élément transmis avec le tableau.

4. Enregistrez votre application logique.

Ajoutez l’action qui enregistre chaque pièce jointe sous la forme d’un objet blob dans votre conteneur de stockage **pièces jointes**.

## <a name="create-blobs-for-attachments"></a>Créer des objets blob pour les pièces jointes

1. Dans la boucle **For Each**, choisissez **Ajouter une action** afin de pouvoir spécifier la tâche à effectuer sur chaque pièce jointe trouvée.

   ![Ajouter une action à la boucle](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. Sous **Choisir une action**, recherchez « objet blob », puis sélectionnez cette action : **Stockage Blob Azure : créer un objet blob**.

   ![Ajouter une action pour créer un objet blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Renommez l’action **Créer un objet blob 2** avec cette description : ```Create blob for each email attachment```.

4. Dans l’action **Créer un objet blob pour chaque pièce jointe d’e-mail**, indiquez ces informations, puis sélectionnez les paramètres pour créer chaque objet blob comme indiqué et décrit :

   ![Fournir des informations sur l’objet blob](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Paramètre | Valeur | Description | 
   | ------- | ----- | ----------- | 
   | **Chemin d’accès du dossier** | /pièces jointes | Chemin d’accès et nom du conteneur que vous avez créé précédemment. Vous pouvez également rechercher un conteneur et le sélectionner. | 
   | **Nom de l’objet blob** | Champ **Nom** | Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez **Nom** pour transmettre le nom de la pièce jointe pour le nom de l’objet blob. | 
   | **Contenu de l’objet blob** | Champ **Contenu** | Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez **Contenu** pour transmettre le contenu de la pièce jointe pour le contenu de l’objet blob. |
   |||| 

5. Enregistrez votre application logique. 

### <a name="check-attachment-handling"></a>Vérifier le traitement des pièces jointes

À présent, vérifiez si votre application logique gère les pièces jointes comme vous l’avez spécifié :

1. Si votre application logique n’est pas déjà en cours d’exécution, choisissez **Exécuter** dans la barre d’outils du concepteur.

2. Envoyez-vous un e-mail qui respecte les critères suivants :

   * L’objet de votre e-mail contient le texte que vous avez spécifié dans le **Filtre Objet** du déclencheur : ```Business Analyst 2 #423501```

   * Votre e-mail comporte au moins deux pièces jointes. 
   Pour le moment, créez simplement deux fichiers texte vides et joignez-les à votre e-mail.

   Si votre application logique n’a pas été activée ni exécutée en dépit d’un déclencheur réussi, voir [Résoudre les problèmes et diagnostiquer les échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

3. Vérifiez que votre application logique a enregistré l’e-mail et les pièces jointes dans le conteneur de stockage approprié. 

   1. Dans l’Explorateur Stockage, développez **(Local and Attached) (local et attaché)** > 
   **Comptes de stockage** > **attachmentstorageacct (Externe)** > 
   **Conteneurs d’objets blob** > **pièces jointes**.

   2. Dans le conteneur **pièces de jointes**, recherchez l’e-mail et les pièces jointes.

      ![Rechercher l’e-mail et les pièces jointes enregistrés](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Lorsque vous avez terminé, supprimez l’e-mail et les pièces jointes dans l’Explorateur Stockage.

Ajoutez une action afin que votre application logique envoie un e-mail pour passer en revue les pièces jointes.

## <a name="send-email-notifications"></a>Envoyer des notifications par e-mail

1. Dans la branche **Si true**, sous la boucle **Pour chaque pièce jointe d’e-mail**, choisissez **Ajouter une action**. 

   ![Ajouter une action sous la boucle « For Each »](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. Sous **Choisir une action**, recherchez « envoyer un e-mail », puis sélectionnez l’action « envoyer un e-mail » pour le fournisseur de messagerie de votre choix. Pour filtrer la liste d’actions pour un service spécifique, vous pouvez sélectionner tout d’abord le connecteur sous **Connecteurs**.

   ![Sélectionner l’action « Envoyer un e-mail » pour votre fournisseur de messagerie](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Pour les comptes Azure professionnels ou scolaires, sélectionnez Office 365 Outlook. 
   * Pour les comptes Microsoft personnels, sélectionnez Outlook.com. 

3. Si vous êtes invité à entrer vos informations d’identification, connectez-vous à votre compte de messagerie afin que Logic Apps puisse établir une connexion avec votre compte de messagerie.

4. Renommez l’action **Envoyer un e-mail** avec cette description : ```Send email for review```.

5. Indiquez les informations de cette action, puis sélectionnez les champs que vous souhaitez inclure dans l’e-mail comme indiqué et décrit. Pour ajouter des lignes vides dans une zone d’édition, appuyez sur Maj + Entrée.  

   Par exemple, si vous utilisez la liste de contenu dynamique :

   ![Envoyer une notification par e-mail](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Si vous ne trouvez pas un champ escompté dans la liste, sélectionnez **Afficher plus** à côté de **À la réception d’un e-mail** dans la liste de contenu dynamique ou à la fin de la liste des paramètres.

   | Paramètre | Valeur | Notes | 
   | ------- | ----- | ----- | 
   | **To** | <*recipient-email-address*> | À des fins de test, vous pouvez utiliser votre propre adresse e-mail. | 
   | **Objet**  | ```ASAP - Review applicant for position: ``` **Subject** | Objet de l’e-mail que vous souhaitez inclure. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez le champ **Objet** sous **À la réception d’un e-mail**. | 
   | **Corps** | ```Please review new applicant:``` <p>```Applicant name: ``` **De** <p>```Application file location: ``` **Chemin d’accès** <p>```Application email content: ``` **Corps** | Contenu du corps de l’e-mail. Dans la liste des paramètres ou la liste de contenu dynamique, sélectionnez les champs suivants : <p>- Champ **De** situé sous **À la réception d’un e-mail** </br>- Champ **Chemin d’accès** situé sous **Créer un objet blob pour le corps de l’e-mail** </br>- Champ **Corps** situé sous **Call RemoveHTMLFunction to clean email body (Appeler RemoveHTMLFunction pour nettoyer le corps de l’e-mail)** | 
   |||| 

   Si vous sélectionnez un champ qui contient un tableau, tel que **Contenu**, qui est un tableau contenant des pièces jointes, le concepteur ajoute automatiquement une boucle For Each autour de l’action qui référence ce champ. 
   De cette façon, votre application logique peut effectuer cette action sur chaque élément du tableau. 
   Pour supprimer la boucle, supprimez le champ du tableau, déplacez l’action de référencement en dehors de la boucle, choisissez les points de suspension (**...** ) dans la barre de titre de la boucle, puis **Supprimer**.
     
6. Enregistrez votre application logique. 

Testez votre application logique, qui ressemble désormais à l’exemple suivant :

![Application logique terminée](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Exécuter votre application logique

1. Envoyez-vous un e-mail qui respecte les critères suivants :

   * L’objet de votre e-mail contient le texte que vous avez spécifié dans le **Filtre Objet** du déclencheur : ```Business Analyst 2 #423501```

   * Votre e-mail contient une ou plusieurs pièces jointes. 
   Vous pouvez réutiliser un fichier texte vide de votre test précédent. 
   Pour un scénario plus réaliste, joignez un fichier de CV.

   * Le corps de l’e-mail contient le texte suivant que vous pouvez copier et coller :

     ```
     Name: Jamal Hartnett   
     
     Street address: 12345 Anywhere Road   
     
     City: Any Town   
     
     State or Country: Any State   
     
     Postal code: 00000   
     
     Email address: jamhartnett@outlook.com   
     
     Phone number: 000-000-0000   
     
     Position: Business Analyst 2 #423501   

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook   

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  
     
     Certifications: Six Sigma Green Belt, Lean Project Management   
     
     Language skills: English, Mandarin, Spanish   
     
     Education: Master of Business Administration   
     ```

2. Exécutez votre application logique. En cas de réussite, votre application logique vous envoie un e-mail qui ressemble à l’exemple suivant :

   ![Notification par e-mail envoyée par l’application logique](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Si vous ne recevez aucun e-mail, vérifiez le dossier Courrier indésirable de votre messagerie. 
   Il se peut que le filtre de courrier indésirable redirige ces types d’e-mails. 
   Sinon, si vous ne savez pas si votre application logique s’est correctement exécutée, consultez [Dépanner votre application logique](../logic-apps/logic-apps-diagnosing-failures.md).

Félicitations ! Vous avez maintenant créé et exécuté une application logique qui automatise les tâches dans différents services Azure et appelle un code personnalisé.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le groupe de ressources qui contient votre application logique et les ressources associées. Dans le menu Azure principal, accédez à **Groupes de ressources**, puis sélectionnez le groupe de ressources de votre application logique. Choisissez **Supprimer un groupe de ressources**. Confirmez le nom du groupe de ressources, puis choisissez **Supprimer**.

![Supprimer le groupe de ressources de l’application logique](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez créé une application logique qui traite et stocke les pièces jointes en intégrant des services Azure, tels que Stockage Azure et Azure Functions. À présent, découvrez d’autres connecteurs que vous pouvez utiliser pour créer des applications logiques.

> [!div class="nextstepaction"]
> [En savoir plus sur les connecteurs de Logic Apps](../connectors/apis-list.md)
