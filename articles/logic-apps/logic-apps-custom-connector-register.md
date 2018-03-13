---
title: "Inscrire des connecteurs personnalisés - Azure Logic Apps | Microsoft Docs"
description: "Configurer des connecteurs personnalisés pour pouvoir les utiliser dans Azure Logic Apps"
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
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 722b82aabe796367d906e9338355b83a1a8b1e1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2018
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Inscrire des connecteurs personnalisés dans Azure Logic Apps

Une fois que vous avez créé votre API REST, configuré l’authentification et récupéré votre fichier de définition OpenAPI ou une collection Postman, vous pouvez inscrire votre connecteur. 

## <a name="prerequisites"></a>configuration requise

Pour inscrire votre connecteur personnalisé, vous aurez besoin de ces éléments :

* Les informations d’inscription de votre connecteur dans Azure, notamment le nom, l’abonnement Azure, le groupe de ressources Azure et l’emplacement que vous souhaitez utiliser.

* Un fichier OpenAPI (Swagger) ou une collection Postman décrivant votre API.

  Dans le cadre de ce didacticiel, vous pouvez utiliser [l’exemple de fichier OpenAPI Azure Resource Manager](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json).

* Une icône qui représente votre connecteur.

* Une brève description de votre connecteur.

* L’emplacement de l’hôte de votre API.

## <a name="1-create-your-connector"></a>1. Créer le connecteur

