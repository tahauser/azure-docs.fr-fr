---
title: "Déployer la solution de simulation d’appareil - Azure | Microsoft Docs"
description: "Ce didacticiel montre comment approvisionner la solution de simulation d’appareil à partir du site azureiotsuite.com."
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: da9fb95ed5d3387c98c3274a53769d3f5f945371
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Déployer la solution de simulation d’appareil Azure IoT

Ce didacticiel montre comment approvisionner une solution de simulation d’appareil. Vous déployez la solution à partir du site azureiotsuite.com.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer la solution de simulation d’appareil
> * Déployer la solution de simulation d’appareil
> * Se connecter à la solution de simulation d’appareil

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Déployer la solution

Avant de déployer la solution dans votre abonnement Azure, vous devez choisir des options de configuration :

1. Connectez-vous à [azureiotsuite.com](https://www.azureiotsuite.com) à l’aide des informations d’identification de votre compte Azure, puis cliquez sur **+** pour créer une solution :

    ![Créer une solution](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. Sur la vignette **Simulation d’appareil**, cliquez sur **Sélectionner**  :

    ![Choisir une simulation d’appareil](media/iot-suite-device-simulation-deploy/select.png)

1. Sur la page **Créer une solution de simulation d’appareil**, entrez un **nom de solution** pour votre solution de simulation d’appareil.

1. Sélectionnez l’**Abonnement** et la **Région** à utiliser pour configurer la solution.

1. Indiquez si vous voulez déployer un nouvel IoT Hub avec votre solution de simulation d’appareil. Si vous cochez cette case, un nouvel IoT Hub est déployé dans votre abonnement. Quel que soit votre choix, vous pouvez toujours faire pointer votre simulation sur n’importe quel IoT Hub.

1. Cliquez sur **Créer une solution** pour commencer le processus de déploiement. L’exécution de ce processus prend généralement plusieurs minutes :

    ![Détails sur la solution de simulation d’appareil](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Se connecter à la solution

Lorsque le processus d’approvisionnement est terminé, vous pouvez vous connecter à votre solution de simulation d’appareil.

1. Sur la page **Solutions approvisionnées**, cliquez sur **Lancer** sous votre nouvelle solution de simulation d’appareil :

    ![Choisir la nouvelle solution](media/iot-suite-device-simulation-deploy/newsolution.png)

1. Vous pouvez consulter les informations relatives à votre solution de simulation d’appareil dans le panneau qui s’affiche. Choisissez **Tableau de bord des solutions** pour vous connecter à votre solution de simulation d’appareil.

    > [!NOTE]
    > Vous pouvez supprimer votre solution de simulation d’appareil dans ce panneau lorsque vous n’en avez plus besoin.

    ![Panneau de solutions](media/iot-suite-device-simulation-deploy/properties.png)

1. Le tableau de bord des solutions de simulation d’appareil s’affiche dans votre navigateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Configuration de la solution
> * Déployer la solution
> * Se connecter à la solution

La solution de simulation d’appareil étant déployée, l’étape suivante consiste à [explorer les fonctionnalités de la solution de simulation d’appareil](./iot-suite-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
