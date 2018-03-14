---
title: Configurer le niveau PremiumV2 pour Azure App Service | Microsoft Docs
description: "Découvrez comment améliorer les performances de vos applications web, mobiles et API dans Azure App Service en les passant au nouveau niveau tarifaire PremiumV2."
keywords: "app service, azure app service, mise à l'échelle, évolutif, plan app service, coût d'app service"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 76897173d9fdfffe7139e7c5648ad0efb1c05b97
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurer le niveau PremiumV2 pour Azure App Service

Le nouveau niveau tarifaire **PremiumV2** offre des processeurs plus rapides, un stockage SSD et le double de ratio mémoire-cœur des niveaux tarifaires précédents. Avec cet avantage de performances, vous pouvez économiser de l’argent en exécutant vos applications sur moins d’instances. Dans cet article, vous allez apprendre à créer une application dans le niveau **PremiumV2** ou à monter en puissance une application en la passant au niveau **PremiumV2**.

## <a name="prerequisites"></a>Prérequis

Pour monter en puissance une application web en la passant au niveau **PremiumV2**, vous devez avoir une application web dans Azure App Service qui s’exécute dans un niveau tarifaire inférieur à **PremiumV2**.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilité de PremiumV2

Le niveau PremiumV2 est actuellement disponible pour App Service sur _Windows_ uniquement. Les conteneurs Linux ne sont pas encore pris en charge.

PremiumV2 est déjà disponible dans la plupart des régions Azure. Il sera progressivement étendu à d’autres régions. Pour savoir si ce niveau est disponible dans votre région, exécutez la commande Azure CLI suivante dans [Azure Cloud Shell](../cloud-shell/overview.md) :

```azurecli-interactive
az appservice list-locations --sku P1V2
```

Si vous obtenez une erreur pendant la création de l’application ou du plan App Service, cela signifie probablement que le niveau **PremiumV2** n’est pas encore disponible dans votre région.

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Créer une application dans le niveau PremiumV2

Le niveau tarifaire d’une application App Service est défini dans le [plan App Service](azure-web-sites-web-hosting-plans-in-depth-overview.md) dans lequel elle s’exécute. Vous pouvez créer un plan App Service de façon séparée ou dans le cadre de la création d’une application web.

Quand vous configurez le plan App Service dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, sélectionnez **Niveau tarifaire**. 

Choisissez l’une des options **PremiumV2** proposées, puis cliquez sur **Sélectionner**.

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> Si vous ne voyez pas les options **P1V2**, **P2V2** et **P3V2**, cela signifie que le niveau **PremiumV2** n’est pas disponible dans la région que vous avez choisie, ou que vous configurez un plan App Service Linux, qui ne prend pas en charge **PremiumV2**.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Monter en puissance une application existante en la passant au niveau PremiumV2

Avant de monter en puissance une application existante en la passant au niveau **PremiumV2**, assurez-vous que **PremiumV2** est disponible dans votre région. Pour plus d’informations, consultez [Disponibilité de PremiumV2](#availability). Si le niveau n’est pas disponible dans votre région, consultez [Monter en puissance une application dans une région non prise en charge](#unsupported).

Selon votre environnement d’hébergement, la montée en puissance peut nécessiter des étapes supplémentaires. 

Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez la page de votre application App Service.

Dans le volet de navigation de gauche dans la page de votre application App Service, sélectionnez **Monter en puissance (plan App Service)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Sélectionnez une des tailles **PremiumV2** proposées, puis cliquez sur **Sélectionner**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Si l’opération se termine sans erreur, la page de présentation de votre application montre que l’application s’exécute maintenant dans le niveau **PremiumV2**.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>En cas d’erreur

Certains plans App Service ne peuvent pas être montés en puissance au niveau PremiumV2. Si l’opération de montée en puissance se solde par une erreur, cela indique que vous avez besoin d’un nouveau plan App Service pour votre application.

Créez un plan App Service _Windows_ dans la même région et le même groupe de ressources que votre application App Service existante. Suivez les étapes de la procédure [Créer une application dans le niveau PremiumV2](#create) pour passer au niveau **PremiumV2**. Si vous le souhaitez, utilisez la même configuration de mise à l’échelle que votre plan App Service existant (nombre d’instances, mise à l’échelle automatique, etc.).

Ouvrez à nouveau la page de votre application App Service. Dans le volet de navigation de gauche de la page de votre application App Service, sélectionnez **Changer le plan App Service**.

![](media/app-service-configure-premium-tier/change-plan.png)

Sélectionnez le plan App Service que vous avez créé.

![](media/app-service-configure-premium-tier/select-plan.png)

Après ce changement, votre application s’exécute dans le niveau **PremiumV2**.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>Monter en puissance une application dans une région non prise en charge

Si votre application s’exécute dans une région où le niveau **PremiumV2** n’est pas encore disponible, vous pouvez la déplacer vers une autre région pour bénéficier du niveau **PremiumV2**. Deux options s'offrent à vous :

- Créez une application dans un nouveau plan **PremiumV2**, puis redéployez votre code d’application. Suivez les étapes de la procédure [Créer une application dans le niveau PremiumV2](#create) pour passer au niveau **PremiumV2**. Si vous le souhaitez, utilisez la même configuration de mise à l’échelle que votre plan App Service existant (nombre d’instances, mise à l’échelle automatique, etc.).
- Si votre application s’exécute déjà dans un niveau **Premium** existant, vous pouvez cloner votre application avec l’ensemble de ses paramètres d’application, chaînes de connexion et paramètres de configuration de déploiement.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Dans la page **Cloner l’application**, vous pouvez créer un plan App Service dans la région de votre choix, puis spécifier les paramètres à cloner.

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la création d’application dans le niveau **PremiumV2** à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

La commande suivante crée un plan App Service dans _P1V2_. Vous pouvez l’exécuter dans Cloud Shell. Les options possibles pour `--sku` sont P1V2, _P2V2_ et _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

La commande suivante crée un plan App Service dans _P1V2_. Les options possibles pour `-WorkerSize` sont _Small_, _Medium_ et _Large_.

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Autres ressources

[Montée en puissance d’une application dans Azure](web-sites-scale.md)  
[Mise à l’échelle manuelle ou automatique du nombre d’instances](../monitoring-and-diagnostics/insights-how-to-scale.md)