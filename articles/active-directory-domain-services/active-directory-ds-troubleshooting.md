---
title: "Services de domaine Azure Active Directory : guide de dépannage | Microsoft Docs"
description: "Guide de dépannage pour les services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: maheshu
ms.openlocfilehash: 0956476931396c6455bf3e4fc7582da3bf3deb33
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Services de domaine Azure AD : guide de dépannage
Cet article fournit des conseils de dépannage pour les problèmes que vous pouvez rencontrer pendant la configuration ou l’administration des services de domaine Azure Active Directory (AD).

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Vous ne pouvez pas activer les services de domaine Azure AD pour votre annuaire Azure AD
Cette section vous permet de résoudre les erreurs quand vous essayez d’activer Azure AD Domain Services pour votre annuaire.

Choisissez les étapes de résolution qui correspondent au message d’erreur que vous rencontrez.

| **Message d’erreur** | **Résolution :** |
| --- |:--- |
| *Le nom contoso100.com est déjà utilisé sur ce réseau. Spécifiez un nom qui n’est pas utilisé.* |[Conflit de nom de domaine dans le réseau virtuel](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *Les services de domaine n’ont pas pu être activés pour ce client Azure AD. Le service ne dispose pas des autorisations adéquates pour l’application appelée « Synchronisation des services de domaine Azure AD ». Supprimez l’application appelée « Synchronisation des services de domaine Azure AD » et réessayez d’activer les services de domaine pour votre client Azure AD.* |[Les services de domaine ne disposent pas des autorisations adéquates pour l’application Synchronisation des services de domaine Azure AD.](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *Les services de domaine n’ont pas pu être activés pour ce client Azure AD. L’application de services de domaine dans votre client Azure AD n’a pas les autorisations requises pour activer les services de domaine. Supprimez l’application avec l’identificateur d’application d87dcbc6-a371-462e-88e3-28ad15ec4e64 et essayez ensuite d’activer les services de domaine pour votre client Azure AD.* |[L’application de services de domaine n’est pas configurée correctement dans votre client.](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *Les services de domaine n’ont pas pu être activés pour ce client Azure AD. L’application Microsoft Azure AD est désactivée dans votre client Azure AD. Activez l’application avec l’identificateur d’application 00000002-0000-0000-c000-000000000000 et essayez ensuite d’activer les services de domaine pour votre client Azure AD.* |[L’application Microsoft Graph est désactivée dans votre client Azure AD.](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflit de nom de domaine
**Message d’erreur :**

*Le nom contoso100.com est déjà utilisé sur ce réseau. Spécifiez un nom qui n’est pas utilisé.*

**Correction :**

Vérifiez qu’aucun domaine existant avec le même nom de domaine n’est disponible sur ce réseau virtuel. Supposons par exemple qu’un domaine appelé « contoso.com » soit déjà disponible sur le réseau virtuel sélectionné. Par la suite, vous essayez d’activer un domaine géré par les services de domaine Azure AD portant le même nom de domaine (soit « contoso.com ») sur ce réseau virtuel. Vous rencontrez un échec lorsque vous essayez d’activer les services de domaine Azure AD

en raison des conflits de noms de domaine sur ce réseau virtuel. Dans ce cas, vous devez utiliser un autre nom pour configurer votre domaine géré par les services de domaine Azure AD. Vous pouvez également annuler l’approvisionnement du domaine existant, puis passer à l’activation des services de domaine Azure AD.

### <a name="inadequate-permissions"></a>Autorisations inappropriées
**Message d’erreur :**

*Les services de domaine n’ont pas pu être activés pour ce client Azure AD. Le service ne dispose pas des autorisations adéquates pour l’application appelée « Synchronisation des services de domaine Azure AD ». Supprimez l’application appelée « Synchronisation des services de domaine Azure AD » et réessayez d’activer les services de domaine pour votre client Azure AD.*

**Correction :**

Vérifiez si votre répertoire Azure AD contient une application nommée « Synchronisation des services de domaine Azure AD ». Si cette application existe, supprimez-la, puis réactivez les services de domaine Azure AD.

Pour vérifier la présence de cette application et la supprimer le cas échant, procédez comme suit :

1. Accédez à la section **Applications** de votre annuaire Azure AD dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Sélectionnez **Toutes les applications** dans la liste déroulante **Afficher**. Sélectionnez **Tout** dans la liste déroulante **État de l’application**. Sélectionnez **Tout** dans la liste déroulante **Visibilité de l’application**.
3. Tapez **Azure AD Domain Services Sync** dans la zone de recherche. Si l’application existe, cliquez dessus, puis cliquez sur le bouton **Supprimer** dans la barre d’outils pour la supprimer.
4. Une fois que vous avez supprimé l’application, essayez de nouveau d’activer les services de domaine Azure AD.

### <a name="invalid-configuration"></a>Configuration non valide
**Message d’erreur :**

*Les services de domaine n’ont pas pu être activés pour ce client Azure AD. L’application de services de domaine dans votre client Azure AD n’a pas les autorisations requises pour activer les services de domaine. Supprimez l’application avec l’identificateur d’application d87dcbc6-a371-462e-88e3-28ad15ec4e64 et essayez ensuite d’activer les services de domaine pour votre client Azure AD.*

**Correction :**

Vérifiez si votre répertoire Azure AD contient une application nommée « AzureActiveDirectoryDomainControllerServices » (avec un identificateur d’application d87dcbc6-a371-462e-88e3-28ad15ec4e64). Si cette application existe, vous devez la supprimer, puis réactiver les services de domaine Azure AD.

Utilisez le script PowerShell suivant pour trouver l’application et la supprimer.

> [!NOTE]
> Ce script utilise les applets de commande **Azure AD PowerShell version 2**. Pour une liste complète de toutes les applets de commande disponibles et pour télécharger le module, consultez la [documentation de référence AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph désactivé
**Message d’erreur :**

Les services de domaine n’ont pas pu être activés pour ce client Azure AD. L’application Microsoft Azure AD est désactivée dans votre client Azure AD. Activez l’application avec l’identificateur d’application 00000002-0000-0000-c000-000000000000 et essayez ensuite d’activer les services de domaine pour votre client Azure AD.

**Correction :**

Vérifiez si vous avez désactivé une application avec l’identificateur 00000002-0000-0000-c000-000000000000. Cette application est l’application Microsoft Azure AD et fournit l’accès de l’API Graph à votre client Azure AD. Les services de domaine Azure AD ont besoin que cette application soit activée pour synchroniser votre client Azure AD pour votre domaine géré.

Pour résoudre cette erreur, activez cette application et réessayez d’activer les services de domaine pour votre client Azure AD.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Les utilisateurs sont incapables de se connecter aux services de domaine Asure AD gérés
Si un ou plusieurs utilisateurs de votre locataire Azure AD sont incapables de se connecter au domaine géré nouvellement créé, effectuez les étapes de dépannage suivantes :

* **Connectez-vous à l’aide du format UPN :** essayez de vous connecter en utilisant le format UPN (par exemple, « joeuser@contoso.com » au lieu du format SAMAccountName (« CONTOSO\joeuser »)). Le format SAMAccountName peut être généré automatiquement pour les utilisateurs dont le préfixe UPN est trop long ou identique à un autre utilisateur sur le domaine géré. Le format UPN garantit des données uniques au sein d’Azure AD.

> [!NOTE]
> Nous vous recommandons d’utiliser le format UPN pour vous connecter au domaine géré des services de domaine Azure AD.
>
>

* Assurez-vous d'avoir [activé la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.
* **Comptes externes** : assurez-vous que le compte d’utilisateur affecté n’est pas un compte externe dans le locataire Azure AD. Les exemples de comptes externes incluent les comptes Microsoft (par exemple, « joe@live.com ») ou les comptes d’utilisateurs d’un annuaire Azure AD externe. Dans la mesure où les services de domaine Azure AD n’ont pas d'informations d'identification pour ces comptes d'utilisateurs, ces utilisateurs ne peuvent pas se connecter au domaine géré.
* **Comptes synchronisés** : si les comptes d’utilisateurs affectés sont synchronisés à partir d’un annuaire local, vérifiez que les points suivants sont respectés :

  * Vous avez déployé la [dernière version recommandée d’Azure AD Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594)ou procédé à la mise à jour vers cette version.
  * Vous avez configuré Azure AD Connect pour [effectuer une synchronisation complète](active-directory-ds-getting-started-password-sync.md).
  * Selon la taille de votre annuaire, la mise à disposition des comptes d'utilisateurs et hachages d’informations d’identification dans les services de domaine Azure AD peut prendre du temps. Assurez-vous de patienter suffisamment longtemps avant d’effectuer une nouvelle tentative d’authentification.
  * Si le problème persiste après la vérification des étapes ci-dessus, essayez de redémarrer le service Microsoft Azure AD Sync. Ouvrez une invite de commande et exécutez les commandes suivantes sur votre ordinateur de synchronisation :

    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **Comptes cloud uniquement**: si le compte d’utilisateur affecté est un compte d’utilisateur dans le cloud uniquement, assurez-vous que l’utilisateur a modifié son mot de passe après avoir activé les services de domaine Azure AD. Cette étape permet de générer les hachures d’informations d'identification requises pour les services de domaine Azure AD.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Il existe une ou plusieurs alertes sur votre domaine géré

Découvrez comment résoudre les alertes sur votre domaine géré en vous rendant sur l’article [Dépanner les alertes](active-directory-ds-troubleshoot-alerts.md).

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Les utilisateurs supprimés de votre client Azure AD ne sont pas supprimés de votre domaine géré
Azure AD vous protège contre la suppression accidentelle d’objets utilisateur. Lorsque vous supprimez un compte d’utilisateur de votre client Azure AD, l’objet utilisateur correspondant est déplacé vers la Corbeille. Lorsque cette opération de suppression est synchronisée avec votre domaine géré, le compte d’utilisateur correspondant est marqué comme étant désactivé. Cette fonctionnalité vous permet de récupérer ou restaurer le compte d’utilisateur ultérieurement.

Le compte d’utilisateur reste dans un état désactivé dans votre domaine managé, même si vous recréez un compte d’utilisateur avec le même nom d’utilisateur principal dans votre annuaire Azure AD. Pour supprimer le compte d’utilisateur de votre domaine managé, vous devez forcer sa suppression de votre locataire Azure AD.

Pour supprimer complètement le compte d’utilisateur de votre domaine géré, supprimez définitivement l’utilisateur de votre client Azure AD. Utilisez l’applet de commande PowerShell `Remove-MsolUser` avec l’option `-RemoveFromRecycleBin`, comme décrit dans cet [article MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
