---
title: Azure IoT Suite et Azure Active Directory | Microsoft Docs
description: "Décrit comment Azure IoT Suite utilise Azure Active Directory pour gérer les autorisations."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a032fc4332c697748e658ad2615ed5b0915c56c1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Autorisations sur le site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Que se passe-t-il lorsque vous vous connectez ?

Lorsque vous vous connectez pour la première fois à [azureiotsuite.com][lnk-azureiotsuite], le site détermine vos niveaux d’autorisation en fonction du client Azure Active Directory (AAD) et de l’abonnement Azure sélectionné.

1. Tout d’abord, afin de remplir la liste des clients qui apparaît en regard de votre nom d’utilisateur, le site recherche dans Azure les clients ADD auxquels vous appartenez. Actuellement, le site peut obtenir des jetons d’utilisateur pour un seul client. Par conséquent, lorsque vous basculez sur d’autres clients en utilisant la liste déroulante dans le coin supérieur droit, le site vous reconnecte à ce client pour obtenir les jetons pour ce client.

2. Ensuite, le site détecte à partir d’Azure, les abonnements que vous avez associés au client sélectionné. Vous pouvez voir les abonnements disponibles lorsque vous créez une nouvelle solution préconfigurée.

3. Enfin, le site extrait toutes les ressources dans les abonnements et les groupes de ressources marqués en tant que solutions préconfigurées et renseigne les mosaïques de la page d’accueil.

Les sections suivantes décrivent les rôles qui contrôlent l’accès aux solutions préconfigurées.

## <a name="aad-roles"></a>Rôles AAD

Les rôles AAD contrôlent la possibilité d’approvisionnement de solutions préconfigurées et gèrent les utilisateurs dans une solution préconfigurée.

Pour plus d’informations sur les rôles d’administrateur dans ADD, consultez la page [Attribution de rôles d’administrateur dans Azure AD][lnk-aad-admin]. Cet article se concentre sur les rôles d’annuaire **Administrateur général** et **Utilisateur** tels qu’utilisés par les solutions préconfigurées.

### <a name="global-administrator"></a>Administrateur général

Il peut y avoir de nombreux administrateurs généraux pour chaque locataire AAD :

* Lorsque vous créez un client AAD, vous en devenez par défaut l’administrateur.
* L’administrateur général peut approvisionner une solution préconfigurée et se voir attribuer le rôle **Administrateur** de l’application au sein de son locataire AAD.
* Si un autre utilisateur du même locataire AAD crée une application, le rôle attribué par défaut à l’administrateur général est **Lecture seule**.
* Les administrateurs généraux peuvent attribuer des rôles aux utilisateurs pour les applications à l’aide du [Portail Azure][lnk-portal].

### <a name="domain-user"></a>Utilisateur de domaine

Il peut y avoir de nombreux utilisateurs de domaine pour chaque locataire AAD :

* Un utilisateur de domaine peut approvisionner une solution préconfigurée via le site [azureiotsuite.com][lnk-azureiotsuite]. Par défaut, l’utilisateur de domaine se voit accorder le rôle **Administrateur** dans l’application approvisionnée.
* Un utilisateur de domaine peut créer une application à l’aide du script build.cmd qui se trouve dans le référentiel [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo] ou [azure-iot-connected-factory][lnk-cf-github-repo]. Toutefois, le rôle accordé par défaut à l’utilisateur de domaine est **Lecture seule**, car un utilisateur de domaine n’est pas autorisé à attribuer des rôles.
* Si un autre utilisateur du locataire AAD crée une application, l’utilisateur de domaine se voit attribuer le rôle **Lecture seule** par défaut pour cette application.
* Un utilisateur de domaine ne peut pas attribuer des rôles pour des applications ; par conséquent, il ne peut pas ajouter des utilisateurs ou des rôles pour les utilisateurs d’une application, même s’ils l’ont approvisionnée.

### <a name="guest-user"></a>Utilisateur invité

Il peut y avoir de nombreux utilisateurs invités pour chaque locataire AAD. Les utilisateurs invités disposent d’un ensemble limité de droits au sein du client AAD. Par conséquent, les utilisateurs invités ne peuvent pas approvisionner une solution préconfigurée dans le client AAD.

Pour plus d’informations sur les utilisateurs et les rôles dans AAD, consultez les ressources suivantes :

