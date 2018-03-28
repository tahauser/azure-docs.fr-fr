---
title: Threat Detection - Azure SQL Database Managed Instance | Microsoft Docs
description: Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données.
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 2112a0a3997af478de6b8c80abcf7924a66302f0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL Database Managed Instance Threat Detection

SQL Threat Detection permet de détecter les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à accéder ou à exploiter des bases de données dans Azure SQL Database Managed Instance (préversion).

## <a name="overview"></a>Vue d'ensemble

Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour Managed Instance. Threat Detection est désormais disponible en préversion pour Managed Instance.

SQL Threat Detection fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités de base de données anormales. Threat Detection vous permet de réagir facilement aux menaces potentielles visant Managed Instance sans devenir un expert en sécurité ou gérer des systèmes avancés de surveillance de la sécurité. Pour une expérience d’analyse complète, il est recommandé d’activer Azure Managed Instance Auditing, qui écrit les événements de la base de données dans un journal d’audit sur votre compte de stockage Azure. 

SQL Threat Detection intègre les alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/). Chaque Managed Instance protégée est facturée au même prix que le niveau Standard d’Azure Security Center, soit 15 USD/nœud/mois : chaque Managed Instance protégée est alors comptabilisée comme un seul nœud.  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Configurer Threat Detection pour votre Managed Instance dans le portail Azure
1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page de configuration de Managed Instance que vous voulez protéger. Dans la page **Paramètres**, sélectionnez **Threat Detection**. 
3. Page de configuration de Threat Detection 
   - **Activez** la détection des menaces.
   - Configurez la **liste des e-mails** qui doivent recevoir des alertes de sécurité en cas de détection d’activités anormales sur la base de données.
   - Sélectionnez le **compte de stockage Azure** dans lequel sont enregistrés les enregistrements d’audit des menaces anormales. 
4.  Cliquez sur **Enregistrer** pour enregistrer la stratégie de détection de menace, nouvelle ou mise à jour.

   ![threat detection](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur Managed Instance en cas de détection d’un événement suspect

1. Vous recevez une notification par e-mail quand des activités anormales sont détectées sur la base de données. 

   Le courrier électronique contient des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur Managed Instance.

   ![e-mail threat detection](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. Cliquez sur le lien **Afficher les alertes SQL récentes** contenu dans l’e-mail pour ouvrir le portail Azure et accéder à la page d’alertes d’Azure Security Center, qui fournit une vue d’ensemble des menaces SQL menaces actives détectées sur la base de données de Managed Instance.

   ![menaces actives](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. Cliquez sur une alerte spécifique pour obtenir des détails supplémentaires et des actions permettant d’examiner cette menace et d’atténuer les menaces futures.

   Par exemple, l’injection SQL représente l’un des problèmes de sécurité auxquels sont le plus exposées les applications web. L’injection SQL est utilisé pour cibler des applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données. Pour les alertes d’injection SQL, les détails de l’alerte incluent l’instruction SQL vulnérable qui a été exploitée.

   ![injection SQL](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>Alertes Managed Instance Threat Detection 

Threat Detection pour Managed Instance détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à utiliser des bases de données ou à exploiter leurs failles de sécurité, et peut déclenche les alertes suivantes :
- **Vulnérabilité aux injections SQL** : cette alerte est déclenchée lorsqu’une application génère une instruction SQL défectueuse dans la base de données. Cela peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Deux raisons possibles expliquent la génération d’une instruction défectueuse :
 - Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
 - Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
- **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée vers l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.
- **Access from unusual location** (Accès à partir d’un emplacement inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès à Managed Instance, quand un utilisateur s’est connecté à Managed Instance à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe, etc.).
- **Access from unusual Azure data center** (Accès à partir d’un centre de données Azure inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès à Managed Instance, quand un utilisateur s’est connecté à Managed Instance à partir d’un centre de données Azure qui n’a pas accédé récemment à Managed Instance. Dans certains cas, l’alerte détecte une action légitime (votre nouvelle application dans Azure, Power BI, l’éditeur de requête SQL Azure, etc.). Dans d’autres cas, l’alerte détecte une action malveillante provenant d’une ressource/d’un service Azure (ancien employé, attaquant externe).
- **Access from unfamiliar principal** (Accès à partir d’un principal inhabituel) : cette alerte est déclenchée en cas de modification du modèle d’accès à Managed Instance, quand un utilisateur s’est connecté à Managed Instance à l’aide d’un principal inhabituel (utilisateur SQL). Dans certains cas, l’alerte détecte une action légitime (opération de maintenance du développeur d’une nouvelle application). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
- **Access from a potentially harmful application** (Accès à partir d’une application potentiellement dangereuse) : cette alerte est déclenchée lorsqu’une application potentiellement dangereuse est utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque à l’aide d’outils d’attaque courants.
- **Brute force SQL credentials** (Informations d’identification par force brute) : cette alerte est déclenchée lorsqu’il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Managed Instance, consultez [What is a Managed Instance (preview)?](sql-database-managed-instance.md) (Présentation de l’option Managed Instance (préversion))
- En savoir plus sur [l’audit de Managed Instance](https://go.microsoft.com/fwlink/?linkid=869430) 
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
