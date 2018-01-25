---
title: "Déplacer des applications depuis BizTalk Services vers Azure Logic Apps | Microsoft Docs"
description: "Déplacer ou migrer Azure BizTalk Services (MABS) vers Logic Apps"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: 01c5376ac5ba9125eede9deb5ee0a7a006a91bb4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>Déplacer depuis BizTalk Services vers Logic Apps

Microsoft Azure BizTalk Services (MABS) fait l’objet d’une mise hors service. Utilisez cette rubrique pour déplacer vos solutions d’intégration MABS vers Azure Logic Apps. 

## <a name="overview"></a>Vue d’ensemble

BizTalk Services se compose de deux services secondaires :

1.  Le service Connexions hybrides Microsoft BizTalk Services
2.  L’intégration de pont IAE et EDI

Si vous cherchez à déplacer des connexions hybrides, l’article [Connexions hybrides d’Azure App Service](../app-service/app-service-hybrid-connections.md) décrit les modifications et les fonctionnalités de ce service. Le service Connexions hybrides Azure remplace le service Connexions hybrides BizTalk Services. Le service Connexions hybrides Azure est disponible avec Azure App Service et est proposé dans le portail Azure. En outre, le service Connexions hybrides Azure fournit un nouveau gestionnaire de connexions hybrides pour gérer les connexions hybrides BizTalk Services existantes et les connexions hybrides que vous créez dans le portail. Le service Connexions hybrides Azure App Service bénéficie d’une mise à disposition générale.

Pour l’intégration de pont IAE et EDI, Logic Apps prend le relais. Logic Apps présente les mêmes fonctionnalités que BizTalk Services et bien plus encore. Logic Apps offre un workflow de consommation à l’échelle du cloud et des fonctionnalités d’orchestration qui vous permettent de créer rapidement et facilement des solutions d’intégration complexes à l’aide d’un navigateur, ou à l’aide d’outils dans Visual Studio.

Le tableau suivant établit une correspondance entre les fonctionnalités de BizTalk Services et celles de Logic Apps.

| BizTalk Services   | Logic Apps            | Objectif                  |
| ------------------ | --------------------- | ---------------------------- |
| Connecteur          | Connecteur             | Envoi et réception de données   |
| Pont             | Application logique             | Processeur de pipeline           |
| Étape de validation     | Action de validation XML      | Valider un document XML par rapport à un schéma             |
| Étape d’enrichissement       | Jetons de données      | Promouvoir les propriétés dans des messages ou pour des décisions de routage             |
| Étape de transformation    | Action de transformation      | Convertir les messages XML d’un format vers un autre             |
| Étape de décodage       | Action de décodage de fichier plat      | Convertir à partir d’un fichier plat au format XML             |
| Étape d’encodage       |  Action d’encodage de fichier plat      | Convertir depuis XML vers un fichier plat             |
| Inspecteur de message       |  Azure Functions ou API Apps      | Exécuter du code personnalisé dans vos intégrations             |
| Action de routage      |  Condition ou commutateur      | Router les messages vers un des connecteurs spécifiés             |

Il existe différents types d’artefact dans BizTalk Services.

## <a name="connectors"></a>Connecteurs
Dans BizTalk Services, les connecteurs permettent aux ponts d’envoyer et de recevoir des données ; cela comprend les ponts bidirectionnels qui ont activé des interactions de demande/réponse HTTP. Dans Logic Apps, la même terminologie est utilisée. Les connecteurs dans les applications logiques ont la même fonction, dont également plus de 140 qui peuvent se connecter à un large éventail de technologies et de services, à la fois en local à l’aide de la passerelle de données locale (remplaçant le service d’adaptateur BizTalk utilisé par BizTalk Services) et dans le cloud, par le biais des services SaaS et PaaS tels que OneDrive, Office 365, Dynamics CRM.

Les sources dans BizTalk Services sont limitées aux abonnements FTP, SFTP et à une rubrique ou une file d’attente Service Bus.

![](media/logic-apps-move-from-mabs/sources.png)

