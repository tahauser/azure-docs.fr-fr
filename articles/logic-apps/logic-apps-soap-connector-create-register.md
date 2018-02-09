---
title: "Créer et inscrire des connecteurs SOAP - Azure Logic Apps | Microsoft Docs"
description: Configurer des connecteurs SOAP pour pouvoir les utiliser dans Azure Logic Apps
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
ms.date: 10/24/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 031762e5639fc52e0b0a6a5bf8d12db25da25e12
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="create-and-register-soap-connectors-in-azure-logic-apps"></a>Créer et inscrire des connecteurs SOAP dans Azure Logic Apps

Pour intégrer les services SOAP à vos flux de travail d’application logique, vous pouvez créer et inscrire un connecteur SOAP (Simple Object Access Protocol) personnalisé à l’aide du langage de description de services (WSDL) qui décrit votre service SOAP. Les connecteurs SOAP fonctionnant comme des connecteurs prédéfinis, vous pouvez les utiliser de la même façon que les autres connecteurs dans vos applications logiques.


## <a name="prerequisites"></a>configuration requise

Pour inscrire votre connecteur SOAP, vous avez besoin de ces éléments :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez commencer avec un [compte Azure gratuit](https://azure.microsoft.com/free/). Sinon, souscrivez un [abonnement avec paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* L’un de ces éléments :
  * Une URL vers un fichier WSDL qui définit votre service SOAP et les API
  * Un fichier WSDL qui définit votre service SOAP et les API

  Pour ce didacticiel, vous pouvez utiliser notre exemple de [service SOAP Orders](http://fazioapisoap.azurewebsites.net/FazioService.svc?singleWsdl).

* Facultatif : une image à utiliser comme icône pour votre connecteur personnalisé.


## <a name="1-create-your-connector"></a>1. Créer le connecteur

1. Sur le Portail Azure, dans le menu principal Azure, cliquez sur **Nouveau**. Dans la zone de recherche, entrez le filtre « connecteur logic apps » et appuyez sur ENTRÉE.

   ![Nouveau, recherchez « connecteur logic apps »](./media/logic-apps-soap-connector-create-register/create-logic-apps-connector.png)

2. Dans la liste des résultats, sélectionnez **Connecteur Logic Apps** > **Créer**.

   ![Créez un connecteur Logic Apps](./media/logic-apps-soap-connector-create-register/choose-logic-apps-connector.png)

3. Entrez les informations d’inscription de votre connecteur, comme dans le tableau. Lorsque c’est fait, cliquez sur **Épingler au tableau de bord** > **Créer**.

   ![Détails du connecteur personnalisé Logic Apps](./media/logic-apps-soap-connector-create-register/logic-apps-soap-connector-details.png)

   | Propriété | Valeur suggérée | Description | 
   | -------- | --------------- | ----------- | 
   | **Name** | *nom-connecteur-soap* | Donnez un nom à votre connecteur. | 
   | **Abonnement** | *nom-abonnement-Azure* | Sélectionnez votre abonnement Azure. | 
   | **Groupe de ressources** | *nom-groupe-de-ressources-Azure* | Créez ou sélectionnez un groupe Azure pour organiser vos ressources Azure. | 
   | **Lieu** | *région-déploiement* | Sélectionnez une région de déploiement de votre connecteur. | 
   |||| 

   Une fois qu’Azure aura déployé votre connecteur, le menu correspondant de l’application logique s’ouvrira automatiquement. 
   Dans le cas contraire, cliquez sur votre connecteur SOAP dans le tableau de bord Azure.

## <a name="2-define-your-connector"></a>2. Définir le connecteur

Spécifiez maintenant le fichier WSDL ou l’URL pour la création de votre connecteur, l’authentification qu’utilise votre connecteur et les actions et déclencheurs fournis par votre connecteur SOAP.


### <a name="2a-specify-the-wsdl-file-or-url-for-your-connector"></a>2a. Spécifiez le fichier WSDL ou l’URL de votre connecteur.

1. Dans le menu de votre connecteur, sélectionnez **Connecteur Logic Apps** si ce n’est pas encore le cas. Dans la barre d’outils, cliquez sur **Modifier**.

   ![Modifiez le connecteur personnalisé](./media/logic-apps-soap-connector-create-register/edit-soap-connector.png)

2. Cliquez sur **Général** pour pouvoir indiquer les informations de ces tableaux nécessaires à la création, la sécurisation et la définition des actions et des déclencheurs de votre connecteur SOAP.

   1. Pour **Connecteurs personnalisés**, sélectionnez **SOAP** pour le **Point de terminaison d’API** afin de pouvoir fournir le fichier WSDL qui décrit votre API.

      ![Fournissez le fichier WSDL de votre API](./media/logic-apps-soap-connector-create-register/provide-wsdl-file.png)

      | Option | Format |DESCRIPTION | 
      | ------ | ------ | ----------- | 
      | **Charger WSDL à partir du fichier** | *Fichier WSDL* | Accédez à l’emplacement de votre fichier WSDL et sélectionnez-le. | 
      | **Charger WSDL à partir de l’URL** | http://*chemin du fichier wsdl* | Indiquez l’URL du fichier WSDL de votre service. | 
      | **SOAP à REST** |   | Transformez les API du service SOAP en API REST. | 
      |||| 

   2. Pour **Informations générales**, chargez une icône pour votre connecteur. 
   En règle générale, les champs **Description**, **Hôte** et **URL de Base** sont remplis automatiquement à partir de votre fichier WSDL. 
   Mais, si ce n’est pas le cas, ajoutez ces informations comme dans le tableau, puis cliquez sur **Continuer**. 

      ![Détails du connecteur](./media/logic-apps-soap-connector-create-register/add-general-details.png)

      | Paramètre ou option | Format | DESCRIPTION | 
      | ----------------- | ------ | ----------- | 
      | **Icône Charger** | *fichier-PNG-ou-JPG-inférieur-à-1-Mo* | Icône qui représente votre connecteur. <p>Couleur : de préférence un logo blanc sur un arrière-plan de couleur. <p>Dimensions : un logo d’environ 160 pixels à l’intérieur d’un carré de 230 pixels. | 
      | **Couleur d’arrière-plan de l’icône** | *code-hexadécimal-couleur-marque-icône* | <p>Couleur derrière l’icône, qui correspond à la couleur d’arrière-plan du fichier d’icône. <p>Format : hexadécimal. Par exemple, #007ee5 représente la couleur bleue. | 
      | **Description** | *description-connecteur* | Rédigez une brève description de votre connecteur. | 
      | **Hôte** | *hôte-connecteur* | Indiquez le domaine hôte de votre service SOAP. | 
      | **URL de base** | *URL-base-connecteur* | Indiquez l’URL de base de votre service SOAP. | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. Décrire l’authentification utilisée par le connecteur

1. Cliquez maintenant sur **Sécurité** afin de consulter ou de décrire l’authentification utilisée par le connecteur. L’authentification permet de veiller à ce que les identités des utilisateurs transitent correctement entre le service et les éventuels clients.

   Par défaut, le **Type d’authentification** de votre connecteur a la valeur **Aucune authentification**.
   
   ![Type d'authentification](./media/logic-apps-soap-connector-create-register/security-authentication-options.png)

   Pour changer le type d’authentification, choisissez **Modifier**. Vous pouvez sélectionner **Authentification de base**. Pour utiliser des étiquettes de paramètre autres que les valeurs par défaut, mettez-les à jour sous **Étiquette du paramètre**.

   ![Authentification de base](./media/logic-apps-soap-connector-create-register/security.png)

   
2. Pour enregistrer votre connecteur après avoir entré les informations de sécurité, cliquez sur **Mettre à jour le connecteur** en haut de la page, puis sur **continuer**. 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. Consulter, mettre à jour ou définir les actions et les déclencheurs du connecteur

1. Sélectionnez maintenant **Définition** pour pouvoir consulter, modifier ou définir de nouvelles actions et de nouveaux déclencheurs que les utilisateurs pourront ajouter à leurs workflows.

   Les actions et les déclencheurs sont basés sur les opérations définies dans le fichier WSDL, qui remplissent automatiquement la page **Définition** avec les valeurs de la demande et de la réponse. Par conséquent, si les opérations requises apparaissent déjà ici, vous pouvez passer à l’étape suivante du processus d’inscription sans apporter de modifications à cette page.

   ![Définition du connecteur](./media/logic-apps-soap-connector-create-register/definition.png)

2. Si vous souhaitez modifier des déclencheurs et actions existants ou en ajouter de nouveaux, [suivez ces étapes](logic-apps-custom-connector-register.md#add-action-or-trigger).


## <a name="3-finish-creating-your-connector"></a>3. Terminer la création du connecteur

Dès que vous avez effectué toutes les opérations nécessaires, cliquez sur **Mettre à jour le connecteur** afin de déployer votre connecteur. 

Félicitations ! Dorénavant, lorsque vous créerez une application logique, vous trouverez votre connecteur dans le Concepteur Logic Apps et vous pourrez l’ajouter à votre application logique.

![Dans le Concepteur Logic Apps, recherchez votre connecteur](./media/logic-apps-soap-connector-create-register/soap-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>Partager le connecteur avec d’autres utilisateurs Logic Apps

Les connecteurs personnalisées inscrits mais non certifiés fonctionnent comme des connecteurs gérés par Microsoft, mais sont visibles et accessibles *uniquement* à l’auteur du connecteur et aux utilisateurs qui ont le même client Azure Active Directory et un abonnement Azure pour les applications logiques dans la région où sont déployées ces applications. Bien que le partage soit facultatif, certains scénarios peuvent vous amener à vouloir partager vos connecteurs avec d’autres utilisateurs. 

> [!IMPORTANT]
> Si vous partagez un connecteur, d’autres personnes peuvent en devenir dépendantes. 
> ***Supprimer votre connecteur a pour conséquence la suppression de toutes les connexions à ce connecteur.***
 
Pour partager votre connecteur avec des utilisateurs externes à l’extérieur de ces limites, par exemple, avec tous les utilisateurs Logic Apps, [soumettez votre connecteur à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="faq"></a>Forum Aux Questions

**Q :** Le connecteur SOAP est-il généralement disponible ? </br>
**R :** Le connecteur SOAP étant en **préversion**, ce n’est pas encore un service généralement disponible.

**Q :** Y a-t-il des restrictions et des problèmes connus concernant le connecteur SOAP ? </br>
**R :** Oui, consultez [Restrictions et problèmes connus relatifs à l’importation d’API](../api-management/api-management-api-import-restrictions.md#wsdl).

**Q :** Y a-t-il des limites qui s’imposent aux connecteurs personnalisés ? </br>
**R :** Oui. Vous trouverez les [limites des connecteurs personnalisés ici](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits).

## <a name="get-support"></a>Obtenir de l’aide

* Pour obtenir de l’aide au développement et à l’intégration, ou pour demander des fonctionnalités qui ne sont pas disponibles dans l’Assistant Inscription, contactez [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Microsoft surveille les problèmes et les questions des développeurs sur ce compte, et les transfère à l’équipe concernée.

* Pour poser ou répondre à des questions, ou voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Afin de contribuer à améliorer Logic Apps, votez pour des idées ou soumettez-en sur le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>étapes suivantes

* [Facultatif : Certifier un connecteur](../logic-apps/custom-connector-submit-certification.md)
* [FAQ sur les connecteurs personnalisés](../logic-apps/custom-connector-faq.md)