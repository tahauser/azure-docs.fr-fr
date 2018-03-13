---
title: "Se connecter à Dynamics 365 (Online) à partir de vos applications Azure Logic Apps | Microsoft Docs"
description: "Créer des flux de travail d’application logique qui gèrent des entités Dynamics 365 (Online) via l’API fournie par le connecteur Dynamics 365"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Se connecter à Dynamics 365 à partir de flux de travail d’application logique

Avec les applications Logic Apps, vous pouvez vous connecter à Dynamics 365 (Online) et créer des flux d’activité utiles qui génèrent des enregistrements, mettent à jour les éléments ou renvoient une liste d’enregistrements. Avec le connecteur Dynamics 365, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Dynamics 365 (Online).
* Utiliser les actions qui obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un élément est mis à jour dans Dynamics 365 (Online), vous pouvez envoyer un courrier électronique à l’aide d’Office 365.

Cette rubrique vous explique comment créer une application logique qui génère une tâche dans Dynamics 365 lorsqu’un prospect est créé dans Dynamics 365.

## <a name="prerequisites"></a>Prérequis
* Un compte Azure.
* Un compte Dynamics 365 (Online).

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Créer une tâche lorsqu’un prospect est créé dans Dynamics 365

1.  [Connectez-vous à Azure](https://portal.azure.com).

2.  Dans la zone de recherche Azure, tapez `Logic apps` et appuyez sur ENTRÉE.

      ![Rechercher Logic Apps](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  Sous **Logic Apps**, cliquez sur **Ajouter**.

      ![LogicApp - Ajouter](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  Pour créer l’application logique, renseignez les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**, puis cliquez sur **Créer**.

5.  Sélectionnez la nouvelle application logique. Lorsque vous recevez la notification **Déploiement réussi**, cliquez sur **Actualiser**.

6.  Sous **Outils de développement**, cliquez sur **Concepteur d’application logique**. Dans la liste des modèles, cliquez sur **Application logique vide**.

7.  Dans la zone de recherche, tapez `Dynamics 365`. Dans la liste des déclencheurs Dynamics 365, sélectionnez **Dynamics 365 – Lorsqu'un enregistrement est créé**.

8.  Si vous êtes invité à vous connecter à Dynamics 365, faites-le maintenant.

9.  Entrez les informations suivantes dans les détails du déclencheur :

  * **Nom de l’organisation**. Sélectionnez l’instance de Dynamics 365 que l’application logique doit écouter.

  * **Nom de l’entité**. Sélectionnez l’entité que vous souhaitez écouter. Cet événement agit comme un déclencheur pour démarrer l’application logique. 
  Dans cette procédure pas à pas, **Prospects** est sélectionné.

  * **How often do you want to check for items?** (À quelle fréquence souhaitez-vous rechercher des éléments ?) Ces valeurs définissent la fréquence à laquelle l’application logique recherche des mises à jour pour le déclencheur. Le paramètre par défaut consiste à rechercher des mises à jour toutes les trois minutes.

    * **Fréquence**. Sélectionnez les secondes, les minutes, les heures ou les jours.

    * **Intervalle**. Entrez le nombre de secondes, de minutes, d’heures ou de jours qui doivent s’écouler avant la recherche suivante.

      ![Application logique - Détails sur le déclencheur](./media/connectors-create-api-crmonline/trigger-details.png)

10. Cliquez sur **Nouvelle étape**, puis sur **Ajouter une action**.

11. Dans la zone de recherche, tapez `Dynamics 365`. Dans la liste des actions, sélectionnez **Dynamics 365 – Créer un enregistrement**.

12. Entrez les informations suivantes :

    * **Nom de l’organisation**. Sélectionnez l’instance de Dynamics 365 dans laquelle vous souhaitez que le flux crée l’enregistrement. 
    Notez qu’il ne s’agit pas nécessairement de la même instance que celle depuis laquelle l’événement est déclenché.

    * **Nom de l’entité**. Sélectionnez l’entité dans laquelle vous souhaitez créer un enregistrement lorsque l’événement est déclenché. 
    Dans cette procédure pas à pas, **Tâches** est sélectionné.

13. Cliquez sur le champ **Objet** qui apparaît. Dans la liste de contenu dynamique qui s’affiche, vous pouvez sélectionner un de ces champs :

    * **Nom**. Sélectionner ce champ permettra d’insérer le nom du prospect dans le champ Objet de la tâche, lorsque l’enregistrement de la tâche est créé.
    * **Rubrique**. Sélectionner ce champ permettra d’insérer le champ Rubrique dans le champ Objet de la tâche, lorsque l’enregistrement de la tâche est créé. 
    Cliquez sur **Rubrique** pour l’ajouter à la zone **Objet**.

      ![Application logique - Détails Créer un enregistrement](./media/connectors-create-api-crmonline/create-record-details.png)

14. Dans la barre d’outils du Concepteur d’application logique, cliquez sur **Enregistrer**.

    ![Barre d’outils du Concepteur d’application logique - Enregistrer](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. Pour démarrer l’application logique, cliquez sur **Exécuter**.

    ![Barre d’outils du Concepteur d’application logique - Enregistrer](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Créez maintenant un enregistrement de prospect dans Dynamics 365 pour les ventes et observez votre flux en action !

## <a name="set-advanced-options-for-a-logic-app-step"></a>Définir les options avancées d’une étape d’application logique

Pour spécifier comment filtrer les données dans une étape d’application logique, cliquez sur **Afficher les options avancées** dans cette étape, puis ajoutez un filtre ou une commande par requête.

Par exemple, vous pouvez utiliser une requête de filtre pour obtenir uniquement les comptes actifs et trier par nom du compte. Pour effectuer cette tâche, entrez la requête de filtre OData `statuscode eq 1` et sélectionnez **Nom de compte** dans la liste de contenu dynamique. Plus d’informations : [MSDN : $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) et [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Application logique - Options avancées](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Meilleures pratiques lors de l’utilisation des options avancées

Lorsque vous ajoutez une valeur à un champ, vous devez faire correspondre le type de champ si vous saisissez une valeur ou sélectionner une valeur dans la liste de contenu dynamique.

Type de champ  |Utilisation  |Comment y accéder  |NOM  |Type de données  
---------|---------|---------|---------|---------
Champs de texte|Les champs de texte nécessitent une seule ligne de texte ou du contenu dynamique qui est un champ de type texte. Exemples : Catégorie et Sous-catégorie.|Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |category |Ligne de texte unique        
Champs de type entier | Certains champs nécessitent un entier ou un contenu dynamique qui est un champ de type entier. Exemples : Pourcentage achevé et Durée. |Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |percentcomplete |Nombre entier         
Champs de date | Certains champs nécessitent qu’une date soit saisie au format mm/jj/aaaa ou un contenu dynamique qui est un champ de type date. Exemples : Créé le, Date de début, Début réel, Dernière durée de suspension, Fin réelle et Date d’échéance. | Paramètres > Personnalisations > Personnaliser le système > Entités > Tâche > Champs |createdon |Date et heure
Champs qui nécessitent à la fois un ID d’enregistrement et un type de recherche |Certains champs qui font référence à un autre enregistrement d’entité nécessitent l’ID d’enregistrement et le type de recherche. |Paramètres > Personnalisations > Personnaliser le système > Entités > Compte > Champs  | accountid  | Clé primaire

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Exemples de champs supplémentaires nécessitant un ID d’enregistrement et un type de recherche
Pour compléter le tableau précédent, voici des exemples de champs supplémentaires qui ne fonctionnent pas avec les valeurs sélectionnées dans la liste de contenu dynamique. À la place, ces champs nécessitent un ID d’enregistrement et un type de recherche saisis dans les champs de PowerApps.  
* Propriétaire et Type de propriétaire. Le champ Propriétaire doit être un ID d’enregistrement d’utilisateur ou d’équipe valide. Le champ Type de propriétaire doit être **systemusers** ou **équipes**.
* Client et Type de client. Le champ Client doit être un ID d’enregistrement de compte ou de contact valide. Le champ Type de propriétaire doit être **comptes** ou **contacts**.
* Concernant et Type Concernant. Le champ Concernant doit être un ID d’enregistrement valide, tel qu’un ID d’enregistrement de contact ou de compte. Le champ Type Concernant doit être le type de recherche pour l’enregistrement, tel que **comptes** ou **contacts**.

L’exemple d’action de création de tâche suivant ajoute un enregistrement de compte qui correspond à l’ID d’enregistrement en l’ajoutant au champ Concernant de la tâche.

![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid-type-account.png)

Cet exemple affecte également la tâche à un utilisateur spécifique en fonction de l’ID d’enregistrement d’utilisateur.

![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid-type-user.png)

Pour rechercher un ID d’enregistrement, consultez la section suivante : *Recherche de l’ID d’enregistrement*

## <a name="find-the-record-id"></a>Recherche de l’ID d’enregistrement

1. Ouvrez un enregistrement, comme un enregistrement de compte.

2. Dans la barre d’outils d’actions, cliquez sur **Ouvrir dans une nouvelle fenêtre** ![enregistrement - fenêtre indépendante](./media/connectors-create-api-crmonline/popout-record.png).
Vous pouvez également cliquer sur **ENVOYER UN LIEN PAR COURRIER ÉLECTRONIQUE** dans la barre d’outils d’actions pour copier l’URL complète dans votre programme de messagerie par défaut.

   L’ID d’enregistrement est affiché entre les caractères d’encodage %7b et %7d de l’URL.

   ![Flux : ID d’enregistrement et type de compte](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Résolution de problèmes
Pour résoudre les problèmes qui peuvent se produire lors d’une étape dans une application logique, affichez les détails de l’état de l’événement.

1. Dans la zone **Logic Apps**, sélectionnez votre application logique, puis cliquez sur **Vue d’ensemble**. 

   La zone Résumé s’affiche et indique l’état d’exécution de l’application logique. 

   ![État d’exécution de l’application logique](./media/connectors-create-api-crmonline/tshoot1.png)

2. Pour afficher plus d’informations sur les exécutions qui ont échoué, cliquez sur l’événement ayant rencontré des problèmes. Pour développer une étape qui a échoué, cliquez sur cette étape.

   ![Développer l’étape qui a échoué](./media/connectors-create-api-crmonline/tshoot2.png)

   Les détails de l’étape s’affichent et peuvent aider à résoudre la cause du problème.

   ![Détails de l’étape qui a échoué](./media/connectors-create-api-crmonline/tshoot3.png)

Pour plus d’informations sur la résolution des problèmes relatifs aux applications logiques, consultez [Diagnostic des échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Consultez l’ensemble des déclencheurs et actions définis dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/crm/). 

## <a name="next-steps"></a>étapes suivantes
Explorez les autres connecteurs disponibles dans les applications logiques en consultant notre [liste d’API](apis-list.md).
