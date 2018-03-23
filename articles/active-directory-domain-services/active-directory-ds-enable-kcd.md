---
title: 'Azure Active Directory Domain Services : Activer la délégation Kerberos contrainte | Microsoft Docs'
description: Activer la délégation Kerberos contrainte sur les domaines gérés par les services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: maheshu
ms.openlocfilehash: 6ed797ca25161919ccf5e69be0073a67bfcef6d6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configurer la délégation Kerberos contrainte (KCD) sur un domaine managé
De nombreuses applications doivent accéder à des ressources dans le contexte de l’utilisateur. Active Directory prend en charge un mécanisme, appelé délégation Kerberos, qui rend possible ce cas d’utilisation. En outre, vous pouvez limiter la délégation afin que seules des ressources spécifiques soient accessibles dans le contexte de l’utilisateur. Les domaines gérés des services de domaine Azure AD diffèrent des domaines Active Directory traditionnels dans la mesure où ils sont verrouillés de façon mieux sécurisée.

Cet article vous montre comment configurer la délégation Kerberos contrainte sur un domaine managé Azure AD Domain Services.

## <a name="kerberos-constrained-delegation-kcd"></a>Délégation Kerberos contrainte (KCD)
La délégation Kerberos contrainte permet à un compte d’emprunter l’identité d’un autre principal de sécurité (par exemple, un utilisateur) pour accéder aux ressources. Imaginez une application web qui accède à une API web principale dans le contexte d’un utilisateur. Dans cet exemple, l’application web (en cours d’exécution dans le contexte d’un compte de service ou d’un compte d’ordinateur) emprunte l’identité de l’utilisateur pour accéder à la ressource (API web principale). La délégation Kerberos n’est pas sécurisée, car elle ne limite pas les ressources auxquelles le compte d’emprunt peut accéder dans le contexte de l’utilisateur.

La délégation Kerberos contrainte (KCD) restreint les services/ressources sur lesquels le serveur spécifié peut agir pour le compte d’un utilisateur. La KCD traditionnelle requiert des privilèges d’administrateur de domaine pour configurer un compte de domaine pour un service, et elle restreint le compte à un seul domaine.

La KCD traditionnelle a également quelques problèmes qui lui sont associés. Dans les systèmes d’exploitation précédents, si l’administrateur de domaine configurait une KCD basée sur le compte pour le service, l’administrateur de service ne possédait aucun moyen de connaître les services frontaux qui déléguaient vers des services de ressources qu’ils détenaient. Et tout service frontal qui pouvait déléguer à un service de ressources représentait un point d’attaque potentiel. Si un serveur hébergeant un service frontal était compromis, et qu’il était configuré pour déléguer aux services de ressources, les services de ressources pouvaient également être compromis.

> [!NOTE]
> Sur un domaine managé Azure AD Domain Services, vous n’avez pas les privilèges d’administrateur de domaine. Par conséquent, la **délégation Kerberos contrainte traditionnelle basée sur les comptes ne peut pas être configurée sur un domaine managé**. Utilisez plutôt la KCD basée sur la ressource, comme décrit dans cet article. Ce mécanisme est également plus sécurisé.
>
>

## <a name="resource-based-kcd"></a>KCD basée sur la ressource
À partir de Windows Server 2012, les administrateurs de service peuvent configurer la délégation contrainte pour leur service. Dans ce modèle, l’administrateur de service principal peut octroyer ou refuser à des services frontaux spécifiques le droit d’utiliser la KCD. Ce modèle est appelé **KCD basée sur la ressource**.

La KCD basée sur la ressource est configurée à l’aide de PowerShell. Vous utilisez les applets de commande `Set-ADComputer` ou `Set-ADUser`, selon que le compte d’emprunt est un compte d’ordinateur ou un compte de service/compte utilisateur.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configuration d’une KCD basée sur la ressource pour un compte d’ordinateur sur un domaine géré
Supposons que vous ayez une application web en cours d’exécution sur l’ordinateur « contoso100-webapp.contoso100.com ». Elle doit accéder à la ressource (une API web s’exécutant sur « contoso100-api.contoso100.com ») dans le contexte des utilisateurs du domaine. Voici comment configurer une délégation Kerberos contrainte basée sur les ressources pour ce scénario :

1. [Créer une unité d’organisation personnalisée](active-directory-ds-admin-guide-create-ou.md) Vous pouvez déléguer des autorisations pour gérer cette unité d’organisation personnalisée aux utilisateurs du domaine managé.
2. Joignez les deux machines virtuelles (celle qui exécute l’application web et celle qui exécute l’API web) au domaine managé. Créez ces comptes d’ordinateur dans l’unité d’organisation personnalisée.
3. Configurez la délégation Kerberos contrainte basée sur les ressources à l’aide de la commande PowerShell suivante :

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Les comptes d’ordinateur de l’application web et de l’API web doivent se trouver dans une unité d’organisation personnalisée dans laquelle vous disposez d’autorisations pour configurer la délégation Kerberos contrainte basée sur les ressources. Vous ne pouvez pas configurer la délégation Kerberos contrainte basée sur les ressources pour un compte d’ordinateur dans le conteneur intégré « Ordinateurs du contrôleur de domaine AAD ».
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configuration d’une KCD basée sur la ressource pour un compte utilisateur sur un domaine géré
Supposons que vous ayez une application web en cours d’exécution en tant que compte de service « appsvc » et qu’elle doit accéder à la ressource (une API web en cours d’exécution en tant que compte de service - 'backendsvc') dans le contexte des utilisateurs du domaine. Voici comment configurer une KCD basée sur les ressources pour ce scénario.

1. [Créer une unité d’organisation personnalisée](active-directory-ds-admin-guide-create-ou.md) Vous pouvez déléguer des autorisations pour gérer cette unité d’organisation personnalisée aux utilisateurs du domaine managé.
2. Joignez la machine virtuelle qui exécute l’API web ou la ressource backend au domaine managé. Créez son compte d’ordinateur dans l’unité d’organisation personnalisée.
3. Créez le compte de service (par exemple, « appsvc ») utilisé pour exécuter l’application web dans l’unité d’organisation personnalisée.
4. Configurez la délégation Kerberos contrainte basée sur les ressources à l’aide de la commande PowerShell suivante :

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Le compte d’ordinateur de l’API web backend et le compte de service doivent se trouver dans une unité d’organisation personnalisée où vous disposez d’autorisations pour configurer la délégation Kerberos contrainte basée sur les ressources. Vous ne pouvez pas configurer la délégation Kerberos contrainte basée sur les ressources pour un compte d’ordinateur dans le conteneur intégré « Ordinateurs du contrôleur de domaine AAD », ou pour les comptes d’utilisateurs dans le conteneur intégré « Utilisateurs du contrôleur de domaine AAD ». Par conséquent, vous ne pouvez pas utiliser de comptes d’utilisateur synchronisés à partir d’Azure AD pour configurer la délégation Kerberos contrainte basée sur les ressources.
>

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Présentation de la délégation Kerberos contrainte](https://technet.microsoft.com/library/jj553400.aspx)
