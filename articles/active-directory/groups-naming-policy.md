---
title: "Paramètres de stratégie de noms de groupes pour les groupes Office 365 dans Azure Active Directory (préversion) | Microsoft Docs"
description: "Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory (préversion)"
services: active-directory
documentationcenter: 
author: curtand
manager: michael.tillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: cc3ea7f81a924f3f4baa6fd2866c4e552b7c160e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Appliquer une stratégie de nommage pour les groupes Office 365 dans Azure Active Directory (préversion)

Pour appliquer des conventions de nommage cohérentes pour les groupes Office 365 créés ou modifiés par vos utilisateurs, configurez une stratégie de nommage de groupes pour vos locataires dans Azure Active Directory (Azure AD). Par exemple, vous pouvez utiliser la stratégie de nommage pour communiquer la fonction d’un groupe, l’appartenance, la région géographique ou le nom de la personne qui a créé le groupe. Vous pouvez aussi utiliser la stratégie de nommage pour faciliter le classement des groupes dans le carnet d’adresses. Vous pouvez utiliser la stratégie pour empêcher l’utilisation de certains mots dans les noms et les alias de groupes.

> [!IMPORTANT]
> Chaque utilisateur unique membre d’un ou plusieurs groupes Office 365 a besoin d’une licence Azure Active Directory Premium P1 pour pouvoir utiliser la stratégie de noms de groupes Office 365 en préversion.

La stratégie de nommage s’applique à la création ou à la modification des groupes créés dans toutes les charges de travail (par exemple, Outlook, Microsoft Teams, SharePoint, Exchange ou Planner). Elle s’applique à la fois au nom de groupe et à l’alias de groupe. Si vous avez configuré votre stratégie de nommage dans Azure AD et qu’il existe une stratégie de nommage de groupes Exchange, la stratégie de nommage Active AD est appliquée.

## <a name="naming-policy-features"></a>Caractéristiques de la stratégie de nommage
Vous pouvez appliquer une stratégie de nommage pour des groupes Office 365 de deux manières différentes :

-   **Stratégie de nommage avec préfixe-suffixe** : vous pouvez définir des préfixes ou des suffixes qui sont par la suite ajoutés automatiquement pour appliquer une convention de nommage à vos groupes (par exemple, dans le nom de groupe « GRP\_JAPON\_Mon groupe\_Ingénierie », GRP\_JAPON\_ est le préfixe et \_Ingénierie est le suffixe). 

-   **Mots bloqués personnalisés** : vous pouvez charger un ensemble de mots bloqués propres à votre organisation pour interdire leur utilisation dans les groupes créés par les utilisateurs (par exemple, « PDG, Paie, RH »).

### <a name="prefix-suffix-naming-policy"></a>Stratégie de nommage avec préfixe-suffixe

La structure générale de la convention de nommage est « Préfixe[NomGroupe]Suffixe ». Même s’il ne vous est pas interdit de définir plusieurs préfixes et suffixes, seule une instance de [NomGroupe] doit figurer dans le paramètre. Les préfixes ou les suffixes peuvent être des chaînes fixes ou des attributs utilisateur tels que \[Service\] qui sont substitués en fonction de l’utilisateur qui crée le groupe. Le nombre total de caractères autorisés pour les chaînes combinées de préfixe et de suffixe est de 53 caractères. 

Les préfixes et les suffixes peuvent contenir les caractères spéciaux pris en charge dans un nom de groupe et un alias de groupe. Les caractères présents dans le préfixe ou le suffixe qui ne sont pas pris en charge dans l’alias de groupe sont quand même appliqués dans le nom de groupe, mais ils sont supprimés de l’alias de groupe. Compte tenu de cette restriction, les préfixes et les suffixes appliqués au nom de groupe peuvent être différents de ceux appliqués à l’alias de groupe. 

#### <a name="fixed-strings"></a>Chaînes fixes

Vous pouvez utiliser des chaînes pour faciliter l’analyse et la différenciation des groupes dans la liste d’adresses globale et dans les liens de navigation de gauche des charges de travail de groupe. Certains préfixes courants sont des mots clés tels que « Nom\_Grp », « \#Nom », «\_Nom »

