---
title: "Gérer les comptes d’administration de l’accès d’urgence dans Azure AD | Microsoft Docs"
description: "Cet article explique comment utiliser des comptes d’accès d’urgence pour aider les organisations à restreindre l’accès privilégié au sein d’un environnement Azure Active Directory existant."
services: active-directory
keywords: "N’ajoutez pas ou ne modifiez pas de mots clés sans consulter votre expert SEO."
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 1545fb9a89794a74efbb855c4480040973c3308e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Gérer les comptes d’administration de l’accès d’urgence dans Azure AD 

Pour la plupart des activités quotidiennes qu’ils accomplissent, les utilisateurs n’ont pas besoin des droits d’*administrateur général*. Ce rôle ne devrait pas leur être attribué de façon permanente, car ils peuvent par inadvertance effectuer des tâches qui nécessitent des autorisations plus élevées que celles auxquelles ils ont droit. Ainsi, lorsque les utilisateurs n’ont pas besoin d’agir en tant qu’administrateur général, ils devraient activer l’attribution de rôle en utilisant Azure Active Directory (Azure AD) Privileged Identity Management (PIM), sur leur propre compte ou sur un autre compte d’administration.

En plus de l’appropriation par les utilisateurs des droits d’accès d’administration pour eux-mêmes, vous devez aussi prévenir votre propre exclusion accidentelle de l’administration de votre locataire Azure AD, car vous ne pouvez ni vous connecter ni activer le compte d’un utilisateur individuel existant en tant qu’administrateur. Vous pouvez pallier l’impact d’un défaut d’accès administratif en stockant plusieurs *comptes d’accès d’urgence* dans votre locataire.

Les comptes d’accès d’urgence peuvent aider les organisations à restreindre l’accès privilégié dans un environnement Azure Active Directory existant. Ces comptes sont hautement privilégiés et ne sont pas affectés à des individus spécifiques. Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours », des situations où il est impossible d’utiliser des comptes d’administration normaux. Les organisations doivent se fixer l’objectif de limiter l’utilisation du compte d’urgence à la stricte durée pendant laquelle celui-ci est nécessaire.

Une organisation peut avoir recours à un compte d’accès d’urgence dans les situations décrites ici.

 - Les comptes d’utilisateurs sont fédérés, et la fédération est actuellement indisponible en raison d’un dysfonctionnement du réseau cellulaire ou d’une panne du fournisseur d’identité. Par exemple, si l’hôte du fournisseur d’identité dans votre environnement s’est arrêté de fonctionner, les utilisateurs risquent de ne pas pouvoir se connecter lors de la redirection d’Azure AD vers leur fournisseur d’identité. 
 - Les administrateurs sont inscrits par le biais d’Azure Multi-Factor Authentication, et tous leurs appareils individuels sont indisponibles. Les utilisateurs peuvent se retrouver dans l’incapacité de procéder à l’authentification multifacteur pour activer un rôle. Par exemple, une panne de réseau cellulaire les empêche de répondre aux appels téléphoniques ou de recevoir des SMS, les deux seuls mécanismes d’authentification à leur disposition qu’ils ont enregistrés pour leur appareil. 
 - La personne disposant de l’accès d’administration générale le plus récent a quitté l’organisation. Azure AD empêche la suppression du dernier compte d’*administrateur général*, mais il n’empêche pas ce compte d’être supprimé ou désactivé en local. Chacune de ces situations peut rendre impossible la récupération du compte par l’organisation.

## <a name="initial-configuration"></a>Configuration initiale

Créez plusieurs comptes d’accès d’urgence. Ces comptes doivent être uniquement des comptes cloud utilisant le domaine \*. onmicrosoft.com, et ne pas être fédérés ou synchronisés à partir d’un environnement local. 

Ils ne doivent être associés à aucun utilisateur de l’organisation. Les organisations sont tenues de garantir la sécurisation des informations d’identification pour ces comptes, et une confidentialité limitée aux seules personnes autorisées à les utiliser. 

> [!NOTE]
> Le mot de passe d’un compte d’accès d’urgence se divise généralement en deux ou trois parties, celles-ci sont écrites sur des feuilles de papier séparées, et stockées dans des coffres-forts ignifuges installés dans des endroits distincts et sécurisés. 
>
> Veillez à ce que vos comptes d’accès d’urgence ne soient pas connectés à un matériel fourni à un employé et voyageant avec celui-ci, tel un téléphone mobile, un module de sécurité matériel, ou d’autres informations d’identification propres à l’employé. Cette précaution de sécurité couvre les cas où un employé n’est pas joignable alors que les informations d’identification doivent être fournies. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configuration initiale avec attributions permanentes

Une solution consiste à convertir les utilisateurs en membres permanents du rôle d’*administrateur général*. Cette option convient aux organisations qui ne disposent pas d’abonnements Azure AD Premium P2.

