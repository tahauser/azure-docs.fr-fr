---
title: Prise en main de Microsoft Power BI Embedded | Microsoft Docs
description: Power BI Embedded dans votre application décisionnelle
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: asaxton
ms.openlocfilehash: 3ea302d8cdd9a08d41f863917adeb4505552649d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Prise en main de Microsoft Power BI Embedded

**Power BI Embedded** permet aux développeurs et aux éditeurs de logiciels indépendants d’ajouter rapidement des éléments visuels, des rapports et des tableaux de bord dans leurs applications, via un modèle limité à l’heure ou dépendant de la capacité.

![Diagramme représentant le flux incorporé](media/get-started/introduction.png)

Power BI Embedded présente des avantages pour un éditeur de logiciels indépendant, les développeurs qu’il emploie et les clients. Pour exemple, un éditeur de logiciels indépendant peut commencer à créer des éléments visuels gratuitement, via Power BI Desktop. Les éditeurs de logiciels indépendants peuvent accélérer la commercialisation en réduisant les efforts de développement d’analyse visuelle et vous permettre de vous démarquer de la concurrence, grâce à des expériences de gestion des données différenciées. Les éditeurs peuvent opter pour le paiement de frais pour la valeur supplémentaire créée avec Embedded Analytics.

Les développeurs peuvent consacrer du temps à la création de la compétence clé de leur application plutôt que de passer du temps à développer des éléments visuels et des données d’analytique. Les développeurs peuvent rapidement répondre aux besoins des tableaux de bord et des rapports des clients, et incorporer aisément des Kits de développement logiciel (SDK) et API entièrement documentés. Pour finir, en simplifiant l’exploration des données dans leurs applications, les éditeurs de logiciels indépendants permettent à leurs clients de prendre en contexte des décisions éclairées, pilotées par les données et ce, en toute transparence et depuis n’importe quel terminal.

## <a name="register-an-application-within-azure-active-directory"></a>Inscrire une application auprès d’Azure Active Directory

Une application inscrite dans Azure Active Directory (AAD) est nécessaire pour l’incorporation dans une application personnalisée. Pour l’application inscrite, votre client doit être un locataire Power BI. Cela signifie qu’au moins un utilisateur de l’organisation s’est inscrit auprès de Power BI. Si un utilisateur est inscrit auprès de Power BI, les API Power BI peuvent s’afficher dans l’application inscrite.

Pour en savoir plus sur l’inscription d’une application dans AAD, voir [Inscrire une application Azure AD pour incorporer du contenu Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Incorporer le contenu dans votre application

Après avoir inscrit votre application dans AAD, incorporez le contenu Power BI dans votre application. Incorporez du contenu à l’aide de l’API REST et des API JavaScript.

Voici quelques exemples vous permettant de vous lancer. Pour une procédure pas à pas basée sur l’exemple, voir [Intégrer un tableau de bord, une vignette ou un rapport dans votre application (l’application possède les données)](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Obtenir de la capacité et passer à l’environnement de production

Créez la capacité Power BI Embedded dans Microsoft Azure pour déplacer votre application vers l’environnement de production. Pour savoir comment créer de la capacité, voir [Créer de la capacité Power BI Embedded dans le portail Azure](create-capacity.md).

> [!IMPORTANT]
> Étant donné que les jetons d’incorporation sont destinés uniquement à des tests de développement, le nombre de jetons d’incorporation qu’un compte principal Power BI peut générer est limité. Une [capacité doit être achetée](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical) pour des scénarios d’incorporation de production. Il n’existe aucune limite à la génération de jetons d’incorporation quand une capacité est achetée.

Gérez votre capacité dans le portail d’administration Power BI. Affectez le composant d’affectation de l’espace de travail pour faciliter la gestion de vos espaces de travail d’application. Pour en savoir plus, voir [Gérer les capacités dans Power BI Premium et Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Étapes suivantes

Pour créer une capacité Power BI Embedded, voir [Créer de la capacité Power BI Embedded dans le portail Azure](create-capacity.md).

Pour accéder à un exemple de procédure pas à pas, voir [Intégrer un tableau de bord, une vignette ou un rapport dans votre application (l’application possède les données)](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)
