---
title: Détection de menaces - Azure SQL Database | Microsoft Docs
description: Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données.
services: sql-database
documentationcenter: ''
author: rmatchoro
manager: shaik
editor: v-romcal
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 06/19/2017
ms.author: ronmat
ms.openlocfilehash: a01721b10305ac45082943fbe4fb7fb199a0e49f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="sql-database-threat-detection"></a>Détection de menaces pour les bases de données SQL

SQL Threat Detection détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à utiliser des bases de données ou à exploiter leurs failles de sécurité.

## <a name="overview"></a>Vue d'ensemble

SQL Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales.  Les utilisateurs reçoivent une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes SQL Threat Detection fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace. Les utilisateurs peuvent explorer les événements suspects à l’aide de l’[audit de base de données SQL](sql-database-auditing.md) pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans la base de données. Threat Detection vous permet de réagir facilement aux menaces potentielles à la base de données sans avoir à acquérir une expertise de la sécurité ou à gérer des systèmes de surveillance de la sécurité avancés.

Par exemple, l’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données.

La détection des menaces SQL intègre les alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Chaque serveur SQL Database protégé est facturé au même prix que le niveau Standard d’Azure Security Center, soit 15 USD/nœud/mois : chaque serveur SQL Database protégé est alors comptabilisé comme un seul nœud.  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Configurer la détection des menaces pour votre base de données dans le portail Azure
1. Accédez à l’adresse [https://portal.azure.com](https://portal.azure.com) et lancez le portail Azure.
2. Accédez à la page de configuration de l’instance SQL Database que vous voulez surveiller. Dans la page Paramètres, sélectionnez **Audit et détection des menaces**. 
    ![Volet de navigation][1]
3. Dans la page de configuration **Audit et détection des menaces**, **activez** l’audit pour afficher les paramètres de détection des menaces.
  
    ![Volet de navigation][2]
4. **Activez** la détection des menaces.
5. Configurez la liste des e-mails qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
6. Cliquez sur **Enregistrer** dans la page **Audit et détection des menaces** pour enregistrer les paramètres d’audit et de détection des menaces que vous avez créés ou modifiés.
       
    ![Volet de navigation][3]

## <a name="set-up-threat-detection-using-powershell"></a>Configurer la détection des menaces avec PowerShell

Pour obtenir un exemple de script, consultez [Configurer l’audit et la détection des menaces avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur la base de données en cas de détection d’un événement suspect
1. Vous recevez une notification par e-mail quand des activités anormales sont détectées sur la base de données. <br/>
   L’e-mail contient des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur, le nom de l’application et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur la base de données.<br/>
     
    ![Volet de navigation][4]
2. L’alerte par courrier inclut un lien direct vers le journal d’audit SQL. Lorsque vous cliquez sur ce lien, le portail Azure s’affiche et ouvre les enregistrements d’audit SQL au moment de l’événement suspect. Cliquez sur un enregistrement d’audit pour afficher plus d’informations sur les activités de base de données suspectes. Vous pouvez ainsi plus facilement trouver les instructions SQL qui ont été exécutées (quel utilisateur, quelles actions et à quel moment) et déterminer si l’événement est légitime ou malveillant (par exemple, la vulnérabilité de l’application aux injections SQL a été exploitée, quelqu’un a opéré une violation des données sensibles, etc.).<br/>
   ![Volet de navigation][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>Explorer les alertes de détection des menaces pour votre base de données dans le portail Azure

SQL Database Threat Detection intègre ses alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Une vignette de sécurité SQL dynamique dans la page de base de données au sein du portail Azure effectue le suivi de l’état des menaces actives. 

   ![Volet de navigation][6]
   
1. Quand vous cliquez sur la vignette de sécurité, la page d’alertes Azure Security Center qui s’ouvre fournit une vue d’ensemble des menaces SQL actives détectées sur la base de données. 

  ![Volet de navigation][7]

2. En cliquant sur une alerte spécifique, vous obtenez des détails supplémentaires et des actions permettant d’examiner cette menace et d’atténuer les menaces futures.

  ![Volet de navigation][8]


## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur Threat Detection, visitez le [blog Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* En savoir plus sur [Audit Azure SQL Database](sql-database-auditing.md)
* En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Pour plus d’informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
* Pour obtenir un exemple de script PowerShell, consultez [Configurer l’audit et la détection des menaces avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


