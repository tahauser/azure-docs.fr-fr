---
title: "Guide de procédures relatives aux notifications par e-mail pour le réglage automatique - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database analyse la requête SQL et s’adapte automatiquement aux charges de travail utilisateur."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
ms.reviewer: carlrab
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: 611c30639b5fb36bb08ebd3e73c90f8aa2bd09d4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>Notifications par e-mail pour le réglage automatique

Des recommandations relatives au réglage de SQL Database sont générées par le [réglage automatique](sql-database-automatic-tuning.md) Azure SQL Database. Cette solution surveille et analyse en permanence les charges de travail des bases de données SQL, et fournit pour chaque base de données des recommandations de réglage personnalisées liées à la création d’index, la suppression d’index et l’optimisation des plans d’exécution de requête.

Les recommandations de réglage automatique Azure SQL Database peuvent être consultées dans le [portail Azure](sql-database-advisor-portal.md) ou récupérées avec des appels [API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) ou à l’aide de commandes [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) et [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction). Cet article est basé sur l’utilisation d’un script PowerShell pour récupérer les recommandations de réglage automatique.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatiser les notifications par e-mail pour les recommandations de réglage automatique

La solution suivante automatise l’envoi des notifications par e-mail contenant des recommandations de réglage automatique. La solution décrite comprend l’automatisation de l’exécution d’un script PowerShell pour récupérer les recommandations de réglage à l’aide [d’Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) et l’automatisation de la planification de la tâche de remise du courrier à l’aide de [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Créer un compte Azure Automation

Pour utiliser Azure Automation, la première étape consiste à créer un compte Automation et à le configurer avec des ressources Azure à utiliser pour l’exécution du script PowerShell. Pour en savoir plus sur Azure Automation et ses fonctionnalités, consultez [Bien démarrer avec Azure Automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Suivez ces étapes pour créer un compte Azure Automation en sélectionnant et en configurant l’application Automation de la Place de Marché :

- Connectez-vous au portail Azure.
- Cliquez sur « **+ Créer une ressource** » dans le coin supérieur gauche.
- Recherchez « **Automation** » (appuyez sur Entrée).
- Cliquez sur l’application Automation dans les résultats de recherche.

![Ajout d’Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Une fois dans le volet « Créer un compte Automation », cliquez sur « **Créer** »
- Fournissez les informations requises : entrez un nom pour ce compte Automation, sélectionnez votre ID d’abonnement Azure et les ressources Azure à utiliser pour l’exécution du script PowerShell.
- Pour l’option « **Créer un compte d’identification Azure** », sélectionnez **Oui** afin de configurer le type de compte sous lequel le script PowerShell s’exécute avec l’aide d’Azure Automation. Pour en savoir plus sur les types de comptes, consultez [Compte d’identification](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
- Terminez la création du compte Automation en cliquant sur **Créer**.

> [!TIP]
> Prenez note du nom de votre compte Azure Automation, de l’ID d’abonnement et des ressources (par exemple en effectuant un copier-coller dans un bloc-notes) exactement comme vous les avez entrés lors de la création de l’application Automation. Vous aurez besoin de ces informations ultérieurement.
>

Si vous avez plusieurs abonnements Azure pour lesquels vous souhaitez générer la même automation, vous devez répéter ce processus pour vos autres abonnements.

## <a name="update-azure-automation-modules"></a>Mettre à jour les modules Azure Automation

Le script PowerShell de récupération des recommandations de réglage automatique utilise les commandes [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) et [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction), pour lesquelles la mise à jour des modules Azure vers la version 4 ou ultérieure est nécessaire.

Suivez ces étapes pour mettre à jour les modules Azure PowerShell :

- Accédez au volet de l’application Automation, puis sélectionnez « **Modules** » dans le menu de gauche (faites défiler la page, car cet élément de menu figure sous Ressources partagées).
- Dans le volet Modules, cliquez sur « **Mettre à jour les modules Azure** » en haut et attendez que le message « Les modules Azure ont été mis à jour » s’affiche. L’exécution de ce processus peut prendre plusieurs minutes.

![Mettre à jour les modules d’automation Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

La version des modules AzureRM.Resources et AzureRM.Sql doit être la version 4 ou version ultérieure.

## <a name="create-azure-automation-runbook"></a>Créer un runbook Azure Automation

L’étape suivante consiste à créer un runbook dans Azure Automation, à l’intérieur duquel réside le script PowerShell de la récupération des recommandations de réglage.

Suivez ces étapes pour créer un runbook Azure Automation :

- Accédez au compte Azure Automation créé à l’étape précédente.
- Une fois dans le volet du compte Automation, cliquez sur l’élément de menu « **Runbooks** » sur le côté gauche pour créer un runbook Azure Automation avec le script PowerShell. Pour en savoir plus sur la création de runbooks d’automation, consultez [Création d’un runbook](../automation/automation-creating-importing-runbook.md).
- Pour ajouter un nouveau runbook, cliquez sur l’option de menu « **+ Ajouter un runbook** », puis sur « **Création rapide – Créer un runbook**».
- Dans le volet Runbook, tapez le nom de votre runbook (pour les besoins de cet exemple, « **AutomaticTuningEmailAutomation** » est utilisé), sélectionnez **PowerShell** comme type de runbook et écrivez une description de ce runbook pour décrire sa fonction.
- Cliquez sur le bouton **Créer** pour terminer la création du runbook.

![Ajouter le runbook Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Suivez ces étapes pour charger un script PowerShell à l’intérieur du runbook créé :

- Dans le volet « **Modifier le runbook PowerShell** », sélectionnez « **RUNBOOKS** » dans l’arborescence et développez l’affichage jusqu’à voir le nom de votre runbook (dans cet exemple, «  **AutomaticTuningEmailAutomation** »). Sélectionnez ce runbook.
- Sur la première ligne de « Modifier le runbook PowerShell » (en commençant par le numéro 1), copiez-collez le code de script PowerShell suivant. Ce script PowerShell est fourni tel quel pour vous aider à démarrer. Modifiez-le en fonction de vos besoins.

Dans l’en-tête du script PowerShell fourni, vous devez remplacer `<SUBSCRIPTION_ID_WITH_DATABASES>` par votre ID d’abonnement Azure. Pour découvrir comment récupérer votre ID d’abonnement Azure, consultez [Getting your Azure Subscription GUID (Obtention de votre GUID d’abonnement Azure)](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/).

Si vous avez plusieurs abonnements, vous pouvez les ajouter en les séparant par des virgules à la propriété « $subscriptions » dans l’en-tête du script.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Cliquez sur le bouton « **Enregistrer** » dans le coin supérieur droit pour enregistrer le script. Une fois satisfait du script, cliquez sur le bouton « **Publier** » pour publier ce runbook. 

Dans le volet principal du runbook, vous pouvez cliquer sur le bouton « **Démarrer** » pour **tester** le script. Cliquez sur « **Sortie** » pour afficher les résultats du script exécuté. Cette sortie sera le contenu de votre e-mail. L’exemple de sortie du script est visible dans la capture d’écran suivante.

![Visualiser les recommandations de réglage automatique avec Azure Automation](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Veillez à ajuster le contenu en personnalisant le script PowerShell en fonction de vos besoins.

Avec les étapes ci-dessus, le script PowerShell pour récupérer les recommandations de réglage automatique est chargé dans Azure Automation. L’étape suivante consiste à automatiser et à planifier la tâche de remise du courrier.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatiser les tâches de messagerie avec Microsoft Flow

Pour terminer la solution, la dernière étape consiste à créer un flux d’automation dans Microsoft Flow composé de trois actions (tâches) : 

1. «**Azure Automation - Créer une tâche** » : sert à exécuter le script PowerShell pour récupérer les recommandations de réglage automatique à l’intérieur du runbook Azure Automation.
2. « **Azure Automation - Obtenir la sortie de tâche** » : sert à récupérer la sortie à partir du script PowerShell exécuté.
3. « **Office 365 Outlook - Envoyer un message électronique** » : sert à envoyer un e-mail. Les e-mails sont envoyés à l’aide du compte Office 365 de la personne qui crée le flux.

Pour en savoir plus sur les fonctionnalités de Microsoft Flow, consultez [Bien démarrer avec Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Les prérequis pour cette étape consistent à créer un compte [Microsoft Flow](https://flow.microsoft.com) et à se connecter. Une fois dans la solution, suivez ces étapes pour configurer un **nouveau flux** :

- Accédez à l’élément de menu « **Mes flux** ».
- Dans Mes flux, sélectionnez le lien « **+ Créer entièrement** » en haut de la page.
- Cliquez sur le lien « **Rechercher parmi des centaines de connecteurs et déclencheurs** » en bas de la page.
- Dans le champ de recherche, tapez « **récurrence** » et sélectionnez « **Planification - Récurrence** » dans les résultats de recherche pour planifier la tâche de remise par e-mail.
- Dans le volet Récurrence, dans le champ Fréquence, sélectionnez la fréquence de planification pour ce flux à exécuter, par exemple envoyer un e-mail automatisé chaque minute, heure, jour, semaine, et ainsi de suite.

L’étape suivante consiste à ajouter trois tâches (créer, obtenir la sortie et envoyer un e-mail) au flux récurrent nouvellement créé. Pour ajouter les tâches requises au flux, effectuez les étapes suivantes :

1. Créez une action pour exécuter le script PowerShell afin de récupérer les recommandations de réglage.
- Sélectionnez « **+ Nouvelle étape** », suivi de « **Ajouter une action** » dans le volet de flux Récurrence.
- Dans le champ de recherche, tapez « **automation** » et sélectionnez « **Azure Automation - Créer une tâche** » dans les résultats de recherche.
- Dans le volet Créer une tâche, configurez les propriétés de la tâche. Pour cette configuration, vous aurez besoin des détails de votre ID d’abonnement Azure, groupe de ressources et compte Automation **notés précédemment** dans le **volet Compte Automation**. Pour en savoir plus sur les options disponibles dans cette section, consultez [Azure Automation - Créer une tâche](https://docs.microsoft.com/connectors/azureautomation/#Create_job).
- Terminez la création de cette action en cliquant sur « **Enregistrer le flux** ».

2. Créez une action pour récupérer la sortie du script PowerShell exécuté.
- Sélectionnez « **+ Nouvelle étape** », suivi de « **Ajouter une action** » dans le volet de flux Récurrence.
- Dans le champ de recherche, tapez « **automation** » et sélectionnez « **Azure Automation - Obtenir la sortie de la tâche** » dans les résultats de recherche. Pour en savoir plus sur les options disponibles dans cette section, consultez [Azure Automation - Obtenir la sortie de la tâche](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output).
- Renseignez les champs requis (comme lors de la création de la tâche précédente). Renseignez votre ID d’abonnement Azure, groupe de ressources et compte Automation (tels qu’ils figurent dans le volet Compte Automation).
- Cliquez dans le champ « **ID de tâche** » pour afficher le menu « **Contenu dynamique** ». Dans ce menu, sélectionnez l’option « **ID de tâche** ».
- Terminez la création de cette action en cliquant sur « **Enregistrer le flux** ».

3. Créez une action pour envoyer un e-mail à l’aide de l’intégration d’Office 365
- Sélectionnez « **+ Nouvelle étape** », suivi de « **Ajouter une action** » dans le volet de flux Récurrence.
- Dans le champ de recherche, tapez « **Envoyer un message électronique** » et sélectionnez « **Outlook Office 365 - Envoyer un message électronique** » dans les résultats de recherche.
- Dans le champ « **À** », tapez l’adresse e-mail à laquelle vous devez envoyer l’e-mail de notification.
- Dans le champ « **Objet** », tapez l’objet de votre e-mail, par exemple « Notification par e-mail concernant les recommandations de réglage automatique ».
- Cliquez dans le champ « **Corps** » pour afficher le menu « **Contenu dynamique** ». Dans ce menu, sous « **Obtenir la sortie de la tâche** », sélectionnez « **Contenu** ». 
- Terminez la création de cette action en cliquant sur « **Enregistrer le flux** ».

> [!TIP]
> Pour envoyer des e-mails automatiques à plusieurs destinataires, créez des flux distincts. Dans ces flux supplémentaires, changez l’adresse e-mail du destinataire dans le champ « À » et la ligne objet de l’e-mail dans le champ « Objet ». La création de runbooks dans Azure Automation avec des scripts PowerShell personnalisés (par exemple avec changement de l’ID d’abonnement Azure) permet de personnaliser encore davantage les scénarios automatisés, par exemple en envoyant des e-mails de recommandations de réglage automatisé à des destinataires distincts pour des abonnements distincts.
>

Ceci était la dernière étape nécessaire pour configurer le flux de travail de tâche de remise par e-mail. Le flux complet constitué des trois actions créées est illustré dans l’image suivante.

![Afficher le flux de notifications par e-mail du réglage automatique](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Pour tester le flux, cliquez sur « **Exécuter maintenant** » dans le coin supérieur droit dans le volet de flux.

Les statistiques sur l’exécution des tâches automatisées, indiquant la réussite des notifications par e-mail envoyées, peuvent être consultées à partir du volet d’analytique de flux.

![Flux en cours d’exécution pour les notifications par e-mail de réglage automatique](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

L’analytique de flux est utile pour surveiller la réussite de l’exécution des tâches et, si nécessaire, pour le dépannage.  Pour le dépannage, vous pouvez également examiner le journal d’exécution de script PowerShell accessible par le biais de l’application Azure Automation.

La version finale de l’e-mail automatisé ressemble à l’e-mail suivant reçu après la création et l’exécution de cette solution :

![Exemple de notification par e-mail pour le réglage automatique](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

En ajustant le script PowerShell, vous pouvez personnaliser la sortie et la mise en forme de l’e-mail automatisé en fonction de vos besoins.

Vous pouvez personnaliser davantage la solution pour générer des notifications par e-mail basées sur un événement de réglage spécifique, envoyées à plusieurs destinataires, pour plusieurs bases de données ou abonnements, en fonction de vos scénarios personnalisés. 

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la façon dont le réglage automatisé peut vous aider à améliorer les performances de la base de données, consultez [Réglage automatique dans Azure SQL Database](sql-database-automatic-tuning.md).
- Pour activer le réglage automatique dans Azure SQL Database afin de gérer votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour consulter et appliquer manuellement des recommandations de réglage d’automatique, consultez [Rechercher et appliquer des recommandations en matière de performances](sql-database-advisor-portal.md).
