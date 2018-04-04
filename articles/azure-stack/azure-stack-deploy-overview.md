---
title: Évaluer le Kit de développement Azure Stack | Microsoft Docs
description: En savoir plus sur le déploiement du Kit de développement Azure Stack à des fins d’évaluation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Démarrage rapide : préparer le Kit de développement Azure Stack
Le [Kit de développement Azure Stack (ASDK)](.\asdk\asdk-what-is.md) est un environnement de développement et de test que vous pouvez déployer pour évaluer et présenter les fonctionnalités et services Azure Stack. Pour prendre en main le kit de développement Azure Stack, vous devez préparer le matériel informatique hôte et exécuter quelques scripts (l’installation prend plusieurs heures). Une fois que vous aurez effectué ces étapes préalables, vous pourrez vous connecter aux portails de l’administrateur et de l’utilisateur pour commencer à utiliser Azure Stack.

## <a name="prerequisites"></a>Prérequis
 
Avant d’installer le kit de développement Azure Stack, vous devez préparer l’ordinateur qui hébergera le kit de développement (l’hôte du kit de développement). L’ordinateur hôte du Kit de développement doit avoir la configuration matérielle, logicielle et réseau requise. 

Vous devez aussi choisir entre utiliser Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) en tant que solution d’identité de votre déploiement. 

Veillez à ce que l’hôte du kit de développement respecte les exigences matérielles minimums requises, et à avoir décidé de votre solution d’identité avant de démarrer le déploiement, afin que le processus d’installation s’exécute correctement. 

**[Consultez les considérations en matière de planification du déploiement du kit de développement Azure Stack](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Vous pouvez utiliser l’[outil de vérification des exigences de déploiement Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) après l’installation du système d’exploitation sur l’ordinateur hôte du kit de développement pour confirmer que votre matériel réponde à toutes les exigences.

## <a name="download-and-extract-the-deployment-package"></a>Téléchargez et extrayez le package de déploiement
Après vous être assuré que votre ordinateur hôte du kit de développement réponde aux exigences de base pour l’installation du kit de développement Azure Stack, il vous faut télécharger et extraire le package de déploiement du Kit de développement Azure Stack. Le package de déploiement contient le fichier Cloudbuilder.vhdx, un disque dur virtuel qui contient un système d’exploitation démarrable et les fichiers d’installation d’Azure Stack.

Vous pouvez télécharger le package de déploiement sur l’hôte du Kit de développement ou sur un autre ordinateur. Une fois extraits, les fichiers de déploiement occupent jusqu’à 60 Go d’espace disque. L’utilisation d’un autre ordinateur peut vous permettre d’avoir une configuration matérielle moins exigeante pour l’hôte du Kit de développement.

**[Télécharger et extraire le Kit de développement Azure Stack (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du Kit de développement
Avant de pouvoir installer le kit de développement Azure Stack sur l’ordinateur hôte, l’environnement doit être préparé et le système doit être configuré pour démarrer à partir du disque dur virtuel. Une fois que l’ordinateur hôte du kit de développement a été préparé, il démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx afin que vous puissiez commencer le déploiement du kit de développement Azure Stack.

**[Préparer l’ordinateur hôte du kit de développement Azure Stack](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Installer le kit de développement Azure Stack sur l’ordinateur hôte
Après avoir préparé l’ordinateur hôte du kit de développement, le kit de développement Azure Stack peut être déployé dans l’image CloudBuilder.vhdx. Le kit de développement Azure Stack peut être déployé à l’aide d’une interface graphique utilisateur (GUI) fournie en téléchargeant et exécutant le script PowerShell asdk-installer.ps1, ou entièrement depuis la [ligne de commande](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Si vous voulez, après le démarrage de l’ordinateur hôte dans le fichier CloudBuilder.vdhx, vous pouvez configurer les [paramètres de télémétrie Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *avant* d’installer le kit de développement Azure Stack.


**[Installer le Kit de développement Azure Stack (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Effectuer des configurations post-déploiement
Après avoir installé le Kit de développement Azure Stack, nous vous recommandons d’effectuer quelques vérifications post-installation et des modifications de configuration. Vous pouvez valider votre installation à l’aide de la cmdlet test-AzureStack, et installer les outils Azure Stack PowerShell et GitHub. 

Après les déploiements qui utilisent Azure AD, vous devez activer les portails d’administrateur et de locataire Azure Stack. Cette activation accepte de donner au portail Azure Stack et à Azure Resource Manager les autorisations appropriées (répertoriées sur la page de consentement) pour tous les utilisateurs du répertoire.

Vous devez aussi réinitialiser la stratégie d’expiration du mot de passe pour faire en sorte que le mot de passe de l’hôte du Kit de développement n’expire pas avant la fin de la période d’expiration.

> [!NOTE]
> Si vous voulez, vous pouvez aussi configurer les [Paramètres de télémétries Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *après* avoir installé le kit de développement Azure Stack.

**[Tâches post-déploiement du kit de développement Azure Stack](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Inscription auprès d’Azure
Vous devez inscrire Azure Stack auprès d’Azure pour pouvoir ensuite [télécharger des éléments marketplace Azure](.\asdk\asdk-marketplace-item.md) dans Azure Stack.

**[Inscrire Azure Stack auprès d’Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Après toutes ces étapes, l’environnement pour le Kit de développement est prêt, avec les portails de l’[administrateur](https://adminportal.local.azurestack.external) et de l’[utilisateur](https://portal.local.azurestack.external). 
