---
title: "Version préliminaire des services de domaine Azure Active Directory : synchronisation sur des domaines gérés | Microsoft Docs"
description: "Synchronisation pour un domaine géré par les services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 5c324ea5e268d97134202eff6e96764bedc6ca75
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synchronisation sur un domaine géré par les services de domaine Azure Active Directory
Le diagramme suivant illustre le fonctionnement de la synchronisation sur les domaines gérés par les services de domaine Azure AD.

![Topologie de synchronisation pour les services de domaine Azure AD](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synchronisation des utilisateurs/groupes de votre annuaire local avec votre client Azure AD
La synchronisation d’Azure AD Connect est utilisée pour synchroniser les comptes d’utilisateur, les appartenances aux groupes et les hachages d’informations d’identification pour votre client Azure AD. Les attributs des comptes d’utilisateur tels que l’UPN et le SID (identificateur de sécurité) sont synchronisés en local. Si vous utilisez les Services de domaine Azure AD, les hachages d’informations d’identification hérités requis pour l’authentification NTLM et Kerberos sont également synchronisés sur votre client Azure AD.

Si vous configurez l’écriture différée, les modifications qui se produisent dans votre annuaire Azure AD sont synchronisées sur votre annuaire Active Directory local. Par exemple, si vous modifiez votre mot de passe à l’aide des fonctionnalités de modification de mot de passe en libre-service d’Azure AD, le mot de passe modifié sera mis à jour dans votre domaine Active Directory local.

> [!NOTE]
> Utilisez toujours la version la plus récente d’Azure AD Connect pour vous assurer d'avoir les correctifs pour tous les bogues connus.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synchronisation de votre client Azure AD avec votre domaine géré
Les comptes d’utilisateur, appartenances de groupe et hachages d’informations d’identification sont synchronisés de votre client Azure AD vers votre domaine géré Services de domaine Azure AD. Ce processus de synchronisation est automatique. Il est inutile de configurer, surveiller ou gérer le processus de synchronisation. Une fois la synchronisation initiale de votre annuaire terminée, il faut généralement environ 20 minutes pour que les modifications apportées dans Azure AD soient présentes dans votre domaine géré. Cet intervalle de synchronisation s’applique aux modifications de mots de passe ou d’attributs dans Azure AD.

Le processus de synchronisation est également unidirectionnel par nature. Votre domaine géré est en grande partie en lecture seule sauf pour les unités d’organisation personnalisées que vous créez. Par conséquent, vous ne pouvez pas modifier les attributs utilisateur, mots de passe utilisateur et appartenances aux groupes dans le domaine géré. Par conséquent, il n’existe aucune synchronisation inverse ou de modifications de votre domaine géré vers votre client Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synchronisation d’un environnement local à plusieurs forêts
De nombreuses organisations possèdent une infrastructure d’identité locale relativement complexe composée de plusieurs forêts de comptes. Azure AD Connect prend en charge la synchronisation des utilisateurs, groupes et hachages d’informations d’identification à partir d’environnements à plusieurs forêts vers votre client Azure AD.

En revanche, votre client Azure AD est un espace de noms beaucoup plus simple et plat. Pour permettre aux utilisateurs d’accéder de manière fiable aux applications sécurisées par Azure AD, résolvez les conflits d’UPN entre comptes d’utilisateur dans des forêts différentes. Votre domaine géré par les services de domaine Azure AD présente une forte ressemblance avec votre client Azure AD. Par conséquent, vous voyez une structure d’unité d’organisation plate dans votre domaine géré. Tous les utilisateurs et groupes sont stockés dans le conteneur « Utilisateurs AADDC », peu importe la forêt ou le domaine local d’origine de la synchronisation. Vous pouvez avoir configuré une structure d’unité d’organisation hiérarchique en local. Toutefois, votre domaine géré a toujours une structure d’unité d’organisation plate simple.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusions - ce qui n’est pas synchronisé avec votre domaine géré
Les objets et attributs suivants ne sont pas synchronisés avec votre client Azure AD ou votre domaine géré :

* **Exclure les attributs :** vous pouvez choisir d’exclure certains attributs de la synchronisation avec votre client Azure AD à partir de votre domaine local à l’aide d’Azure AD Connect. Ces attributs exclus ne sont pas disponibles dans votre domaine géré.
* **Stratégies de groupe :** les stratégies de groupe configurées dans votre domaine local ne sont pas synchronisées sur votre domaine géré.
* **Partage SYSVOL :** de la même façon, le contenu du partage SYSVOL sur le domaine local n’est pas synchronisé avec votre domaine géré.
* **Objets ordinateur :** les objets ordinateurs pour les ordinateurs joints à votre domaine local ne sont pas synchronisés avec votre domaine géré. Ces ordinateurs n’ont pas une relation d’approbation avec votre domaine géré et appartiennent à votre domaine local uniquement. Dans votre domaine géré, vous recherchez des objets ordinateurs uniquement pour les ordinateurs que vous avez explicitement joints au domaine géré.
* **Attributs SidHistory des utilisateurs et groupes :** les SID de l’utilisateur principal et du groupe principal de votre domaine local sont synchronisés avec votre domaine géré. Toutefois, les attributs SidHistory existants pour les utilisateurs et les groupes ne sont pas synchronisés de votre domaine local vers votre domaine géré.
* **Structures d’unités d’organisation (OU) :** les unités d’organisation définies dans votre domaine local ne sont pas synchronisées avec votre domaine géré. Il existe deux unités d’organisation intégrées dans votre domaine géré. Par défaut, votre domaine géré a une structure d’unité d’organisation plate. Vous pouvez toutefois choisir de [créer une unité d’organisation personnalisée dans votre domaine géré](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Synchronisation d’attributs spécifiques avec votre domaine géré
Le tableau suivant répertorie certains des attributs communs et décrit comment ils sont synchronisés avec votre domaine géré.

| Attribut dans votre domaine géré | Source | Remarques |
|:--- |:--- |:--- |
| UPN |Attribut UPN de l’utilisateur dans votre client Azure AD |L’attribut UPN de votre client Azure AD est synchronisé tel quel dans votre domaine géré. Par conséquent, la méthode la plus fiable pour vous connecter à votre domaine géré est d’utiliser votre UPN. |
| SAMAccountName |Attribut mailNickname de l’utilisateur, dans votre client Azure AD ou généré automatiquement |L’attribut SAMAccountName provient de l’attribut mailNickname dans votre client Azure AD. Si plusieurs comptes d’utilisateurs ont le même attribut mailNickname, SAMAccountName est généré automatiquement. Si le paramètre mailNickname ou préfixe UPN dépasse 20 caractères, SAMAccountName est généré automatiquement pour répondre à la limite de 20 caractères sur les attributs SAMAccountName. |
| Mot de passe |Mot de passe utilisateur de votre client Azure AD |Les hachages d’informations d’identification requis pour l’authentification NTLM ou Kerberos (également appelés informations d’identification supplémentaires) sont synchronisés à partir de votre client Azure AD. Si votre client Azure AD est un client synchronisé, ces informations d’identification proviennent de votre domaine local. |
| SID groupe/utilisateur principal |Généré automatiquement |Le SID principal pour les comptes de groupe/d’utilisateur est généré automatiquement dans votre domaine géré. Cet attribut ne correspond pas au SID de groupe/d’utilisateur principal de l’objet dans votre domaine AD local. Cette incohérence existe car le domaine géré a un espace de noms SID différent de celui de votre domaine local. |
| Historique des SID des utilisateurs et groupes |SID d’utilisateur et de groupe principal local |L’attribut SidHistory pour les utilisateurs et groupes dans votre domaine géré est défini de sorte à correspondre au SID de groupe ou d’utilisateur principal correspondant sur votre domaine local. Cette fonctionnalité permet de faciliter la migration des applications sur site vers le domaine géré, étant donné que vous n’avez pas besoin de redéfinir les ACL des ressources. |

> [!NOTE]
> **Se connecter au domaine géré en utilisant le format UPN :** l’attribut SAMAccountName peut être généré automatiquement pour certains comptes d’utilisateurs dans votre domaine géré. Si plusieurs utilisateurs ont le même attribut mailNickname ou que des utilisateurs ont des préfixes UPN anormalement longs, la valeur SAMAccountName pour ces utilisateurs peut être générée automatiquement. Par conséquent, le format SAMAccountName (par exemple, « CONTOSO100\joeuser ») n’est pas toujours une méthode fiable pour se connecter au domaine. La valeur de SAMAccountName générée automatiquement pour l’utilisateur peut différer du préfixe UPN de ce dernier. Utilisez le format UPN (par exemple, 'joeuser@contoso100.com') pour vous connecter de manière fiable au domaine managé.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Mappage d’attributs pour les comptes d’utilisateur
Le tableau suivant illustre la façon dont certains attributs pour les objets utilisateur dans votre client Azure AD sont synchronisés avec les attributs correspondants dans votre domaine géré.

| Attribut d’utilisateur dans votre client Azure AD | Attribut d’utilisateur dans votre domaine géré |
|:--- |:--- |
| accountEnabled |userAccountControl (définit ou efface le bit ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickName |msDS-AzureADMailNickname |
| mailNickName |SAMAccountName (peut parfois être généré automatiquement) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (définit ou efface le bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mappage d’attributs pour les groupes
Le tableau suivant illustre la façon dont certains attributs pour les objets de groupe dans votre client Azure AD sont synchronisés avec les attributs correspondants dans votre domaine géré.

| Attribut de groupe dans votre client Azure AD | Attribut de groupe dans votre domaine géré |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (peut parfois être généré automatiquement) |
| mail |mail |
| mailNickName |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objets qui ne sont pas synchronisés avec votre client Azure AD à partir de votre domaine géré
Comme décrit dans une section précédente de cet article, il n’existe aucune synchronisation de votre domaine géré vers votre client Azure AD. Vous pouvez choisir de [créer une unité d’organisation (OU) personnalisée](active-directory-ds-admin-guide-create-ou.md) dans votre domaine géré. En outre, vous pouvez créer d’autres unités d’organisation, utilisateurs, groupes ou comptes de service au sein de ces unités d’organisation personnalisées. Les objets créés dans les unités d’organisation personnalisées sont de nouveau synchronisés avec votre client Azure AD. Ces objets sont disponibles uniquement dans votre domaine géré. Par conséquent, ces objets ne sont pas visibles à l’aide des applets de commande PowerShell d’Azure AD, l’API Graph d’Azure AD ou l’interface utilisateur de gestion d’Azure AD.

## <a name="related-content"></a>Contenu connexe
* [Fonctionnalités - Services de domaine Azure AD](active-directory-ds-features.md)
* [Scénarios de déploiement - Services de domaine Azure AD](active-directory-ds-scenarios.md)
* [Considérations relatives à la mise en réseau pour les services de domaine Azure AD](active-directory-ds-networking.md)
* [Prise en main des services de domaine Azure AD](active-directory-ds-getting-started.md)
