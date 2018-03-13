---
title: Authentification par certificat Azure Active Directory sur iOS | Microsoft Docs
description: "En savoir plus sur les scénarios pris en charge et la configuration requise pour la configuration de l’authentification par certificat dans les solutions avec les appareils iOS"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: mtillman
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c9ae098df6e2357ca4bda513c0de5f5d42c7d9af
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Authentification par certificat Azure Active Directory sur iOS

L’authentification par certificat (CBA) vous permet d’être authentifié par Azure Active Directory avec un certificat client sur un appareil Windows, Android ou iOS lors de la connexion de votre compte Exchange Online à :

* Des applications mobiles Office, telles que Microsoft Outlook et Microsoft Word ;   
* Des clients Exchange ActiveSync (EAS).

La configuration de cette fonctionnalité élimine le besoin d’entrer un nom d’utilisateur et un mot de passe dans certaines applications de messagerie et Microsoft Office sur votre appareil mobile.

Cette rubrique vous indique la configuration requise et les scénarios pris en charge pour la configuration de l’authentification par certificat sur un appareil iOS (Android) pour les utilisateurs des locataires des plans Office 365 Enterprise, Business, Education, US Government, Chine et Allemagne.

Cette fonctionnalité est disponible en version préliminaire dans les plans Office 365 US Government Defense et US Government Federal.




## <a name="microsoft-mobile-applications-support"></a>Prise en charge des applications mobiles Microsoft

| Applications | Support |
| --- | --- |
| Application Azure Information Protection |![Vérification][1] |
| Portail d’entreprise Intune |![Vérification][1] |
| Microsoft Teams |![Vérification][1] |
| OneNote |![Vérification][1] |
| OneDrive |![Vérification][1] |
| Outlook |![Vérification][1] |
| Power BI |![Vérification][1] |
| Skype Entreprise |![Vérification][1] |
| Word / Excel / PowerPoint |![Vérification][1] |
| Yammer |![Vérification][1] |


## <a name="requirements"></a>Configuration requise

La version du système d’exploitation de l’appareil doit être iOS 9 ou toute version ultérieure

Un serveur de fédération doit être configuré.  

Microsoft Authenticator est requis pour les applications Office sur iOS.  

Pour qu’Azure Active Directory révoque un certificat client, le jeton ADFS doit posséder les déclarations suivantes :  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (Le numéro de série du certificat client)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (La chaîne de l’émetteur du certificat client)

Azure Active Directory ajoute ces déclarations au jeton d’actualisation si elles sont disponibles dans le jeton ADFS (ou n’importe quel autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation.

La meilleure pratique consiste à mettre à jour les pages d’erreur ADFS avec les éléments suivants :

* La configuration requise pour l’installation de Microsoft Authenticator sur iOS
* Instructions sur l’obtention d’un certificat utilisateur.

Pour plus d’informations, consultez [Personnalisation des pages de connexion AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Certaines applications Office (avec l’authentification moderne activée) envoient « *prompt=login* » à Azure AD dans leur demande. Par défaut, Azure AD traduit cela dans la demande aux services ADFS en « *wauth=usernamepassworduri* » (demande aux services ADFS d’effectuer l’authentification U/P) et « *wfresh=0* » (demande aux services ADFS d’ignorer l’état d’authentification unique et d’effectuer une nouvelle authentification). Si vous souhaitez activer l’authentification par certificat pour ces applications, vous devez modifier le comportement par défaut d’Azure AD. Définissez simplement « *PromptLoginBehavior* » dans vos paramètres de domaine fédéré sur « *Désactivé* ».
Vous pouvez utiliser l’applet de commande [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) pour effectuer cette tâche :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`


## <a name="exchange-activesync-clients-support"></a>Prise en charge des clients Exchange ActiveSync
Dans iOS 9 ou version ultérieure, le client de messagerie iOS natif est pris en charge. Pour toutes les autres applications Exchange ActiveSync, pour savoir si cette fonctionnalité est prise en charge, contactez le développeur de votre application.  


## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer l’authentification par certificat dans votre environnement, consultez [Bien démarrer avec l’authentification par certificat sur Android](active-directory-certificate-based-authentication-get-started.md) pour obtenir des instructions.


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
