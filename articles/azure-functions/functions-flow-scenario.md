---
title: "Appeler une fonction Azure à partir de Microsoft Flow | Documents Microsoft"
description: "Créez un connecteur personnalisé, puis appelez une fonction à l’aide du connecteur."
services: functions
keywords: "applications cloud, services cloud, Microsoft Flow, processus métier, application métier"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 38d2e3f2f2aa057b50ba12138cafc512ac110f9b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="call-a-function-from-microsoft-flow"></a>Appeler une fonction à partir de Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) facilite l’automatisation des workflows et des processus métier entre vos services et applications préférés. Les développeurs professionnels peuvent utiliser Azure Functions pour étendre les fonctionnalités de Microsoft Flow, tout en évitant les détails techniques liés à la création de flux.

Dans cette rubrique, vous allez créer un flux en suivant un scénario de maintenance d’éoliennes. Cette rubrique vous montre comment appeler la fonction que vous avez définie dans [Créer une définition OpenAPI pour une fonction](functions-openapi-definition.md). La fonction détermine si la réparation d’urgence d’une éolienne est rentable. Si elle est rentable, le flux envoie un e-mail pour recommander la réparation.

Pour plus d’informations sur l’appel à la même fonction à partir de PowerApps, consultez [Appeler une fonction dans PowerApps](functions-powerapps-scenario.md).

Dans cette rubrique, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une liste dans SharePoint
> * Exporter une définition d’API
> * Ajouter une connexion à l’API
> * Créer un flux pour envoyer un e-mail si une réparation est rentable
> * Exécuter le flux

## <a name="prerequisites"></a>Prérequis

+ Un [compte Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) actif avec les mêmes informations d’identification que votre compte Azure. 
+ SharePoint, que vous utilisez comme source de données pour ce flux. Inscrivez-vous à un [essai gratuit d’Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) si vous n’avez pas encore SharePoint.
+ Avoir suivi le didacticiel [Créer une définition OpenAPI pour une fonction](functions-openapi-definition.md)

## <a name="create-a-sharepoint-list"></a>Créer une liste SharePoint
Vous commencez par créer une liste à utiliser comme source de données pour le flux. La liste comporte les colonnes suivantes.

| Colonne de liste     | Type de données           | Notes                                    |
|-----------------|---------------------|------------------------------------------|
| **Titre**           | Ligne de texte unique | Nom de l’éolienne                      |
| **Dernier entretien** | Date                |                                          |
| **Production max.**       | Number              | Débit de l’éolienne, en kWh            |
| **Entretien nécessaire** | Oui/Non              |                                          |
| **Effort estimé** | Number              | Durée estimée de la réparation, en heures |

1. Dans votre site SharePoint, cliquez ou appuyez sur **Nouveau**, puis sur **Liste**.

    ![Créer une liste SharePoint](./media/functions-flow-scenario/new-list.png)

2. Entrez le nom `Turbines`, puis cliquez ou appuyez sur **Créer**.

    ![Spécifier le nom de la nouvelle liste](./media/functions-flow-scenario/create-list.png)

    La liste **Turbines** (Éoliennes) est créée, avec le champ **Titre** par défaut.

    ![Liste des éoliennes](./media/functions-flow-scenario/initial-list.png)

3. Cliquez ou appuyez sur ![l’icône de nouvel élément](./media/functions-flow-scenario/icon-new.png), puis sur **Date**.

    ![Ajouter un champ de ligne de texte unique](./media/functions-flow-scenario/add-column.png)

4. Entrez le nom `LastServiceDate`, puis cliquez ou appuyez sur **Créer**.

    ![Créer la colonne Dernier entretien](./media/functions-flow-scenario/date-column.png)

5. Répétez les deux dernières étapes pour les trois autres colonnes de la liste :

    1. **Nombre** > « Production max. »

    2. **Oui/Non** > « Entretien nécessaire »

    3. **Nombre** > « Effort estimé »

Voilà pour l’instant ; vous devez avoir une liste vide qui ressemble à l’image suivante. Vous ajoutez des données à la liste après avoir créé le flux.

