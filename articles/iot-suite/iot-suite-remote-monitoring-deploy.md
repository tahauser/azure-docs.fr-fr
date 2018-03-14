---
title: "Déployer la solution de surveillance à distance - Azure | Microsoft Docs"
description: "Ce didacticiel montre comment approvisionner la solution préconfigurée de surveillance à distance à partir du site azureiotsuite.com."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: fa08817c870372488b1e03a2c282add2eaa85478
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Déployer la solution préconfigurée de surveillance à distance

Ce didacticiel montre comment configurer la solution préconfigurée de surveillance à distance. Vous déployez la solution à partir du site azureiotsuite.com. Vous pouvez également déployer la solution à l’aide de l’interface CLI. Pour en savoir plus sur cette option, consultez la rubrique [Deploy a preconfigured solution from the command line](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line) (Déployer une solution préconfigurée à partir de la ligne de commande).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configuration de la solution préconfigurée
> * Déploiement de la solution préconfigurée
> * Connexion à la solution préconfigurée

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Déploiement de la solution préconfigurée

Avant de déployer la solution préconfigurée dans votre abonnement Azure, vous devez choisir des options de configuration :

1. Connectez-vous à [azureiotsuite.com](https://www.azureiotsuite.com) à l’aide des informations d’identification de votre compte Azure, puis cliquez sur **+** pour créer une solution :

    ![Créer une solution](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Cliquez sur **Sélectionner** sur la vignette d’aperçu de la **Surveillance à distance**.

    ![Choisir la surveillance à distance](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. Sur la page de **création d’une solution de surveillance à distance**, entrez un **nom de solution** pour votre solution préconfigurée de surveillance à distance.

1. Sélectionnez un déploiement **De base** ou **Standard**. Si vous déployez la solution pour découvrir son fonctionnement ou effectuer une démonstration, choisissez l’option **De base** afin de réduire les coûts.

1. Choisissez le langage **Java** ou **.NET**. Tous les microservices sont disponibles sous la forme d’implémentations Java ou .NET.

1. Examinez le contenu du panneau **Détails de la Solution** pour plus d’informations sur vos choix de configuration.

1. Sélectionnez l’**Abonnement** et la **Région** à utiliser pour configurer la solution.

1. Cliquez sur **Créer une solution** pour commencer le processus de déploiement. L’exécution de ce processus prend généralement plusieurs minutes :

    ![Détails de la solution de surveillance à distance](media/iot-suite-remote-monitoring-deploy/createform.png)

Pour obtenir des informations de dépannage, consultez la rubrique [What to do when a deployment fails](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) (Que faire si le déploiement échoue ?) dans le référentiel GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Connexion à la solution préconfigurée

Lorsque le processus d’approvisionnement est terminé, vous pouvez vous connecter à votre solution préconfigurée de surveillance à distance.

1. Dans la page **Solutions approvisionnées**, choisissez votre nouvelle solution de surveillance à distance :

    ![Choisir la nouvelle solution](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Vous pouvez consulter les informations relatives à votre solution de surveillance à distance dans le panneau qui s’affiche. Choisissez **Tableau de bord des solutions** pour vous connecter à votre solution de surveillance à distance.

    > [!NOTE]
    > Vous pouvez supprimer votre solution de surveillance à distance dans ce panneau lorsque vous n’en avez plus besoin.

    ![Panneau de solutions](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. Le tableau de bord des solutions de surveillance à distance s’affiche dans votre navigateur.

## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configuration de la solution préconfigurée
> * Déploiement de la solution préconfigurée
> * Connexion à la solution préconfigurée

La solution de surveillance à distance étant déployée, l’étape suivante consiste à [explorer les fonctionnalités du tableau de bord des solutions](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->