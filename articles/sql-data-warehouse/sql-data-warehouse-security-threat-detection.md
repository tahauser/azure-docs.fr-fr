---
title: Prise en main de Threat Detection pour SQL Data Warehouse
description: "Prise en main de la détection de menaces"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 7f5dab6936e8cac10ac7a4a7dc4c3be116de5ad5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-threat-detection"></a>Prise en main de la détection de menaces
> [!div class="op_single_selector"]
> * [Audit](sql-data-warehouse-auditing-overview.md)
> * [Détection de menaces](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. Threat Detection est disponible en version préliminaire et est pris en charge pour SQL Data Warehouse.

Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Les utilisateurs peuvent explorer les événements suspects à l’aide de l’ [audit d’Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans l’entrepôt de données.
Threat Detection vous permet de réagir facilement aux menaces potentielles envers l'entrepôt de données sans avoir à acquérir une expertise de la sécurité ou à gérer des systèmes de surveillance de la sécurité avancés.

Par exemple, il détecte certaines activités de base de données anormales indiquant des tentatives d’injection SQL potentielles. L’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

## <a name="set-up-threat-detection-for-your-database"></a>Configurer Threat Detection pour votre base de données
1. Accédez à l’adresse [https://portal.azure.com](https://portal.azure.com)et exécutez le portail Azure.
2. Accédez au volet de configuration de l'entrepôt SQL Data Warehouse que voulez surveiller. Dans le panneau Paramètres, sélectionnez **Audit et détection des menaces**.
   
    ![Volet de navigation][1]
3. Dans le panneau de configuration **Audit et détection des menaces**, **activez** l’audit pour afficher les paramètres Détection des menaces.
   
    ![Volet de navigation][2]
4. **Activez** la détection des menaces.
5. Configurez la liste des adresses électroniques qui recevront les alertes de sécurité en cas de détection d'activités anormales sur l'entrepôt de données.
6. Cliquez sur **Enregistrer** dans le panneau de configuration **Audit et détection des menaces** pour enregistrer la stratégie d’audit et de détection des menaces que vous avez créée ou modifiée.
   
    ![Volet de navigation][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur l'entrepôt de données en cas de détection d'un événement suspect
1. Vous recevrez une notification par courrier électronique lorsque des activités anormales sont détectées au niveau de la base de données. <br/>
   Le courrier électronique contiendra des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées afin d’examiner et atténuer la menace potentielle pesant sur la base de données.<br/>
   
    ![Volet de navigation][4]
2. Dans le courrier électronique, cliquez sur le lien **Azure SQL Auditing Log** pour ouvrir le portail Azure et afficher les enregistrements d’audit pertinents au moment de l’événement suspect.
   
    ![Volet de navigation][5]
3. Cliquez sur les enregistrements d’audit pour afficher plus de détails sur les activités de base de données suspects, comme l’instruction SQL, la cause de l’échec et l’adresse IP client.
   
    ![Volet de navigation][6]
4. Dans le panneau des enregistrements d’audit, cliquez sur **Ouvrir dans Excel** pour ouvrir un modèle Excel préconfiguré à importer et exécuter une analyse plus approfondie du journal d’audit au moment de l’événement suspect.<br/>
   **Remarque :** dans Excel 2010 ou version ultérieure, les paramètres Power Query et **Combinaison rapide** sont requis.
   
    ![Volet de navigation][7]
5. Pour configurer le paramètre **Combinaison rapide** : sous l’onglet du ruban **POWER QUERY**, sélectionnez **Options** pour afficher la boîte de dialogue correspondante. Sélectionnez la section Confidentialité et choisissez la deuxième option « gnore the Privacy Levels and potentially improve performance » :
   
    ![Volet de navigation][8]
6. Pour charger les journaux d’audit SQL, vérifiez que les paramètres de l’onglet Paramètres sont correctement définis, puis sélectionnez le ruban « Données » et cliquez sur le bouton « Actualiser tout ».
   
    ![Volet de navigation][9]
7. Les résultats s’affichent dans la feuille **SQL Audit Logs** , qui vous permet d’analyser de manière plus approfondie les activités anormales détectées et de limiter l’impact de l’événement de sécurité sur votre application.

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
