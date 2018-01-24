---
title: "Comment utiliser une identité MSI affectée par l’utilisateur à partir de kits de développement logiciel Azure sur une machine virtuelle"
description: "Exemples de code pour l’utilisation de kits de développement logiciel Azure avec une MSI affectée par un utilisateur sur une machine virtuelle."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f9a31a0500a6f5f1c49fc45d5811e28788e6f2b1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Utiliser les kits de développement logiciel Azure avec une identité MSI (Managed Service Identity) affectée par un utilisateur

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)] Cet article fournit une liste d’exemples de kits de développement logiciel, qui illustrent l’utilisation de la prise en charge d’une MSI affectée par l’utilisateur par leurs kits de développement logiciel Azure respectifs.

## <a name="prerequisites"></a>configuration requise

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Tous les scripts/exemples de code de cet article supposent que le client exécute une machine virtuelle avec le paramètre MSI activé. Utilisez la fonctionnalité « Se connecter » de machine virtuelle dans le portail Azure, pour vous connecter à distance à votre machine virtuelle. Pour plus d’informations sur l’activation de MSI sur une machine virtuelle, consultez [Configurer une identité du service administré (MSI) d’une machine virtuelle à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md), ou l’un des autres articles (à l’aide de PowerShell, du portail Azure, d’un modèle ou d’un kit de développement logiciel Azure). 

## <a name="sdk-code-samples"></a>Exemples de code de kit de développement logiciel

| Foundation             | Exemple de code |
| --------------- | ----------- |
| Python          | [Utiliser une identité du service administré pour s’authentifier simplement depuis l’intérieur d’une machine virtuelle](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gérer des ressources à partir d’une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>étapes suivantes

- Consultez [Kits de développement logiciel Azure](https://azure.microsoft.com/downloads/) pour obtenir la liste complète des ressources des kits de développement Azure, y compris les téléchargements de bibliothèque, la documentation et bien plus encore.

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.








