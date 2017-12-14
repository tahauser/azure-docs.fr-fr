---
title: "Vue d’ensemble d’Azure Automation DSC | Microsoft Docs"
description: "Vue d'ensemble de la configuration d'état souhaité (DSC) Azure Automation, les termes s'y rapportant et les problèmes connus"
services: automation
documentationcenter: dev-center-name
author: georgewallace
manager: carmonm
keywords: "PowerShell DSC, Desired State Configuration, configuration d'état souhaité PowerShell DSC Azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: gwallace
ms.openlocfilehash: 7440fa58f9cbd201984d178a7e9a2c12afc84783
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-automation-dsc-overview"></a>Vue d'ensemble d'Azure Automation DSC

Azure Automation DSC est un service Azure qui vous permet d’écrire, de gérer et de compiler les [configurations](https://msdn.microsoft.com/powershell/dsc/configurations) PowerShell DSC, d’importer des [ressources DSC](https://msdn.microsoft.com/powershell/dsc/resources) et d’assigner des configurations aux nœuds cibles, le tout dans le cloud.

## <a name="why-use-azure-automation-dsc"></a>Pourquoi utiliser Azure Automation DSC ?

Azure Automation DSC offre plusieurs avantages par rapport à l’utilisation de DSC en dehors d’Azure.

### <a name="built-in-pull-server"></a>Serveur collecteur intégré

Azure Automation fournit un [serveur collecteur DSC](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) afin que les nœuds cibles reçoivent automatiquement les configurations, soient conformes à l’état souhaité et renvoient un rapport attestant de leur conformité.
Le serveur collecteur intégré dans Azure Automation vous permet d’éviter d’avoir à configurer et à gérer votre propre serveur collecteur.
Azure Automation peut cibler des machines physiques ou virtuelles Windows ou Linux, dans le cloud ou en local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestion de tous vos artefacts DSC

Azure Automation DSC apporte la même couche de gestion à la [configuration d’état souhaité PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) que celle proposée par Azure Automation pour l’écriture de scripts PowerShell.

À partir du portail Azure, ou de PowerShell, vous pouvez gérer toutes vos configurations DSC, vos ressources et vos nœuds cibles.

![Capture d’écran du panneau Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importer des données de création de rapports dans Log Analytics

Les nœuds gérés dans Azure Automation DSC envoient des données détaillées sur l’état de création de rapports au serveur collecteur intégré.
Vous pouvez configurer Azure Automation DSC pour envoyer ces données à votre espace de travail Microsoft Operations Management Suite (OMS) Log Analytics.
Pour savoir comment envoyer des données d’état DSC à votre espace de travail Log Analytics, consultez [Forward Azure Automation DSC reporting data to OMS Log Analytics](automation-dsc-diagnostics.md) (Transférer des données de création de rapports Azure Automation DSC à OMS Log Analytics).

## <a name="introduction-video"></a>Vidéo de présentation

Lire de la documentation vous enchante moyennement ? Jetez un œil à la vidéo ci-dessous, publiée en mai 2015 à l’occasion de l’annonce d’Azure Automation DSC.

>[!NOTE]
>Bien que les concepts et le cycle de vie abordés dans cette vidéo soient corrects, Azure Automation DSC a beaucoup progressé depuis l’enregistrement de cette vidéo.
>Désormais disponible au public, il dispose d’une interface utilisateur plus étendue dans le portail Azure et prend en charge des fonctionnalités supplémentaires.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment intégrer des nœuds devant être gérés avec Azure Automation DSC, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).
* Pour prendre en main Azure Automation DSC, consultez [Prise en main d’Azure Automation DSC](automation-dsc-getting-started.md).
* Pour en savoir plus sur la compilation des configurations DSC pour les assigner à des nœuds cibles, consultez [Compilation de configurations dans Azure Automation DSC](automation-dsc-compile.md).
* Pour la référence de cmdlet PowerShell pour Azure Automation DSC, consultez [AzureRM.Automation](/powershell/module/azurerm.automation/#automation).
* Pour plus d’informations sur la tarification, consultez [Tarification de Automation](https://azure.microsoft.com/pricing/details/automation/).
* Pour voir un exemple d’utilisation d’Azure Automation DSC dans un pipeline de déploiement continu, consultez [Exemple d’utilisation : Déploiement continu sur des machines virtuelles à l’aide d’Automation DSC et Chocolatey](automation-dsc-cd-chocolatey.md).