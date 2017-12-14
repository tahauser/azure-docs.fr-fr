---
title: "Azure Active Directory Domain Services : activer la synchronisation de mot de passe | Microsoft Docs"
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: maheshu
ms.openlocfilehash: ea65659b1d35246bc3e7235b3faed8d2a5368010
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Activer la synchronisation du mot de passe pour Azure Active Directory Domain Services
Dans les tâches précédentes, vous avez activé Azure Active Directory Domain Services pour votre locataire Azure Active Directory (Azure AD). Dans la tâche suivante, vous allez activer la synchronisation des hachages d’informations d’identification requis pour l’authentification NT LAN Manager (NTLM) et Kerberos avec Services de domaine Azure AD. Une fois la synchronisation des informations d’identification configurée, les utilisateurs peuvent se connecter au domaine managé à l’aide de leurs informations d’identification d’entreprise.

Les étapes nécessaires sont différentes pour les comptes d’utilisateurs uniquement dans le cloud par rapport aux comptes d’utilisateurs qui sont synchronisés à partir de votre répertoire local à l’aide d’Azure AD Connect. 

<br>
| **Type de compte d’utilisateur** | **Étapes à effectuer** |
| --- |---|
| **Comptes d’utilisateur cloud créés dans Azure AD** |**&#x2713;** [Suivez les instructions de cet article](active-directory-ds-getting-started-password-sync.md#task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts) |
| **Comptes d’utilisateur synchronisés à partir d’un répertoire local** |**&#x2713;** [Synchroniser les mots de passe des comptes d’utilisateur synchronisés à partir de votre répertoire AD local avec votre domaine managé](active-directory-ds-getting-started-password-sync-synced-tenant.md) | 

<br>

> [!TIP]
> **Vous devrez peut-être effectuer les deux ensembles d’étapes.**
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs dans le cloud uniquement et d’utilisateurs provenant de votre répertoire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-cloud-only-user-accounts"></a>Tâche 5 : activer la synchronisation de mot de passe avec votre domaine géré pour les comptes d’utilisateurs uniquement dans le cloud
Pour authentifier les utilisateurs sur le domaine managé, Azure AD DS exige que les hachages d’informations d’identification présentent un format adapté à l’authentification NTLM et Kerberos. Azure AD ne génère pas et ne stocke pas les hachages d’informations d’identification dans le format requis pour l’authentification NTLM ou Kerberos, à moins que vous n’activiez Azure Active Directory Domain Services pour votre locataire. Pour des raisons évidentes de sécurité, Azure AD ne stocke pas non plus d’informations de mot de passe dans un format en texte clair. Par conséquent, Azure AD n’a pas la capacité de générer automatiquement ces hachages d’informations d’identification NTLM ou Kerberos en fonction des informations d’identification existantes des utilisateurs.

> [!NOTE]
> **Si votre organisation utilise des comptes d’utilisateur dans le cloud uniquement, tous les utilisateurs ayant besoin d’Azure Active Directory Domain Services doivent modifier leur mot de passe.** Un compte d’utilisateur uniquement dans le cloud est un compte qui a été créé dans votre répertoire Azure AD à l’aide du portail Azure ou d’applets de commande PowerShell Azure AD. Ces comptes d’utilisateurs ne sont pas synchronisés à partir d’un répertoire local.
>
>

Le processus de modification du mot de passe entraîne la génération, dans Azure AD, des hachages d’informations d’identification requis par Azure AD DS pour l’authentification Kerberos et NTLM. Vous pouvez faire expirer les mots de passe de tous les utilisateurs du locataire qui doivent recourir à Azure AD DS, ou demander à ces utilisateurs de modifier leur mot de passe.

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-user-account"></a>Activer la génération du hachage des informations d’identification NTLM et Kerberos pour un compte d’utilisateur dans le cloud uniquement
Voici les instructions que vous devez fournir aux utilisateurs pour qu’ils puissent modifier leur mot de passe :

1. Accédez à la [page du volet d’accès Azure AD](http://myapps.microsoft.com) de votre organisation.

    ![Ouvrez le panneau d’accès Azure AD](./media/active-directory-domain-services-getting-started/access-panel.png)

2. Dans l’angle supérieur droit, cliquez sur votre nom, puis sélectionnez **Profil** dans le menu.

    ![Sélectionner un profil](./media/active-directory-domain-services-getting-started/select-profile.png)

3. Sur la page **profil**, cliquez sur **Modifier le mot de passe**.

    ![Cliquez sur « Modifier le mot de passe »](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!TIP]
   > Si l’option **Modifier le mot de passe** ne s’affiche pas sur la fenêtre du volet d’accès, vérifiez que votre organisation a configuré la [gestion des mots de passe dans Azure AD](../active-directory/active-directory-passwords-getting-started.md).
   >
   >
4. Sur la page **Modifier le mot de passe**, saisissez votre ancien mot de passe, puis tapez un nouveau mot de passe et confirmez-le.

    ![Créer un réseau virtuel pour les services de domaine Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. Cliquez sur **Envoyer**.

Quelques minutes après cette modification, le nouveau mot de passe est utilisable Azure AD DS. Au bout de 20 minutes environ, vous pouvez vous connecter aux ordinateurs joints au domaine managé à l’aide du nouveau mot de passe.

## <a name="related-content"></a>Contenu connexe
* [Comment mettre à jour votre mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md)
* [Prise en main de la gestion de mot de passe dans Azure AD](../active-directory/active-directory-passwords-getting-started.md)
* [Activer la synchronisation de mot de passe dans Azure Active Directory Domain Services pour un locataire Azure AD synchronisé](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [Administrer un domaine managé par Azure Active Directory Domain Services ](active-directory-ds-admin-guide-administer-domain.md)
* [Joindre une machine virtuelle Windows à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Red Hat Enterprise Linux à un domaine managé par Azure Active Directory Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