1. Dans le portail Azure, dans le menu principal Azure, choisissez **Créer une ressource**. Dans la zone de recherche, entrez le filtre « connecteur logic apps » et appuyez sur ENTRÉE.

   ![Nouveau, recherchez « connecteur logic apps »](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. Dans la liste des résultats, sélectionnez **Connecteur Logic Apps** > **Créer**.

   ![Créez un connecteur Logic Apps](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. Entrez les informations d’inscription de votre connecteur, comme dans le tableau. Lorsque c’est fait, cliquez sur **Épingler au tableau de bord** > **Créer**.

   ![Détails du connecteur personnalisé Logic Apps](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | Propriété | Valeur suggérée | Description | 
   | -------- | --------------- | ----------- | 
   | **Name** | *nom-connecteur-personnalisé* | Donnez un nom à votre connecteur. | 
   | **Abonnement** | *nom-abonnement-Azure* | Sélectionnez votre abonnement Azure. | 
   | **Groupe de ressources** | *nom-groupe-de-ressources-Azure* | Créez ou sélectionnez un groupe Azure pour organiser vos ressources Azure. | 
   | **Lieu** | *région-déploiement* | Sélectionnez une région de déploiement de votre connecteur. | 
   |||| 

   Une fois qu’Azure aura déployé votre connecteur personnalisé, le menu correspondant s’ouvrira automatiquement. 
   Dans le cas contraire, cliquez sur votre connecteur personnalisé dans le tableau de bord Azure.

## <a name="2-define-your-connector"></a>2. Définir le connecteur

Maintenant, spécifiez le fichier OpenAPI ou la collection Postman permettant de créer le connecteur, l’authentification qu’il utilise, les actions et les déclencheurs qu’il fournit et les paramètres utilisables par les actions et les déclencheurs.

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. Spécifier le fichier OpenAPI ou la collection Postman du connecteur

1. Dans le menu de votre connecteur, sélectionnez **Connecteur Logic Apps** si ce n’est pas encore le cas. Dans la barre d’outils, cliquez sur **Modifier**.

   ![Modifiez le connecteur personnalisé](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. Cliquez sur **Général** pour pouvoir indiquer les informations de ces tableaux nécessaires à la création, la sécurisation et la définition des actions et des déclencheurs de votre connecteur personnalisé.

   1. Pour **Connecteurs personnalisés**, sélectionnez une option vous permettant de fournir le fichier OpenAPI (Swagger) qui décrit votre API.

      ![Fournissez le fichier OpenAPI de votre API](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | Option | Format |DESCRIPTION | 
      | ------ | ------ | ----------- | 
      | **Charger un fichier OpenAPI** | *fichier-JSON-OpenAPI (Swagger)* | Accédez à l’emplacement de votre fichier OpenAPI et sélectionnez-le. | 
      | **Utiliser une URL OpenAPI** | http://*chemin-d-acces-au-fichier-JSON-Swagger* | Indiquez l’URL du fichier OpenAPI de votre API. | 
      | **Charger une collection Postman V1** | *fichier-V1-collection-Postman-exportée* | Accédez à l’emplacement d’une collection Postman exportée au format V1. | 
      |||| 

   2. Pour **Informations générales**, chargez une icône pour votre connecteur. 
   En règle générale, les champs **Description**, **Hôte** et **URL de Base** sont remplis automatiquement à partir de votre fichier OpenAPI. 
   Mais, si ce n’est pas le cas, ajoutez ces informations comme dans le tableau, puis cliquez sur **Continuer**. 

      ![Détails du connecteur](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | Paramètre ou option | Format | DESCRIPTION | 
      | ----------------- | ------ | ----------- | 
      | **Icône Charger** | *fichier-PNG-ou-JPG-inférieur-à-1-Mo* | Icône qui représente votre connecteur. <p>Couleur : de préférence un logo blanc sur un arrière-plan de couleur. <p>Dimensions : un logo d’environ 160 pixels à l’intérieur d’un carré de 230 pixels. | 
      | **Couleur d’arrière-plan de l’icône** | *code-hexadécimal-couleur-marque-icône* | <p>Couleur derrière l’icône, qui correspond à la couleur d’arrière-plan du fichier d’icône. <p>Format : hexadécimal. Par exemple, #007ee5 représente la couleur bleue. | 
      | **Description** | *description-connecteur* | Rédigez une brève description de votre connecteur. | 
      | **Hôte** | *hôte-connecteur* | Indiquez le domaine hôte de votre API Web. | 
      | **URL de base** | *URL-base-connecteur* | Indiquez l’URL de base de votre API Web. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Décrire l’authentification utilisée par le connecteur

1. Cliquez maintenant sur **Sécurité** afin de consulter ou de décrire l’authentification utilisée par le connecteur. L’authentification permet de veiller à ce que les identités des utilisateurs transitent correctement entre le service et les éventuels clients.

   ![Type d'authentification](./media/logic-apps-custom-connector-register/security.png)

   * Si vous chargez un fichier OpenAPI, l’Assistant Inscription détecte automatiquement le type d’authentification qu’utilise votre API Web et remplit la section **Sécurité** en fonction de l’objet `securityDefinitions` de ce fichier. Par exemple, voici une section qui impose l’utilisation d’OAuth2.0 :

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * Si votre fichier OpenAPI n’a pas rempli les propriétés et le type d’authentification, cliquez sur **Modifier** de façon à pouvoir ajouter ces informations. 
   
     Par exemple, si vous avez au préalable [configuré l’authentification Azure AD dans ce didacticiel](../logic-apps/custom-connector-azure-active-directory-authentication.md), vous avez créé des applications Azure AD permettant de sécuriser votre API Web et votre connecteur. 
     Vous pouvez donc à présent indiquer la clé client et l’ID d’application que vous avez enregistrés auparavant :
    
     | Paramètre | Valeur suggérée | DESCRIPTION | 
     | ------- | --------------- | ----------- | 
     | **Type d’authentification** | OAuth 2.0 | | 
     | **Fournisseur d’identité** | Azure Active Directory | | 
     | **ID client** | *ID-application-Azure-AD-connecteur* | L’ID d’application que vous avez auparavant enregistré pour l’application Azure AD de votre connecteur. | 
     | **Clé secrète client** | *clé-client-application-Azure-AD-connecteur* | La clé client de l’application Azure AD de votre connecteur. | 
     | **URL de connexion** | `https://login.windows.net` | | 
     | **URL des ressources** | `https://management.core.windows.net/` | Veillez à ajouter l’URL exactement comme elle est spécifiée, barre oblique finale comprise. | 
     |||| 

   * Une collection Postman, qui génère automatiquement un fichier OpenAPI, ne remplit automatiquement le type d’authentification *que* s’il est pris en charge (par exemple, de base ou OAuth 2.0).

2. Pour enregistrer votre connecteur après avoir entré les informations de sécurité, cliquez sur **Mettre à jour le connecteur** en haut de la page, puis sur **continuer**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Consulter, mettre à jour ou définir les actions et les déclencheurs du connecteur

1. Sélectionnez maintenant **Définition** pour pouvoir consulter, modifier ou définir de nouvelles actions et de nouveaux déclencheurs que les utilisateurs pourront ajouter à leurs workflows.

   Les actions et les déclencheurs sont basés sur les opérations définies dans le fichier OpenAPI ou la collection de Postman, qui remplissent automatiquement la page **Définition** avec les valeurs de la demande et de la réponse. Par conséquent, si les opérations requises apparaissent déjà ici, vous pouvez passer à l’étape suivante du processus d’inscription sans apporter de modifications à cette page.

   ![Définition du connecteur](./media/logic-apps-custom-connector-register/definition.png)

2. Si jamais vous souhaitez modifier des déclencheurs et actions existants ou en ajouter de nouveaux, suivez ces étapes.

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>Modifier ou ajouter des actions au connecteur

1. Pour modifier une action existante, cliquez sur son numéro. Pour ajouter une action qui n’existait pas dans votre fichier OpenAPI ou dans votre collection Postman, cliquez sur **Nouvelle action** sous **Actions**.

2. Sous **Général**, indiquez ou modifiez les informations suivantes pour l’action :
   
   | Paramètre | Valeur suggérée | DESCRIPTION | 
   | ------- | --------------- | ----------- | 
   | **Résumé** | *nom-opération* | Titre de cette action. | 
   | **Description** | *description-opération* | Description de cette action. <p>**Conseil** : Vérifiez que votre description se termine par un point. |
   | **ID d’opération** | *identificateur-opération* | Nom unique permettant d’identifier cette action. Utilisez la casse mixte, par exemple : « GetPullRequest ». | 
   |**Visibilité**| **Aucune**, **avancée**, **interne** ou **importante** | Visibilité de cette action vis-à-vis des utilisateurs. Pour plus d’informations, consultez la page [Extensions OpenAPI](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   |||| 

3. Maintenant, définissez la demande de l’action.
  
   1. Dans la section **Demande**, cliquez sur **Importer à partir d’un exemple**. 

   2. Sur la page **Importer à partir d’un exemple**, collez un exemple de demande. 

      En règle générale, les exemples de demandes sont disponibles dans la documentation API, qui fournit des informations sur les champs **Verb**, **URL**, **Headers** et **Body**. Par exemple, consultez la [Documentation de l’API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importer un exemple de demande](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Si vous créez un connecteur à partir d’une collection Postman, veillez à supprimer l’en-tête `Content-type` des actions et des déclencheurs. Logic Apps l’ajoute automatiquement. Par ailleurs, supprimez les en-têtes d’authentification définis dans la section `Security` des actions et des déclencheurs.

      Pour découvrir les fonctionnalités avancées d’OpenAPI, consultez la page [Extensions OpenAPI pour les connecteurs personnalisés](../logic-apps/custom-connector-openapi-extensions.md).

   3. Pour terminer la définition de requête, cliquez sur **Importer**.

4. Maintenant, définissez la réponse de l’action.

   1. Sous **Réponse**, vous pouvez spécifier une réponse par défaut. 
   Cliquez sur **Ajouter la réponse par défaut**.

   2. Sur la page **Importer à partir d’un exemple**, collez un exemple de réponse, puis cliquez sur **Importer**.

5. Enfin, sous **Validation**, passez en revue et corrigez les problèmes potentiels identifiés pour l’action.

#### <a name="edit-or-add-triggers-for-your-connector"></a>Modifier ou ajouter des déclencheurs au connecteur

1. Pour modifier un déclencheur existant, cliquez sur son numéro. Pour ajouter un déclencheur qui n’existait pas dans votre fichier OpenAPI ou dans votre collection Postman, cliquez sur **Nouveau déclencheur** sous **Déclencheurs**.

2. Sous **Général**, indiquez ou modifiez les informations suivantes pour le déclencheur :

   | Paramètre | Valeur suggérée | DESCRIPTION | 
   | ------- | --------------- | ----------- | 
   | **Résumé** | *nom-opération* | Titre de ce déclencheur. | 
   | **Description** | *description-opération* | Description de ce déclencheur. <p>**Conseil** : Vérifiez que votre description se termine par un point. | 
   | **ID d’opération** | *identificateur-opération* | Nom unique permettant d’identifier ce déclencheur. Utilisez la casse mixte, par exemple : « TriggerOnGitHubPushEvent ». | 
   |**Visibilité**| **Aucune**, **avancée**, **interne** ou **importante** | Visibilité de ce déclencheur vis-à-vis des utilisateurs. Pour plus d’informations, consultez la page [Extensions OpenAPI](../logic-apps/custom-connector-openapi-extensions.md#visibility). | 
   | **Type de déclencheur** | **Webhook** ou **Interrogation** | Type de ce déclencheur. Par exemple, un déclencheur Webhook attend qu’un événement précis se produise pour déclencher l’événement. Un déclencheur d’interrogation vérifie régulièrement un point de terminaison de service à un intervalle et une fréquence spécifiés. <p>Pour plus d’informations sur les modèles de déclencheurs Webhook et de déclencheurs d’interrogation, consultez la page [Créer des API personnalisées](../logic-apps/logic-apps-create-api-app.md). | 
   |||| 

3. Maintenant, définissez la demande qui crée le déclencheur. 

   1. Dans la section **Demande**, cliquez sur **Importer à partir d’un exemple**.

   2. Sur la page **Importer à partir d’un exemple**, collez un exemple de demande. 

      En règle générale, les exemples de demandes sont disponibles dans la documentation API, qui fournit des informations sur les champs **Verb**, **URL**, **Headers** et **Body**. Par exemple, consultez la [Documentation de l’API Analyse de texte](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

      ![Importer un exemple de demande](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Si vous créez un connecteur à partir d’une collection Postman, veillez à supprimer l’en-tête `Content-type` des actions et des déclencheurs. Logic Apps l’ajoute automatiquement. Par ailleurs, supprimez les en-têtes d’authentification définis dans la section `Security` des actions et des déclencheurs.

      Pour découvrir les fonctionnalités avancées d’OpenAPI, consultez la page [Extensions OpenAPI pour les connecteurs personnalisés](../logic-apps/custom-connector-openapi-extensions.md).

   3. Pour terminer la définition de requête, cliquez sur **Importer**. 

4. À présent, définissez la réponse du déclencheur. Dans la section **Réponse**, selon le type du déclencheur, suivez les étapes ci-dessous :

   **Déclencheur Webhook**
   1. Dans **Réponse Webhook**, cliquez sur **Importer à partir d’un exemple**. 

   2. Sur la page **Importer à partir d’un exemple**, collez un exemple de réponse, puis cliquez sur **Importer**. Vous trouverez un exemple de réponse dans la [Référence de l’API GitHub pour la création d’un Webhook](https://developer.github.com/v3/repos/hooks/#create-a-hook).

   3. Sous **Configuration du déclencheur**, sélectionnez un paramètre à utiliser pour la demande de création du Webhook. Logic Apps se sert de cette valeur de paramètre afin de remplir l’URL de rappel utilisée par le service pour communiquer avec le déclencheur.

   **Déclencheur d’interrogation**
   1. Sous **Réponse**, vous pouvez spécifier une réponse par défaut. 
   Cliquez sur **Ajouter la réponse par défaut**.

   2. Sur la page **Importer à partir d’un exemple**, collez un exemple de réponse, puis cliquez sur **Importer**.

   3. Sous **Configuration du déclencheur**, spécifiez le paramètre de la requête, la valeur à lui transmettre et un *Indicateur de déclencheur* qui spécifie un intervalle d’interrogation approprié pour la demande suivante.

5. Enfin, sous **Validation**, passez en revue et corrigez les problèmes potentiels identifiés pour le déclencheur.

#### <a name="review-reference-definitions-for-your-connector"></a>Vérifier les définitions de référence du connecteur

La section **Références**, qui se remplit automatiquement à partir de la description de votre API, décrit les champs de paramètre auxquels les actions et les déclencheurs peuvent faire référence. 

1. Sous **Références**, cliquez sur le numéro de la définition de référence que vous souhaitez examiner.

2. Sous **Nom**, vérifiez ou mettez à jour le nom de définition de référence.

3. Sous **Validation**, passez en revue et corrigez les problèmes potentiels identifiés pour la définition de référence.

## <a name="3-finish-creating-your-connector"></a>3. Terminer la création du connecteur

Dès que vous avez effectué toutes les opérations nécessaires, cliquez sur **Créer** afin de déployer votre connecteur. Si vous mettez à jour un connecteur existant, cliquez sur **Mettre à jour le connecteur**.

Félicitations ! Dorénavant, lorsque vous créerez une application logique, vous trouverez votre connecteur dans le Concepteur Logic Apps et vous pourrez l’ajouter à votre application logique.

![Dans le Concepteur Logic Apps, recherchez votre connecteur](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Partager le connecteur avec d’autres utilisateurs Logic Apps

Les connecteurs personnalisées inscrits mais non certifiés fonctionnent comme des connecteurs gérés par Microsoft, mais sont visibles et accessibles *uniquement* à l’auteur du connecteur et aux utilisateurs qui ont le même client Azure Active Directory et un abonnement Azure pour les applications logiques dans la région où sont déployées ces applications. Bien que le partage soit facultatif, certains scénarios peuvent vous amener à vouloir partager vos connecteurs avec d’autres utilisateurs. 

> [!IMPORTANT]
> Si vous partagez un connecteur, d’autres personnes peuvent en devenir dépendantes. 
> ***Supprimer votre connecteur a pour conséquence la suppression de toutes les connexions à ce connecteur.***
 
Pour partager votre connecteur avec des utilisateurs externes à l’extérieur de ces limites, par exemple, avec tous les utilisateurs Logic Apps, Flow et PowerApps, [soumettez votre connecteur à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>Forum Aux Questions

**Q :** Y a-t-il des limites qui s’imposent aux connecteurs personnalisés ? </br>
**R :** Oui. Vous trouverez les [limites des connecteurs personnalisés ici](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtenir de l’aide

* Pour obtenir de l’aide au développement et à l’intégration, ou pour demander des fonctionnalités qui ne sont pas disponibles dans l’Assistant Inscription, contactez [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft surveille les problèmes et les questions des développeurs sur ce compte, et les transfère à l’équipe concernée.

* Pour poser ou répondre à des questions, ou voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Afin de contribuer à améliorer Logic Apps, votez pour des idées ou soumettez-en sur le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Étapes suivantes

* [Facultatif : Certifier un connecteur](../logic-apps/custom-connector-submit-certification.md)
* [FAQ sur les connecteurs personnalisés](../logic-apps/custom-connector-faq.md)