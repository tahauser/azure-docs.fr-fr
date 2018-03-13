---
title: "Inscription en libre-service ou à la version d’évaluation dans Azure Active Directory | Microsoft Docs"
description: "Utilisez l’inscription en libre-service dans un locataire Azure Active Directory (Azure AD)."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 9f2b541d5028596f9beabc7fd82001b4c9dacad4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Présentation de l’inscription en libre-service pour Azure Active Directory
Cet article explique l’inscription en libre-service et comment la prendre en charge dans Azure Active Directory (Azure AD). Si vous souhaitez prendre le contrôle d’un nom de domaine d’un locataire Azure AD non géré, consultez [Prendre le contrôle d’un annuaire non géré en tant qu’administrateur](domains-admin-takeover.md).

## <a name="why-use-self-service-signup"></a>Pourquoi utiliser l’inscription libre-service ?
* Permettre aux clients de bénéficier plus rapidement des services dont ils ont besoin.
* Créer des offres envoyées par e-mail pour un service.
* Créer des flux d’inscription par e-mail permettant aux utilisateurs de créer rapidement des identités à l’aide de leurs alias de messagerie professionnelle, faciles à mémoriser.
* Un répertoire Azure AD créé en libre-service peut être transformé en répertoire géré utilisable pour d’autres services.

## <a name="terms-and-definitions"></a>Termes et définitions
* **Inscription en libre-service** : méthode via laquelle un utilisateur s‘abonne à un service cloud et bénéficie automatiquement d’une identité créée pour lui dans Azure AD en fonction de son domaine de messagerie.
* **Répertoire Azure AD non géré** : répertoire dans lequel cette identité est créée. Un répertoire non géré est un répertoire qui ne possède aucun administrateur général.
* **Utilisateur vérifié par e-mail**: type de compte d'utilisateur dans Azure AD. Un utilisateur qui possède une identité créée automatiquement après s’être abonné à une offre libre-service est considéré comme un utilisateur vérifié par e-mail. Un utilisateur vérifié par e-mail est un membre ordinaire d'un répertoire marqué par la valeur creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Comment vérifier les paramètres libre-service ?
Les administrateurs peuvent désormais effectuer deux vérifications libre-service. Ils peuvent contrôler si :

* Les utilisateurs peuvent rejoindre le répertoire par e-mail.
* Les utilisateurs peuvent s’abonner eux-mêmes aux applications et services.

### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je vérifier ces fonctionnalités ?
Un administrateur peut configurer ces fonctionnalités à l’aide des paramètres Set-MsolCompanySettings d’applet de commande Azure AD suivants :

* **AllowEmailVerifiedUsers** vérifie si un utilisateur peut créer ou rejoindre un répertoire non géré. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié par e-mail ne peut rejoindre le répertoire.
* **AllowAdHocSubscriptions** contrôle la capacité des utilisateurs à effectuer une inscription en libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer une inscription libre-service. 
  
  > [!NOTE]
  > Les inscriptions à la version d’évaluation de Flow et de PowerApps ne sont pas contrôlées par le paramètre **AllowAdHocSubscriptions**. Pour plus d’informations, consultez les articles suivants :
  > * [Comment faire pour empêcher les utilisateurs existants de commencer à utiliser Power BI ?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Questions et réponses sur Flow dans l’organisation](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Comment les vérifications parviennent-elles à fonctionner ensemble ?
Ces deux paramètres peuvent être utilisés conjointement pour définir un contrôle plus précis de l’inscription en libre-service. Par exemple, la commande suivante permettra aux utilisateurs d’effectuer une inscription en libre-service, mais uniquement si ces utilisateurs possèdent déjà un compte dans Azure AD (en d’autres termes, les utilisateurs qui ont besoin qu’un compte vérifié par e-mail soit d’abord créé ne peuvent pas effectuer d’inscription en libre-service) :

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
L‘organigramme suivant décrit les différentes combinaisons de paramètres et les conditions qui en résultent pour le répertoire et l’inscription en libre-service.

![][1]

Pour en savoir plus et obtenir des exemples d'utilisation de ces paramètres, consultez [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter un nom de domaine personnalisé à Azure AD](add-custom-domain.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Guide de référence des cmdlets Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
