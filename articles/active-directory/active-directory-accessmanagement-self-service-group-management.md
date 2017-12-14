---
title: "Configuration de la gestion de l’accès aux applications en libre-service dans Azure Active Directory | Microsoft Docs"
description: "Créer et gérer des groupes de sécurité ou Office 365 dans Azure Active Directory et demander des appartenances aux groupes de sécurité ou Office 365"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 4798c9f9b289f8fa089498398079565c35ccdd2a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Configurer Azure Active Directory pour la gestion de groupes en libre-service
Vos utilisateurs peuvent créer et gérer leurs propres groupes de sécurité ou Office 365 dans Azure Active Directory (Azure AD). Les utilisateurs peuvent également faire une demande d’appartenance à un groupe de sécurité ou Office 365. Le propriétaire du groupe peut alors approuver ou refuser l’appartenance. Le contrôle quotidien de l’appartenance à un groupe peut être délégué à des personnes qui ont une bonne connaissance du contexte professionnel de cette appartenance. Les fonctionnalités de gestion de groupes en libre-service ne sont disponibles que pour les groupes de sécurité et les groupes Office 365. Elles ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

La gestion de groupes en libre-service comprend actuellement deux scénarios essentiels : la gestion de groupes déléguée et la gestion de groupes en libre-service.

* **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. L’administrateur accorde l’accès à l’application au nouveau groupe et ajoute au groupe toutes les personnes accédant déjà à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application. Il n’a pas besoin d’attendre que l’administrateur gère l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un responsable d’un autre groupe de l’entreprise, cette personne peut également gérer l’accès de ses propres utilisateurs. Le propriétaire de l’entreprise ne peut pas visualiser ni gérer les utilisateurs du responsable, et inversement. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.
* **Gestion de groupes en libre-service** Prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Ils les gèrent indépendamment. Cependant, ils veulent les rendre accessibles à chacune des deux équipes. Pour cela, ils peuvent créer un groupe dans Azure AD, puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès au site doivent également pouvoir accéder à une application SaaS. L’administrateur de l’application SaaS peut ajouter des droits d’accès à l’application pour le site SharePoint Online. Les demandes qu’il approuvera par la suite donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Mettre un groupe à disposition en libre-service pour un utilisateur
1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**, puis **Paramètres de groupe**.
3. Définissez **Gestion de groupes en libre-service activée** sur **Oui**.
4. Définissez **Les utilisateurs peuvent créer des groupes de sécurité** ou **Les utilisateurs peuvent créer des groupes Office 365** sur **Oui**.
  * Une fois ces paramètres activés, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. 
  * Quand ces paramètres sont désactivés, les utilisateurs ne peuvent pas créer de groupes, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes.

Vous pouvez également utiliser **Utilisateurs pouvant gérer les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** pour obtenir un contrôle d’accès plus fin de la gestion de groupes en libre-service pour vos utilisateurs. Quand l’option **Les utilisateurs peuvent créer des groupes** est activée, tous les utilisateurs présents dans votre locataire sont autorisés à créer des groupes et à ajouter des membres à ces groupes. En définissant l’option sur **Certains**, vous restreignez la gestion de groupes à un groupe d’utilisateurs limité uniquement. Dans ce cas, vous devez ajouter des utilisateurs au groupe SSGMSecurityGroupsUsers avant que ceux-ci ne puissent créer des groupes et y ajouter des membres. En définissant **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** sur **Tous**, vous autorisez tous les utilisateurs de votre locataire à créer des groupes.

Vous pouvez également utiliser **Groupe pouvant gérer les groupes de sécurité** ou **Groupe pouvant gérer les groupes Office 365** pour spécifier un groupe unique dont les membres peuvent utiliser le libre-service.

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gérer l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégrer des répertoires locaux à Azure Active Directory](active-directory-aadconnect.md)
