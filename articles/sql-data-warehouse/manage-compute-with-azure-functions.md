---
title: "Azure SQL Data Warehouse : Utilisation d’Azure Functions pour automatiser les niveaux de calcul d’Azure SQL Data Warehouse | Microsoft Docs"
description: "Comment utiliser Azure Functions pour gérer le calcul de votre entrepôt de données."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: e2138f26a78338406b466bdd2aa6e756e602726d
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>Utiliser Azure Functions pour automatiser les niveaux de calcul de SQL Data Warehouse

Ce didacticiel va vous montrer comment vous pouvez utiliser Azure Functions pour gérer les niveaux de calcul d’Azure SQL Data Warehouse. Ces architectures sont recommandées pour une utilisation avec SQL Data Warehouse [Optimisé pour l’élasticité][Performance Tiers].

Pour utiliser l’application Azure Function avec Azure SQL Data Warehouse, vous devez créer un [compte de principal de service](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) avec accès collaborateur sous le même abonnement que votre instance d’entrepôt de données. 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>Déployer un minuteur basé sur la minuterie à l’aide d’un modèle Azure Resource Manager

Pour déployer le modèle, vous aurez besoin des informations suivantes :

- Nom du groupe de ressources dans lequel se trouve votre instance SQL Data Warehouse
- Nom du serveur logique dans lequel se trouve votre instance SQL Data Warehouse
- Nom de votre instance SQL Data Warehouse
- ID de locataire (ID du répertoire) de votre annuaire Azure Active Directory
- Identifiant d’abonnement 
- ID d'application du principal de service
- Clé secrète du principal de service

Une fois que vous avez les informations ci-dessus, déployez ce modèle :

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Une fois que vous avez déployé le modèle, vous devriez trouver trois nouvelles ressources : un plan Azure App Service gratuit, un plan Function App basé sur la consommation et un compte de stockage qui gère la journalisation et la file d’attente des opérations. Continuez à lire les autres sections pour savoir comment modifier les fonctions déployées pour s’ajuster à vos besoins.

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>Augmenter ou réduire l’échelle de niveau de calcul

1. Accédez à votre service Function App. Si vous avez déployé le modèle avec les valeurs par défaut, ce service doit être nommé *DWOperations*. Une fois que votre Function App est ouverte, vous devriez remarquer que cinq fonctions sont déployées dans votre service Function App. 

   ![Fonctions déployées avec modèle](media/manage-compute-with-azure-functions/five-functions.png)

2. Sélectionnez *DWScaleDownTrigger* ou *DWScaleUpTrigger* selon si vous souhaitez réduire ou augmenter l’échelle de temps. Dans la liste déroulante, sélectionnez Intégrer.

   ![Sélectionnez Intégrer pour la fonction](media/manage-compute-with-azure-functions/select-integrate.png)

3. À présent, la valeur affichée devrait indiquer *%ScaleDownTime%* ou *%ScaleUpTime%*. Ces valeurs indiquent que la planification est basée sur les valeurs définies dans vos [Paramètres de l’application][Application Settings]. Vous pouvez ignorer cela pour le moment et définir la planification sur votre heure de préférence en fonction des étapes suivantes.

4. Dans la zone de planification, ajoutez l’heure de l’expression CRON pour refléter la fréquence à laquelle vous souhaitez que SQL Data Warehouse soit mis à l’échelle. 

  ![Modifier la planification de fonction](media/manage-compute-with-azure-functions/change-schedule.png)

  La valeur de `schedule` est une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui contient les six champs suivants : 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Par exemple *« 0 30 9 * * 1-5 »* reflète un déclencheur chaque jour de semaine à 9 h 30. Pour plus d’informations, consultez les [Exemples de planification][schedule examples] Azure Functions.


### <a name="change-the-scale-up-or-scale-down-time"></a>Augmenter ou réduire l’échelle de temps

1. Accédez à votre service Function App. Si vous avez déployé le modèle avec les valeurs par défaut, ce service doit être nommé *DWOperations*. Une fois que votre Function App est ouverte, vous devriez remarquer que cinq fonctions sont déployées dans votre service Function App. 

