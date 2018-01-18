---
title: "Clonage d’application web à l’aide de PowerShell"
description: "Découvrez comment cloner vos applications web vers de nouvelles applications web à l’aide de PowerShell."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
ms.openlocfilehash: 8f58464ac212b84623d2287205271301dbaa0ffb
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonage de l’application Azure App Service à l’aide de PowerShell
Avec la publication de Microsoft Azure PowerShell version 1.1.0, une nouvelle option a été ajoutée à l’applet de commande New-AzureRMWebApp, qui permet à l’utilisateur de cloner une application web existante à une application qui vient d’être créée dans une autre région ou dans la même région. Les utilisateurs peuvent ainsi déployer de nombreuses applications dans différentes régions.

Le clonage d’application n’est actuellement pris en charge que pour les plans de service d’application de niveau Premium. Cette nouvelle fonctionnalité utilise les mêmes limitations que la fonctionnalité de sauvegarde des applications web. Pour plus d’informations, consultez l’article [Sauvegarder une application web dans Azure App Service](web-sites-backup.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>Clonage d’une application existante
Scénario : un utilisateur souhaite cloner le contenu d’une application web du Sud du centre des États-Unis vers une nouvelle application web de la région Nord du centre des États-Unis. Ce peut être effectué à l’aide de la version Azure Resource Manager de l’applet de commande PowerShell pour créer une application web avec l’option -SourceWebApp.

Si nous connaissons le nom du groupe de ressources qui contient l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir les informations de l’application web source (dans ce cas nommée source-webapp) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Pour créer un plan App Service, nous pouvons utiliser la commande New-AzureRmAppServicePlan comme dans l’exemple suivant :

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

À l’aide de la commande New-AzureRmWebApp, nous pouvons créer l’application web dans la région Nord du centre des États-Unis et la lier à un plan App Service existant de niveau Premium. Par ailleurs, nous pouvons utiliser le même groupe de ressources que l’application web source ou en définir un nouveau, comme le montre le code suivant :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Pour cloner une application web existante, y compris le déploiement de tous les emplacements, l’utilisateur doit utiliser le paramètre IncludeSourceWebAppSlots, la commande PowerShell suivante illustre l’utilisation de ce paramètre avec la commande New-AzureRmWebApp :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots

Pour cloner une application web existante dans la même région, l’utilisateur doit créer un groupe de ressources et un plan de service d’application dans la même région, puis utiliser la commande PowerShell suivante pour cloner l’application web :

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonage d’une application existante vers un App Service Environment
Scénario : un utilisateur souhaite cloner le contenu d’une application web du Sud du centre des États-Unis vers une nouvelle application web d’un App Service Environment (ASE) existant.

Si nous connaissons le nom du groupe de ressources qui contient l’application web source, nous pouvons utiliser la commande PowerShell suivante pour obtenir les informations de l’application web source (dans ce cas nommée source-webapp) :

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

S’il connaît le nom de l’environnement ASE et le nom du groupe de ressources auquel l’ASE appartient, l’utilisateur peut exécuter la commande New-AzureRmWebApp pour créer l’application web dans l’environnement ASE, comme le montre le code suivant :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Le paramètre -Location est requis en raison de l’héritage, mais il est ignoré dans le cas de la création d’une application dans un environnement ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonage d’un emplacement d’application existant
Scénario : un utilisateur souhaite cloner un emplacement d’application web existant vers une nouvelle application web ou un nouvel emplacement d’application web. La nouvelle application web peut résider dans la même région que l’emplacement d’application web d’origine ou dans une autre région.

Si nous connaissons le nom du groupe de ressources qui contient l’application web source, vous pouvons utiliser la commande PowerShell suivante pour obtenir les informations de l’emplacement d’application web source (dans ce cas nommé source-webappslot) lié à source-webapp de l’application web :

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Le code suivant illustre la création d’un clone de l’application web source vers une nouvelle application web :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuration de Traffic Manager lors du clonage d’une application
La création d’applications web sur plusieurs régions et la configuration d’Azure Traffic Manager pour acheminer le trafic vers toutes les applications web constituent un scénario contribuant largement à garantir que les applications client sont hautement disponibles. Au moment de cloner une application web existante, vous avez la possibilité de connecter les deux applications web à un nouveau profil de gestionnaire de trafic ou à un profil existant. Notez que seule la version Azure Resource manager de Traffic Manager est prise en charge.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Création d’un profil Traffic Manager lors du clonage d’une application
Scénario : un utilisateur souhaite cloner une application web vers une autre région tout en configurant un profil Traffic Manager Azure Resource Manager qui inclut les deux applications web. Le code suivant illustre la création d’un clone de l’application web source vers une nouvelle application web tout en configurant un nouveau profil Traffic Manager :

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Ajout d’une nouvelle application web clonée à un profil Traffic Manager existant
Scénario : un utilisateur dispose déjà d’un profil Traffic Manager Azure Resource Manager auquel il souhaite ajouter les deux applications web comme points de terminaison. Pour ce faire, nous devons tout d’abord assembler l’ID de profil Traffic Manager existant. Nous devons disposer de l’ID d’abonnement, du nom de groupe de ressources et du nom du profil Traffic Manager existant.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Le code suivant illustre la création d’un clone de l’application web source vers une nouvelle application web tout en les ajoutant à un profil Traffic Manager existant :

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restrictions actuelles
Cette fonctionnalité en est actuellement à sa version préliminaire. Nous nous efforçons d’ajouter de nouvelles fonctionnalités au fil du temps. La liste suivante répertorie les restrictions connues de la version actuelle du clonage d’application :

* Les paramètres de mise à l’échelle automatique ne sont pas clonés.
* Les paramètres de planification de sauvegarde ne sont pas clonés.
* Les paramètres de réseau virtuel ne sont pas clonés.
* Application Insights n’est pas automatiquement configuré dans l’application web de destination
* Les paramètres d’authentification simple ne sont pas clonés.
* Les paramètres d’extension Kudu ne sont pas clonés.
* Les règles TiP ne sont pas clonées.
* Les contenus de la base de données ne sont pas clonés.
* Les adresses IP sortantes changent si vous clonez vers une unité d’échelle différente

### <a name="references"></a>Références
* [Clonage Web App](app-service-web-app-cloning.md)
* [Sauvegarde d’une application web dans Azure App Service](web-sites-backup.md)
* [Prise en charge d’Azure Resource Manager pour la version préliminaire d’Azure Traffic Manager](../traffic-manager/traffic-manager-powershell-arm.md)
* [Présentation de l'environnement App Service](environment/intro.md)
* [Utilisation d'Azure PowerShell avec Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)

