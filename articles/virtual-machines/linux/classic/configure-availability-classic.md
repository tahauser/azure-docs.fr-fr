---
title: "Groupes à haute disponibilité pour machines virtuelles Linux Classic | Microsoft Docs"
description: "Configurez un groupe à haute disponibilité pour une machine virtuelle Linux nouvelle ou existante dans le modèle de déploiement classique à l’aide du portail Azure et d'Azure PowerShell."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: b8624315-beca-4ec7-8441-2e98b166b548
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: cynthn
ms.openlocfilehash: b6243614bf7cbd97a4ce8ceff5a3ba0d33fc42f4
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-configure-an-availability-set-for-linux-virtual-machines-in-the-classic-deployment-model"></a>Configuration d’un groupe à haute disponibilité pour des machines virtuelles Linux dans le modèle de déploiement classique
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Vous pouvez également [configurer des groupes à haute disponibilité](../../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets) dans des déploiements Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [virtual-machines-common-classic-configure-availability](../../../../includes/virtual-machines-common-classic-configure-availability.md)]