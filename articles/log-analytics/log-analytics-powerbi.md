---
title: "Importation de données Azure Log Analytics dans Power BI | Microsoft Docs"
description: "Power BI est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Cet article explique comment configurer l'importation de données Log Analytics dans Power BI et les configurer pour s'actualiser automatiquement."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: e687a1ee8ac4f565062e57b07cdfa9ac5e6bbf4f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importation de données Azure Log Analytics dans Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est un service Microsoft d’analyse commerciale basé sur le cloud qui fournit de riches fonctions de visualisation et de rapport afin de faciliter l’analyse de différents jeux de données.  Vous pouvez importer les résultats d'une recherche de journal Log Analytics dans un jeu de données Power BI afin de bénéficier de ses fonctionnalités telles que la combinaison de données provenant de différentes sources et le partage de rapports sur le Web et sur des appareils mobiles.

Cet article fournit des détails sur l'importation de données Log Analytics dans Power BI et sur la planification de leur actualisation automatique.  Différents processus sont inclus pour un espace de travail [mis à niveau](#upgraded-workspace) et un espace de travail [existant](#legacy-workspace).

## <a name="upgraded-workspace"></a>Espace de travail mis à niveau


Pour importer les données d'un [espace de travail Log Analytics mis à niveau](log-analytics-log-search-upgrade.md) dans Power BI, vous créez un jeu de données dans Power BI à partir d'une requête de recherche de journal dans Log Analytics.  La requête est exécutée chaque fois que le jeu de données est actualisé.  Vous pouvez ensuite créer des rapports Power BI qui utilisent les informations du jeu de données.  Pour créer le jeu de données dans Power BI, vous exportez votre requête depuis Log Analytics vers le [langage Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Vous l'utilisez ensuite pour créer une requête dans Power BI Desktop, avant de la publier dans Power BI en tant que jeu de données.  Les détails de ce processus sont décrits ci-dessous.

![Log Analytics vers Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Exporter une requête
Commencez par créer une [recherche de journal](log-analytics-log-search-new.md) qui renvoie les données Log Analytics que vous voulez ajouter au jeu de données Power BI.  Vous exportez ensuite cette requête dans le [langage Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx) qui peut être utilisé par Power BI Desktop.

1. Créez la recherche de journal Log Analytics pour extraire les informations de votre jeu de données.
2. Si vous utilisez le portail de recherche de journal, cliquez sur **Power BI**.  Si vous utilisez le portail Analytics, sélectionnez **Exporter** > **Requête Power BI (M)**.  Ces deux options exportent la requête vers un fichier texte intitulé **PowerBIQuery.txt**. 

    ![Exporter une recherche de journal](media/log-analytics-powerbi/export-logsearch.png) ![Exporter une recherche de journal](media/log-analytics-powerbi/export-analytics.png)

3. Ouvrez le fichier texte et copiez son contenu.

### <a name="import-query-into-power-bi-desktop"></a>Importation d'une requête dans Power BI Desktop
Power BI Desktop est une application de bureau qui vous permet de créer des jeux de données et des rapports pouvant être publiés sur Power BI.  Vous pouvez également l'utiliser pour créer une requête à l'aide du langage Power Query exporté à partir de Log Analytics. 

1. Installez [Power BI Desktop](https://powerbi.microsoft.com/desktop/) si vous ne l'avez pas déjà, puis ouvrez l'application.
2. Sélectionnez **Obtenir les données** > **Requête vide** pour ouvrir une nouvelle requête.  Sélectionnez ensuite **Éditeur avancé** puis collez le contenu du fichier dans la requête. Cliquez sur **Done**.

    ![Requête Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La requête s'exécute puis les résultats s'affichent.  Vous pouvez être invité à entrer vos informations d'identification pour vous connecter à Azure.  
6. Entrez un nom descriptif pour la requête.  La valeur par défaut est **Query1**. Cliquez sur **Fermer et appliquer** pour ajouter le jeu de données au rapport.

    ![Nom Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Publier vers Power BI
Lorsque vous publiez sur Power BI, un jeu de données et un rapport sont créés.  Si vous créez un rapport dans Power BI Desktop, celui-ci sera publié avec vos données.  Sinon, un rapport vide sera créé.  Vous pouvez modifier le rapport dans Power BI, ou en créer un basé sur le jeu de données.

8. Créez un rapport basé sur vos données.  Consultez la [documentation Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) si vous avez besoin d'aide.  Lorsque vous être prêt à envoyer le rapport à Power BI, cliquez sur **Publier**.  À l'invite, sélectionnez une destination dans votre compte Power BI.  À moins de vouloir choisir une destination spécifique, utilisez **Mon espace de travail**.

    ![Publication Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Une fois la publication terminée, cliquez sur **Ouvrir dans Power BI** pour ouvrir Power BI avec votre nouveau jeu de données.


### <a name="configure-scheduled-refresh"></a>Configuration d'une actualisation planifiée
Le jeu de données créé dans Power BI contiendra les mêmes informations que celles que vous avez déjà consultées dans Power BI Desktop.  Vous devez régulièrement actualiser le jeu de données pour réexécuter la requête et la remplir avec les dernières informations de Log Analytics.  

1. Cliquez sur l'espace de travail dans lequel vous avez chargé votre rapport puis sélectionnez le menu **Jeux de données**. Sélectionnez le menu contextuel en regard de votre nouveau jeu de données puis choisissez **Paramètres**. Sous **Informations d'identification de la source de données**, vous devriez voir un message indiquant que les informations d'identification ne sont pas valides.  Cela est dû au fait que vous n'avez pas encore fourni d'informations d'identification pour le jeu de données à utiliser lors de l'actualisation de ses informations.  Cliquez sur **Modifier les informations d'identification** et spécifiez les informations d'identification pour accéder à Log Analytics.

    ![Planification Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. Sous **Actualisation planifiée**, activez l'option pour **conserver vos données à jour**.  Vous pouvez aussi modifier la **fréquence d'actualisation** et une ou plusieurs heures spécifiques pour exécuter l'actualisation.

    ![Actualisation Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>Espace de travail hérité
Lorsque vous configurez Power BI avec un [espace de travail Log Analytics existant](log-analytics-powerbi.md), vous créez des requêtes de journal qui exportent les résultats vers les jeux de données correspondants dans Power BI.  La requête et l’exportation poursuivent automatiquement leur exécution selon une planification que vous avez définie afin de maintenir à jour le jeu de données avec les dernières données collectées par Log Analytics.

![Log Analytics vers Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Planifications Power BI
Une *planification Power BI* inclut une recherche dans les journaux qui exporte un jeu de données de Log Analytics vers un jeu de données correspondant dans Power BI, ainsi qu’une planification qui définit la fréquence d’exécution de cette recherche afin de maintenir à jour le jeu de données.

Les champs du jeu de données correspondent aux propriétés des enregistrements renvoyés par la recherche de journal.  Si la recherche renvoie des enregistrements de différents types, le jeu de données inclura toutes les propriétés de chacun des types d’enregistrements inclus.  

### <a name="connecting-log-analytics-workspace-to-power-bi"></a>Connexion de l’espace de travail Log Analytics à Power BI
Avant de pouvoir exporter des données de Log Analytics vers Power BI, vous devez connecter votre espace de travail à votre compte Power BI à l’aide de la procédure suivante.  

1. Dans la console OMS, cliquez sur la vignette **Paramètres** .
2. Sélectionnez **Comptes**.
3. Dans la section **Informations sur l’espace de travail**, cliquez sur **Se connecter à un compte Power BI**.
4. Entrez les informations d’identification de votre compte Power BI.

### <a name="create-a-power-bi-schedule"></a>Création d’une planification Power BI
Créez une planification Power BI pour chaque jeu de données à l’aide de la procédure suivante.

1. Dans la console OMS, cliquez sur la vignette **Recherche de journal** .
2. Entrez une nouvelle requête ou sélectionnez une recherche enregistrée qui retourne les données que vous souhaitez exporter vers **Power BI**.  
3. Cliquez sur le bouton **Power BI** en haut de la page pour ouvrir la boîte de dialogue **Power BI**.
4. Renseignez les informations du tableau suivant et cliquez sur **Enregistrer**.

| Propriété | Description |
|:--- |:--- |
| NOM |Nom permettant d’identifier la planification dans la liste des planifications Power BI. |
| Recherche enregistrée |Recherche de journal à exécuter.  Vous pouvez sélectionner la requête en cours ou sélectionner une recherche enregistrée dans la zone de liste déroulante. |
| Planification |Fréquence d’exécution de la recherche enregistrée et d’exportation des résultats vers le jeu de données Power BI.  La valeur doit être comprise entre 15 minutes et 24 heures. |
| Nom du jeu de données |Nom du jeu de données dans Power BI.  Ce nom sera créé s’il n’existe pas ; dans le cas contraire, il sera mis à jour. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Affichage et suppression de planifications Power BI
Utilisez la procédure suivante pour afficher la liste des planifications Power BI.

1. Dans la console OMS, cliquez sur la vignette **Paramètres** .
2. Sélectionnez **Power BI**.

Vous obtenez, outre les détails de la planification, le nombre d’exécutions de la planification au cours de la semaine précédente ainsi que l’état de la dernière synchronisation.  Si la synchronisation a rencontré des erreurs, vous pouvez cliquer sur le lien pour exécuter une recherche de journal afin d’obtenir les enregistrements et les détails de l’erreur.

Vous pouvez supprimer une planification en cliquant sur le **X** dans **Supprimer la colonne**.  Pour désactiver une planification, sélectionnez **Désactivé**.  Pour modifier une planification, vous devez la supprimer et la recréer avec les nouveaux paramètres.

![Planifications Power BI](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Exemple de procédure pas à pas
La section suivante explique, au travers d’un exemple, comment création une planification Power BI et utiliser son jeu de données pour créer un rapport simple.  Dans cet exemple, toutes les données de performances d’un ensemble d’ordinateurs sont exportées vers Power BI. Un graphique linéaire est ensuite créé pour afficher l’utilisation du processeur.

#### <a name="create-log-search"></a>Création de la recherche de journal
Nous allons commencer par créer une recherche de journal sur les données que vous souhaitez envoyer au jeu de données.  Dans cet exemple, nous allons utiliser une requête qui retourne toutes les données de performances des ordinateurs dont le nom commence par *srv*.  

![Planifications Power BI](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Création d’une recherche Power BI
Cliquez sur le bouton **Power BI** pour ouvrir la boîte de dialogue Power BI et renseignez les informations requises.  Vous souhaitez exécuter cette recherche une fois par heure et créer un jeu de données nommé *Contoso Perf*.  Puisque vous avez déjà ouvert la recherche qui permet de créer les données souhaitées, conservez la valeur par défaut de l’option *Utiliser la requête de recherche active* pour **Recherche enregistrée**.

![Recherche Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Vérification de la recherche Power BI
Pour vérifier que la planification a été correctement créée, affichez la liste des recherches Power BI sous la vignette **Paramètres** dans le tableau de bord OMS.  Attendez quelques minutes et actualisez cette vue jusqu’à ce que vous obteniez confirmation que la synchronisation a été exécutée.  Vous planifiez généralement le jeu de données afin de l'actualiser automatiquement.

![Recherche Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Vérification du jeu de données dans Power BI
Connectez-vous à votre compte à l’adresse [powerbi.microsoft.com](http://powerbi.microsoft.com/) et faites défiler le volet gauche vers le bas jusque **Jeux de données** .  Comme vous pouvez le voir, le jeu de données *Contoso Perf* apparaît, ce qui signifie que l’exportation a réussi.

![Jeu de données Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Création d’un rapport dérivé du jeu de données
Sélectionnez le jeu de données **Contoso Perf**, puis cliquez sur **Résultats** dans le volet **Champs** pour afficher les champs qui font partie de ce jeu de données.  Pour créer un graphique linéaire illustrant l’utilisation des ressources processeur de chaque ordinateur, effectuez les actions suivantes.

1. Sélectionnez la visualisation Graphique en courbes.
2. Faites glisser **ObjectName** sur **Filtre du niveau de rapport** et cochez la case **Processeur**.
3. Faites glisser **CounterName** sur **Filtre du niveau de rapport** et cochez **% temps processeur**.
4. Faites glisser **CounterValue** sur **Valeurs**.
5. Faites glisser **Ordinateur** sur **Légende**.
6. Faites glisser **TimeGenerated** sur **Axe**.

Comme vous pouvez le voir, le graphique en courbes obtenu s’affiche avec les données de notre jeu de données.

![Graphique en courbes Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Enregistrement du rapport
Pour enregistrer le rapport, cliquez sur le bouton Enregistrer en haut de l’écran, puis vérifiez qu’il apparaît désormais dans la section Rapports dans le volet gauche.

![Rapports Power BI](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment les [recherches de journaux](log-analytics-log-searches.md) peuvent vous aider à générer des requêtes pouvant être exportées vers Power BI.
* Découvrez comment utiliser [Power BI](http://powerbi.microsoft.com) pour créer des visualisations basées sur des exportations Log Analytics.