![Liste vide](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Ajouter une connexion à l’API
L’API personnalisée (également appelée « connecteur personnalisé ») est disponible dans Microsoft Flow. Toutefois, vous devez établir une connexion avec l’API avant de pouvoir l’utiliser dans un flux.

1. Dans [flow.microsoft.com](https://flow.microsoft.com), cliquez sur l’icône d’engrenage (dans le coin supérieur droit), puis cliquez sur **Connexions**.

    ![Connexions de flux](media/functions-flow-scenario/flow-connections.png)

1. Cliquez sur **Créer une connexion**, faites défiler jusqu’au connecteur **Turbine Repair** (Réparation de l’éolienne), puis cliquez dessus.

    ![Créer une connexion](media/functions-flow-scenario/create-connection.png)

1. Entrez la clé API, puis cliquez sur **Créer une connexion**.

    ![Entrer la clé API et créer la connexion](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Si vous partagez votre flux avec d’autres utilisateurs, chaque personne qui collabore à la création du flux ou qui l’utilise doit également entrer la clé API pour se connecter à l’API. Ce comportement est susceptible d’être modifié, auquel cas cette rubrique sera mise à jour.


## <a name="create-a-flow"></a>Créer un flux

Vous êtes maintenant prêt à créer un flux qui utilise le connecteur personnalisé et la liste SharePoint que vous avez créés.

### <a name="add-a-trigger-and-specify-a-condition"></a>Ajouter un déclencheur et spécifier une condition

Dans un premier temps, vous créez un flux entièrement (sans modèle) et ajoutez un *déclencheur* qui se déclenche quand un élément est créé dans la liste SharePoint. Dans un deuxième temps, vous ajoutez une *condition* pour déterminer ce qui se produit ensuite.

1. Dans [flow.microsoft.com](https://flow.microsoft.com), cliquez sur **Mes flux**, puis sur **Créer entièrement**.

    ![Créer entièrement](media/functions-flow-scenario/create-from-blank.png)

2. Cliquez sur le déclencheur SharePoint **Lorsqu’un élément est créé**.

    ![Choisir un déclencheur](media/functions-flow-scenario/choose-trigger.png)

    Si vous ne vous êtes pas déjà connecté à SharePoint, vous êtes invité à le faire.

3. Pour **Adresse du site**, entrez le nom de votre site SharePoint, et pour **Nom de la liste**, entrez la liste qui contient les données des turbines.

    ![Choisir un déclencheur](media/functions-flow-scenario/site-list.png)

4. Cliquez sur **Nouvelle étape**, puis sur **Ajouter une condition**.

    ![Ajouter une condition](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow ajoute deux branches au flux : **Si Oui** et **Si Non**. Vous ajoutez des étapes à une ou aux deux branches après avoir défini la condition à vérifier.

    ![Branches de la condition](media/functions-flow-scenario/condition-branches.png)

5. Sur la carte **Condition**, cliquez sur la première zone, puis sélectionnez **ServiceRequired** (Entretien nécessaire) dans la boîte de dialogue **Contenu dynamique**.

    ![Sélectionnez un champ pour la condition](media/functions-flow-scenario/condition1-field.png)

6. Entrez la valeur `True` pour la condition.

    ![Entrer true pour la condition](media/functions-flow-scenario/condition1-yes.png)

    La valeur apparaît sous la forme `Yes` ou `No` dans la liste SharePoint, mais elle est stockée en tant que *booléen* (`True` ou `False`). 

7. Cliquez sur **Créer un flux** en haut de la page. Veillez à cliquer sur **Mettre à jour le flux** régulièrement.

Pour tous les éléments créés dans la liste, le flux vérifie si le champ **ServiceRequired** (Entretien nécessaire) est défini sur `Yes`, puis passe à la branche **Si Oui** ou **Si Non**, selon le cas. Pour gagner du temps, dans cette rubrique, vous ne spécifiez des actions que pour la branche **Si Oui**.

### <a name="add-the-custom-connector"></a>Ajouter le connecteur personnalisé

Vous ajoutez à présent le connecteur personnalisé qui appelle la fonction dans Azure. Vous l’ajoutez au flux comme un connecteur standard. 

1. Dans la branche **Si Oui**, cliquez sur **Ajouter une action**.

    ![Ajouter une action](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Dans la boîte de dialogue **Choisir une action**, recherchez `Turbine Repair`, puis sélectionnez l’action **Turbine Repair - Calculates costs** (Réparation de l’éolienne - Calcule les coûts).

    ![Choisir une action](media/functions-flow-scenario/choose-turbine-repair.png)

    L’illustration suivante montre la carte ajoutée au flux. Les champs et les descriptions proviennent de la définition OpenAPI du connecteur.

    ![Valeurs par défaut du calcul des coûts](media/functions-flow-scenario/calculates-costs-default.png)

3. Sur la carte **Calculates costs** (Calcule les coûts), utilisez la boîte de dialogue **Contenu dynamique** pour sélectionner les entrées de la fonction. Microsoft Flow affiche les champs numériques, mais pas le champ de date, car la définition OpenAPI spécifie que **Hours** (Heures) et **Capacity** (Capacité) sont numériques.

    Pour **Hours** (Heures), sélectionnez **EstimatedEffort** (Effort estimé), et pour **Capacity** (Capacité), sélectionnez **MaxOutput** (Production max.).

    ![Choisir une action](media/functions-flow-scenario/calculates-costs-fields.png)

     Vous ajoutez à présent une autre condition basée sur la sortie de la fonction.

4. En bas de la branche **Si Oui**, cliquez sur **Plus**, puis sur **Ajouter une condition**.

    ![Ajouter une condition](media/functions-flow-scenario/condition2-add.png)

5. Sur la carte **Condition 2**, cliquez sur la première zone, puis sélectionnez **Message** dans la boîte de dialogue **Contenu dynamique**.

    ![Sélectionnez un champ pour la condition](media/functions-flow-scenario/condition2-field.png)

6. Entrez la valeur `Yes`. Le flux passe à la branche suivante **Si Oui** ou **Si Non** selon que le message retourné par la fonction est oui (effectuer la réparation) ou non (ne pas effectuer la réparation). 

    ![Entrer Oui pour la condition](media/functions-flow-scenario/condition2-yes.png)

Le flux doit maintenant ressembler à l’image suivante :

![Entrer Oui pour la condition](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Envoyer un e-mail basé sur les résultats de la fonction

À ce stade du flux, la fonction a retourné une valeur **Message** de `Yes` ou `No`, ainsi que d’autres informations sur les coûts et le gain potentiel. Dans la branche **Si Oui** de la deuxième condition, vous allez envoyer un e-mail, mais vous pourriez effectuer une multitude d’opérations, comme écrire dans la liste SharePoint ou démarrer un [processus d’approbation](https://flow.microsoft.com/documentation/modern-approvals/).

1. Dans la branche **Si Oui** de la deuxième condition, cliquez sur **Ajouter une action**.

    ![Ajouter une action](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Dans la boîte de dialogue **Choisir une action**, recherchez `email`, puis sélectionnez une action d’envoi d’e-mail basée sur le système de messagerie que vous utilisez (dans ce cas Outlook).

    ![Envoi d’un e-mail dans Outlook](media/functions-flow-scenario/outlook-send-email.png)

3. Sur la carte **Envoyer un message électronique**, composez un e-mail. Entrez un nom valide dans votre organisation pour le champ **À**. Dans l’image ci-dessous, vous pouvez voir que les autres champs sont une combinaison de texte et de jetons issus de la boîte de dialogue **Contenu dynamique**. 

    ![Champs de l’e-mail](media/functions-flow-scenario/email-fields.png)

    Le jeton **Titre** provient de la liste SharePoint, tandis que **CostToFix** (Coût de réparation) et **RevenueOpportunity** (Gain potentiel) sont retournés par la fonction.

    Le flux complet doit ressembler à l’image suivante (nous avons omis la première branche **Si Non** pour économiser de l’espace).

    ![Flux complet](media/functions-flow-scenario/complete-flow.png)

4. Cliquez sur **Mettre à jour le flux** en haut de la page, puis cliquez sur **Terminé**.

## <a name="run-the-flow"></a>Exécuter le flux

Le flux étant complet, vous ajoutez une ligne à la liste SharePoint pour voir comment il réagit.

1. Revenez à la liste SharePoint, puis cliquez sur **Modification rapide**.

    ![Modification rapide](media/functions-flow-scenario/quick-edit.png)

2. Entrez les valeurs suivantes dans la grille d’édition.

    | Colonne de liste     | Valeur           |
    |-----------------|---------------------|
    | **Titre**           | Éolienne 60 |
    | **Dernier entretien** | 08/04/2017 |
    | **Production max.**       | 2 500 |
    | **Entretien nécessaire** | OUI |
    | **Effort estimé** | 10 |

3. Cliquez sur **Done**.

    ![Modification rapide terminée](media/functions-flow-scenario/quick-edit-done.png)

    Quand vous ajoutez l’élément, il déclenche le flux, que vous allez examiner maintenant.

4. Dans [flow.microsoft.com](https://flow.microsoft.com), cliquez sur **Mes flux**, puis sur le flux que vous avez créé.

    ![Mes flux](media/functions-flow-scenario/my-flows.png)

5. Sous **Historique d’exécution**, cliquez sur l’exécution de flux.

    ![Historique d’exécution](media/functions-flow-scenario/run-history.png)

    Si l’exécution a réussi, vous pouvez consulter les opérations de flux à la page suivante. Si l’exécution a échoué pour une raison quelconque, la page suivante fournit des informations de dépannage.

6. Développez les cartes pour voir ce qui s’est produit pendant le flux. Par exemple, développez la carte **Calculates costs** (Calcule les coûts) pour afficher les entrées et les sorties de la fonction. 

    ![Entrées et sorties du calcul des coûts](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Vérifier le compte e-mail de la personne que vous avez spécifiée dans le champ **À** de la carte **Envoyer un message électronique**. L’e-mail envoyé par le flux doit ressembler à l’image suivante.

    ![E-mail envoyé par le flux](media/functions-flow-scenario/flow-email.png)

    Comme vous pouvez le constater, les jetons ont été remplacés par les valeurs correctes à partir de la liste SharePoint et de la fonction.

## <a name="next-steps"></a>étapes suivantes
Dans cette rubrique, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une liste dans SharePoint
> * Exporter une définition d’API
> * Ajouter une connexion à l’API
> * Créer un flux pour envoyer un e-mail si une réparation est rentable
> * Exécuter le flux

Pour en savoir plus sur Microsoft Flow, consultez [Prise en main de Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Pour connaître d’autres scénarios intéressants qui utilisent Azure Functions, consultez [Appeler une fonction dans PowerApps](functions-powerapps-scenario.md) et [Créer une fonction qui s’intègre avec Azure Logic Apps](functions-twitter-email.md).