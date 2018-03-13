---
title: "Créer un nom de domaine complet pour une machine virtuelle Linux dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 81182f5a03821dc42aed2390d9a0145460b1ddd7
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Créer un nom de domaine complet dans le portail Azure pour une machine virtuelle Linux

Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com), une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), vous pouvez en ajouter un après la création de la machine virtuelle. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet.

## <a name="create-a-fqdn"></a>Créer un nom de domaine complet
Cet article suppose que vous avez déjà créé une machine virtuelle. Si nécessaire, vous pouvez [créer une machine virtuelle dans le portail](quick-create-portal.md) ou [avec Azure CLI](quick-create-cli.md). Une fois que votre machine virtuelle est en cours d’exécution, procédez comme suit :

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS comme avec `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre machine virtuelle a un nom DNS et une IP publique, vous pouvez déployer des services ou des infrastructures d’applications tels que nginx, MongoDB, Docker, etc.

Vous pouvez également lire un autre article sur [l’utilisation de Resource Manager](../../azure-resource-manager/resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.

