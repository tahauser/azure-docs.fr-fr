---
title: "Azure AD Connect : authentification unique transparente - Questions fréquentes | Microsoft Docs"
description: "Réponse à des questions fréquentes sur l’authentification unique transparente Azure Active Directory."
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 8eb575d6647b123119ceff9452ee8fc4a489f1ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Authentification unique transparente Azure Active Directory : questions fréquentes

Dans cet article, nous répondons au forum aux questions sur l’authentification unique et transparente Azure Active Directory. N’hésitez pas à le consulter régulièrement, du contenu nouveau y est fréquemment ajouté.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Avec quelles méthodes de connexion l’authentification unique transparente est-elle compatible ?

L’authentification unique transparente peut être combinée avec la [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) et [l’authentification directe](active-directory-aadconnect-pass-through-authentication.md). Toutefois cette fonctionnalité ne peut pas être utilisée avec les services de fédération Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>La fonctionnalité d’authentification unique transparente est-elle gratuite ?

L’authentification unique transparente est une fonctionnalité gratuite et il n’est pas utile de disposer des éditions payantes d’Azure AD pour l’utiliser. Elle restera gratuite même une fois qu’elle sera mise à la disponibilité générale.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quelles sont les applications qui tirent parti des paramètres `domain_hint` et `login_hint` de l’authentification unique transparente ?

Nous compilons actuellement la liste des applications qui envoient ces paramètres et celles qui ne les envoient pas. Si vous disposez d’applications qui peuvent y figurer, faites-le nous savoir dans la section des commentaires.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L’authentification unique transparente prend-elle en charge `Alternate ID` comme nom d’utilisateur à la place de `userPrincipalName` ?

Oui. L’authentification unique transparente prend en charge `Alternate ID` comme nom d’utilisateur dans Azure AD Connect comme indiqué [ici](active-directory-aadconnect-get-started-custom.md). Toutes les applications Office 365 ne prennent pas en charge `Alternate ID`. Reportez-vous à la documentation de l’application qui vous intéresse pour avoir des précisions sur sa prise en charge.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Je souhaite inscrire des appareils non Windows 10 auprès d’Azure AD sans utiliser les services AD FS. Puis-je à la place utiliser l’authentification unique transparente ?

Oui, ce scénario nécessite la version 2.1 ou ultérieure du [client workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Comment puis-je substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur ?

Il est important de fréquemment substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur (c’est-à-dire Azure AD) créé dans votre forêt AD locale.

>[!IMPORTANT]
>Nous vous recommandons vivement de substituer la clé de déchiffrement Kerberos au moins tous les 30 jours.

Procédez comme suit sur le serveur local où vous exécutez Azure AD Connect :

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Étape 1. Obtenez la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée.

1. Commencez par télécharger et installer l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.
5. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une fenêtre contextuelle dans laquelle vous devez entrer vos informations d’identification d’administrateur général de locataire.
6. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts AD (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Étape 2. Mettre à jour la clé de déchiffrement Kerberos sur chaque forêt AD à laquelle elle a été attribuée.

1. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt AD souhaitée.
2. Appelez `Update-AzureADSSOForest -OnPremCredentials $creds`. Cette commande met à jour la clé de déchiffrement de Kerberos pour le `AZUREADSSOACC` compte de l’ordinateur et la forêt AD spécifique et dans Azure AD.
3. Répétez les étapes précédentes pour chaque forêt AD dans laquelle vous avez configuré la fonctionnalité.

>[!IMPORTANT]
>Assurez-vous de _ne pas_ exécuter la `Update-AzureADSSOForest` commande plusieurs fois. Dans le cas contraire, la fonctionnalité cesse de fonctionner jusqu’à ce que les tickets Kerberos de vos utilisateurs expirent et soient régénérés par votre annuaire Active Directory local.

## <a name="how-can-i-disable-seamless-sso"></a>Comment désactiver l’authentification unique transparente ?

L’authentification unique transparente peut être désactivée à l’aide d’Azure AD Connect.

Exécutez Azure AD Connect, cliquez sur "Modifier la connexion utilisateur" puis sur "Suivant". Ensuite, désactivez l’option "Activer l’authentification unique". Suivez les instructions de l’Assistant. À la fin de l’Assistant, l’authentification unique transparente est désactivée pour votre locataire.

Cependant, le message suivant s’affiche à l’écran :

"L’authentification unique est désormais désactivée, mais des étapes manuelles supplémentaires restent à effectuer pour terminer le nettoyage. En savoir plus."

Pour terminer l’opération, procédez comme suit sur le serveur local où vous exécutez Azure AD Connect :

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Étape 1. Obtenez la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée.

1. Commencez par télécharger et installer l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.
5. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Cette commande doit afficher une fenêtre contextuelle dans laquelle vous devez entrer vos informations d’identification d’administrateur général de locataire.
6. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts AD (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Étape 2. Supprimez manuellement le compte d’ordinateur `AZUREADSSOACCT` de chaque forêt AD répertoriée.

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-sso-quick-start.md) : découvrez l’authentification unique transparente Azure AD.
- [**Immersion technique**](active-directory-aadconnect-sso-how-it-works.md) : découvrez le mode opératoire de cette fonctionnalité.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants rencontrés avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
