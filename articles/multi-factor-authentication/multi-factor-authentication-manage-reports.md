---
title: "Rapports d’accès et d’utilisation pour Azure MFA | Microsoft Docs"
description: "Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 696f4ae3cb479a208e73e53a9a9a437caeabd294
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation, et qui sont accessibles via le portail Azure. Le tableau ci-après répertorie les rapports disponibles :

| Rapport | Lieu | Description |
|:--- |:--- |:--- |
| Historique de l'utilisateur bloqué | Azure AD > Serveur MFA > Bloquer/débloquer des utilisateurs | Affiche l’historique des demandes de blocage et de déblocage d’utilisateurs. |
| Alertes relatives à l’utilisation et aux fraudes | Azure AD > Connexions | Fournit des informations sur l’utilisation globale, un récapitulatif par utilisateur, des informations détaillées sur les utilisateurs, ainsi que l’historique des alertes de fraude émises au cours de la plage de dates spécifiée. |
| Utilisation des composants locaux | Azure AD > Serveur MFA > Rapport d’activité | Fournit des informations sur l’utilisation globale de MFA via l’extension NPS, AD FS et le serveur MFA. |
| Historique de l'utilisateur contourné | Azure AD > Serveur MFA > Contournement à usage unique | Affiche l’historique des demandes de contournement de Multi-Factor Authentication pour un utilisateur. |
| État du serveur | Azure AD > Serveur MFA > État du serveur | Affiche l’état des serveurs Multi-Factor Authentication associés à votre compte. |

## <a name="view-reports"></a>Afficher des rapports 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, sélectionnez **Azure Active Directory** > **Serveur MFA**.
3. Sélectionnez le rapport que vous souhaitez afficher.

   <center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Génération de rapports PowerShell

Identifiez les utilisateurs qui se sont inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiez les utilisateurs qui ne se sont pas inscrits auprès de MFA à l’aide du code PowerShell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Étapes suivantes

* [Pour les utilisateurs](end-user/multi-factor-authentication-end-user.md)
* [Où déployer](multi-factor-authentication-get-started.md)
