---
title: Déployer le Kit de développement Azure Stack | Microsoft Docs
description: Dans ce didacticiel, vous allez installer kit de développement Azure Stack à l’aide des scripts Installer.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: de31ed75b79ddb3832e32fad141ea7aef806d4d3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-using-the-installer"></a>Didacticiel : déployer le kit de développement Azure Stack à l’aide du programme d’installation
Dans ce didacticiel, vous allez déployer le kit de développement Azure Stack dans un environnement non productif. 

Le Kit de développement Azure Stack est un environnement de développement et de test que vous pouvez déployer pour évaluer et présenter les fonctionnalités et services Azure Stack. Pour prendre en main le kit de développement Azure Stack, vous devez préparer le matériel informatique hôte et exécuter quelques scripts (l’installation prend plusieurs heures). Une fois que vous aurez effectué ces étapes préalables, vous pourrez vous connecter aux portails de l’administrateur et de l’utilisateur pour commencer à utiliser Azure Stack.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Téléchargez et extrayez le package de déploiement
> * Préparer l’ordinateur hôte du Kit de développement 
> * Installer le kit de développement Azure Stack sur l’ordinateur hôte
> * Effectuer des configurations post-déploiement
> * Inscription auprès d’Azure

## <a name="prerequisites"></a>Prérequis
 
Avant d’installer le kit de développement Azure Stack, vous devez préparer l’ordinateur qui hébergera le kit de développement (l’hôte du kit de développement). L’ordinateur hôte du Kit de développement doit avoir la configuration matérielle, logicielle et réseau requise. 

Vous devez aussi choisir entre utiliser Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) en tant que solution d’identité de votre déploiement. 

Veillez à ce que l’hôte du kit de développement respecte les exigences matérielles minimums requises, et à avoir décidé de votre solution d’identité avant de démarrer le déploiement, afin que le processus d’installation s’exécute correctement. 

**[Consultez les considérations en matière de planification du déploiement du kit de développement Azure Stack](asdk-deploy-considerations.md)**

> [!TIP]
> Vous pouvez utiliser l’[outil de vérification des exigences de déploiement Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) après l’installation du système d’exploitation sur l’ordinateur hôte du kit de développement pour confirmer que votre matériel réponde à toutes les exigences.

## <a name="download-and-extract-the-deployment-package"></a>Téléchargez et extrayez le package de déploiement
Après vous être assuré que votre ordinateur hôte du kit de développement réponde aux exigences de base pour l’installation du kit de développement Azure Stack, il vous faut télécharger et extraire le package de déploiement du kit de développement Azure Stack. Le package de déploiement contient le fichier Cloudbuilder.vhdx, un disque dur virtuel qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack.

Vous pouvez télécharger le package de déploiement sur l’hôte du Kit de développement ou sur un autre ordinateur. Une fois extraits, les fichiers de déploiement occupent jusqu’à 60 Go d’espace disque. L’utilisation d’un autre ordinateur peut vous permettre d’avoir une configuration matérielle moins exigeante pour l’hôte du Kit de développement.

**[Télécharger et extraire le Kit de développement Azure Stack (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du Kit de développement
Avant de pouvoir installer le kit de développement Azure Stack sur l’ordinateur hôte, l’environnement doit être préparé et le système doit être configuré pour démarrer à partir du disque dur virtuel. Une fois que l’ordinateur hôte du kit de développement a été préparé, il démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx afin que vous puissiez commencer le déploiement du kit de développement Azure Stack.

**[Préparer l’ordinateur hôte du kit de développement Azure Stack](asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installer le kit de développement Azure Stack sur l’ordinateur hôte
Après avoir préparé l’ordinateur hôte du kit de développement, le kit de développement Azure Stack peut être déployé dans l’image CloudBuilder.vhdx. Le kit de développement Azure Stack peut être déployé à l’aide d’une interface graphique utilisateur (GUI) fournie en téléchargeant et exécutant le script PowerShell asdk-installer.ps1, ou entièrement depuis la [ligne de commande](asdk-deploy-powershell.md). 

> [!NOTE]
> Si vous voulez, après le démarrage de l’ordinateur hôte dans le fichier CloudBuilder.vdhx, vous pouvez configurer les [paramètres de télémétrie Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *avant* d’installer le kit de développement Azure Stack.


**[Installer le Kit de développement Azure Stack (ASDK)](asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Effectuer des configurations post-déploiement
Après avoir installé le kit de développement Azure Stack, nous vous recommandons d’effectuer quelques vérifications post-installation et des modifications de configuration. Vous pouvez valider votre installation à l’aide de la cmdlet test-AzureStack, et installer les outils Azure Stack PowerShell et GitHub. 

Après les déploiements qui utilisent Azure AD, vous devez activer les portails d’administrateur et de locataire Azure Stack. Cette activation accepte de donner au portail Azure Stack et à Azure Resource Manager les autorisations appropriées (répertoriées sur la page de consentement) pour tous les utilisateurs du répertoire.

Vous devez aussi réinitialiser la stratégie d’expiration du mot de passe pour faire en sorte que le mot de passe de l’hôte du kit de développement n’expire pas avant la fin de la période d’expiration.

> [!NOTE]
> Si vous voulez, vous pouvez aussi configurer les [Paramètres de télémétries Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *après* avoir installé le kit de développement Azure Stack.

**[Tâches post-déploiement du kit de développement Azure Stack](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Inscription auprès d’Azure
Vous devez inscrire Azure Stack auprès d’Azure pour pouvoir ensuite [télécharger des éléments marketplace Azure](asdk-marketplace-item.md) dans Azure Stack.

**[Inscrire Azure Stack auprès d’Azure](asdk-register.md)**

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Après toutes ces étapes, l’environnement pour le Kit de développement est prêt, avec les portails de l’[administrateur](https://adminportal.local.azurestack.external) et de l’[utilisateur](https://portal.local.azurestack.external). 

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Téléchargez et extrayez le package de déploiement
> * Préparer l’ordinateur hôte du Kit de développement 
> * Installer le kit de développement Azure Stack sur l’ordinateur hôte
> * Effectuer des configurations post-déploiement
> * Inscription auprès d’Azure

Passez au didacticiel suivant pour apprendre à ajouter un élément marketplace Azure Stack.

> [!div class="nextstepaction"]
> [Ajouter un élément marketplace Azure Stack](asdk-marketplace-item.md)




