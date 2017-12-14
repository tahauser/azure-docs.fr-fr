---
title: "Configurer des paramètres de groupe avec PowerShell dans Azure Active Directory | Microsoft Docs"
description: "Comment gérer les paramètres des groupes à l’aide des applets de commande Azure Active Directory ?"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 331dafc9164e315c84036fa0af11820e89066f36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Configuration des paramètres de groupe avec les applets de commande Azure Active Directory
Cet article contient des instructions concernant l’utilisation des applets de commande PowerShell Azure Active Directory (Azure AD) pour créer et mettre à jour des groupes. Ce contenu s’applique uniquement aux groupes Office 365. 

> [!IMPORTANT]
> Certains paramètres nécessitent une licence Azure Active Directory Premium P1. Pour plus d’informations, consultez le tableau [Paramètres de modèle](#template-settings).

Pour plus d’informations sur la façon d’empêcher les utilisateurs non-administrateurs de créer des groupes de *sécurité*, définissez `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` tel que décrit dans [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Les paramètres des groupes Office 365 sont configurés à l’aide d’un objet Settings et d’un objet SettingsTemplate. Au départ, vous ne voyez aucun objet Paramètres dans votre répertoire, car votre répertoire est configuré avec les paramètres par défaut. Pour changer les paramètres par défaut, vous devez créer un objet de paramètres en utilisant un modèle de paramètres. Les modèles de paramètres sont définis par Microsoft. Il existe différents modèles de paramètres. Pour configurer les paramètres du groupe Office 365 pour votre répertoire, utiliserez le modèle nommé « Group.Unified ». Pour configurer les paramètres du groupe Office 365 sur un seul groupe, utilisez le modèle nommé « Group.Unified.Guest ». Ce modèle est utilisé pour gérer l’accès invité à un groupe Office 365. 

Les applets de commande font partie du module Azure Active Directory PowerShell V2. Pour obtenir des instructions sur le téléchargement et l’installation du module sur votre ordinateur, reportez-vous à l’article [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Vous pouvez installer la version 2 du module depuis [la galerie PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Récupérer une valeur de paramètres spécifique
Si vous connaissez le nom du paramètre à récupérer, vous pouvez utiliser l’applet de commande ci-dessous pour récupérer la valeur des paramètres actuelle. Dans cet exemple, nous récupérons la valeur d’un paramètre nommé « UsageGuidelinesUrl ». Vous trouverez plus d’informations sur les paramètres de répertoire et leurs noms plus loin dans cet article.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Créer des paramètres au niveau du répertoire
Les étapes suivantes permettent de créer des paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office 365 (groupes unifiés) du répertoire.

1. Dans les applets de commande DirectorySettings, vous devez spécifier l’ID du modèle SettingsTemplate que vous voulez utiliser. Si vous ne connaissez pas cet ID, cette applet de commande renvoie la liste de tous les modèles de paramètres :
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Cet appel d’applet de commande retourne tous les modèles disponibles :
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Pour ajouter une URL d’instructions d’utilisation, vous devez d’abord obtenir l’objet SettingsTemplate qui définit la valeur de l’URL d’instructions d’utilisation ; autrement dit, le modèle Group.Unified :
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Ensuite, créez un nouvel objet Settings basé sur ce modèle :
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Puis mettez à jour la valeur des instructions d’utilisation :
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. Enfin, appliquez les paramètres :
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Une fois terminée, l’applet de commande retourne l’ID du nouvel objet de paramètres :
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Paramètres de modèle
Voici les paramètres définis dans l’objet SettingsTemplate Group.Unified. Sauf indication contraire, ces fonctionnalités nécessitent une licence Azure Active Directory Premium P1. 

| **Paramètre** | **Description** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Type : booléen<li>Par défaut : True |Indicateur spécifiant si la création de groupes unifiés est autorisée dans le répertoire par les utilisateurs non administrateurs. Ce paramètre ne nécessite pas une licence Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Type : string<li>Valeur par défaut : “” |GUID du groupe de sécurité pour lequel les membres sont autorisés à créer des groupes unifiés même lorsque EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Type : string<li>Valeur par défaut : “” |Lien vers les instructions d’utilisation du groupe. |
|  <ul><li>ClassificationDescriptions<li>Type : string<li>Valeur par défaut : “” | Liste séparée par des virgules des descriptions de classification. |
|  <ul><li>DefaultClassification<li>Type : string<li>Valeur par défaut : “” | Classification qui doit être utilisée en tant que classement par défaut pour un groupe si aucune classification n’a été spécifiée.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Type : string<li>Valeur par défaut : “” | Ne pas utiliser. Pas encore implémenté. |
| <ul><li>CustomBlockedWordsList<li>Type : string<li>Valeur par défaut : “” | Ne pas utiliser. Pas encore implémenté. |
| <ul><li>EnableMSStandardBlockedWords<li>Type : booléen<li>Par défaut : « False » | Ne pas utiliser
|  <ul><li>AllowGuestsToBeGroupOwner<li>Type : booléen<li>Par défaut : False | Valeur booléenne indiquant si un utilisateur invité peut être ou non un propriétaire de groupes. |
|  <ul><li>AllowGuestsToAccessGroups<li>Type : booléen<li>Par défaut : True | Valeur booléenne indiquant si un utilisateur invité peut avoir ou non accès au contenu des groupes unifiés.  Ce paramètre ne nécessite pas une licence Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Type : string<li>Valeur par défaut : “” | URL d’un lien vers les instructions d’utilisation de l’invité. |
|  <ul><li>AllowToAddGuests<li>Type : booléen<li>Par défaut : True | Une valeur booléenne indiquant si l’utilisateur est autorisé ou non à ajouter des invités à ce répertoire.|
|  <ul><li>ClassificationList<li>Type : string<li>Valeur par défaut : “” |Liste des valeurs de classification valides séparées par des virgules pouvant être appliquées à des groupes unifiés. |


## <a name="read-settings-at-the-directory-level"></a>Lire les paramètres au niveau du répertoire
Les étapes suivantes permettent de lire les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.

1. Lisez tous les paramètres du répertoire existant :
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Cette applet de commande retourne une liste de tous les paramètres du répertoire :
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Lisez tous les paramètres d’un groupe spécifique :
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Lisez toutes les valeurs de paramètres de répertoire d’un objet settings de répertoire spécifique, à l’aide du GUID de l’ID de paramètres :
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Cette applet de commande retourne les noms et les valeurs dans cet objet de paramètres pour ce groupe spécifique :
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Mettre à jour les paramètres d’un groupe spécifique

1. Recherchez le modèle de paramètres nommé « Groups.Unified.Guest »
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Récupérez l’objet de modèle pour le modèle Groups.Unified.Guest :
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Créez un nouvel objet settings à partir du modèle :
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Définissez le paramètre sur la valeur requise :
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Créez un nouveau paramètre pour le groupe requis dans le répertoire :
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Mettre à jour des paramètres au niveau du répertoire

Les étapes suivantes mettent à jour les paramètres au niveau du répertoire qui s’appliquent à tous les groupes unifiés du répertoire. Ces exemples supposent qu’un objet Settings existe déjà dans votre répertoire.

1. Recherchez l’objet Settings existant :
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Mettez à jour la valeur :
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Mettez à jour le paramètre :
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Supprimer des paramètres au niveau du répertoire
Les étapes suivantes permettent de supprimer les paramètres au niveau du répertoire qui s’appliquent à tous les groupes Office du répertoire.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Informations de référence sur la syntaxe des applets de commande
Vous trouverez plus d’informations sur Azure Active Directory PowerShell dans la page dédiée aux [applets de commande Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Documentation supplémentaire

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
