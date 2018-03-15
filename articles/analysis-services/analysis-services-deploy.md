---
title: "Procéder à un déploiement sur Azure Analysis Services à l’aide de SSDT | Microsoft Docs"
description: "Découvrez comment déployer un modèle tabulaire sur un serveur Azure Analysis Services à l’aide de SSDT."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: f356b5d8e2b047add41873ab2676bd46db8d1fd2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="deploy-a-model-from-ssdt"></a>Déployer un modèle à partir de SSDT
Une fois que vous avez créé un serveur dans votre abonnement Azure, vous êtes prêt à déployer une base de données de modèle tabulaire sur celui-ci. Vous pouvez utiliser SQL Server Data Tools (SSDT) pour créer et déployer un projet de modèle tabulaire sur lequel vous travaillez. 

## <a name="prerequisites"></a>Prérequis
Pour commencer, vous avez besoin des éléments suivants :

* **Serveur Analysis Services** dans Azure. Pour plus d’informations, consultez l’article [Création d’un serveur Azure Analysis Services dans le portail Azure](analysis-services-create-server.md).
* **Projet de modèle tabulaire** dans SSDT ou modèle tabulaire existant au niveau de compatibilité 1200 ou supérieur. Vous ne l’avez jamais fait ? Essayez le [Didacticiel de modélisation tabulaire des ventes Internet Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx).
* **Passerelle locale** : si une ou plusieurs sources de données sont locales dans le réseau de votre entreprise, vous devez installer une [Passerelle de données locale](analysis-services-gateway.md). La passerelle est nécessaire pour que votre serveur se connecte dans le cloud à vos sources de données locales pour traiter et actualiser les données du modèle.

> [!TIP]
> Avant de déployer, assurez-vous que vous pouvez traiter les données de vos tables. Dans SSDT, cliquez sur **Modèle** > **Traiter** > **Traiter tout**. Si le traitement échoue, vous ne pourrez pas procéder au déploiement.
> 
> 

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Pour déployer un modèle tabulaire à partir de SSDT

1. Avant de déployer, vous devez obtenir le nom du serveur. Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur.
   
    ![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Dans SSDT > **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Propriétés**. Ensuite, dans **Déploiement** > **Serveur**, collez le nom du serveur.   
   
    ![Coller le nom du serveur dans la propriété du serveur de déploiement](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Propriétés**, puis cliquez sur **Déployer**. Vous serez peut-être invité à vous connecter à Azure.
   
    ![Déployer sur un serveur](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    L’état du déploiement s’affiche dans la fenêtre Sortie et dans Déployer.
   
    ![état du déploiement](./media/analysis-services-deploy/aas-deploy-status.png)

C’est tout !


## <a name="troubleshooting"></a>Résolution de problèmes
Si le déploiement échoue lors du déploiement de métadonnées, il est probable que SSDT n’ait pas pu se connecter à votre serveur. Vérifiez que vous pouvez vous connecter à votre serveur à l’aide de SSMS. Vérifiez ensuite que la propriété Serveur de déploiement du projet est correcte.

Si le déploiement échoue sur une table, il est probable que votre serveur n’ait pas pu se connecter à une source de données. Si votre source de données est locale dans le réseau de votre entreprise, veillez à installer une [Passerelle de données locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre modèle tabulaire est déployé sur votre serveur, vous êtes prêt à vous connecter à celui-ci. Vous pouvez vous [connecter à celui-ci avec SSMS](analysis-services-manage.md) pour le gérer. Vous pouvez également vous [connecter à celui-ci à l’aide d’un outil client](analysis-services-connect.md) tel que Power BI, Power BI Desktop ou Excel, et commencer à créer des rapports.

