---
title: "Rapport d’utilisation sans licence | Microsoft Docs"
description: "Le rapport d’utilisation sans licence vous aide à identifier les utilisateurs qui utilisent les fonctionnalités Azure AD payantes sans licence."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 92138f43-9528-4c8a-b834-66a47da476e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 8bcd814ee7b7bfc158e62ad26e6cc23781f5352d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="unlicensed-usage-report"></a>Rapport d’utilisation sans licence
Le rapport d’utilisation sans licence vous aide à identifier les utilisateurs qui utilisent les fonctionnalités Azure AD payantes sans licence. Cela vous permet de tirer un meilleur profit des licences que vous avez achetées et vous aide à déterminer le bon moment pour faire l’acquisition de licences supplémentaires. 

Le rapport présente l’utilisation active des fonctionnalités payantes au cours des 30 derniers jours. 

## <a name="report-structure"></a>Structure du rapport
| Nom de la colonne | DESCRIPTION |
|:--- |:--- |
| Utilisateur sans licence |Nom de l’utilisateur |
| Fonctionnalité |Nom de la fonctionnalité. Par exemple : accès conditionnel |
| Application utilisée |Le nom de l’application à laquelle l’utilisateur a accédé via la fonctionnalité utilisée. Par exemple : Office 365 SharePoint Online |

> [!NOTE]
> Si un compte d’utilisateur a été supprimé, un ID du type 1003000090D8B285 sera renseigné dans la colonne « Utilisateur sans licence »
> 
> 

## <a name="conditional-access-feature"></a>Fonctionnalité d’accès conditionnel
Les utilisateurs sans licence seront signalés lorsqu’ils accèdent à un service limité par une stratégie d’accès conditionnel s’ils ne disposent pas d’une licence Azure AD Premium. 

Cela s’applique aux stratégies MFA / d’emplacement, ainsi qu’aux stratégies de périphériques utilisant Intune.

## <a name="see-also"></a>Voir aussi
* [Utilisation de l’accès conditionnel avec Office 365 et les autres applications connectées à Azure Active Directory](active-directory-conditional-access-azure-portal.md)
* [Prise en main de l’accès conditionnel à Azure AD](active-directory-conditional-access-azure-portal-get-started.md) 