#### <a name="user-attributes"></a>Attributs utilisateur

Vous pouvez utiliser des attributs pour vous aider, à vous et vos utilisateurs, à identifier le service, le bureau ou la région géographique pour lequel/laquelle le groupe a été créé. Par exemple, si vous définissez la stratégie de nommage `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”` et `User’s department = Engineering`, vous pouvez obtenir le nom de groupe appliqué « GRP Mon groupe Ingénierie ». Les attributs Azure AD pris en charge sont \[Department\], \[Company\], \[Office\], \[StateOrProvince\], \[CountryOrRegion \], \[Title\]. Les attributs utilisateur non pris en charge sont traités comme des chaînes fixes ; par exemple, « \[postalCode\] ». Les attributs d’extension et les attributs personnalisés ne sont pas pris en charge.

Nous vous recommandons d’utiliser des attributs dont les valeurs sont remplies pour tous les utilisateurs de votre organisation et évitez d’utiliser des attributs ayant des valeurs de type Long.

### <a name="custom-blocked-words"></a>Mots bloqués personnalisés

Une liste de mots bloqués est une liste d’expressions séparées par des virgules dont l’utilisation est interdite dans les noms et alias de groupe. Aucune recherche de sous-chaîne n’est effectuée. Une correspondance exacte entre le nom de groupe et un ou plusieurs mots bloqués personnalisés est nécessaire pour déclencher un échec. Sachant qu’aucune recherche de sous-chaîne n’est effectuée, les utilisateurs peuvent utiliser des mots courants tels que « Classe » même si « lasse » est un mot bloqué.

Règles de liste de mots bloqués :
- Les mots bloqués ne sont pas sensibles à la casse.
- Quand un utilisateur entre un mot bloqué dans un nom de groupe, un message d’erreur s’affiche avec le mot bloqué.
- Les mots bloqués ne sont soumis à aucune restriction de caractères.
- Le nombre d’expressions pouvant être configurées dans la liste de mots bloqués est limité à 5 000. 

### <a name="administrator-override"></a>Substitution par l’administrateur

Certains administrateurs peuvent être exemptés de ces stratégies dans toutes les charges de travail et tous les points de terminaison de groupe. Ils peuvent ainsi créer des groupes en utilisant des mots bloqués et avec leurs propres conventions de nommage. Les rôles d’administrateur exemptés de la stratégie de nommage de groupes sont indiqués ci-dessous.

- Administrateur général
- Support de niveau 1 partenaire
- Support de niveau 2 partenaire
- Administrateur de compte d’utilisateur
- Enregistreurs de répertoire

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Installer les applets de commande PowerShell pour configurer une stratégie de nommage

Veillez à désinstaller toute version ancienne du module Azure Active Directory PowerShell for Graph pour Windows PowerShell et à installer [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) avant d’exécuter les commandes PowerShell. 

1. Ouvrez l’application Windows PowerShell en tant qu’administrateur.
2. Désinstallez toute version précédente d’AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Installez la dernière version d’AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Si vous êtes invité à accéder à un référentiel non approuvé, tapez **Y**. L’installation du nouveau module peut prendre quelques minutes.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Configurer la stratégie de nommage de groupes pour un locataire à l’aide d’Azure AD PowerShell

1. Ouvrez une fenêtre Windows PowerShell sur votre ordinateur. Vous pouvez l’ouvrir sans privilèges élevés.

2. Exécutez les commandes suivantes pour préparer l’exécution des applets de commande.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  Dans l’écran **Connectez-vous à votre compte** qui s’ouvre, entrez votre compte d’administrateur et votre mot de passe pour vous connecter à votre service, puis sélectionnez **Se connecter**.

3. Suivez les étapes de [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md) pour créer des paramètres de groupe pour ce locataire.

### <a name="view-the-current-settings"></a>Afficher les paramètres actuels

1. Récupérez la stratégie de nommage active pour afficher les paramètres actuels.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Affichez les paramètres de groupe actuels.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Définir la stratégie de nommage et les mots bloqués personnalisés