Chaque pont a un point de terminaison HTTP par défaut, qui est configuré avec les propriétés d’adresse d’exécution et d’adresse relative du pont. Pour obtenir la même configuration avec Logic Apps, utilisez les actions de [demande et réponse](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Traitement XML et ponts
Un pont dans BizTalk Services est analogue à un pipeline de traitement. Un pont peut prendre les données reçues d’un connecteur, les traiter, puis les envoyer à un autre système. Logic Apps fait de même en prenant en charge les mêmes modèles d’interaction basés sur un pipeline que BizTalk Services et fournit également plusieurs autres modèles d’intégration. Le [pont demande-réponse XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) dans BizTalk Services est appelé pipeline VETER dont les étapes effectuent les opérations suivantes :

* (V) Valider
* (E) Enrichir
* (T) Transformer
* (E) Enrichir
* (R) Router

Comme indiqué dans l’image suivante, le traitement est partagé entre la demande et la réponse et permet de contrôler séparément les processus de demande et de réponse (par exemple, à l’aide de différents mappages pour chacun) :

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

En outre, un pont unidirectionnel XML ajoute des étapes de décodage et d’encodage au début et à la fin du traitement, et le pont direct contient une seule étape d’enrichissement.

### <a name="message-processing-and-decodingencoding"></a>Traitement et décodage/encodage des messages
Dans BizTalk Services, vous recevez des messages XML de types différents et déterminez le schéma correspondant pour le message reçu. Cette opération est effectuée à l’étape **Types de messages** du pipeline de traitement de la réception. Ensuite, la phase de décodage utilise le type de message détecté pour le décoder avec le schéma fourni. Si le schéma est un schéma de fichier plat, il convertit le fichier plat entrant au format XML. 

Logic Apps offre des fonctionnalités similaires. Vous recevez un fichier plat par le biais d’une multitude de différents protocoles en utilisant les différents déclencheurs de connecteur (système de fichiers, FTP, HTTP, etc.) et utilisez l’action de [décodage de fichier plat](../logic-apps/logic-apps-enterprise-integration-flatfile.md) pour convertir les données entrantes au format XML. Vous pouvez déplacer vos schémas de fichier plat existants directement vers les applications logiques sans apporter de modifications, puis charger les schémas sur votre compte d’intégration.

### <a name="validation"></a>Validation
Une fois les données entrantes converties au format XML (ou si XML correspondait au format du message reçu), la validation s’exécute pour déterminer si le message est conforme à votre schéma XSD. Pour ce faire, dans Logic Apps, utilisez l’action [Validation XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Là encore, vous pouvez utiliser les mêmes schémas de BizTalk Services sans aucune modification.

### <a name="transform-messages"></a>Transformer des messages
Dans BizTalk Services, la phase de transformation convertit un format de message basé sur XML en un autre. Cette opération consiste à appliquer un mappage à l’aide du mappeur basé sur une transformation. Dans Logic Apps, le processus est similaire. L’action de transformation exécute un mappage à partir de votre compte d’intégration. La différence principale est que les mappages dans Logic Apps sont au format XSLT. XSLT inclut la possibilité de réutiliser le XSLT existant, y compris les mappages créés pour BizTalk Server qui contiennent des fonctoids. 

### <a name="routing-rules"></a>Règles de routage
BizTalk Services prend une décision de routage quant au point de terminaison/connecteur pour l’envoi des données/messages entrants. Vous pouvez sélectionner un des points de terminaison préconfigurés grâce à l’option de filtre de routage :

![](media/logic-apps-move-from-mabs/route-filter.png)

Logic Apps offre des fonctionnalités de logique plus sophistiquées avec les options de [condition](../logic-apps/logic-apps-use-logic-app-features.md) et de [commutateur](../logic-apps/logic-apps-switch-case.md), permettant un flux de contrôle et un routage avancés. La conversion des filtres de routage dans BizTalk Services est mieux obtenue en utilisant une **condition** *si* uniquement deux options sont disponibles. S’il en existe plus de deux, utilisez un **commutateur**.

### <a name="enrich"></a>Enrichissement
L’étape d’enrichissement dans le traitement BizTalk Services ajoute des propriétés au contexte de message associé aux données reçues. Par exemple, la promotion d’une propriété à utiliser pour le routage (voir ci-dessous) à partir d’une recherche de base de données ou en extrayant une valeur à l’aide d’une expression XPath. Logic Apps permet d’accéder à toutes les sorties de données contextuelles des actions précédentes, simplifiant ainsi la réplication d’un même comportement. Par exemple, à l’aide de l’action de connexion SQL `Get Row`, vous retournez les données à partir d’une base de données SQL Server et utilisez les données dans une action de décision pour le routage. De même, les propriétés sur les messages Service Bus entrants mis en file d’attente par un déclencheur sont adressables, ainsi que XPath à l’aide de l’expression de langage de définition de workflow xpath.

### <a name="use-custom-code"></a>Utiliser un code personnalisé
BizTalk Services permet [d’exécuter un code personnalisé](https://msdn.microsoft.com/library/azure/dn232389.aspx) chargé dans vos propres assemblys. Cette possibilité est implémentée par l’interface [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx). Chaque étape du pont inclut deux propriétés (On Enter Inspector et On Exit Inspector) qui fournissent le type .Net créé par vos soins qui implémente cette interface. Le code personnalisé vous permet d’effectuer un traitement plus complexe sur les données, ainsi que de réutiliser le code existant dans les assemblys qui effectuent une logique métier courante. 

Logic Apps offre deux méthodes principales pour exécuter du code personnalisé : Azure Functions et API Apps. Les fonctions Azure Functions peuvent être créées et appelées à partir d’applications logiques. Consultez [Ajout et exécution d’un code personnalisé pour des applications logiques avec Azure Functions](../logic-apps/logic-apps-azure-functions.md). Utilisez API Apps, qui fait partie d’Azure App Service, pour créer vos propres déclencheurs et actions. Découvrez-en plus sur la [création d’une API personnalisée à utiliser avec Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Si vous avez un code personnalisé dans des assemblys que vous appelez depuis BizTalk Services, vous pouvez déplacer ce code vers Azure Functions, ou créer des API personnalisées avec API Apps, suivant ce que vous implémentez. Par exemple, si vous avez du code qui encapsule un autre service pour lequel Logic Apps n’a pas de connecteur, créez une application API et utilisez les actions que votre application API fournit au sein de votre application logique. Si vous avez des bibliothèques ou des fonctions d’assistance, Azure Functions est probablement la meilleure solution.

### <a name="edi-processing-and-trading-partner-management"></a>Traitement EDI et gestion des partenaires commerciaux
BizTalk Services inclut le traitement EDI et B2B avec prise en charge d’AS2 (Applicability Statement 2), X12 et EDIFACT. Logic Apps également. Dans BizTalk Services, vous créez des ponts EDI et créez/gérez des partenaires commerciaux et des contrats dans le portail de suivi et de gestion dédié.

Dans Logic Apps, cette fonctionnalité est incluse dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Cela inclut le compte d’intégration et les actions B2B pour le traitement EDI et B2B. Le [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) est utilisé pour créer et gérer des [partenaires commerciaux](../logic-apps/logic-apps-enterprise-integration-partners.md) et des [contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md). Une fois que vous avez créé un compte d’intégration, vous pouvez y associer une ou plusieurs applications logiques. Ensuite, vous pouvez utiliser les actions B2B pour accéder aux informations des partenaires commerciaux au sein de votre application logique. Les actions suivantes sont fournies :

* Encodage AS2
* Décodage AS2
* Encodage X12
* Décodage X12
* Encodage EDIFACT
* Décodage EDIFACT

Contrairement à BizTalk Services, ces actions sont dissociées des protocoles de transport. Ainsi, quand vous créez vos applications logiques, vous avez plus de flexibilité quant aux connecteurs à utiliser pour envoyer et recevoir des données. Par exemple, vous pouvez recevoir les fichiers X12 en tant que pièces jointes de courrier électronique, puis traiter ces fichiers dans une application logique. 

## <a name="manage-and-monitor"></a>Gestion et surveillance
Outre la gestion des partenaires commerciaux, le portail dédié pour les BizTalk Services fournissait des fonctions de suivi pour surveiller et résoudre les problèmes. 

Logic Apps offre des fonctionnalités de suivi et de surveillance plus riches dans le [portail Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) et avec la [solution Operations Management Suite B2B](../logic-apps/logic-apps-monitor-b2b-message.md), y compris une application mobile pour vous tenir informé quand vous êtes en déplacement.

## <a name="high-availability"></a>Haute disponibilité
Pour obtenir une haute disponibilité dans BizTalk Services, vous utilisez plusieurs instances dans une région donnée pour partager la charge de traitement. Avec les applications logiques, la haute disponibilité dans une région est intégrée et est fournie sans coût supplémentaire. Pour une récupération d’urgence hors région d’un traitement B2B dans BizTalk Services, un processus de sauvegarde et de restauration est requis. Dans Logic Apps, vous disposez d’une [capacité de récupération d’urgence](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) active/passive entre régions, grâce à laquelle vous pouvez synchroniser les données B2B entre les comptes d’intégration dans différentes régions afin de garantir la continuité d’activité.

## <a name="next"></a>Suivant
* [Qu’est-ce qu’une application logique ?](logic-apps-overview.md)
* [Créez votre première application logique](quickstart-create-first-logic-app-workflow.md), ou devenez rapidement opérationnel à l’aide d’un [modèle prédéfini](logic-apps-create-logic-apps-from-templates.md)  
* [Affichez tous les connecteurs disponibles](../connectors/apis-list.md) que vous pouvez utiliser dans une application logique
