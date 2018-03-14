---
title: "Utiliser Azure Automation pour lancer une tâche dans StorSimple Data Manager | Microsoft Docs"
description: "Découvrez comment utiliser Azure Automation pour déclencher des travaux StorSimple Data Manager"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Utiliser Azure Automation pour déclencher un travail

Cet article vous explique comment utiliser la fonctionnalité de transformation des données au sein du service StorSimple Data Manager afin de transformer les données de l’appareil StorSimple. Vous pouvez lancer un travail de transformation de données de deux manières : 

 - Utiliser le kit de développement logiciel (SDK) .NET
 - Utilisation du Runbook Azure Automation
 
Cet article explique comment créer un runbook Azure Automation, puis l’utiliser pour lancer un travail de transformation de données. Pour en savoir plus sur l’initialisation de la transformation des données via le kit de développement logiciel (SDK) .NET, accédez à [Utilisez le SDK .NET SDK pour déclencher des travaux de transformation des données](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de satisfaire les exigences suivantes :

*   Azure PowerShell installé sur l’ordinateur client. [Téléchargez Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   Une définition de travail correctement configurée dans un service StorSimple Data Manager au sein d’un groupe de ressources.
*   Téléchargez le fichier [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) à partir du référentiel GitHub. 
*   Téléchargez le script [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) à partir du référentiel GitHub.

## <a name="step-by-step-procedure"></a>Procédure pas à pas

### <a name="set-up-the-automation-account"></a>Configurer le compte Automation

1. Créez un compte Automation d’identification Azure dans le portail Azure. Pour ce faire, accédez à **Place de marché Azure > Tout**, puis recherchez **Automation**. Sélectionnez **Comptes Automation**.

    ![Créer un compte d’identification Automation](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Pour ajouter un nouveau compte Automation, cliquez sur **+ Ajouter**.

    ![Créer un compte d’identification Automation](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Dans **Ajouter Automation**:

    1. Entrez le **Nom** de votre compte Automation.
    2. Sélectionnez l’**Abonnement** lié à votre service StorSimple Data Manager.
    3. Créez un groupe de ressources ou sélectionnez un groupe de ressources existant.
    4. Sélectionnez un **emplacement**.
    5. Laissez l’option par défaut **Créer un compte d’identification** activée.
    6. Pour obtenir un lien d’accès rapide sur le tableau de bord, consultez **Épingler au tableau de bord**. Cliquez sur **Créer**.

    ![Créer un compte d’identification Automation](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Une fois le compte Automation créé, vous êtes averti.
    
    ![Notification pour le déploiement du compte Automation](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Pour plus d’informations, consultez l’article [Créer un compte d’identification](../automation/automation-create-runas-account.md).

3. Dans le compte que vous venez de créer, accédez à **Ressources partagées > Modules**, puis cliquez sur **+ Ajouter un module**.

    ![Module d’importation 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Accédez à l’emplacement du fichier `DataTransformationApp.zip` à partir de votre ordinateur local, puis sélectionnez et ouvrez le module. Cliquez sur **OK** pour importer le module.

    ![Module d’importation 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quand Azure Automation importe un module dans votre compte, il extrait les métadonnées sur le module. Cette opération peut prendre plusieurs minutes.

   ![Module d’importation 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Vous recevez une notification indiquant que le module est en cours de déploiement et une autre notification lorsque le processus est terminé.  L’état dans **Modules** devient **Disponible**.

    ![Module d’importation 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importer, publier et exécuter un runbook Automation

Procédez comme suit pour importer, publier et exécuter le runbook pour déclencher la définition du travail.

1. Dans le portail Azure, ouvrez votre compte Automation. Accédez à **Automatisation des processus > Runbooks**, puis cliquez sur **+ Ajouter un runbook**.

    ![Ajouter Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Dans **Ajouter un runbook**, cliquez sur **Importer un runbook existant**.

3. Pointez sur le fichier de script Azure PowerShell `Trigger-DataTransformation-Job.ps1` pour le **Fichier de runbook**. Le type de runbook est sélectionné automatiquement. Fournissez un nom et une description facultative pour le runbook. Cliquez sur **Créer**.

    ![Ajouter Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Le nouveau runbook apparaît dans la liste des runbooks pour le compte Automation. Sélectionnez et cliquez sur ce runbook.

    ![Ajouter Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Modifiez le runbook, puis cliquez sur le volet **Test**.

    ![Ajouter Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Fournissez les paramètres tels que le nom de votre service StorSimple Data Manager, le groupe de ressource associé et le nom de définition de travail. **Démarrez** le test. Le rapport est généré une fois l’exécution du test terminée. Pour plus d’informations, accédez à [Comment tester un runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Ajouter Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Inspectez la sortie du runbook dans le volet de test. Si vous êtes satisfait, fermez le volet. Cliquez sur **Publier**, puis, lorsque vous êtes invité à confirmer l’opération, confirmez-la et publiez le runbook.

    ![Ajouter Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Revenez à **Runbooks** et sélectionnez le Runbook nouvellement créé.

    ![Ajouter Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Démarrez** le runbook. Dans **Démarrer le runbook**, entrez tous les paramètres. Cliquez sur **OK** pour envoyer et commencer le travail de transformation des données.

10. Pour surveiller la progression du travail dans le portail Azure, accédez à **Travaux** dans votre service StorSimple Data Manager. Sélectionnez et cliquez sur le travail pour afficher ses détails.

    ![Ajouter Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md).