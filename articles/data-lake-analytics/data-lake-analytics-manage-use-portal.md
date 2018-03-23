---
title: Gérer Azure Data Lake Analytics à l’aide du portail Azure | Microsoft Docs
description: Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des utilisateurs et des travaux.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: kfile
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 93815904e7e21e1ba8283d7a522297c7e3466702
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Gérer Azure Data Lake Analytics à l’aide du portail Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données de compte, des utilisateurs et des travaux à l’aide du portail Azure. Pour afficher les rubriques de gestion sur l’utilisation d’autres outils, cliquez sur un onglet en haut de la page.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Gérer les comptes Data Lake Analytics

### <a name="create-an-account"></a>Créer un compte

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Créer une ressource** > **Intelligence + analyse**  > **Data Lake Analytics**.
3. Sélectionnez des valeurs pour les éléments suivants : 
   1. **Nom** : nom du compte Data Lake Analytics.
   2. **Abonnement** : abonnement Azure utilisé pour le compte.
   3. **Groupe de ressources** : groupe de ressources Azure dans lequel créer le compte. 
   4. **Emplacement** : centre de données Azure pour le compte Data Lake Analytics. 
   5. **Data Lake Store** : Store par défaut à utiliser pour le compte Data Lake Analytics. Le compte Azure Data Lake Store et le compte Data Lake Analytics doivent se trouver dans le même emplacement.
4. Cliquez sur **Créer**. 

### <a name="delete-a-data-lake-analytics-account"></a>Supprimer un compte Data Lake Analytics

Avant de supprimer un compte Data Lake Analytics, vous devez supprimer le compte Data Lake Store dépendant.

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Supprimer**.
3. Entez le nom du compte.
4. Cliquez sur **Supprimer**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gérer les sources de données

Data Lake Analytics prend en charge les sources de données suivantes :

* Data Lake Store
* Stockage Azure

Vous pouvez utiliser l’Explorateur de données pour parcourir les sources de données et effectuer des opérations de gestion des fichiers de base. 

### <a name="add-a-data-source"></a>Ajouter une source de données

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Sources de données**.
3. Cliquez sur **Ajouter une source de données**.
    
   * Pour ajouter un compte Data Lake Store, vous avez besoin du nom du compte et de l’accès au compte pour pouvoir l’interroger.
   * Pour ajouter du stockage Blob Azure, vous avez besoin du compte de stockage et de la clé du compte. Pour les trouver, accédez au compte de stockage dans le portail.

## <a name="set-up-firewall-rules"></a>Configurer des règles de pare-feu

Vous pouvez utiliser Data Lake Analytics pour mieux verrouiller l’accès à votre compte Data Lake Analytics au niveau du réseau. Vous pouvez activer un pare-feu, spécifier une adresse IP ou définir une plage d’adresses IP pour vos clients approuvés. Une fois ces mesures activées, seuls les clients possédant des adresses IP dans la plage définie peuvent se connecter au Store.

Si d’autres services Azure, comme Azure Data Factory ou des machines virtuelles, se connectent au compte Data Lake Analytics, vérifiez que l’option **Autoriser les services Azure** est **activée**. 

### <a name="set-up-a-firewall-rule"></a>Configurer une règle de pare-feu

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Dans le menu de gauche, cliquez sur **Pare-feu**.

## <a name="add-a-new-user"></a>Ajouter un nouvel utilisateur

Vous pouvez utiliser **l’Assistant Ajout d’un utilisateur** pour configurer facilement de nouveaux utilisateurs Data Lake.

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Sur la gauche, sous **Prise en main**, cliquez sur **Assistant Ajout d’un utilisateur**.
3. Sélectionnez un utilisateur, puis cliquez sur **Enregistrer**.
4. Sélectionnez un rôle, puis cliquez sur **Enregistrer**. Pour configurer un nouveau développeur qui utilisera Azure Data Lake, sélectionnez le rôle **Développeur Data Lake Analytics**.
5. Sélectionnez les listes de contrôle d’accès (ACL) pour les bases de données U-SQL. Lorsque vous êtes satisfait de vos choix, cliquez sur **Sélectionner**.
6. Sélectionnez les listes de contrôle d’accès pour les fichiers. Pour le Store par défaut, ne modifiez pas les listes de contrôle d’accès pour le dossier racine « / » et pour le dossier /system. Cliquez sur **Sélectionner**.
7. Passez en revue toutes vos sélections, puis cliquez sur **Exécuter**.
8. Une fois l’Assistant exécuté, cliquez sur **Terminé**.

