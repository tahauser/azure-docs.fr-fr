---
title: 'Services de domaine Azure AD : Activer la synchronisation de mot de passe | Microsoft Docs'
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0f6204e8f0f779809cd9c657acbcbcf39d57d481
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
| --- | --- |
| **Comptes d’utilisateur synchronisés à partir d’un répertoire local** |**&#x2713;** [Suivez les instructions de cet article](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Comptes d’utilisateur cloud créés dans Azure AD** |**&#x2713;** [Synchroniser les mots de passe des comptes d’utilisateur dans le cloud uniquement avec votre domaine managé](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **Vous devrez peut-être effectuer les deux ensembles d’étapes.**
> Si votre locataire Azure AD utilise une combinaison d’utilisateurs dans le cloud uniquement et d’utilisateurs provenant de votre répertoire Active Directory local, vous devez effectuer ces deux ensembles d’étapes.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Tâche 5 : activer la synchronisation de mot de passe avec votre domaine géré pour les comptes d’utilisateurs synchronisés avec votre répertoire Active Directory local
Un client Azure AD connecté est défini pour se synchroniser avec le répertoire local de votre organisation à l’aide d’Azure AD Connect. Par défaut, Azure AD Connect ne synchronise pas les hachages des informations d’identification NTLM et Kerberos avec Azure AD. Pour utiliser les Services de domaine Azure AD, vous devez configurer Azure AD Connect pour synchroniser les hachages d’informations d’identification requis pour l’authentification NTLM et Kerberos. Les étapes suivantes permettent la synchronisation des hachages d’informations d’identification avec votre local Azure AD à partir de votre répertoire local.

> [!NOTE]
> **Si votre organisation utilise des comptes d’utilisateur qui sont synchronisés à partir de votre répertoire local, vous devez activer la synchronisation des hachages NTLM et Kerberos afin d’utiliser le domaine géré.** Un compte d’utilisateur synchronisé est un compte qui a été créé dans votre répertoire local puis synchronisé avec votre locataire Azure AD à l’aide d’Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Installer ou mettre à jour Azure AD Connect
Installer la dernière version recommandée d’Azure AD Connect sur un ordinateur joint à un domaine. Si une instance d’Azure AD Connect est déjà configurée, vous devez la mettre à jour pour utiliser la dernière version d’Azure AD Connect. Utilisez toujours la dernière version d’Azure AD Connect afin d’éviter les problèmes/bogues connus déjà corrigés.

**[Télécharger Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Version recommandée : **1.1.614.0**, publiée le 5 septembre 2017.

> [!WARNING]
> Vous DEVEZ installer la dernière version recommandée d’Azure AD Connect pour que les informations d’identification de mot de passe héritées (nécessaires pour l’authentification NTLM et Kerberos) puissent être synchronisées avec votre client Azure AD. Cette fonctionnalité n’est pas disponible dans les versions antérieures d’Azure AD Connect ou avec l’outil DirSync hérité.
>
>

Les instructions d’installation d’Azure AD Connect sont disponibles dans l’article suivant : [Prise en main d’Azure AD Connect](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Activez la synchronisation des hachages des informations d’identification NTLM et Kerberos avec Azure AD
Exécutez le script PowerShell suivant sur chaque forêt AD. Le script permet la synchronisation des hachages de mot de passe NTLM et Kerberos de l’ensemble des utilisateurs locaux avec votre locataire Azure AD. Il lance également une synchronisation complète dans Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

En fonction de la taille de votre répertoire (nombre d’utilisateurs, de groupes, etc.), la synchronisation des hachages d’informations d’identification avec Azure AD peut prendre du temps. Les mots de passe seront utilisables sur le domaine géré de Services de domaine Azure AD peu après la synchronisation des hachages d'informations d'identification avec Azure AD.

<br>

## <a name="related-content"></a>Contenu connexe
* [Activer la synchronisation de mot de passe pour les services de domaine AAD pour un répertoire Azure AD uniquement dans le cloud](active-directory-ds-getting-started-password-sync.md)
* [Administrer un domaine géré par les services de domaine Azure Active Directory](active-directory-ds-admin-guide-administer-domain.md)
* [Joindre une machine virtuelle Windows à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Joindre une machine virtuelle Linux Red Hat Enterprise à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
