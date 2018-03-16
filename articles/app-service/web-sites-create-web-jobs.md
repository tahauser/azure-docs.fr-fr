---
title: "Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service"
description: "Découvrez comment utiliser des tâches web pour exécuter des tâches en arrière-plan dans des applications Web Azure App Service, des applications API ou des applications mobiles."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 91839d8f547340d55f6badb3350a393a48a13c7d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service

## <a name="overview"></a>Vue d'ensemble
WebJobs est une fonctionnalité de [Microsoft Azure App Service](https://docs.microsoft.com/azure/app-service/) qui vous permet d’exécuter un programme ou un script dans un même contexte, en tant qu’application web, application API ou application mobile. L’utilisation des tâches web n’entraîne aucun coût supplémentaire.

Cet article explique comment déployer WebJobs à l’aide du [portail Azure](https://portal.azure.com) pour charger un fichier exécutable ou un script. Pour plus d’informations sur la façon de développer et de déployer WebJobs à l’aide de Visual Studio, consultez [Déploiement de WebJobs à l’aide de Visual Studio](websites-dotnet-deploy-webjobs.md).

Le SDK Azure WebJobs peut être utilisé avec WebJobs pour simplifier de nombreuses tâches de programmation. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions fournit une autre façon d’exécuter des programmes et des scripts. Pour obtenir une comparaison entre WebJobs et Functions, consultez [Choisir entre Flow, Logic Apps, Functions et WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Types de tâches web

Le tableau suivant décrit les différences entre une tâche Web *continue* et une tâche web *déclenchée*.


|Continue  |Déclenchée  |
|---------|---------|
| Démarre immédiatement lorsque la tâche web est créée. Pour empêcher la tâche de se terminer, le programme ou le script est généralement exécuté à l’intérieur d’une boucle sans fin. Si la tâche se termine, vous pouvez la redémarrer. | Démarre uniquement en cas de déclenchement manuel ou selon une planification. |
| S’exécute sur toutes les instances sur lesquelles l’application web s’exécute. Vous pouvez limiter la tâche web à une seule instance. |S’exécute sur une seule instance sélectionnée par Azure pour l’équilibrage de charge.|
| Prend en charge le débogage à distance. | Ne prend pas en charge le débogage à distance.|

> [!NOTE]
> Une application web peut expirer après 20 minutes d’inactivité. Seules les demandes vers le site scm (déploiement) ou des pages de l’application web dans le portail permettent de réinitialiser le minuteur. Les requêtes à destination du site actif ne réinitialisent pas le minuteur. Si votre application exécute des tâches Web continues ou planifiées, activez l’option **Toujours actif** pour vous assurer que les tâches web s’exécutent de manière fiable. Cette fonctionnalité est disponible uniquement dans les [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) De base, Standard et Premium.

## <a name="acceptablefiles"></a>Types de fichier pris en charge pour les scripts ou les programmes

Les types de fichiers suivants sont pris en charge :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant PowerShell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node.js)
* .jar (en utilisant Java)

## <a name="CreateContinuous"></a> Création d’une tâche web continue

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page **App Service** de votre application Web App Service, application API ou application mobile.

2. Sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Dans la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

   ![Ajouter une page WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myContinuousWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »). |
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Continue | Les [types de tâches web](#webjob-types) sont décrites précédemment dans cet article. |
   | **Mettre à l'échelle** | Multi-instances | Disponible uniquement pour les tâches web continues. Détermine si le programme ou le script s’exécute sur toutes les instances ou une seule instance. L’option permettant une exécution sur plusieurs instances ne s’applique pas aux [niveaux tarifaires](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratuit ou Partagé. | 

4. Cliquez sur **OK**.

   La nouvelle tâche web apparaît dans la page **WebJobs**.

   ![Liste des tâches web](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Pour arrêter ou redémarrer une tâche web continue, cliquez avec le bouton droit sur la tâche web dans la liste, puis sélectionner **Démarrer** ou **Arrêter**.

    ![Arrêt d’une tâche web continue](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Créer une tâche web déclenchée manuellement

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page **App Service** de votre application Web App Service, application API ou application mobile.

2. Sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Dans la page **WebJobs**, sélectionnez **Ajouter**.

    ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

   ![Ajouter une page WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myTriggeredWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »).|
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Déclenchée | Les [types de tâches web](#webjob-types) sont décrites précédemment dans cet article. |
   | **Déclencheurs** | Manuel | |

4. Cliquez sur **OK**.

   La nouvelle tâche web apparaît dans la page **WebJobs**.

   ![Liste des tâches web](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Pour exécuter la tâche Web, cliquez sur son nom dans la liste avec le bouton droit, puis cliquez sur **Exécuter**.
   
    ![Exécuter une tâche Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Créer une tâche web planifiée

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la page **App Service** de votre application Web App Service, application API ou application mobile.

2. Sélectionnez **WebJobs**.

   ![Sélection de WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Dans la page **WebJobs**, sélectionnez **Ajouter**.

   ![Page WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Utilisez les paramètres **Ajouter une tâche web**, comme spécifié dans le tableau.

   ![Ajouter une page WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Paramètre      | Exemple de valeur   | Description  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myScheduledWebJob | Un nom unique au sein d’une application App Service. Doit commencer par une lettre ou un chiffre et ne peut pas contenir de caractères spéciaux, à part les tirets et les traits de soulignement (« - » et « _ »). |
   | **Chargement de fichiers** | ConsoleApp.zip | Un fichier *.zip* qui contient votre exécutable un fichier script ainsi que les fichiers de prise en charge requis pour exécuter le programme ou le script. Les types de fichiers exécutables ou scripts pris en charge sont répertoriés dans la section [Types de fichiers pris en charge](#acceptablefiles). |
   | **Type** | Déclenchée | Les [types de tâches web](#webjob-types) sont décrites précédemment dans cet article. |
   | **Déclencheurs** | Planifiée | Pour que la planification fonctionne correctement, activez la fonctionnalité Toujours actif. La fonctionnalité Toujours actif est disponible uniquement dans les niveaux tarifaires De base, Standard et Premium.|
   | **Expression CRON** | 0 0/20 * * * * | Les [expressions CRON](#cron-expressions) sont décrites dans la section suivante. |

4. Cliquez sur **OK**.

   La nouvelle tâche web apparaît dans la page **WebJobs**.

   ![Liste des tâches web](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Expressions CRON

Une [expression CRON](https://en.wikipedia.org/wiki/Cron) est composée de six champs : `{second} {minute} {hour} {day} {month} {day of the week}`.  Voici quelques exemples :

* Toutes les 15 minutes : `0 */15 * * * *`
* Toutes les heures (autrement dit, chaque fois que le nombre de minutes est 0) : `0 0 * * * *` 
* Toutes les heures entre 9h et 17h : `0 0 9-17 * * *` 
* À 9h30 tous les jours : `0 30 9 * * *`
* À 9h30 tous les jours de la semaine : `0 30 9 * * 1-5`

Vous pouvez entrer l’expression CRON dans le portail ou inclure un fichier `settings.job` à la racine du fichier *.zip* de votre tâche web, comme dans l’exemple suivant :

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Quand vous déployez une tâche web à partir de Visual Studio, marquez les propriétés du fichier `settings.job` comme **Copier si plus récent**.

## <a name="ViewJobHistory"></a> Affichage de l’historique des tâches

1. Choisissez la tâche web dont vous souhaitez afficher l’historique, puis sélectionnez le bouton **Journaux**.
   
   ![Bouton Journaux](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. À la page **Détails de la tâche web**, sélectionnez une heure pour afficher les détails d’une exécution.
   
   ![Détails de la tâche web](./media/web-sites-create-web-jobs/webjobdetails.png)

3. À la page **WebJob Run Details**, sélectionnez **Activer/désactiver la sortie** pour afficher le texte du contenu du journal.
   
    ![Détails d'exécution de la tâche WebJob](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, sélectionnez **télécharger** . Pour télécharger le texte, cliquez avec le bouton droit sur **télécharger** et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.
   
5. Sélectionnez le lien de navigation **WebJobs** en haut de la page pour accéder à une liste de tâches web.

    ![Barre de navigation de la tâche web](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Liste des tâches Web dans le tableau de bord d’historique](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Étapes suivantes

Le SDK Azure WebJobs peut être utilisé avec WebJobs pour simplifier de nombreuses tâches de programmation. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).