* [Créer des utilisateurs dans Azure AD][lnk-create-edit-users]
* [Affecter des utilisateurs aux applications][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Rôles de l’administrateur d’abonnement Azure

Les rôles d’administration Azure permettent de contrôler la fonctionnalité de mappage d’un abonnement Azure sur un client AD.

Pour plus d’informations sur les rôles d’administrateur Azure, consultez l’article [Guide pratique pour ajouter ou modifier le coadministrateur, l’administrateur de services fédérés et l’administrateur de compte][lnk-admin-roles].

## <a name="application-roles"></a>Rôles d’application

Les rôles d’application contrôlent l’accès aux périphériques de votre solution préconfigurée.

Une application approvisionnée comporte deux rôles définis et un rôle implicite défini :

* **Administrateur :** contrôle totalement l’ajout, la gestion et la suppression des appareils, ainsi que la modification des paramètres.
* **Lecture seule :** peut afficher les appareils, actions, règles, travaux et télémétrie.

Vous pouvez trouver les autorisations attribuées à chaque rôle dans le fichier source [RolePermissions.cs][lnk-resource-cs].

### <a name="changing-application-roles-for-a-user"></a>Modification des rôles d’application pour un utilisateur

Vous pouvez utiliser la procédure suivante pour définir un utilisateur dans Active Directory en tant qu’administrateur de votre solution préconfigurée.

Vous devez être un administrateur général AAD pour modifier les rôles d’un utilisateur :

1. Accédez au [portail Azure][lnk-portal].
2. Sélectionnez **Azure Active Directory**.
3. Veillez à utiliser l’annuaire que vous avez choisi sur azureiotsuite.com lorsque vous avez approvisionné votre solution. Si vous avez plusieurs annuaires associés à votre abonnement, vous pouvez basculer entre eux si vous cliquez sur le nom de votre compte en haut à droite du portail.
4. Cliquez sur **Applications d’entreprise**, puis sur **Toutes les applications**.
4. Affichez **toutes les applications** avec **n’importe quel** état. Recherchez ensuite une application avec le nom de votre solution préconfigurée.
5. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfigurée.
6. Cliquez sur **Utilisateurs et groupes**.
7. Sélectionnez l’utilisateur dont vous souhaitez permuter les rôles.
8. Cliquez sur **Attribuer**, puis sélectionnez le rôle (par exemple, **Admin**) que vous souhaitez attribuer à l’utilisateur en cochant la case correspondante.

## <a name="faq"></a>Forum Aux Questions

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Je suis un administrateur de service et je souhaite modifier le mappage d’annuaire entre mon abonnement et un client AAD spécifique. Comment mener à bien cette tâche ?

Voir [Comment ajouter un abonnement existant à votre répertoire Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Je suis un utilisateur/membre du domaine sur le client AAD et j’ai créé une solution préconfigurée. Comment puis-je me voir attribuer un rôle pour mon application ?

Demandez à un administrateur général de vous faire passer administrateur général du locataire AAD, puis attribuez vous-même des rôles aux utilisateurs. Vous pouvez également demander à un administrateur général d’attribuer directement un rôle. Si vous souhaitez modifier le client AAD sur lequel votre solution préconfigurée a été déployée, passez à la question suivante.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Comment puis-je activer le client AAD auquel la solution préconfigurée de contrôle à distance et l’application sont affectées ?

Vous pouvez exécuter un déploiement cloud à partir de <https://github.com/Azure/azure-iot-remote-monitoring>, puis redéployer avec un client AAD nouvellement créé. Comme vous êtes, par défaut, administrateur général, lorsque vous créez un locataire AAD, vous disposez des autorisations nécessaires pour ajouter des utilisateurs et leur attribuer des rôles.

1. Créez un annuaire ADD dans le [portail Azure ][lnk-portal].
2. Accédez à <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Exécutez `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (par exemple, `build.cmd cloud debug myRMSolution`)
4. Lorsque vous y êtes invité, définissez le **tenantid** du client que vous venez de créer et non celui du client précédent.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Je souhaite modifier la fonctionnalité administrateur de service ou coadministrateur lors d’une connexion avec un compte de société

Voir l’article de support [Modification de la fonctionnalité Administrateur de service et Coadministrateur lors d’une connexion avec un compte de société][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Pourquoi est-ce que je reçois cette erreur ? « Votre compte n’a pas les autorisations suffisantes pour créer une solution. Veuillez contacter votre administrateur de compte ou essayer avec un autre compte. »

Examinez le schéma suivant pour obtenir des conseils :

![][img-flowchart]

> [!NOTE]
> Si l’erreur persiste après votre validation en tant qu’administrateur global sur le client AAD et que coadministrateur sur l’abonnement, demandez à votre administrateur de compte de supprimer l’utilisateur et de réattribuer les autorisations nécessaires dans l’ordre suivant : tout d’abord, ajoutez l’utilisateur en tant qu’administrateur global, puis ajoutez un utilisateur en tant que coadministrateur sur l’abonnement Azure. Si les problèmes persistent, contactez le service [Aide et support][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Pourquoi affiche-t-il cette erreur alors que j’ai un abonnement Azure ? « Vous devez avoir un abonnement Azure pour créer des solutions préconfigurées. Vous pouvez créer un compte d'essai gratuit en quelques minutes seulement. »

Si vous êtes sûr de disposer d’un abonnement Azure, validez le mappage de votre abonnement client et assurez-vous que c’est le bon client qui est sélectionné dans la liste déroulante. Si vous avez validé le locataire souhaité, suivez le schéma ci-dessus et validez le mappage de votre abonnement et de ce locataire AAD.

## <a name="next-steps"></a>Étapes suivantes
Pour poursuivre votre formation concernant IoT Suite, découvrez comment [personnaliser une solution préconfigurée][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
