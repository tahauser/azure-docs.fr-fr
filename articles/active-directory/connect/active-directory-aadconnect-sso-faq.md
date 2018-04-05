---
title: 'Azure AD Connect : authentification unique transparente - Questions fréquentes | Microsoft Docs'
description: Réponse à des questions fréquentes sur l’authentification unique transparente Azure Active Directory.
services: active-directory
keywords: Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 819d8ce9793f785726f55a89d49d08d818401b33
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Authentification unique transparente Azure Active Directory : questions fréquentes

Dans cet article, nous répondons au forum aux questions sur l’authentification unique et transparente Azure Active Directory. N’hésitez pas à le consulter régulièrement, du contenu nouveau y est fréquemment ajouté.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Avec quelles méthodes de connexion l’authentification unique transparente est-elle compatible ?

L’authentification unique transparente peut être combinée avec la [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-hash-synchronization.md) et [l’authentification directe](active-directory-aadconnect-pass-through-authentication.md). Toutefois cette fonctionnalité ne peut pas être utilisée avec les services de fédération Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>La fonctionnalité d’authentification unique transparente est-elle gratuite ?

L’authentification unique transparente est une fonctionnalité gratuite et il n’est pas utile de disposer des éditions payantes d’Azure AD pour l’utiliser.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>L’authentification unique transparente est-elle disponible dans le [cloud Microsoft Azure Allemagne](http://www.microsoft.de/cloud-deutschland) et le [cloud Microsoft Azure Government](https://azure.microsoft.com/features/gov/) ?

Non. L’authentification unique transparente est uniquement disponible dans l’instance à l’échelle mondiale d’Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Quelles sont les applications qui tirent parti des paramètres `domain_hint` et `login_hint` de l’authentification unique transparente ?

Vous trouverez ci-dessous une liste non exhaustive des applications qui envoient ces paramètres à Azure AD et permettent donc aux utilisateurs de se connecter de manière silencieuse à l’aide de l’authentification unique transparente (par ex. les utilisateurs n’ont pas besoin d’entrer leur nom d’utilisateur) :

| Nom de l’application | URL d’application à utiliser |
| -- | -- |
| Panneau d’accès | myapps.microsoft.com/contoso.com |
| Outlook sur le Web | outlook.office365.com/contoso.com |

En outre, les utilisateurs obtiennent également une expérience de connexion silencieuse si une application envoie des requêtes aux points de terminaison desquels Azure AD est abonné, c'est-à-dire, https://login.microsoftonline.com/contoso.com/<..> ou https://login.microsoftonline.com/<tenant_ID>/<..>, au lieu du point de terminaison commun d’Azure AD, c'est-à-dire, https://login.microsoftonline.com/common/<...>. Vous trouverez ci-dessous une liste non exhaustive d’applications qui rendent ces types de requêtes de connexion.

| Nom de l’application | URL d’application à utiliser |
| -- | -- |
| SharePoint Online | contoso.sharepoint.com |
| Portail Azure | portal.azure.com/contoso.com |

Dans les tables ci-dessus, remplacez « contoso.com » par votre nom de domaine pour obtenir les URL d’application de votre abonné.

Si vous souhaitez que d’autres applications utilisent notre expérience d’authentification en mode silencieux, faites-le nous savoir dans la section des commentaires.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>L’authentification unique transparente prend-elle en charge `Alternate ID` comme nom d’utilisateur à la place de `userPrincipalName` ?

Oui. L’authentification unique transparente prend en charge `Alternate ID` comme nom d’utilisateur dans Azure AD Connect comme indiqué [ici](active-directory-aadconnect-get-started-custom.md). Toutes les applications Office 365 ne prennent pas en charge `Alternate ID`. Reportez-vous à la documentation de l’application qui vous intéresse pour avoir des précisions sur sa prise en charge.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Quelle est la différence entre l’expérience d’authentification unique fournie par [Azure AD Join](../active-directory-azureadjoin-overview.md) et l’authentification unique transparente ?

[Azure AD Join](../active-directory-azureadjoin-overview.md) fournit l’authentification unique aux utilisateurs si leurs appareils sont inscrits auprès d’Azure AD. Ces appareils ne doivent pas nécessairement être joints au domaine. L’authentification unique est fournie à l’aide de *jetons d’actualisation principaux* ou *PRTs*, et non Kerberos. L’expérience utilisateur est optimale sur des appareils Windows 10. L’authentification unique s’effectue automatiquement sur le navigateur Edge. Elle fonctionne également sur Chrome avec une extension de navigateur.

Vous pouvez utiliser Azure AD Join et l’authentification unique transparente sur votre client. Ces deux fonctionnalités sont complémentaires. Si les deux fonctionnalités sont activées, l’authentification unique à partir d’Azure AD Join est prioritaire sur l’authentification unique transparente.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Je souhaite inscrire des appareils non Windows 10 auprès d’Azure AD sans utiliser les services AD FS. Puis-je à la place utiliser l’authentification unique transparente ?

Oui, ce scénario nécessite la version 2.1 ou ultérieure du [client workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Comment puis-je substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur ?

Il est important de fréquemment substituer la clé de déchiffrement Kerberos du `AZUREADSSOACC` compte d’ordinateur (c’est-à-dire Azure AD) créé dans votre forêt AD locale.

>[!IMPORTANT]
>Nous vous recommandons vivement de substituer la clé de déchiffrement Kerberos au moins tous les 30 jours.

Procédez comme suit sur le serveur local où vous exécutez Azure AD Connect :

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Étape 1. Obtenez la liste des forêts AD dans lesquelles l’authentification unique transparente a été activée.

1. Commencez par télécharger et installer l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).
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
- [**Immersion technique**](active-directory-aadconnect-sso-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants rencontrés avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.
