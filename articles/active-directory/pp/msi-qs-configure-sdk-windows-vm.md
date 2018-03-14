---
title: "Guide pratique pour configurer une identité MSI affectée par l’utilisateur pour une machine virtuelle Azure à l’aide d’un SDK Azure"
description: "Instructions détaillées pour configurer une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle Azure avec un SDK Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 097304162b85599acd1f4591091f986a646ebc2a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Configurer une identité MSI (Managed Service Identity) affectée par l’utilisateur pour une machine virtuelle, à l’aide d’un SDK Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment activer et supprimer une identité MSI affectée par l’utilisateur pour une machine virtuelle Azure à l’aide d’un SDK Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Kits de développement logiciel (SDK) Azure avec prise en charge de MSI 

Azure prend en charge plusieurs plates-formes de programmation via une série de [Kits de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads). Plusieurs d’entre elles ont été mises à jour pour prendre en charge les MSI, et donnent des exemples illustrant leur utilisation. Cette liste a été mise à jour, suite à l’ajout d’une prise en charge supplémentaire :

| Foundation | Exemple |
| --- | ------ | 
| Python | [Créer une machine virtuelle avec le paramètre MSI activé](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Créer une machine virtuelle Azure avec une MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>étapes suivantes

- Pour découvrir comment configurer une identité MSI affectée par l’utilisateur sur une machine virtuelle Azure, consultez les articles connexes sous « Configurer MSI pour une machine virtuelle Azure ».

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
