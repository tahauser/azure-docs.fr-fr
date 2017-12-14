---
title: "Gestion de la disponibilité des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Découvrez comment utiliser plusieurs machines virtuelles afin de garantir une haute disponibilité pour votre application Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ac83373c1c8a8077a235be2a7b5f5df9353add45
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Gestion de la disponibilité des machines virtuelles Linux

Découvrez comment configurer et gérer plusieurs machines virtuelles afin de garantir une haute disponibilité pour votre application Linux dans Azure. Vous pouvez également [gérer la disponibilité des machines virtuelles Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour obtenir des instructions sur la création d’un groupe à haute disponibilité à l’aide de l’interface de ligne de commande dans le modèle de déploiement Resource Manager, consultez [azure availset : commandes pour gérer vos groupes à haute disponibilité](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’équilibrage de charge de vos machines virtuelles, consultez la rubrique concernant [l’équilibrage de charge des machines virtuelles](../virtual-machines-linux-load-balance.md).