## <a name="manage-role-based-access-control"></a>Gérer le contrôle d’accès en fonction du rôle

Comme d’autres services Azure, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour contrôler la façon dont les utilisateurs interagissent avec le service.

Les rôles RBAC standard ont les fonctionnalités suivantes :
* **Propriétaire** : peut envoyer des travaux, surveiller des travaux, annuler des travaux de n’importe quel utilisateur et configurer le compte.
* **Contributeur** : peut envoyer des travaux, surveiller des travaux, annuler des travaux de n’importe quel utilisateur et configurer le compte.
* **Lecteur** : peut surveiller des travaux.

Utilisez le rôle Développeur Data Lake Analytics pour permettre aux développeurs U-SQL d’utiliser le service Data Lake Analytics. Vous pouvez utiliser le rôle Développeur Data Lake Analytics pour les tâches suivantes :
* Envoyer des travaux.
* Surveiller l’état du travail et la progression des travaux soumis par les utilisateurs.
* Afficher les scripts U-SQL à partir des travaux soumis par les utilisateurs.
* Annuler uniquement vos propres travaux.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Ajouter des utilisateurs ou des groupes de sécurité à un compte Data Lake Analytics

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Contrôle d’accès (IAM)** > **Ajouter**.
3. Sélectionnez un rôle
4. Ajoutez un utilisateur.
5. Cliquez sur **OK**.

