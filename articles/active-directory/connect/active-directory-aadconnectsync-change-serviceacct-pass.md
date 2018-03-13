---
title: 'Azure AD Connect Sync : Modification du compte de service Azure AD Connect Sync | Microsoft Docs'
description: "Cette rubrique décrit la clé de chiffrement et comment l’annuler une fois le mot de passe modifié."
services: active-directory
keywords: Compte de service de synchronisation Azure AD, mot de passe
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: c22dd91f236454c774c00371bff668da245d45c6
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Modification du mot de passe du compte de service de synchronisation Azure AD
Si vous modifiez le mot de passe du service Azure AD Connect Sync, le service de synchronisation ne sera pas en mesure de démarrer correctement jusqu'à ce que vous abandonniez la clé de chiffrement et réinitialisiez le mot de passe du service Azure AD Connect Sync. 

Azure AD Connect, dans le cadre des services de synchronisation, utilise une clé de chiffrement pour stocker les mots de passe des comptes de service AD DS et Azure AD.  Ces comptes sont chiffrés avant d’être stockés dans la base de données. 

La clé de chiffrement utilisée est sécurisée à l’aide de [l’API de protection des données Windows (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx). DPAPI protège la clé de chiffrement à l’aide du **mot de passe du compte de service Azure AD Connect Sync**. 

Si vous devez modifier le mot de passe du compte de service, vous pouvez utiliser les procédures présentées dans [Abandon de la clé de chiffrement d’Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) pour y parvenir.  Ces procédures doivent également être utilisées si vous souhaitez abandonner la clé de chiffrement pour une raison quelconque.

##<a name="issues-that-arise-from-changing-the-password"></a>Problèmes survenant lors de la modification du mot de passe
Vous devez faire deux choses lorsque vous modifiez le mot de passe du compte de service.

Tout d’abord, vous devez modifier le mot de passe sous le Gestionnaire de contrôle des services Windows.  Vous verrez les erreurs suivantes jusqu’à résolution du problème :


- Si vous essayez de démarrer le service de synchronisation dans le Gestionnaire de contrôle des services Windows, vous recevez l’erreur « **Windows n’a pas pu démarrer le service Microsoft Azure AD Sync sur l’ordinateur local** ». **Erreur 1069 : Le service n’a pas démarré en raison d’un échec d’ouverture de session.**  »
- Dans l’observateur d’événements Windows, le journal des événements système contient une erreur avec **l’ID d’événement 7038** et le message « **Le service ADSync n’a pas pu se connecter avec le mot de passe actuellement configuré en raison de l’erreur suivante : le nom d’utilisateur ou mot de passe est incorrect.** »

Ensuite, sous certaines conditions, si le mot de passe est mis à jour, le service de synchronisation ne peut plus extraire la clé de chiffrement avec DPAPI. Sans la clé de chiffrement, le service de synchronisation ne peut pas déchiffrer les mots de passe requis pour la synchronisation locale vers/depuis AD et Azure AD.
Vous voyez des erreurs telles que :

- Sous le Gestionnaire de contrôle des services Windows, si vous essayez de démarrer le service de synchronisation et qu’il ne peut pas récupérer la clé de chiffrement, il échoue avec l’erreur « **Windows n’a pas pu démarrer Microsoft Azure AD Sync sur l’ordinateur local. Pour plus d’informations, consultez le journal des événements système. S’il s’agit d’un service hors Microsoft, contactez le fournisseur de services et faites référence au code d’erreur propre au service **-21451857952****.”
- Dans l’observateur d’événements Windows, le journal des événements contient une erreur avec **l’ID d’événement 6028** et le message d’erreur *”**La clé de chiffrement du serveur n’est pas accessible.**”*

Pour vous assurer que vous ne recevez pas ces erreurs, suivez les procédures de [Abandon de la clé de chiffrement Azure AD Connect Sync](#abandoning-the-azure-ad-connect-sync-encryption-key) lorsque vous modifiez le mot de passe.
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Abandon de la clé de chiffrement Azure AD Connect Sync
>[!IMPORTANT]
>Les procédures suivantes s’appliquent uniquement à Azure AD Connect version 1.1.443.0 ou antérieure.

Utilisez les procédures suivantes pour abandonner la clé de chiffrement.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Que faire si vous souhaitez abandonner la clé de chiffrement

Si vous souhaitez abandonner la clé de chiffrement, procédez comme suit.

1. [Abandonner la clé de chiffrement existante](#abandon-the-existing-encryption-key)

2. [Fournir le mot de passe du compte AD DS](#provide-the-password-of-the-ad-ds-account)

3. [Réinitialiser le mot de passe du compte Azure AD Sync](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [Lancer le service de synchronisation](#start-the-synchronization-service)

#### <a name="abandon-the-existing-encryption-key"></a>Abandon de la clé de chiffrement existante
Abandonnez la clé de chiffrement existante pour que la nouvelle clé de chiffrement puisse être créée :

1. Connectez-vous à votre serveur Azure AD Connect en tant qu’administrateur.

2. Démarrez une nouvelle session PowerShell.

3. Accédez au dossier `$env:Program Files\Microsoft Azure AD Sync\bin\`

4. Exécutez la commande `./miiskmu.exe /a`

![Utilitaire de clé de chiffrement d’Azure AD Connect Sync](media/active-directory-aadconnectsync-encryption-key/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>Fournissez le mot de passe du compte AD DS
Comme les mots de passe existants stockés dans la base de données ne peuvent plus être déchiffrés, vous devez fournir le mot de passe du compte AD DS au service de synchronisation. Le service de synchronisation chiffre les mots de passe à l’aide de la nouvelle clé de chiffrement :

1. Démarrez Synchronization Service Manager (DÉMARRER → Service de synchronisation).
</br>![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/startmenu.png)  
2. Allez dans l’onglet **Connecteurs** .
3. Sélectionnez le **connecteur AD** qui correspond à votre Active Directory local. Si vous avez plusieurs connecteurs AD, répétez les étapes suivantes pour chacun d’entre eux.
4. Sous **Actions**, sélectionnez **Propriétés**.
5. Dans la boîte de dialogue contextuelle, sélectionnez **Se connecter à la forêt Active Directory** :
6. Dans la zone de texte **Mot de passe**, tapez le mot de passe du compte AD DS. Si vous ne connaissez pas son mot de passe, vous devez le définir sur une valeur connue avant d’effectuer cette étape.
7. Cliquez sur **OK** pour enregistrer le nouveau mot de passe et fermer la boîte de dialogue contextuelle.
![Utilitaire de clé de chiffrement d’Azure AD Connect Sync](media/active-directory-aadconnectsync-encryption-key/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Réinitialisation du mot de passe du compte Azure AD Sync
Vous ne pouvez pas directement fournir le mot de passe du compte de service Azure AD au service de synchronisation. Au lieu de cela, vous devez utiliser l’applet de commande **Add-ADSyncAADServiceAccount** pour réinitialiser le compte de service Azure AD. L’applet de commande réinitialise le mot de passe du compte et le rend disponible pour le service de synchronisation :

1. Lancez une nouvelle session PowerShell sur le serveur Azure AD Connect.
2. Exécutez l’applet de commande `Add-ADSyncAADServiceAccount`.
3. Dans la boîte de dialogue contextuelle, fournissez les informations d’identification d’administrateur général d’Azure AD pour votre client Azure AD.
![Utilitaire de clé de chiffrement d’Azure AD Connect Sync](media/active-directory-aadconnectsync-encryption-key/key7.png)
4. Si cela réussit, vous verrez l’invite de commandes PowerShell.

#### <a name="start-the-synchronization-service"></a>Lancer le service de synchronisation
Maintenant que le service de synchronisation a accès à la clé de chiffrement et à tous les mots de passe dont il a besoin, vous pouvez redémarrer le service dans le Gestionnaire de contrôle des services Windows :


1. Accédez au Gestionnaire de contrôle des services Windows (DÉMARRER → Services).
2. Sélectionnez **Microsoft Azure AD Sync** et cliquez sur Redémarrer.

## <a name="next-steps"></a>Étapes suivantes
**Rubriques de présentation**

* [Azure AD Connect Sync - Présentation et personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