2. Sélectionnez *DWScaleDownTrigger* ou *DWScaleUpTrigger* selon si vous souhaitez réduire ou augmenter la valeur de calcul. Lors de la sélection des fonctions, le volet devrait afficher le fichier *index.js*.

   ![Modifiez le niveau de calcul du déclencheur de fonction](media/manage-compute-with-azure-functions/index-js.png)

3. Définissez la valeur de *ServiceLevelObjective* sur le niveau souhaité et cliquez sur enregistrez. C’est le niveau de calcul sur lequel votre instance d’entrepôt de données sera mise à l’échelle en fonction de la planification définie dans la section Intégrer.

### <a name="use-pause-or-resume-instead-of-scale"></a>Utiliser mise en pause ou reprise au lieu de mise à l'échelle 

Actuellement, les fonctions activées par défaut sont *DWScaleDownTrigger* et *DWScaleUpTrigger*. Si vous préférez utiliser les fonctionnalités de mise en pause et de reprise, vous pouvez activer *DWPauseTrigger* ou *DWResumeTrigger*.

1. Accédez au volet Fonctions.

   ![Volet Fonctions](media/manage-compute-with-azure-functions/functions-pane.png)



2. Cliquez sur le bouton bascule correspondant aux déclencheurs que vous souhaitez activer.

3. Accédez aux onglets *Intégrer* des déclencheurs respectifs pour modifier leur planification.

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>Ajouter une nouvelle fonction de déclenchement

Actuellement, il n’y a que deux fonctions de mise à l’échelle incluses dans le modèle. Cela signifie qu’au cours d’une journée, vous ne pouvez réduire et augmenter l’échelle qu’une seule fois. Pour un contrôle plus précis, par exemple, pour réduire l’échelle plusieurs fois par jour ou pour avoir un comportement de mise à l’échelle différent les week-ends, vous devrez ajouter un autre déclencheur.

1. Créez une nouvelle fonction vide. Sélectionnez le bouton *+* proche de l’emplacement de votre fonction pour afficher le volet de modèle de fonction.

   ![Créer une fonction](media/manage-compute-with-azure-functions/create-new-function.png)

2. Sous Langage, sélectionnez *Javascript*, puis sélectionnez *TimerTrigger*.

   ![Créer une fonction](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nommez votre fonction et définissez votre planification. L’illustration montre un exemple de déclenchement de fonction tous les samedis à minuit (dans la nuit de vendredi à samedi).

   ![Mise à l’échelle inférieure samedi](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Copiez le contenu de *index.js* parmi les autres fonctions de déclencheur.

   ![Copiez index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Définissez votre variable de l’opération sur le comportement souhaité comme suit :

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


### <a name="complex-scheduling"></a>Planification complexe

Cette section décrit ce dont vous avez besoin pour réaliser une planification plus complexes des fonctionnalités de mise en pause, de reprise et de mise à l’échelle.

#### <a name="example-1"></a>Exemple 1 :

Montée en puissance quotidienne à 8 h 00 jusqu’à DW600 et descente en puissance à 20 h 00 jusqu’à DW200.

| Fonction  | Planification     | Opération                                |
| :-------- | :----------- | :--------------------------------------- |
| Fonction1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Fonction2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>Exemple 2 : 

Montée en puissance quotidienne à 8 h 00 jusqu’à DW1000, descente en puissance à 16 h 00 jusqu’à DW600 et descente en puissance à 22 h 00 jusqu’à DW200.

| Fonction  | Planification     | Opération                                |
| :-------- | :----------- | :--------------------------------------- |
| Fonction1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Fonction2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Fonction3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>Exemple 3 : 

Montée en puissance à 8 h 00 jusqu’à DW1000, descente en puissance unique jusqu’à DW600 à 16 h 00 les jours de semaine. Mise en pause le vendredi à 23 h 00, reprise à 7 h 00 le lundi matin.

| Fonction  | Planification       | Opération                                |
| :-------- | :------------- | :--------------------------------------- |
| Fonction1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Fonction2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Fonction3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Fonction4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctions Azure [Déclencheur de minuteur](../azure-functions/functions-create-scheduled-function.md).

Consultez le [référentiel d’exemples](https://github.com/Microsoft/sql-data-warehouse-samples) SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
