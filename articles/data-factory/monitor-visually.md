---
title: "Surveiller visuellement des fabriques de données Azure | Microsoft Docs"
description: "Découvrez comment surveiller visuellement des fabriques de données Azure"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: 60d7897caebcb138180d99358ec754c1b9594fe5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="visually-monitor-azure-data-factories"></a>Surveiller visuellement des fabriques de données Azure
Azure Data Factory est un service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement et la transformation des données. Grâce à Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser.
Dans ce guide de démarrage rapide, vous allez découvrir comment contrôler visuellement des pipelines Data Factory v2 sans écrire une seule ligne de code.
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez la page [Surveiller et gérer les pipelines dans Data Factory version 1](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Surveiller des pipelines Data Factory v2

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome.
2. Connectez-vous au [portail Azure](https://portal.azure.com/).
3. Accédez au panneau de fabrique de données créé dans le portail Azure, puis cliquez sur la vignette « Surveiller et gérer ». Cette action lance l’expérience de surveillance visuelle ADF v2.

## <a name="list-view-monitoring"></a>Surveillance avec affichage de liste

Surveillez les exécutions d’activités et de pipelines avec une interface d’affichage de liste simple. Toutes les exécutions sont affichées dans le fuseau horaire du navigateur local. Vous pouvez changer le fuseau horaire. Dans ce cas, tous les champs d’heure et de date seront alignés sur le fuseau horaire sélectionné.  

#### <a name="monitoring-pipeline-runs"></a>Surveillance des exécutions de pipelines
Liste montrant chaque exécution de pipeline pour vos pipelines Data Factory v2. Les colonnes affichées sont les suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline. |
| Actions | Action unique disponible pour afficher les exécutions d’activités. |
| Démarrage de l’exécution | Heure et date de début de l’exécution de pipeline (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Déclenché par | Déclenchement manuel, déclenchement planifié |
| Statut | Échec, Réussite, En cours |
| parameters | Paramètres d’exécution de pipeline (paires nom/valeur) |
| Error | Erreur d’exécution de pipeline (le cas échéant) |
| ID d’exécution | ID de l’exécution de pipeline |

![Surveiller des exécutions de pipelines](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Surveillance d’exécutions d’activités
Liste montrant les exécutions d’activités correspondant à chaque exécution de pipeline. Cliquez sur l’icône **Exécution de l’activité** sous la colonne **Actions** pour afficher les exécutions d’activités pour chaque exécution de pipeline. Les colonnes affichées sont les suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom de l’activité | Nom de l’activité dans le pipeline. |
| Type d’activité | Type de l’activité (Copie, HDInsightHive, HDInsightSpark, et ainsi de suite). |
| Démarrage de l’exécution | Heure et date de début de l’exécution d’activité (MM/JJ/AAAA, HH:MM:SS AM/PM) |
| Duration | Durée d’exécution (HH:MM:SS) |
| Statut | Échec, Réussite, En cours |
| Entrée | Tableau JSON décrivant les entrées de l’activité |
| Sortie | Tableau JSON décrivant les sorties de l’activité |
| Error | Erreur d’exécution d’activité (le cas échéant) |

![Surveiller des exécutions d’activités](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Vous devez cliquer sur l’icône **Actualiser** en haut pour actualiser la liste des exécutions de pipelines et d’activités. L’actualisation automatique n’est pas prise en charge actuellement.
>

![Actualiser](media/monitor-visually/refresh.png)

## <a name="features"></a>Caractéristiques

#### <a name="rich-ordering-and-filtering"></a>Classement et filtrage enrichis

Vous pouvez classer les exécutions de pipelines par ordre croissant ou décroissant de Démarrage de l’exécution, et filtrer les exécutions de pipelines par les colonnes suivantes :

| **Nom de la colonne** | **Description** |
| --- | --- |
| Nom du pipeline | Nom du pipeline. Il existe des options de filtre rapide comme « Dernières 24 heures », « La semaine dernière », « Les 30 derniers jours », et vous pouvez aussi sélectionner une heure et une date personnalisées. |
| Démarrage de l’exécution | Heure et date de début de l’exécution de pipeline. |
| État de l’exécution | Filtrez les exécutions par état (Réussite, Échec ou En cours). |

![Filtrer](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Ajouter/supprimer des colonnes dans la liste
Cliquez avec le bouton droit sur l’en-tête de liste et sélectionnez les colonnes que vous souhaitez afficher.

![Colonnes](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Réorganiser les largeurs de colonne dans la liste
Augmentez et diminuez les largeurs de colonne dans la liste en plaçant simplement le curseur sur l’en-tête de colonne.

#### <a name="select-data-factory"></a>Sélectionner une fabrique de données
Placez le curseur sur l’icône « Data Factory » en haut à gauche. Cliquez sur l’icône « Flèche » pour afficher la liste des fabriques de données et des abonnements Azure que vous pouvez surveiller.

![Sélectionner une fabrique de données](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Visites guidées
Cliquez sur l’icône « Informations » dans l’angle inférieur gauche, puis cliquez sur « Visites guidées » pour obtenir des instructions détaillées sur la façon de surveiller vos exécutions d’activités et de pipelines.

![Visites guidées](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Commentaires
Cliquez sur l’icône « Commentaires » pour nous envoyer vos commentaires sur les différentes fonctionnalités ou les problèmes auxquels vous êtes confrontés.

![Commentaires](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article [Surveiller et gérer les pipelines par programmation](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) pour en savoir plus sur la surveillance et la gestion des pipelines.
