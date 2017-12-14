---
title: "Exiger l’authentification multifacteur | Microsoft Docs"
description: "Découvrez comment exiger l’application de la solution MFA pour les identités dotées de privilèges avec l’extension Azure Active Directory Privileged Identity Management."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: faee62bdaca3f80fdd8f6be8aaf28c881314333a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Comment exiger l’authentification MFA dans Azure AD Privileged Identity Management
Il est vivement recommandé d’exiger l’application de la solution Multi-Factor Authentication (MFA) pour tous vos administrateurs. Cela réduit le risque d'attaque en raison d'un mot de passe compromis.

Vous pouvez exiger que les utilisateurs se soumettent à une authentification MFA lors de leur connexion. Le billet de blog [MFA pour Office 365 et MFA pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compare ce qui est inclus dans Office et les abonnements Azure avec les fonctionnalités contenues dans l'offre Microsoft Azure Multi-Factor Authentication.

Vous pouvez également exiger que les utilisateurs effectuent une demande d'authentification MFA lorsqu’ils activent un rôle dans Azure AD PIM. De cette façon, si l'utilisateur n'a pas terminé une demande d'authentification MFA lors de sa connexion, il sera invité à le faire par PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Exigence d’application de la solution MFA dans Azure AD Privileged Identity Management
Quand vous gérez des identités dans PIM en tant qu’administrateur de rôle privilégié, vous pouvez voir des alertes qui recommandent l’authentification MFA pour des comptes privilégiés. Cliquez sur l’alerte de sécurité dans le tableau de bord PIM. Un nouveau panneau s’ouvre, affichant la liste des comptes administrateur qui doivent exiger l’authentification MFA.  Vous pouvez exiger l’authentification MFA en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Corriger**. Vous pouvez également cliquer sur les points de suspension en regard d’un rôle, puis sur le bouton **Corriger**.

> [!IMPORTANT]
> L’authentification multifacteur Azure ne fonctionne actuellement qu’avec des comptes professionnels ou scolaires et pas avec des comptes Microsoft (généralement un compte personnel utilisé pour se connecter à des services Microsoft tels que Skype, Xbox, Outlook.com, etc.). Pour cette raison, toute personne disposant d’un compte Microsoft ne peut pas être un administrateur éligible car elle ne peut pas utiliser l’authentification multifacteur pour activer leur rôle. Si ces utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, convertissez-les en administrateurs permanents pour l’instant.
> 
> 

En outre, vous pouvez modifier l'exigence d’authentification MFA pour un rôle spécifique en cliquant dessus dans la section Rôles du tableau de bord PIM. Cliquez ensuite sur **Paramètres** dans le panneau de rôle, puis sélectionnez **Activer** sous l’authentification multifacteur.

## <a name="how-azure-ad-pim-validates-mfa"></a>Validation de Multi-Factor Authentication (MFA) par Azure AD PIM
Il existe deux options pour valider l'authentification multifacteur lorsqu’un utilisateur active un rôle.

Le plus simple consiste à s’appuyer sur Azure MFA pour les utilisateurs qui activent un rôle privilégié. Pour ce faire, vérifiez tout d’abord que ces utilisateurs bénéficient d’une licence si nécessaire, et qu’il se sont enregistrés pour l’authentification Azure MFA. Pour plus d’informations, consultez [Prise en main d’Azure Multi-Factor Authentication dans le cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Il est recommandé, mais pas obligatoire, de configurer Azure AD pour appliquer l’authentification MFA à ces utilisateurs lorsqu’ils se connectent. En effet, les vérifications de l’authentification multifacteur sont effectuées par Azure AD PIM directement.

Si les utilisateurs s’authentifient en local, vous pouvez également faire en sorte que votre fournisseur d’identité soit responsable de l’authentification MFA. Par exemple, si vous avez configuré des services ADFS pour exiger l’authentification par carte à puce avant d’accéder à Azure AD, la section [Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et ADFS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclut des instructions pour configurer ADFS afin d’envoyer les revendications à Azure AD. Lorsqu’un utilisateur tente d’activer un rôle, Azure AD PIM accepte cette authentification MFA déjà validée pour l’utilisateur dès réception des revendications appropriées.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

