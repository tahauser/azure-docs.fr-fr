---
title: "Collecter des métriques de ressources PaaS Azure avec Log Analytics | Microsoft Docs"
description: "Découvrez comment activer la collecte de métriques de ressources PaaS Azure à l’aide de PowerShell pour la rétention et l’analyse dans Log Analytics."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 83491c4902dabc6bab1e222551298cfaffbaecf4
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Configurer la collecte de métriques de ressources Azure PaaS avec Log Analytics

Les ressources PaaS (Platform as a Service) Azure, telles que SQL Azure et Sites Web (Web Apps), peuvent envoyer des données de métriques de performances de manière native à Log Analytics. Ce script permet aux utilisateurs d’activer la journalisation des métriques pour les ressources PaaS déjà déployées dans un groupe de ressources spécifique ou sur un abonnement entier. 

À l’heure actuelle, il n’existe aucun moyen d’activer la journalisation des métriques pour les ressources PaaS par le biais du portail Azure. Vous devez donc utiliser un script PowerShell. Cette fonctionnalité de journalisation des métriques en mode natif, associée à la surveillance Log Analytics, vous permet de surveiller les ressources Azure à l’échelle. 

## <a name="prerequisites"></a>configuration requise
Vérifiez que les modules Azure Resource Manager suivants sont installés sur votre ordinateur avant de continuer :

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>Nous vous recommandons de faire en sorte que tous vos modules Azure Resource Manager aient la même version, afin d’assurer la compatibilité quand vous exécutez des commandes Azure Resource Manager à partir de PowerShell.
>
Pour installer la dernière version des modules Azure Resource Manager sur votre ordinateur, consultez [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps).  

## <a name="enable-azure-diagnostics"></a>Activer Azure Diagnostics  
Pour configurer Azure Diagnostics pour des ressources PaaS, vous devez exécuter le script **Enable-AzureRMDiagnostics.ps1**, qui est disponible dans la [Galerie PowerShell](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript).  Ce script prend en charge les scénarios suivants :
  
* Spécification d’une ressource liée à un ou plusieurs groupes de ressources dans un abonnement  
* Spécification d’une ressource liée à un groupe de ressources spécifique dans un abonnement  
* Reconfiguration d’une ressource à transférer à un autre espace de travail

Seules les ressources qui prennent en charge la collecte des métriques avec Azure Diagnostics et leur envoi direct à Log Analytics sont prises en charge.  Pour obtenir une liste détaillée, consultez [Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics](log-analytics-azure-storage.md). 

Pour télécharger et exécuter le script, effectuez les étapes suivantes.

1.  À partir de l’écran d’accueil de Windows, tapez **PowerShell** et cliquez avec le bouton droit sur PowerShell dans les résultats de la recherche.  Sélectionnez **Exécuter en tant qu’administrateur** dans le menu.   
2. Enregistrez le fichier de script **Enable-AzureRMDiagnostics.ps1** localement en exécutant la commande suivante et en spécifiant un chemin où stocker le script.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. Exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.   
4. Exécutez le script suivant `.\Enable-AzureRmDiagnostics.ps1` sans aucun paramètre pour activer la collecte des données à partir d’une ressource spécifique dans votre abonnement, ou avec le paramètre `-ResourceGroup <myResourceGroup>` pour spécifier une ressource dans un groupe de ressources spécifique.   
5. Sélectionnez l’abonnement approprié dans la liste, si vous en avez plusieurs, en entrant la valeur correcte.<br><br> ![Sélectionnez l’abonnement retourné par le script](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> Dans le cas contraire, le seul abonnement disponible est sélectionné automatiquement.
6. Ensuite, le script retourne une liste d’espaces de travail Log Analytics inscrits dans l’abonnement.  Sélectionnez l’espace de travail approprié dans la liste.<br><br> ![Sélectionnez l’espace de travail retourné par le script](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. Sélectionnez la ressource Azure à partir de laquelle vous souhaitez activer la collecte. Par exemple, si vous tapez 5, vous activez la collecte des données pour les bases de données SQL Azure.<br><br> ![Sélectionnez le type de ressource retourné par le script](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Vous ne pouvez sélectionner que les ressources qui prennent en charge la collecte des métriques avec Azure Diagnostics et leur envoi direct à Log Analytics.  Le script affiche la valeur **True** dans la colonne **Métriques** pour la liste des ressources qu’il découvre dans votre abonnement ou dans le groupe de ressources spécifié.    
8. Vous êtes invité à confirmer votre sélection.  Tapez **O** pour activer la journalisation des métriques pour toutes les ressources sélectionnées pour l’étendue définie (dans notre exemple, il s’agit de toutes les bases de données SQL dans l’abonnement).  

Le script sera exécuté sur chaque ressource correspondant aux critères sélectionnés, et la collecte des métriques sera activée pour ces ressources. Une fois terminé, vous verrez un message indiquant que la configuration est terminée.  

Peu après, vous commencerez à voir des données de la ressource PaaS Azure dans votre référentiel Log Analytics.  Un enregistrement du type `AzureMetrics` est créé, et l’analyse de ces enregistrements est prise en charge par les solutions de gestion [Azure SQL Analytics](log-analytics-azure-sql.md) et [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md).   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>Mettre à jour une ressource pour envoyer des données à un autre espace de travail
Si vous avez une ressource qui envoie déjà des données à un espace de travail Log Analytics, et que vous décidez ultérieurement de la reconfigurer pour qu’elle référence un autre espace de travail, vous pouvez exécuter le script avec le paramètre `-Update`.  

**Exemple :** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

Vous serez invité à fournir les mêmes informations que quand vous avez exécuté le script pour effectuer la configuration initiale.  

## <a name="next-steps"></a>étapes suivantes

* Découvrez les [recherches de journaux](log-analytics-log-searches.md) pour analyser les données collectées à partir de sources de données et de solutions. 

* Utilisez des [champs personnalisés](log-analytics-custom-fields.md) pour analyser les enregistrements d’événements dans des champs individuels.

* Pour découvrir comment visualiser vos recherches dans les journaux de manière significative pour l’organisation, consultez [Créer un tableau de bord personnalisé à utiliser dans Log Analytics](log-analytics-dashboards.md).