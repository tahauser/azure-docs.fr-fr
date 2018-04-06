---
title: Surveiller le cluster DC/OS Azure - Gestion des opérations
description: Analysez un cluster DC/OS Azure Container Service avec Log Analytics.
services: container-service
author: keikhara
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: ba76f8480dedb37326505f7ed756eb51a41ee0fe
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-log-analytics"></a>Analyser un cluster DC/OS Azure Container Service avec Log Analytics

Log Analytics est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud. La solution de conteneurs fait partie intégrante de Log Analytics, qui vous octroie une visibilité sur le stock, les performances et les fichiers journaux des conteneurs à un emplacement unique. Vous pouvez auditer les conteneurs, résoudre les problèmes en consultant les fichiers journaux de manière centralisée, et identifier les conteneurs bruyants à consommation supérieure sur un hôte.

![](media/container-service-monitoring-oms/image1.png)

Pour plus d’informations sur la solution Conteneurs, reportez-vous à [Solution Conteneurs (version préliminaire) Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-log-analytics-from-the-dcos-universe"></a>Configuration de Log Analytics à partir de l’univers DC/OS


Cet article suppose que vous avez configuré un univers DC/OS et avez déployé des applications web simples de conteneurs sur le cluster.

### <a name="pre-requisite"></a>Conditions préalables
- [Abonnement Microsoft Azure](https://azure.microsoft.com/free/) : vous pouvez l’obtenir gratuitement.  
- Configuration de l’espace de travail Log Analytics : consultez « l’étape 3 » ci-dessous
- [Interface CLI DC/OS](https://dcos.io/docs/1.8/usage/cli/install/) installées.

1. Dans le tableau de bord DC/OS, cliquez sur l’univers, puis recherchez « OMS », tel que représenté ci-dessous.

![](media/container-service-monitoring-oms/image2.png)

2. Cliquez sur **Installer**. Vous découvrez une fenêtre contextuelle avec les informations de version et un bouton **Installer le package** ou **Installation avancée**. Lorsque vous cliquez sur **Installation avancée**, vous accédez à la page des **propriétés de configuration spécifique OMS**.

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Ici, vous êtes invité à entrer `wsid` (l’ID d’espace de travail Log Analytics) et `wskey` (la clé primaire pour l’ID de l’espace de travail). Pour obtenir les deux `wsid` et `wskey` vous devez créer un compte sur <https://mms.microsoft.com>.
Suivez la procédure de création de compte. Une fois que vous avez créé le compte, pour obtenir votre `wsid` et votre `wskey`, cliquez sur **Paramètres**, puis sur **Sources connectés**, puis sur **Serveurs Linux**, tel que représenté ci-dessous.

 ![](media/container-service-monitoring-oms/image5.png)

4. Sélectionnez le nombre d’instances souhaité, puis cliquez sur le bouton « Réviser et installer ». Généralement, vous avez intérêt à ce que le nombre d’instances soit équivalent au nombre de machines virtuelles dans votre cluster d’agent. L’Agent OMS pour Linux s’installe en tant que conteneurs individuels sur chaque machine virtuelle qu’il souhaite affecter à la collecte d’informations pour l’analyse et l’enregistrement des informations.

## <a name="setting-up-a-simple-oms-dashboard"></a>Configuration d’un tableau de bord OMS simple

Une fois que vous avez installé l’Agent OMS pour Linux sur les machines virtuelles, il vous faut configurer le tableau de bord OMS. Il existe deux manières de procéder : via le portail OMS et via le portail Azure.

### <a name="oms-portal"></a>Portail OMS 

Connectez-vous au portail OMS (<https://mms.microsoft.com>), puis accédez à la **Galerie de solutions**.

![](media/container-service-monitoring-oms/image6.png)

Une fois dans la **Galerie de solutions**, sélectionnez **Conteneurs**.

![](media/container-service-monitoring-oms/image7.png)

Une fois que vous avez sélectionné la solution de conteneur, la vignette s’affiche sur la page du tableau de bord de présentation OMS. Dès que les données de conteneurs fournies sont indexées, les vignettes de vue de la solution sont renseignées avec les informations.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Portail Azure 

Connectez-vous au Portail Azure sur <https://portal.microsoft.com/>. Go to **Marketplace**, sélectionnez **Surveillance + gestion**, puis cliquez sur **Afficher tout**. Ensuite, saisissez `containers` dans la recherche. L’indication « conteneurs » apparaît dans les résultats de la recherche. Sélectionnez **Conteneurs**, puis cliquez sur **Créer**.

![](media/container-service-monitoring-oms/image9.png)

Une fois que vous avez cliqué sur **Créer**, vous êtes invité à saisir votre espace de travail. Sélectionnez votre espace de travail. Si vous n’en avez pas, créez-en un.

![](media/container-service-monitoring-oms/image10.PNG)

Une fois la sélection effectuée, cliquez sur **Créer**.

![](media/container-service-monitoring-oms/image11.png)

Pour plus d’informations sur la solution Conteneurs Log Analytics, reportez-vous à la rubrique [Solution Conteneurs Log Analytics](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Mise à l’échelle de l’Agent OMS avec ACS DC/OS 

Si vous devez avoir installé l’Agent OMS en-deçà du nombre réel de nœuds ou si vous mettez à l’échelle VMSS en ajoutant davantage de machines virtuelles, vous pouvez mettre à l’échelle le service `msoms`.

Pour ce faire, accédez à Marathon ou à l’onglet des services d’interface utilisateur DC/OS, puis augmentez le nombre de nœuds.

![](media/container-service-monitoring-oms/image12.PNG)

Cette action se déploie sur d’autres nœuds qui n’ont pas encore déployé l’Agent OMS.

## <a name="uninstall-ms-oms"></a>Désinstaller MS OMS

Pour désinstaller MS OMS, saisissez la commande suivante :

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Dites-le-nous !
Qu’est-ce qui fonctionne ? Quels sont les manquements ? Quels éléments pourraient vous être utiles ? Faites-le nous savoir sur <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Étapes suivantes

 Maintenant que vous avez configuré Log Analytics pour analyser vos conteneurs, [consultez votre tableau de bord des conteneurs](../../log-analytics/log-analytics-containers.md).