>[!NOTE]
>Si un utilisateur ou un groupe de sécurité doit envoyer des travaux, ils doivent également être autorisés sur le compte du Store. Pour plus d’informations, consultez [Sécuriser les données stockées dans Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Gestion des travaux

### <a name="submit-a-job"></a>Soumettre un travail

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.

2. Cliquez sur **Nouveau travail**. Pour chaque travail, configurez les éléments suivants :

    1. **Nom du travail** : nom du travail.
    2. **Priorité** : les nombres inférieurs ont une priorité supérieure. Si deux travaux sont en file d’attente, celui dont la valeur de la priorité est la plus faible s’exécute en premier.
    3. **Parallélisme** : nombre maximal de processus de calcul à réserver pour ce travail.

3. Cliquez sur **Envoyer le travail**.

### <a name="monitor-jobs"></a>Surveiller des travaux

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Afficher tous les travaux**. Une liste de tous les travaux actifs et récemment terminés dans le compte s’affiche.
3. Le cas échéant, cliquez sur **Filtrer** pour rechercher les travaux par **Intervalle de temps**, **Nom du travail** et **Auteur**. 

### <a name="monitoring-pipeline-jobs"></a>Surveillance des tâches de pipeline
Les tâches qui font partie d’un pipeline fonctionnent ensemble, généralement séquentiellement, pour réaliser un scénario spécifique. Par exemple, vous pouvez avoir un pipeline qui nettoie, extrait, transforme, regroupe l’utilisation des informations du client. Les tâches de pipeline sont identifiées à l’aide de la propriété « Pipeline » lorsque la tâche a été soumise. Les tâches planifiées à l’aide du fichier de définition d’application V2 auront automatiquement cette propriété remplie. 

Pour afficher une liste des tâches U-SQL qui font partie de pipelines : 

1. Dans le portail Azure, accédez à vos comptes Data Lake Analytics.
2. Cliquez sur **Informations sur les tâches**. L’onglet « Toutes les tâches » s’affiche par défaut avec la liste des tâches en cours d’exécution, en file d’attente et terminées.
3. Cliquez sur l’onglet **Tâches de pipeline**. Une liste des tâches de pipeline s’affiche, ainsi que des statistiques agrégées pour chaque pipeline.

### <a name="monitoring-recurring-jobs"></a>Surveillance des tâches récurrentes
Une tâche récurrente a la même logique métier, mais utilise les données d’entrée différente chaque fois qu’elle est exécutée. Dans l’idéal, les tâches récurrentes doivent toujours réussir et ont une exécution relativement stable ; La surveillance de ces comportements permet de garantir que la tâche est intègre. Les tâches récurrentes sont identifiées à l’aide de la propriété « Récurrence ». Les tâches planifiées à l’aide du fichier de définition d’application V2 auront automatiquement cette propriété remplie.

Pour afficher une liste des tâches U-SQL récurrentes : 

1. Dans le portail Azure, accédez à vos comptes Data Lake Analytics.
2. Cliquez sur **Informations sur les tâches**. L’onglet « Toutes les tâches » s’affiche par défaut avec la liste des tâches en cours d’exécution, en file d’attente et terminées.
3. Cliquez sur l’onglet **Tâches récurrentes**. Une liste des tâches récurrentes s’affiche, ainsi que des statistiques agrégées pour chaque tâche récurrente.

## <a name="manage-policies"></a>Gérer les stratégies

### <a name="account-level-policies"></a>Stratégies au niveau du compte

Ces stratégies s’appliquent à tous les travaux dans un compte Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Nombre maximal d’unités Analytics dans un compte Data Lake Analytics
Une stratégie contrôle le nombre total d’unités Analytics que votre compte Data Lake Analytics peut utiliser. Par défaut, la valeur est définie sur 250. Par exemple, si cette valeur est définie sur 250 unités Analytics, vous pouvez avoir un travail qui s’exécute avec 250 unités Analytics assignées ou 10 travaux exécutant chacun 25 unités Analytics. Les travaux supplémentaires qui sont envoyés sont placés en file d’attente jusqu'à ce que les travaux en cours d’exécution soient terminés. Lorsque les travaux en cours d’exécution sont terminés, des unités Analytics sont libérées pour l’exécution des travaux dans la file d’attente.

Pour modifier le nombre d’unités Analytics pour votre compte Data Lake Analytics :

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Propriétés**.
3. Sous **Nombre maximale d’unités Analytics**, déplacez le curseur pour sélectionner une valeur ou entrez la valeur dans la zone de texte. 
4. Cliquez sur **Enregistrer**.

> [!NOTE]
> Si vous avez besoin de plus d’unités Analytics que la valeur par défaut (250), cliquez sur **Aide + support** dans le portail pour envoyer une demande de support. Il est possible d’augmenter le nombre d’unités Analytics disponibles dans votre compte Data Lake Analytics.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Nombre maximal de travaux pouvant s’exécuter simultanément
Une stratégie contrôle le nombre de travaux qui peuvent s’exécuter simultanément. Par défaut, cette valeur est définie sur 20. Si des unités Analytics sont disponibles dans votre Data Lake Analytics, de nouveaux travaux sont planifiés pour s’exécuter immédiatement jusqu’à ce que le nombre total de travaux en cours d’exécution atteigne la valeur de cette stratégie. Lorsque vous atteignez le nombre maximal de travaux pouvant s’exécuter simultanément, les travaux suivants sont placés en file d’attente par ordre de priorité jusqu’à ce qu’un ou plusieurs des travaux en cours d’exécution se termine (selon la disponibilité des unités Analytics).

Pour modifier le nombre maximal de travaux pouvant s’exécuter simultanément :

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Propriétés**.
3. Sous **Nombre maximal de travaux en cours d’exécution**, déplacez le curseur pour sélectionner une valeur ou entrez la valeur dans la zone de texte. 
4. Cliquez sur **Enregistrer**.

> [!NOTE]
> Si vous avez besoin d’exécuter plus de travaux que la valeur par défaut (20), cliquez sur **Aide + support** dans le portail pour envoyer une demande de support. Il est possible d’augmenter le nombre de travaux pouvant s’exécuter simultanément dans votre compte Data Lake Analytics.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Durée de conservation des métadonnées et des ressources d’un travail 
Lorsque vos utilisateurs exécutent des travaux U-SQL, le service Data Lake Analytics conserve tous les fichiers associés. Les fichiers associés incluent le script U-SQL, les fichiers DLL référencés dans le script U-SQL, les ressources compilées et les statistiques. Les fichiers se trouvent dans le dossier /system/ du compte de stockage Azure Data Lake par défaut. Cette stratégie contrôle la durée pendant laquelle ces ressources sont stockées avant d’être automatiquement supprimées (la valeur par défaut est de 30 jours). Vous pouvez utiliser ces fichiers pour le débogage et pour régler les performances des travaux que vous exécuterez à nouveau à l’avenir.

Pour modifier la durée de conservation des métadonnées et des ressources d’un travail :

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Propriétés**.
3. Sous **Jours de rétention des requêtes de tâche**, déplacez le curseur pour sélectionner une valeur ou entrez la valeur dans la zone de texte.  
4. Cliquez sur **Enregistrer**.

### <a name="job-level-policies"></a>Stratégies au niveau du travail
Avec les stratégies au niveau du travail, vous pouvez contrôler les unités Analytics maximales et la priorité maximale que les utilisateurs individuels (ou les membres de groupes de sécurité spécifiques) peuvent définir sur les travaux qu’ils soumettent. Cela vous permet de contrôler les coûts générés par les utilisateurs. Cela vous permet également de contrôler l’effet que les travaux planifiés peuvent avoir sur des travaux de production à priorité élevée qui s’exécutent dans le même compte Data Lake Analytics.

Data Lake Analytics inclut deux stratégies que vous pouvez définir au niveau du travail :

* **Limite d’unités Analytics par travail** : les utilisateurs peuvent uniquement soumettre des travaux dont le nombre maximal d’unités Analytics est inférieur ou égal à cette valeur. Par défaut, cette limite est identique à la limite maximale d’unités Analytics du compte.
* **Priorité** : les utilisateurs peuvent uniquement soumettre les travaux dont la priorité est inférieure ou égale à cette valeur. Notez qu’un nombre plus élevé signifie une priorité plus faible. Par défaut, elle est définie sur 1, qui est la priorité la plus élevée.

Chaque compte contient une stratégie par défaut. La stratégie par défaut s’applique à tous les utilisateurs du compte. Vous pouvez définir des stratégies supplémentaires pour des utilisateurs et des groupes spécifiques. 

> [!NOTE]
> Les stratégies au niveau du compte et les stratégies au niveau du travail s’appliquent simultanément.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Ajouter une stratégie pour un utilisateur ou un groupe spécifique

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Propriétés**.
3. Sous **Limites d’envoi de tâches**, cliquez sur le bouton **Ajouter une stratégie**. Puis, sélectionnez ou saisissez les paramètres suivants :
    1. **Nom de la stratégie de calcul** : entrez un nom de stratégie, pour vous rappeler de l’objectif de la stratégie.
    2. **Sélectionner un utilisateur ou un groupe** : sélectionnez l’utilisateur ou le groupe auquel la stratégie s’applique.
    3. **Définir la limite d’unités Analytics du travail** : définissez la limite d’unités Analytics qui s’applique à l’utilisateur ou au groupe sélectionné.
    4. **Définir la limite de priorité** : définissez la limite de priorité qui s’applique à l’utilisateur ou au groupe sélectionné.

4. Cliquez sur **OK**.

5. La nouvelle stratégie est répertoriée dans la table des stratégies **Par défaut**, sous **Limites d’envoi de tâches**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Supprimer ou modifier une stratégie existante

1. Dans le portail Azure, accédez à votre compte Data Lake Analytics.
2. Cliquez sur **Propriétés**.
3. Sous **Limites d’envoi de tâches**, recherchez la stratégie que vous souhaitez modifier.
4.  Pour voir les options **Supprimer** et **Modifier**, dans la colonne la plus à droite de la table, cliquez sur **...**.

### <a name="additional-resources-for-job-policies"></a>Ressources supplémentaires relatives aux stratégies de travail
* [Post de blog Vue d’ensemble de la stratégie](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Post de blog Stratégies au niveau du compte](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Post de blog Stratégies au niveau du travail](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d’Azure Data Lake Analytics à l’aide d’Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