1. Définissez les préfixes et les suffixes de nom de groupe dans Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Définissez les mots bloqués personnalisés sur lesquels vous voulez imposer des restrictions. L’exemple suivant illustre la façon dont vous pouvez ajouter vos propres mots personnalisés.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Enregistrez les paramètres de la nouvelle stratégie pour qu’elle prenne effet, comme dans l’exemple suivant.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
Vous avez terminé. Vous avez défini votre stratégie de nommage et ajouté vos mots bloqués.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Exporter ou importer la liste de mots bloqués personnalisés

Pour plus d’informations, consultez l’article [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md).

Voici un exemple de script PowerShell permettant d’exporter plusieurs mots bloqués :

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Voici un exemple de script PowerShell permettant d’importer plusieurs mots bloqués :

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Expériences de stratégie de nommage dans les applications Office 365

Une fois qu’une stratégie de nommage de groupes a été définie dans Azure AD, voici ce que voit un utilisateur quand il crée un groupe dans une application Office 365 : 

* Un aperçu du nom conformément à votre stratégie de nommage (avec préfixes et suffixes) à mesure que l’utilisateur tape le nom du groupe.
* Si l’utilisateur entre des mots bloqués, un message d’erreur s’affiche pour qu’il supprime ces mots.

Charge de travail | Conformité
----------- | -------------------------------
Portails Azure Active Directory | Le portail Azure AD et le portail Panneau d’accès affichent le nom appliqué de la stratégie de nommage quand l’utilisateur tape un nom de groupe pour créer ou modifier un groupe. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche avec le mot bloqué pour qu’il puisse le supprimer.
Outlook Web Access (OWA) | Outlook Web Access affiche le nom appliqué de la stratégie de nommage quand l’utilisateur tape un nom ou alias de groupe. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche dans l’interface utilisateur avec le mot bloqué pour qu’il puisse le supprimer.
Bureau Outlook | Les groupes créés dans le bureau Outlook sont conformes aux paramètres de stratégie de nommage. L’application de bureau Outlook n’affiche pas encore l’aperçu du nom de groupe appliqué et ne retourne pas d’erreurs liées aux mots bloqués personnalisés au moment où l’utilisateur entre le nom du groupe. Cependant, la stratégie de nommage est appliquée automatiquement pendant la création ou la modification d’un groupe, et les utilisateurs obtiennent des messages d’erreur si le nom ou l’alias de groupe contient des mots bloqués personnalisés.
Microsoft Teams | Microsoft Teams présente le nom appliqué de la stratégie de nommage de groupes quand l’utilisateur entre un nom d’équipe. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche avec le mot bloqué pour qu’il puisse le supprimer.
SharePoint  |  SharePoint affiche le nom appliqué de la stratégie de nommage quand l’utilisateur tape un nom de site ou une adresse e-mail de groupe. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche avec le mot bloqué pour qu’il puisse le supprimer.
Microsoft Stream | Microsoft Stream affiche le nom appliqué de la stratégie de nommage de groupes quand l’utilisateur tape un nom de groupe ou un alias d’e-mail de groupe. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche avec le mot bloqué pour qu’il puisse le supprimer.
Application Outlook pour iOS et Android | Les groupes créés dans les applications Outlook sont conformes à la stratégie de nommage configurée. L’application mobile Outlook n’affiche pas encore l’aperçu du nom appliqué de la stratégie de nommage et ne retourne pas d’erreurs liées aux mots bloqués personnalisés au moment où l’utilisateur entre le nom du groupe. Cependant, la stratégie de nommage est appliquée automatiquement quand l’utilisateur clique sur Créer ou Modifier, et des messages d’erreur s’affichent en présence de mots bloqués personnalisés dans le nom ou l’alias de groupe.
Application mobile Groups | Les groupes créés dans l’application mobile Groups sont conformes à la stratégie de nommage. L’application mobile Groups n’affiche pas l’aperçu de la stratégie de nommage et ne retourne pas d’erreurs liées aux mots bloqués personnalisés au moment où l’utilisateur entre le nom du groupe. En revanche, la stratégie de nommage est appliquée automatiquement pendant la création ou la modification d’un groupe, et les erreurs appropriées sont présentées aux utilisateurs si le nom ou l’alias de groupe contient des mots bloqués personnalisés.
Planner | Planner est conforme à la stratégie de nommage. Planner affiche un aperçu de la stratégie de nommage au moment où le nom du plan est entré. Quand un utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche pendant la création du plan.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement est conforme à la stratégie de nommage. Dynamics 365 affiche le nom appliqué de la stratégie d’appellation quand l’utilisateur tape un nom de groupe ou un alias d’e-mail de groupe. Quand l’utilisateur entre un mot bloqué personnalisé, un message d’erreur s’affiche avec le mot bloqué pour qu’il puisse le supprimer.
School Data Sync (SDS) | Les groupes créés via SDS sont conformes à la stratégie de nommage, mais celle-ci ne s’applique pas automatiquement. Les administrateurs SDS doivent ajouter les préfixes et les suffixes aux noms de classes pour lesquels des groupes doivent être créés puis chargés dans SDS. À défaut, la création ou la modification de groupe échoue.
Outlook Customer Manager (OCM) | Outlook Customer Manager est conforme à la stratégie de nommage, qui est appliquée automatiquement au groupe créé dans Outlook Customer Manager. Si un mot bloqué personnalisé est détecté, la création de groupe dans OCM est bloquée, et l’utilisateur ne peut pas utiliser l’application OCM.
Application Classroom | Les groupes créés dans l’application Classroom sont conformes à la stratégie de nommage, mais celle-ci ne s’applique pas automatiquement et l’aperçu de la stratégie de nommage n’est pas présenté aux utilisateurs quand ils entrent un nom de groupe de classe. Les utilisateurs doivent entrer le nom de groupe de classe appliqué avec les préfixes et les suffixes. Dans le cas contraire, l’opération de création ou de modification du groupe de classe échoue avec des erreurs.
Power BI | Les espaces de travail Power BI sont conformes à la stratégie de nommage.    
Yammer | Les groupes connectés Yammer n’appliquent pas la stratégie de nommage configurée. Pour les organisations qui ont activé une stratégie de nommages, Yammer crée des groupes Yammer hérités qui ne sont pas connectés à Office 365 pour les groupes non conformes à la stratégie de nommage.
StaffHub  | Les équipes StaffHub ne respectent pas la stratégie de nommage, mais le groupe Office 365 sous-jacent, oui. Le nom de l’équipe StaffHub n’applique pas les préfixes et les suffixes et ne recherche pas les mots bloqués personnalisés. En revanche, StaffHub applique les préfixes et les suffixes et supprime les mots bloqués du groupe Office 365 sous-jacent.
Exchange PowerShell | Les applets de commande Exchange PowerShell sont conformes à la stratégie de nommage. Les utilisateurs reçoivent des messages d’erreur appropriés avec les préfixes et les suffixes suggérés et pour les mots bloqués personnalisés s’ils ne suivent pas la stratégie de nommage dans le nom de groupe et l’alias de groupe (mailNickname).
Applets de commande Azure Active Directory PowerShell | Les applets de commande Azure Active Directory PowerShell sont conformes à la stratégie de nommage. Les utilisateurs reçoivent des messages d’erreur appropriés avec les préfixes et les suffixes suggérés et pour les mots bloqués personnalisés s’ils ne suivent pas la convention de nommage dans les noms de groupe et les alias de groupe.
Centre d’administration Exchange | Le centre d’administration Exchange est conforme à la stratégie de nommage. Les utilisateurs reçoivent des messages d’erreur appropriés avec les préfixes et les suffixes suggérés et pour les mots bloqués personnalisés s’ils ne suivent pas la convention de nommage dans le noms de groupe et l’alias de groupe.
Centre d’administration Office 365 | Le centre d’administration Office 365 est conforme à la stratégie de nommage. Quand un utilisateur crée ou modifie des noms de groupes, la stratégie de nommage s’applique automatiquement, et les utilisateurs reçoivent les erreurs appropriées quand ils entrent des mots bloqués personnalisés. Le centre d’administration Office 365 n’affiche pas encore un aperçu de la stratégie de nommage et ne retourne pas d’erreurs liées aux mots bloqués personnalisés au moment où l’utilisateur entre le nom du groupe.

## <a name="next-steps"></a>étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure AD.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Stratégie d’expiration pour les groupes Office 365](active-directory-groups-lifecycle-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
