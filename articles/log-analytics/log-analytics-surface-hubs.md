---
title: "Surveiller les Surface Hubs avec Azure Log Analytics | Microsoft Docs"
description: "La solution Surface Hub permet de suivre l’intégrité de vos Surface Hubs et de comprendre comment ils sont utilisés."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 525b3ced979834a956f91ef8c6f647b659ca21f1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Surveiller les Surface Hubs avec Log Analytics pour suivre leur intégrité

![Symbole de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-symbol.png)

Cet article décrit comment utiliser la solution Surface Hub dans Log Analytics pour surveiller des appareils Microsoft Surface Hub. Log Analytics vous aide à suivre l’intégrité de vos Surface Hubs ainsi qu’à comprendre comment ils sont utilisés.

Chaque Surface Hub a un Microsoft Monitoring Agent installé. C’est via cet agent que vous pouvez envoyer des données de votre Surface Hub à Log Analytics. Les fichiers journaux sont lus à partir de vos Surface Hubs, puis envoyés à Log Analytics. Des problèmes tels que des serveurs hors connexion, un calendrier qui ne se synchronise pas ou un compte d’appareil incapable de se connecter à Skype s’affichent sur le tableau de bord Surface Hub de Log Analytics. Les données du tableau de bord vous permettent d’identifier les appareils qui ne sont pas en cours d’exécution ou qui rencontrent d’autres problèmes, voire d’appliquer des correctifs pour les problèmes détectés.

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Utilisez les informations suivantes pour installer et configurer la solution. Pour gérer votre Surface Hub dans Log Analytics, vous avez besoin des éléments suivants :

* Un niveau d’[abonnement Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) prenant en charge le nombre d’appareils à analyser. La tarification de Log Analytics varie selon le nombre d’appareils inscrits et la quantité de données traitées. Vous devez prendre cela en compte lors de la planification de votre déploiement de Surface Hub.

Vous allez ensuite ajouter un espace de travail Log Analytics existant ou en créer un. Pour des instructions détaillées sur ces deux méthodes, voir [Prise en main de Log Analytics](log-analytics-get-started.md). Après avoir configuré l’espace de travail Log Analytics, vous pouvez inscrire vos appareils Surface Hub de deux façons différentes :

* automatiquement via Intune ;
* manuellement, via les **Paramètres** de votre appareil Surface Hub.

## <a name="set-up-monitoring"></a>Configurez l’analyse
Vous pouvez analyser l’intégrité et l’activité de votre Surface Hub à l’aide de Log Analytics. Vous pouvez inscrire le Surface Hub via Intune, ou localement via les **Paramètres** du Surface Hub.

## <a name="connect-surface-hubs-to-log-analytics-through-intune"></a>Connecter des Surface Hubs à Log Analytics via Intune
Vous devez disposer de l’ID et de la clé de l’espace de travail Log Analytics devant gérer vos Surface Hubs. Ces derniers sont disponibles dans les paramètres de l’espace de travail dans le portail Azure.

Intune est un produit Microsoft permettant de gérer de manière centralisée les paramètres de configuration Log Analytics appliqués à un ou plusieurs de vos appareils. Pour configurer vos appareils via Intune, procédez comme suit :

1. Connectez-vous à Intune.
2. Accédez à **Paramètres** > **Sources connectées**.
3. Créez ou modifiez une stratégie basée sur le modèle Surface Hub.
4. Accédez à la section OMS (Azure Operational Insights) de la stratégie, puis ajoutez l’*ID de l’espace de travail* et la *Clé de l’espace de travail* Log Analytics à la stratégie.
5. Enregistrez la stratégie.
6. Associez la stratégie au groupe approprié d’appareils.

   ![Stratégie Intune](./media/log-analytics-surface-hubs/intune.png)

Intune synchronise ensuite les paramètres Log Analytics avec les appareils du groupe cible en inscrivant ceux-ci dans votre espace de travail Log Analytics.

## <a name="connect-surface-hubs-to-log-analytics-using-the-settings-app"></a>Connecter des Surface Hubs à Log Analytics en utilisant l’application Paramètres
Vous devez disposer de l’ID et de la clé de l’espace de travail Log Analytics devant gérer vos Surface Hubs. Ces derniers sont disponibles dans les paramètres de l’espace de travail Log Analytics dans le portail Azure.

Si vous n’utilisez pas Intune pour gérer votre environnement, vous pouvez inscrire des appareils manuellement via les **Paramètres** de chaque Surface Hub :

1. Sur votre Surface Hub, ouvrez **Paramètres**.
2. Entrez les informations d’identification d’administrateur de l’appareil lorsque vous y êtes invité.
3. Cliquez sur **Cet appareil**, puis, sous **Analyse**, cliquez sur **Configurer les paramètres OMS**.
4. Sélectionnez **Activer l’analyse**.
5. Dans la boîte de dialogue Paramètres OMS, entrez l’**ID de l’espace de travail** et la **Clé de l’espace de travail**.  
   ![Paramètres](./media/log-analytics-surface-hubs/settings.png)
6. Cliquez sur **OK** pour achever la configuration.

Une confirmation s’affiche, vous indiquant si la configuration a été correctement appliquée à l’appareil. Si c’est le cas, un message s’affiche, indiquant que l’agent est bien connecté à Log Analytics. L’appareil commence alors à envoyer des données à Log Analytics, où vous pouvez les consulter et les utiliser.

## <a name="monitor-surface-hubs"></a>Analyser les Surface Hubs
L’analyse de vos Surface Hubs à l’aide de Log Analytics est très similaire à l’analyse d’autres appareils inscrits.

1. Connectez-vous au portail Azure.
2. Accédez à votre espace de travail Log Analytics et sélectionnez **Vue d’ensemble**.
2. Cliquez sur la vignette Surface Hub.
3. L’intégrité de votre appareil s’affiche.

   ![Tableau de bord de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Vous pouvez créer des [alertes](log-analytics-alerts.md) basées sur des recherches de journal existantes ou personnalisées. En utilisant les données que Log Analytics collecte à partir de vos Surface Hubs, vous pouvez rechercher des problèmes et générer des alertes sur les conditions que vous définissez pour vos appareils.

## <a name="next-steps"></a>Étapes suivantes
* Utiliser des [recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher des données détaillées de Surface Hub.
* Créer des [alertes](log-analytics-alerts.md) pour être averti en cas de problèmes avec vos Surface Hubs.
