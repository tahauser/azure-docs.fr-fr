---
title: "Découverte et classification des données Azure SQL Database | Microsoft Docs"
description: "Découverte et classification des données Azure SQL Database"
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: da4f72e61607dcad7314a2fe65324da4635752c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Découverte et classification des données Azure SQL Database
Découverte et classification des données (actuellement en préversion) offre des fonctionnalités avancées intégrées à Azure SQL Database pour la **découverte**, la **classification**, l’**étiquetage**  & et la **protection** des données sensibles dans vos bases de données.
La découverte et la classification de vos données les plus sensibles (professionnelles/financières, soins de santé, informations d’identification personnelle, et ainsi de suite) peuvent jouer un rôle essentiel dans la protection des informations de l’organisation. Elles peuvent servir d’infrastructure pour :
* Aider à répondre aux normes de confidentialité des données et aux exigences de conformité aux normes, telles que RGPD.
* Divers scénarios de sécurité, comme la surveillance (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles.
* Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.

## <a id="subheading-1"></a>Vue d’ensemble
Découverte et classification des données introduit un ensemble de services avancés et de nouvelles fonctionnalités SQL qui forment un nouveau paradigme de protection des informations SQL visant à protéger les données, et pas seulement la base de données :
* **Découverte et recommandations** : Le moteur de classification analyse votre base de données et identifie les colonnes contenant des données potentiellement sensibles. Il vous permet ensuite de vérifier et d’appliquer facilement les recommandations de classification appropriées par le biais du portail Azure.
* **Étiquetage** : Vous pouvez appliquer de manière permanente des étiquettes de classification de sensibilité sur des colonnes à l’aide de nouveaux attributs de métadonnées de classification introduits dans le moteur SQL. Vous pouvez ensuite utiliser ces métadonnées pour des scénarios avancés d’audit et de protection basés sur la sensibilité.
* **Interrogation de la sensibilité du jeu de résultats** : La sensibilité du jeu de résultats est calculée en temps réel à des fins d’audit.
* **Visibilité** : Vous pouvez afficher l’état de classification de la base de données dans un tableau de bord détaillé dans le portail. Vous pouvez aussi télécharger un rapport (au format Excel) à utiliser entre autres à des fins de conformité et d’audit.

## <a id="subheading-2"></a>Découverte, classification et étiquetage des colonnes sensibles
La section suivante décrit les étapes de découverte, de classification et d’étiquetage des colonnes contenant des données sensibles dans votre base de données, ainsi que l’affichage de l’état actuel de la classification de votre base de données et l’exportation de rapports.

La classification comprend deux attributs de métadonnées :
* Étiquettes : principaux attributs de classification, utilisés pour définir le niveau de confidentialité des données stockées dans la colonne.  
* Types d’informations : spécifiez une granularité supplémentaire concernant le type des données stockées dans la colonne.

<br>
**Pour classifier votre base de données SQL**

1. Accédez au [portail Azure](https://portal.azure.com).

2. Accédez au paramètre **Découverte et classification des données (préversion)** dans votre base de données SQL.

    ![Volet de navigation][1]

3. L’onglet **Vue d’ensemble** comprend une synthèse de l’état actuel de la classification de la base de données, notamment une liste détaillée de toutes les colonnes classifiées, que vous pouvez aussi filtrer pour afficher uniquement des parties de schéma, des types d’informations et des étiquettes spécifiques. Si vous n’avez pas encore classifié de colonne, [passez à l’étape 5](#step-5).

    ![Volet de navigation][2]

4. Pour télécharger un rapport au format Excel, cliquez sur l’option **Exporter** dans le menu en haut de la fenêtre.

    ![Volet de navigation][3]

5.  <a id="step-5"></a>Pour commencer à classifier vos données, cliquez sur l’onglet **Classification** en haut de la fenêtre.

    ![Volet de navigation][4]

6. Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de **classifications de colonnes recommandées**. Pour afficher et appliquer les recommandations de classification :

    * Pour afficher la liste des classifications de colonnes recommandées, cliquez sur le panneau de recommandations en bas de la fenêtre :

        ![Volet de navigation][5]

    * Passez en revue la liste des recommandations. Pour accepter une recommandation pour une colonne spécifique, cochez la case dans la colonne de gauche de la ligne concernée. Vous pouvez également accepter *toutes les recommandations* en cochant la case dans l’en-tête de table de recommandations.

        ![Volet de navigation][6]

    * Pour appliquer les recommandations sélectionnées, cliquez sur le bouton bleu **Accepter les recommandations sélectionnées**.

        ![Volet de navigation][7]

7. Vous pouvez aussi, en guise d’alternative, **classifier manuellement** des colonnes ou, en plus de la classification basée sur les recommandations :

    * Cliquez sur **Ajouter une classification** dans le menu en haut de la fenêtre.

        ![Volet de navigation][8]

    * Dans la fenêtre contextuelle qui apparaît, sélectionnez le schéma > table > colonne que vous souhaitez classifier, ainsi que l’étiquette de sensibilité et le type d’informations. Cliquez sur le bouton bleu **Ajouter une classification** en bas de la fenêtre contextuelle.

        ![Volet de navigation][9]

8. Pour terminer votre classification et étiqueter de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification, cliquez sur **Enregistrer** dans le menu en haut de la fenêtre.

    ![Volet de navigation][10]

## <a id="subheading-3"></a>Audit de l’accès aux données sensibles

Un aspect important du paradigme de protection des informations est la possibilité de surveiller l’accès aux données sensibles.

[L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a été amélioré pour inclure dans le journal d’audit un nouveau champ nommé *data_sensitivity_information*, qui enregistre las classifications de la sensibilité (étiquettes) des données réelles retournées par la requête.

![Volet de navigation][11]

## <a id="subheading-4"></a>Étapes suivantes
Vous pouvez configurer [l’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) pour effectuer la surveillance et l’audit de l’accès à vos données sensibles classifiées.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
