---
title: Connecteurs pour Azure Logic Apps | Microsoft Docs
description: Choisir parmi les connecteurs Microsoft pour générer et créer des applications logiques
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: estfan; ladocs
ms.openlocfilehash: f140f1a3d751b5d83b139edde931a6193334b6ee
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="connectors-list"></a>Liste des connecteurs
Pour rechercher les déclencheurs et les actions définis par la description du Swagger de chaque connecteur ainsi que les limites des connecteurs, consultez les [Détails du connecteur](/connectors/).

Les connecteurs font partie intégrante de la création d’applications logiques. À l’aide de ces connecteurs, vous pouvez développer vos applications locales et cloud pour effectuer différentes opérations avec les données créées et existantes. Les connecteurs sont disponibles en tant qu’actions intégrées ou que connecteurs gérés.

**Actions intégrées** : le moteur Logic Apps fournit lui-même des actions intégrées pour la communication aux points de terminaison et la réalisation de tâches. Par exemple, vous pouvez utiliser ces actions pour appeler des points de terminaison HTTP, des opérations de Azure Functions et de la Gestion des API Azure ainsi que manipulation des messages avec des variables et des opérations de données.

**Connecteurs gérés** : ils fournissent l’accès aux API pour différents services en créant des connexions d’API hébergées et gérées par le service Logic Apps. Les connecteurs gérés peuvent être classés dans les catégories suivantes :

* **Connecteurs standard** : disponibles et inclus automatiquement lorsque vous utilisez des applications logiques. Il s’agit par exemple de Service Bus, Power BI, OneDrive, et bien d’autres encore.

* **Connecteurs locaux** : ils se connectent aux applications de serveur locales à l’aide de la [passerelle de données locale][gatewaydoc]. Les connecteurs locaux incluent la connectivité aux applications de serveur telles que SharePoint Server, SQL Server, Oracle DB, les partages de fichiers et d’autres.

* **Connecteurs de compte d’intégration** : disponibles lorsque vous achetez un compte d’intégration. À l’aide de ces connecteurs, vous pouvez transformer et valider du code XML, traiter les messages entreprise-entreprise avec AS2/X12/EDIFACT, ainsi que coder et décoder des fichiers plats. Si vous utilisez BizTalk Server, ces connecteurs sont adaptés au développement de vos flux de travail BizTalk dans Azure.  

    BizTalk Server comprend également un [adaptateur Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx) qui inclut la réception et l’envoi depuis et vers une application logique.

* **Connecteurs d’entreprise** : inclut MQ et SAP. Disponibles moyennant un coût supplémentaire. 

