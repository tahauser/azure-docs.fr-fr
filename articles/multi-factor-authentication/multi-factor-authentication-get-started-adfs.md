---
title: "Vérification en deux étapes et AD FS : Azure MFA | Microsoft Docs"
description: "Ceci est la page d'Azure Multi-Factor Authentication qui explique la prise en main d’Azure MFA et des services AD FS."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: 7b597f45a13c4f31c662a7832d9571dcc6b25e52
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Prise en main d’Azure Multi-Factor Authentication et des services de fédération Active Directory (AD FS)
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si votre organisation a fédéré votre Active Directory local avec Azure Active Directory à l’aide d’AD FS, vous disposez de deux options pour l’utilisation d’Azure Multi-Factor Authentication.

* Sécuriser les ressources de cloud à l’aide de l’authentification multifacteur Azure ou des services de fédération Active Directory (AD FS)
* Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication

Le tableau suivant résume le processus de vérification avec la sécurisation des ressources à l’aide d’Azure Multi-Factor Authentication et AD FS

| Processus de vérification : applications basées sur le navigateur | Processus de vérification : applications hors navigateur |
|:--- |:--- |:--- |
| Sécurisation des ressources Azure AD à l'aide d’Azure Multi-Factor Authentication |<li>La première étape de vérification est effectuée en local à l’aide d’AD FS.</li> <li>La seconde étape est une méthode par téléphone effectuée à l’aide de l’authentification cloud.</li> |
| Sécurisation des ressources Azure AD à l'aide des services ADFS |<li>La première étape de vérification est effectuée en local à l’aide d’AD FS.</li><li>La seconde étape est effectuée localement en répondant à la demande.</li> |

Mises en garde relatives aux mots de passe d'application pour les utilisateurs fédérés :

* Les mots de passe d’applications sont vérifiés à l’aide de l’authentification cloud et contournent donc la fédération. La fédération n’est utilisée activement que lorsque vous configurez un mot de passe d’application.
* Les paramètres de contrôle d’accès client locaux ne sont pas honorés par les mots de passe d’application.
* Vous perdez la fonctionnalité de journalisation-authentification locale pour les mots de passe d’application.
* La désactivation/suppression de compte peut mettre jusqu’à 3 heures pour synchroniser les répertoires, ce qui peut retarder la désactivation/suppression des mots de passe d’application dans l’identité de cloud.

Pour plus d’informations sur la configuration d’Azure Multi-Factor Authentication ou du serveur Azure Multi-Factor Authentication avec AD FS, consultez les articles suivants :

* [Sécuriser les ressources cloud à l’aide d’Azure Multi-Factor Authentication et AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
* [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
* [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
