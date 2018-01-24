---
title: "Personnaliser la solution Usine connectée - Azure | Microsoft Docs"
description: "Découvrez comment personnaliser le comportement de la solution préconfigurée Usine connectée."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 48c8036d0bc9534ce94529b96d32b004769246c1
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personnaliser la façon dont la solution Usine connectée présente les données à partir de vos serveurs OPC UA

La solution Usine connectée agrège et affiche les données des serveurs OPC UA qui y sont connectés. Vous pouvez parcourir les serveurs OPC UA et leur envoyer des commandes dans votre solution. Pour plus d’informations sur OPC UA, consultez les [questions fréquentes (FAQ) sur l’usine connectée](iot-suite-faq-cf.md).

Des exemples de données agrégées dans la solution incluent l’efficacité globale des équipements (OEE) et les indicateurs de performance clés (KPI), que vous pouvez afficher dans le tableau de bord au niveau d’une usine, d’une ligne de production et d’un poste. La capture d’écran suivante illustre les valeurs d’OEE et de KPI pour le poste d’assemblage **Assembly** de la ligne de production **Production line 1** dans l’usine de **Munich** :

![Exemple de valeurs d’OEE et de KPI dans la solution][img-oee-kpi]

La solution permet d’afficher des informations détaillées pour des éléments de données spécifiques des serveurs OPC UA appelés *postes*. La capture d’écran suivante illustre des graphiques du nombre d’articles fabriqués à partir d’un poste spécifique :

![Graphiques du nombre d’éléments fabriqués][img-manufactured-items]

Si vous cliquez sur l’un des graphiques, vous pouvez explorer les données plus en détail à l’aide de Time Series Insights (TSI) :

![Explorer les données à l’aide de Time Series Insights][img-tsi]

Cet article aborde les points suivants :

- Comment rendre disponibles les données dans les différentes vues de la solution.
- Comment personnaliser le mode d’affichage des données par la solution.

## <a name="data-sources"></a>Sources de données

La solution Usine connectée affiche les données des serveurs OPC UA qui y sont connectés. L’installation par défaut inclut plusieurs serveurs OPC UA exécutant une simulation d’usine. Vous pouvez ajouter vos propres serveurs OPC UA qui [se connectent via une passerelle][lnk-connect-cf] à votre solution.

Vous pouvez parcourir les éléments de données qu’un serveur OPC UA peut envoyer à votre solution dans le tableau de bord :

1. Choisissez **Navigateur** pour accéder à la vue **Sélectionner un serveur OPC UA** :

    ![Accéder à la vue Select an OPC UA server (Sélectionner un serveur OPC UA)][img-select-server]

1. Sélectionnez un serveur et cliquez sur **Connect** (Connexion). Lorsque l’avertissement de sécurité s’affiche, cliquez sur **Proceed** (Continuer).

    > [!NOTE]
    > Cet avertissement s’affiche une seule fois pour chaque serveur et établit une relation d’approbation entre le tableau de bord de la solution et le serveur.

1. Vous pouvez maintenant parcourir les éléments de données que le serveur peut envoyer à la solution. Les éléments qui sont envoyés à la solution présentent une coche :

    ![Éléments publiés][img-published]

1. Si vous êtes un *administrateur* dans la solution, vous pouvez choisir de publier un élément de données pour le rendre disponible dans la solution Usine connectée. En tant qu’administrateur, vous pouvez également modifier la valeur des éléments de données et appeler des méthodes sur le serveur OPC UA.

## <a name="map-the-data"></a>Mapper les données

La solution Usine connectée mappe et agrège les éléments de données publiés à partir du serveur OPC UA dans les différentes vues de la solution. La solution Usine connectée se déploie sur votre compte Azure quand vous la provisionnez. Un fichier JSON de la solution Visual Studio Usine connectée stocke ces informations de mappage. Vous pouvez afficher et modifier ce fichier de configuration JSON dans la solution Visual Studio Usine connectée. Vous pouvez redéployer la solution une fois que vous apportez une modification.

Vous pouvez utiliser le fichier de configuration pour :

- Modifier les usines, les lignes de production existantes et les postes simulés existants.
- Mapper les données des serveurs OPC UA réels que vous connectez à la solution.

Pour plus d’informations sur le mappage et l’agrégation des données en fonction de vos besoins, consultez [Guide pratique pour configurer la solution préconfigurée Usine connectée](iot-suite-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Déployer les modifications

Une fois que vous avez apporté toutes les modifications requises au fichier **ContosoTopologyDescription.json**, vous devez redéployer la solution Usine connectée dans votre compte Azure.

Le référentiel **azure-iot-connected-factory** inclut un script PowerShell **build.ps1** que vous pouvez utiliser pour régénérer et déployer la solution.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la solution préconfigurée Usine connectée, consultez les articles suivants :

* [Procédure pas à pas de la solution préconfigurée d’usine connectée][lnk-rm-walkthrough]
* [Déployer une passerelle pour la solution Usine connectée][lnk-connect-cf]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]
* [FAQ sur la fabrique connectée](iot-suite-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md