Pour plus d’informations sur les coûts, consultez les [Détails de la tarification](https://azure.microsoft.com/pricing/details/logic-apps/) et le [Modèle de tarification](../logic-apps/logic-apps-pricing.md) pour Logic Apps. 

## <a name="popular-connectors"></a>Connecteurs les plus courants
Des milliers d’applications et des millions d’exécutions utilisent ces connecteurs pour traiter correctement les données et les informations. 

### <a name="built-in-actions"></a>Actions intégrées
Le moteur de Logic Apps fournit les actions qui peuvent manipuler des données, communiquer en HTTP et contrôler le flux de la définition Logic Apps. Certaines de ces actions sont les suivantes :

| |  |  |  |
| --- | --- | --- | --- |
| [![Icône d’API][HTTPicon]<br/>**HTTP**][httpdoc] | Utilisez des applications logiques pour communiquer avec n’importe quel point de terminaison via HTTP.| [![Icône d’API][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Créez des fonctions qui exécutent des extraits de code personnalisés de C# ou node.js, puis utilisez ces fonctions dans vos applications logiques.  |
| [![Icône d’API][HTTP-Requesticon]<br/>**Requête**][HTTP-Requestdoc] | Fournit une URL HTTPS pouvant être appelée, généralement utilisée comme un Webhook dans d’autres applications. Lorsque l’application logique reçoit une demande sur cette URL, l’application logique démarre. | [![Icône d’API][Recurrenceicon]<br/>**Planification**][recurrencedoc] | Démarrez les applications logiques basées sur des planifications de périodicité simples ou complexes. Par exemple, créez des planifications aussi simples qu’une répétition quotidienne ou aussi élaborées qu’un rappel toutes les heures le dernier vendredi de chaque mois entre 9 h 00 et 17 h 00. |
| [![Icône d’API][CallLogicApp-icon]<br/>**Appeler<br/>Logic App**][nested-logic-appdoc] | Appelez une application logique imbriquée. N’importe quelle application logique avec un déclencheur de requête peut être appelée application logique imbriquée.| [![Icône d’API][API/Web-Appicon]<br/>**Application API**][api/web-appdoc] | Appelez une application API App Service. Applications API avec rendus Swagger tout comme d’autres actions de première classe.|

### <a name="standard-connectors"></a>Connecteurs standard
Le tableau suivant répertorie les plus courants, ainsi que les connecteurs privilégiés par nos utilisateurs :

| |  |  |  |
| --- | --- | --- | --- |
| [![Icône d’API][AzureBlobStorageicon]<br/>**Stockage Blob<br/>Azure**][AzureBlobStoragedoc] | Si vous souhaitez automatiser des tâches avec votre compte de stockage, vous pouvez envisager d’utiliser ce connecteur. Prend en charge les opérations CRUD (créer, lire, mettre à jour, supprimer). | [![Icône d’API][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | L’un des connecteurs les plus demandés. Il fournit des déclencheurs et des actions qui automatisent les flux de travail avec les prospects et bien plus encore. |
| [![Icône d’API][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Consommez et publiez des événements sur un Event Hub. Par exemple, vous pouvez obtenir une sortie à partir de votre application logique à l’aide des Event Hubs, puis l’envoyer à un fournisseur d’analyses en temps réel. | [![Icône d’API][FTPicon]<br/>**FTP**][FTPdoc] | Si votre serveur FTP est accessible à partir d’Internet, vous pouvez automatiser les flux de travail pour travailler avec des fichiers et des dossiers. <br/><br/>SFTP est également disponible avec le connecteur SFTP. |
| [![Icône d’API][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Grand nombre de déclencheurs et encore plus d’actions permettant d’utiliser la messagerie et les événements Office 365 dans vos flux de travail. <br/><br/>Ce connecteur comprend une action *e-mail d’approbation* pour approuver les demandes de congés, les notes de frais et ainsi de suite. <br/><br/>Les utilisateurs d’Office 365 sont également disponibles avec le connecteur Office 365 Users.| [![Icône d’API][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Connectez-vous facilement à votre compte Salesforce pour accéder aux objets, tels que les prospects et bien plus encore. | 
| [![Icône d’API][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | Connecteur le plus populaire au sein des applications logiques. Il inclut des déclencheurs et des actions pour l’utilisation de la messagerie asynchrone et la publication/l’abonnement avec les files d’attente, les abonnements et les rubriques. |  [![Icône d’API][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Si vous devez utiliser SharePoint et que vous pouvez tirer parti d’automatisation, nous vous recommandons d’envisager ce connecteur. Peut être utilisé avec une installation SharePoint locale et SharePoint Online. |
| [![Icône d’API][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | L’un des connecteurs les plus utilisés. Il peut se connecter à une installation SQL Server locale et à Azure SQL Database. | [![Icône d’API][Twittericon]<br/>**Twitter**][Twitterdoc] | Connectez-vous facilement avec un compte Twitter, puis démarrez un flux de travail dès qu’un nouveau tweet est publié. Ensuite, enregistrez ces tweets dans une base de données SQL ou une liste SharePoint. | 

### <a name="on-premises-connectors"></a>Connecteurs locaux 

Les connecteurs locaux fournissent un accès aux données dans des serveurs locaux.  La création d’une connexion à un serveur local nécessite une [passerelle de données locale][gatewaydoc] fournissant un canal de communication sécurisé sans avoir besoin de configurer une infrastructure réseau.  Certains des connecteurs sont les suivants :

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icône d’API][db2icon]<br/>**DB2**][db2doc] | [![Icône d’API][oracle-DB-icon]<br/>**Oracle DB**][oracle-db-doc] | [![Icône d’API][sharepointicon]<br/>**Serveur</br> SharePoint**][sharepointserver] | [![Icône d’API][filesystem-icon]<br/>**Système de </br> fichiers**][filesystemdoc] |
[![Icône d’API][sql-servericon]<br/>**Serveur</br> SQL**][sql-serverdoc] | ![Icône API][Biztalk-Servericon]<br/>**Serveur</br> BizTalk**| |

### <a name="integration-account-connectors"></a>Connecteurs de compte d’intégration 

Enterprise Integration Pack (EIP) inclut des connecteurs bien connus de la communauté BizTalk Server. Lorsque vous achetez un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), vous obtenez également les connecteurs suivants : 

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icône d’API][as2icon]<br/>**Décodage</br>AS2**][as2decode] | [![Icône d’API][as2icon]<br/>**Codage</br>AS2**][as2encode] | [![Icône d’API][x12icon]<br/>**Décodage</br>EDIFACT**][EDIFACTdecode] | [![Icône d’API][x12icon]<br/>**Codage</br>EDIFACT**][EDIFACTencode] |
[![Icône d’API][flatfileicon]<br/>**Codage</br>du fichier plat**][flatfiledoc] | [![Icône d’API][flatfiledecodeicon]<br/>**Décodage</br>du fichier plat**][flatfiledecodedoc] | [![Icône d’API][integrationaccounticon]<br/>**Compte<br/>d’intégration**][integrationaccountdoc] | [![Icône d’API][xmltransformicon]<br/>**Transformation<br/>du code XML**][xmltransformdoc] |
| [![Icône d’API][x12icon]<br/>**Décodage</br> X12**][x12decode] | [![Icône d’API][x12icon]<br/>**Codage</br> X12**][x12encode] | [![Icône d’API][xmlvalidateicon]<br/>**Validation <br/>XML**][xmlvalidatedoc] | [![Icône d’API][liquidicon]<br/>**Transformer <br/>JSON**][JSONliquidtransformdoc] |

### <a name="enterprise-connectors"></a>Connecteurs d’entreprise

Connectez-vous à vos applications d’entreprise au sein de vos applications logiques.

|  |  |
| --- | --- |
|[![Icône d’API][MQicon]<br/>**MQ**][mqdoc]|[![Icône d’API][SAPicon]<br/>**SAP**][sapconnector]|

> [!TIP]
> Pour commencer à utiliser Azure Logic Apps avant d’ouvrir un compte Azure, accédez à [Try Logic Apps (Essayer Logic Apps)](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application logique temporaire. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## <a name="connectors-as-triggers-and-actions"></a>Connecteurs en tant que déclencheurs et actions

Un **déclencheur** démarre ou exécute une instance de votre application logique. Certains connecteurs fournissent des déclencheurs qui préviennent votre application lorsque des événements spécifiques se produisent. Par exemple, le connecteur FTP comporte le déclencheur `OnUpdatedFile` qui démarre votre application logique lorsqu’un fichier est mis à jour. 

Les applications logiques incluent les types de déclencheurs suivants :  

* *Déclencheurs d’interrogation* : ces déclencheurs interrogent votre service selon une fréquence spécifiée pour vérifier l’existence de nouvelles données. 

    Quand de nouvelles données sont disponibles, une nouvelle instance de votre application logique s’exécute avec les données comme entrée. 

* *Déclencheurs d’émission* : ces déclencheurs écoutent les données sur un point de terminaison ou attendent qu’un événement se produise, puis déclenchent une nouvelle instance de votre application logique.

* *Déclencheur récurrence* : ce déclencheur instancie une instance de votre application logique sur une planification prescrite.

Les connecteurs fournissent également des **actions** que vous pouvez utiliser dans votre flux de travail. Par exemple, votre application logique peut rechercher des données et les utiliser par la suite. Plus précisément, vous pouvez rechercher des données client dans une base de données SQL et les utiliser ensuite pour créer votre flux de travail. 

> [!TIP]
> [Vue d’ensemble des connecteurs](connectors-overview.md) fournit des informations détaillées sur les déclencheurs et actions. 


## <a name="message-manipulation-actions"></a>Actions de manipulation des messages

Les applications logiques incluent les actions intégrées qui peuvent changer ou manipuler vos données de charge utile. Le connecteur **Opérations sur les données** inclut les actions suivantes : 

| | |
|---|---|
| **Composer** | Créez ou générez des valeurs ou des objets à utiliser ultérieurement ou lors de la création de votre flux de travail. Par exemple, vous pouvez créer un objet JSON avec les valeurs de plusieurs étapes, ou calculer une constante à référencer ultérieurement dans une exécution d’application logique. |
| **Créer un tableau CSV**<br/>**Créer un tableau HTML** | Transformez un tableau de jeu de résultats en tableau CSV ou HTML. Par exemple, ajoutez l’action CRM « Répertorier les enregistrements », puis ajoutez un filtre pour les enregistrements ajoutés aujourd’hui. Puis, envoyez les résultats sous forme de tableau HTML dans un e-mail. |
| **Filtrer un tableau** (requête) | Filtrez un jeu de résultats sur les entrées qui vous intéressent. Par exemple, recherchez tous les tweets avec `#Azure`, puis « filtrez » les tweets renvoyés pour ne garder que les résultats `Tweeted_by_followers > 50`. |
| **Join** | Joignez un tableau en utilisant un délimiteur. Par exemple, l’opération Détecter les phrases clés renvoie un tableau de phrases clés. Vous pouvez les « joindre » avec une `,` ou quelque chose de similaire. Dans ce cas au lieu de `["Some", "Phrase"]`, vous avez `"Some, Phrase"`. |
| **Analyser JSON** | Analysez et accédez aux valeurs d’un objet JSON dans le concepteur. Par exemple, si votre fonction Azure renvoie une charge utile JSON, vous pouvez l’analyser pour accéder aux propriétés JSON ultérieurement au cours d’une autre étape. L’action vérifie également que le JSON correspond au schéma spécifié lors de l’exécution. | 
| **Sélection** | Sélectionnez certaines propriétés d’un tableau pour un traitement ultérieur. Si vous « répertoriez les enregistrements » à partir de SQL et que vous obtenez 15 colonnes, n’en sélectionnez que quelques-unes en vue d’un traitement supplémentaire. La sortie est un tableau ne contenant que les propriétés que vous avez sélectionnées. |

## <a name="custom-connectors-and-azure-certification"></a>Connecteurs personnalisés et certification Azure 

Pour appeler des API qui exécutent du code personnalisé ou qui ne sont pas disponibles en tant que connecteurs, vous pouvez étendre la plateforme Logic Apps [en créant des applications API REST](../logic-apps/logic-apps-create-api-app.md). Vous pouvez également créer vos propres [connecteurs personnalisés](../logic-apps/custom-connector-overview.md) qui peuvent être accessibles pour n’importe quelle application logique dans votre abonnement.

Si vous souhaitez que vos applications API personnalisées deviennent publiques et utilisables dans Azure, vous pouvez [soumettre vos connecteurs à la certification Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions, répondre aux questions et voir ce que font les autres utilisateurs d’Azure Logic Apps, accédez au [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps et les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires des utilisateurs de Logic Apps](http://aka.ms/logicapps-wish).

Manque-t-il une rubrique de connecteur ou des informations que vous jugez importantes ? Si tel est le cas, aidez-nous en complétant nos rubriques existantes ou écrivez votre propre rubrique. Notre documentation est open source et hébergée sur GitHub. Commencez sans tarder dans notre [référentiel GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Étapes suivantes
* [Créez votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Créer des API personnalisées pour les applications logiques](../logic-apps/logic-apps-create-api-app.md)
* [Analyser vos applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Connexion à des sources de données locales à partir d’applications logiques avec la passerelle de données locale"
[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Permet d’intégrer des applications logiques à App Service API Apps"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Gérer les fichiers de votre conteneur d’objets blob avec le connecteur Azure Blob Storage"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Permet d’intégrer des applications logiques à Azure Functions"
[db2doc]: ./connectors-create-api-db2.md "Se connecter à IBM DB2 dans le nuage ou sur site. Mettre à jour une ligne, obtenir une table et bien plus encore"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Se connecter à Dynamics CRM Online pour exploiter les données CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connectez-vous à Azure Event Hubs. Recevez et envoyez des événements entre les applications logiques et Azure Event Hubs"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Se connecter à un système de fichiers local"
[ftpdoc]: ./connectors-create-api-ftp.md "Se connecter à un serveur FTP/FTPS pour les tâches FTP (notamment télécharger, obtenir et supprimer des fichiers)."
[httpdoc]: ./connectors-native-http.md "Passer des appels HTTP avec le connecteur HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Ajouter des actions pour les demandes et les réponses HTTP à vos applications logiques"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Passer des appels HTTP avec le connecteur HTTP + Swagger"
[informixdoc]: ./connectors-create-api-informix.md "Se connecter à Informix dans le nuage ou sur site. Lire une ligne, répertorier les tables et bien plus encore"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Intégrer des applications logiques à des flux de travail imbriqués"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Se connecter à votre compte Office 365. Envoyer et recevoir du courrier électronique, gérer votre calendrier et vos contacts et bien plus encore"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connectez-vous à une base de données Oracle pour ajouter, insérer, supprimer des lignes et bien plus encore"
[mqdoc]: ./connectors-create-api-mq.md "Se connecter à MQ au niveau local ou sur Azure, et envoyer et recevoir des messages"
[recurrencedoc]:  ./connectors-native-recurrence.md "Déclencher des actions récurrentes pour les applications logiques"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Se connecter à votre compte Salesforce. Gérer les comptes, les prospects, les opportunités et bien plus encore"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Se connecter à un système SAP local"
[service-busdoc]: ./connectors-create-api-servicebus.md "Envoyer des messages à partir de files d’attente et de rubriques Service Bus, et recevoir des messages de files d’attente et d’abonnements ServiceBus"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Se connecter à SharePoint Online. Gérer des documents, des éléments de liste et bien plus encore"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Se connecter au serveur local SharePoint. Gérer des documents, des éléments de liste et bien plus encore"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Se connecter à Azure SQL Database ou SQL Server. Créer, mettre à jour, obtenir et supprimer des entrées dans une table de base de données SQL."
[twitterdoc]: ./connectors-create-api-twitter.md "Se connecter à Twitter. Consulter les fils d’actualité, publier des tweets et bien plus encore"
[webhookdoc]: ./connectors-native-webhook.md "Ajouter des actions Webhook et déclencher vos applications logiques"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Découvrez l’intégration d’entreprise AS2."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Découvrez l’intégration d’entreprise X12"
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrez le fichier plat d’intégration d’entreprise."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Découvrez le fichier plat d’intégration d’entreprise."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Découvrez la validation XML d’intégration d’entreprise."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Découvrez les transformations d’intégration d’entreprise."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Découvrez le décodage AS2 pour intégration d’entreprise"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Découvrez le codage AS2 pour intégration d’entreprise"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Découvrez le décodage X12 pour intégration d’entreprise"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Découvrez le codage X12 pour intégration d’entreprise"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Découvrez le décodage EDIFACT pour intégration d’entreprise"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Découvrez le codage EDIFACT pour intégration d’entreprise"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Recherchez des schémas, des cartes, des partenaires et autres dans votre compte d’intégration"
[JSONliquidtransformdoc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "En savoir plus sur les transformations de JSON avec Liquid"


[boxDoc]: ./connectors-create-api-box.md "Se connecter à Box. Télécharger, obtenir, supprimer, répertorier vos fichiers et bien plus encore"
[delaydoc]: ./connectors-native-delay.md "Effectuer des actions différées"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Se connecter à Dropbox. Télécharger, obtenir, supprimer, répertorier vos fichiers et bien plus encore"
[facebookdoc]: ./connectors-create-api-facebook.md "Se connecter à Facebook. Publier sur une timeline, obtenir un flux de page et bien plus encore."
[githubdoc]: ./connectors-create-api-github.md "Se connecter à GitHub et suivre les problèmes"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Se connecter à GoogleDrive pour utiliser vos données"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Se connecter à Google Sheets pour modifier vos feuilles"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Se connecter à Google Tasks pour gérer vos tâches"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Se connecte à Google Agenda et permet de gérer un agenda."
[http-responsedoc]: ./connectors-native-reqres.md "Ajouter des actions pour les demandes et les réponses HTTP à vos applications logiques"
[instagramdoc]: ./connectors-create-api-instagram.md "Se connecter à Instagram. Déclencher ou agir sur les événements"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Se connecter à votre compte MailChimp. Gérer et automatiser les courriers électroniques"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Se connecter à Mandrill pour la communication"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Se connecter à Microsoft Translator. Traduire du texte, détecter les langues et bien plus encore" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Obtenir des informations vidéo, des listes et des canaux vidéo, et des URL de lecture pour les vidéos Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Se connecter à votre compte Microsoft OneDrive personnel. Télécharger, supprimer, répertorier des fichiers et bien plus encore"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Se connecter à votre compte Microsoft OneDrive professionnel. Télécharger, supprimer, répertorier vos fichiers et bien plus encore"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Se connecter à votre boîte aux lettres Outlook. Gérer votre courrier électronique, vos calendriers, vos contacts et bien plus encore"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Se connecter à Microsoft Project Online. Gérer vos projets, vos tâches, vos ressources et bien plus encore"
[querydoc]: ./connectors-native-query.md "Sélectionner et filtrer des tableaux avec l’action de requête"
[rssdoc]: ./connectors-create-api-rss.md "Publier et récupérer des éléments de flux, déclencher des opérations lorsqu’un nouvel élément est publié sur un flux RSS."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Se connecter à SendGrid. Envoyer un courrier électronique et gérer les listes des destinataires"
[sftpdoc]: ./connectors-create-api-sftp.md "Se connecter à votre compte SFTP. Télécharger, obtenir, supprimer des fichiers et bien plus encore"
[slackdoc]: ./connectors-create-api-slack.md "Se connecter à Slack et publier des messages sur les canaux Slack"
[smtpdoc]: ./connectors-create-api-smtp.md "Se connecter à un serveur SMTP et envoyer du courrier électronique avec des pièces jointes"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Se connecter à SparkPost pour la communication"
[trellodoc]: ./connectors-create-api-trello.md "Se connecter à Trello. Gérer vos projets et organiser ce que vous voulez avec qui vous voulez"
[twiliodoc]: ./connectors-create-api-twilio.md "Se connecter à Twilio. Envoyer et obtenir des messages, obtenir des numéros disponibles, gérer des numéros de téléphone entrants et bien plus encore"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Se connecter à Wunderlist. Gérer vos tâches et vos listes de tâches, tenir votre vie à jour et bien plus encore"
[yammerdoc]: ./connectors-create-api-yammer.md "Se connecter à Yammer. Publier des messages, obtenir de nouveaux messages et bien plus encore"
[youtubedoc]: ./connectors-create-api-youtube.md "Se connecter à YouTube. Gérer vos vidéos et vos canaux"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
[liquidicon]: ./media/apis-list/liquidtransform.png