Afin de réduire le risque d’attaques résultant d’un mot de passe compromis, Azure AD recommande l’utilisation de l’authentification multifacteur généralisée à toutes les personnes utilisatrices. Ce groupe doit inclure les administrateurs et tous les autres utilisateurs (par exemple, les responsables financiers) dont le compte compromis aurait un impact important. 

Toutefois, si votre organisation n’utilise pas d’appareils partagés, il est fort possible que l’authentification multifacteur ne puisse pas s’appliquer pour ces comptes d’accès d’urgence. Si vous configurez une stratégie d’accès conditionnel pour obliger [l’inscription de chaque administrateur à l’authentification multifacteur ](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) auprès d’Azure AD et d’autres applications SaaS (software as a service) connectées, vous devrez peut-être configurer des exclusions de stratégie, afin d’excepter les comptes d’accès d’urgence de cette exigence.

### <a name="initial-configuration-with-approvals"></a>Configuration initiale avec approbations

Une autre solution consiste à configurer vos utilisateurs comme étant éligibles et approbateurs pour activer le rôle d’*administrateur général*. Cette option suppose que votre organisation a souscrit des abonnements Azure AD Premium P2. Elle implique également une option d’authentification multifacteur convenant pour une utilisation partagée entre plusieurs personnes et l’environnement réseau. Ces exigences sont dues à l’activation du rôle d’*administrateur général* qui impose préalablement aux utilisateurs une authentification multifacteur. Pour plus d’informations, consultez [Guide pratique pour exiger l’authentification multifacteur dans Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Nous vous déconseillons d’utiliser l’authentification multifacteur qui est associée à des appareils personnels pour les comptes d’accès d’urgence. En cas d’urgence réelle, la personne qui a besoin d’accéder à un appareil inscrit à l’authentification multifacteur peut ne pas être la détentrice de l’appareil personnel. 

Envisagez également le contexte des menaces. Par exemple, un événement imprévu, comme une intervention d’urgence après une catastrophe naturelle, peut survenir au cours duquel téléphones mobiles ou autres réseaux ne sont pas disponibles. Il est important de s’assurer que tous les appareils inscrits sont conservés dans un endroit sûr et connu, disposant de plusieurs moyens de communication avec Azure AD.

## <a name="ongoing-monitoring"></a>Surveillance continue

Analysez les [journaux d’audit et de connexion à Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) à la recherche d’activités de connexion et de modification à partir des comptes d’accès d’urgence. Normalement, ces comptes ne doivent pas se connecter ni effectuer de modification, par conséquent leur éventuelle utilisation doit être considérée comme suspecte et nécessiter une enquête de sécurité.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Obligation de procéder à la vérification du compte à intervalles réguliers

Pour valider le compte, au minimum effectuez les étapes suivantes :
- Tous les 90 jours
- En cas de modification récente au sein de l’équipe informatique, par exemple suite à un changement de poste, un départ ou à l’arrivée d’un nouvel employé
- Lorsque les abonnements Azure AD de l’organisation ont changé

Pour former des membres du personnel à l’utilisation des comptes d’accès d’urgence, effectuez les actions répertoriées ici.

* Assurez-vous que les agents de la surveillance et de la sécurité sont informés que l’activité de vérification du compte est permanente.
* Confirmez que les comptes d’utilisateur cloud peuvent se connecter et activer leurs rôles, que les utilisateurs devant éventuellement effectuer ces étapes en cas d’urgence sont également formés sur la procédure à suivre.
* Veillez à qu’ils n’aient pas enregistré d’authentification multifacteur ni de réinitialisation de mot de passe libre-service (SSPR) sur un appareil d’utilisateur individuel ou dans ses détails personnels. 
* Si les comptes sont inscrits pour une authentification multifacteur sur un appareil, en vue de servir pendant l’activation de rôle, vérifiez que tous les administrateurs susceptibles d’utiliser l’appareil en cas d’urgence ont accès à celui-ci. Assurez-vous également que l’appareil est enregistré par le biais d’au moins deux techniques ne partageant pas de mode de défaillance en commun. Par exemple, l’appareil doit être capable de communiquer sur internet via le réseau sans fil d’une installation et le réseau d’un fournisseur de données cellulaires.
* Mettez à jour les informations d’identification du compte.

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter un utilisateur cloud](add-users-azure-active-directory.md) et [Attribuer au nouvel utilisateur le rôle d’administrateur général](active-directory-users-assign-role-azure-portal.md).
- [S’inscrire à Azure Active Directory Premium](active-directory-get-started-premium.md), si ce n’est pas déjà fait.
- [Exiger Azure Multi-Factor Authentication pour les utilisateurs individuels désignés comme administrateurs](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Configurer des protections supplémentaires pour les administrateurs généraux dans Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), si vous utilisez Office 365.
- [Effectuer une vérification de l’accès des administrateurs généraux](active-directory-privileged-identity-management-how-to-start-security-review.md) et de la [transition des administrateurs généraux existants à des rôles d’administrateur plus spécifiques](active-directory-assign-admin-roles-azure-portal.md).


