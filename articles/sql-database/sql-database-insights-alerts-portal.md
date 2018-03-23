---
title: Utiliser le portail Azure pour créer des alertes SQL Database | Microsoft Docs
description: Utilisez le portail Azure pour créer des alertes SQL Database permettant de déclencher des notifications ou Automation lorsque les conditions spécifiées sont remplies.
author: aamalvea
manager: craigg
services: sql-database
ms.service: sql-database
ms.custom: monitor and tune
ms.topic: article
ms.date: 06/06/2017
ms.author: aamalvea
ms.openlocfilehash: 611b88c540902bc7a72d53671dacd098d9798b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-portal-to-create-alerts-for-azure-sql-database-and-data-warehouse"></a>Utiliser le portail Azure pour créer des alertes pour Azure SQL Database et Data Warehouse

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment configurer des alertes Azure SQL Database et Data Warehouse à l’aide du portail Azure. Il présente également les meilleures pratiques à adopter pour définir les périodes d’alerte.    

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou uniquement lorsqu’un certain nombre d’événements se produisent.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook

Vous pouvez configurer et obtenir des informations sur les règles d’alerte avec

* [Portail Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [interface de ligne de commande (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique avec le Portail Azure
1. Sur le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller et sélectionnez-la.
2. Cette étape est différente pour SQL DS et les pools élastiques par rapport à SQL DW : 

   - **SQL DB et pools élastiques UNIQUEMENT** : sélectionnez **Alertes** ou **Règles d’alerte** dans la section SURVEILLANCE. Le texte et l’icône peuvent varier légèrement pour les différentes ressources.  
   
     ![Surveillance](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButton.png)
  
   - **SQL DW UNIQUEMENT** : sélectionnez **Surveillance** dans la section TÂCHES COURANTES. Cliquez sur le graphique **Utilisation DWU**.

     ![TÂCHES COURANTES](../monitoring-and-diagnostics/media/insights-alerts-portal/AlertRulesButtonDW.png)

3. Sélectionnez la commande **Ajouter une alerte** et renseignez les champs.
   
    ![Ajouter une alerte](../monitoring-and-diagnostics/media/insights-alerts-portal/AddDBAlertPage.png)
4. **Nommez** votre règle d’alerte, puis choisissez une **Description** qui indique également les adresses électroniques de notification.
5. Sélectionnez la **Métrique** que vous souhaitez surveiller, puis choisissez une **Condition** et une valeur de **Seuil** pour la métrique. Choisissez également la **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche. Par exemple, si vous utilisez la période « PT5M » et que votre alerte recherche une utilisation de l’UC supérieure à 80 %, elle se déclenche quand l’utilisation **moyenne** de l’UC est supérieure à 80 % depuis cinq minutes. Après le premier déclenchement, elle se déclenchera à nouveau si l’utilisation moyenne de l’UC reste au-dessous de 80 % pendant cinq minutes. La mesure de l’UC se produit toutes les minutes. Consultez le tableau ci-dessous pour connaître les fenêtres de temps prises en charge et le type d’agrégation que chaque alerte utilise, puisque toutes les alertes n’utilisent pas la valeur moyenne.   
6. Cochez **Propriétaires de messagerie...** si vous souhaitez que les administrateurs et les coadministrateurs reçoivent un courrier électronique lorsque l’alerte se déclenche.
7. Si vous souhaitez que d’autres adresses électroniques reçoivent une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** . Séparez les adresses e-mails par des points-virgules : *email@contoso.com;email2@contoso.com*
8. Insérez un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.
9. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.   

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent.
* La modifier ou la supprimer.
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte.


## <a name="sql-database-alert-values"></a>Valeurs d’alerte SQL Database

| Type de ressource | Nom de métrique | Nom convivial | Type d’agrégation | Fenêtre de temps minimale avant l’alerte|
| --- | --- | --- | --- | --- |
| Base de données SQL | cpu_percent | Pourcentage UC | Moyenne | 5 minutes |
| Base de données SQL | physical_data_read_percent | Pourcentage E/S des données | Moyenne | 5 minutes |
| Base de données SQL | log_write_percent | Pourcentage E/S du journal | Moyenne | 5 minutes |
| Base de données SQL | dtu_consumption_percent | Pourcentage DTU | Moyenne | 5 minutes |
| Base de données SQL | storage | Taille de base de données totale | Maximale | 30 minutes |
| Base de données SQL | connection_successful | Connexions réussies | Total | 10 minutes |
| Base de données SQL | connection_failed | Connexions ayant échoué | Total | 10 minutes |
| Base de données SQL | blocked_by_firewall | Bloqué par le pare-feu | Total | 10 minutes |
| Base de données SQL | deadlock | Blocages | Total | 10 minutes |
| Base de données SQL | storage_percent | Pourcentage de la taille de la base de données | Maximale | 30 minutes |
| Base de données SQL | xtp_storage_percent | Pourcentage de stockage OLTP en mémoire (aperçu) | Moyenne | 5 minutes |
| Base de données SQL | workers_percent | Pourcentage de travaux | Moyenne | 5 minutes |
| Base de données SQL | sessions_percent | Pourcentage sessions | Moyenne | 5 minutes |
| Base de données SQL | dtu_limit | Limite DTU | Moyenne | 5 minutes |
| Base de données SQL | dtu_used | DTU utilisé | Moyenne | 5 minutes |
||||||
| Pool élastique | cpu_percent | Pourcentage UC | Moyenne | 10 minutes |
| Pool élastique | physical_data_read_percent | Pourcentage E/S des données | Moyenne | 10 minutes |
| Pool élastique | log_write_percent | Pourcentage E/S du journal | Moyenne | 10 minutes |
| Pool élastique | dtu_consumption_percent | Pourcentage DTU | Moyenne | 10 minutes |
| Pool élastique | storage_percent | Pourcentage de stockage | Moyenne | 10 minutes |
| Pool élastique | workers_percent | Pourcentage de travaux | Moyenne | 10 minutes |
| Pool élastique | eDTU_limit | Limite eDTU | Moyenne | 10 minutes |
| Pool élastique | storage_limit | Limite de stockage | Moyenne | 10 minutes |
| Pool élastique | eDTU_used | eDTU utilisé | Moyenne | 10 minutes |
| Pool élastique | storage_used | Stockage utilisé | Moyenne | 10 minutes |
||||||               
| Entrepôt de données SQL | cpu_percent | Pourcentage UC | Moyenne | 10 minutes |
| Entrepôt de données SQL | physical_data_read_percent | Pourcentage E/S des données | Moyenne | 10 minutes |
| Entrepôt de données SQL | storage | Taille de base de données totale | Maximale | 10 minutes |
| Entrepôt de données SQL | connection_successful | Connexions réussies | Total | 10 minutes |
| Entrepôt de données SQL | connection_failed | Connexions ayant échoué | Total | 10 minutes |
| Entrepôt de données SQL | blocked_by_firewall | Bloqué par le pare-feu | Total | 10 minutes |
| Entrepôt de données SQL | service_level_objective | Objectif de niveau de service de la base de données | Total | 10 minutes |
| Entrepôt de données SQL | dwu_limit | limite dwu | Maximale | 10 minutes |
| Entrepôt de données SQL | dwu_consumption_percent | Pourcentage DWU | Moyenne | 10 minutes |
| Entrepôt de données SQL | dwu_used | DWU utilisé | Moyenne | 10 minutes |
||||||


## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](../monitoring-and-diagnostics/monitoring-overview.md) , notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détail la [configuration des webhooks dans les alertes](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Consultez une [vue d’ensemble des journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) et collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
