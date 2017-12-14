---
title: Utilisateurs sous licence dans Azure Active Directory | Microsoft Docs
description: "Découvrez comment accorder une licence à vos utilisateurs et à vous-même dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: mtillman
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: bc210b83a9eeb947a15b60548e43096bd9e11c45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Guide de démarrage rapide : Utilisateurs sous licence dans Azure Active Directory
Les services Azure AD basés sur des licences reposent sur l’activation d’un abonnement Azure Active Directory (Azure AD) dans votre client Azure. Une fois l’abonnement actif, les fonctionnalités de service sont gérées par des administrateurs Azure AD et utilisées par des utilisateurs sous licence. Quand vous achetez Enterprise Mobility + Security, Azure AD Premium ou Azure AD Basic, votre locataire est mis à jour avec l’abonnement, notamment sa période de validité et les licences prépayées. Les informations de votre abonnement, notamment le nombre de licences attribuées ou disponibles, sont accessibles par l’intermédiaire du portail Azure sous **Azure Active Directory** en ouvrant la vignette **Licences**. Le panneau **Licences** constitue également l’emplacement idéal pour gérer vos attributions de licence.

Bien qu’il suffise d’obtenir un abonnement pour configurer les fonctionnalités payantes, vous devez quand même attribuer des licences utilisateur pour les fonctionnalités Azure AD payantes. Une licence doit être attribuée à tout utilisateur ayant besoin d’accéder à une fonctionnalité Azure AD payante ou géré par le biais de ce type de fonctionnalité. L’attribution de licence est un mappage entre un utilisateur et un service acheté, comme Azure AD Premium, Azure AD Basic ou Enterprise Mobility + Security.

Vous pouvez utiliser l’[attribution de licence basée sur le groupe](active-directory-licensing-whatis-azure-portal.md) pour configurer des règles telles que les suivantes :
* Tous les utilisateurs de votre annuaire obtiennent automatiquement une licence
* Toute personne avec le poste approprié obtient une licence
* Vous pouvez déléguer la décision à d’autres responsables au sein de l’organisation (à l’aide de [groupes en libre-service](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Pour obtenir une discussion détaillée de l’attribution des licences à des groupes, avec notamment des scénarios avancés et des scénarios d’attribution de licences Office 365, consultez [Affecter des licences à un groupe d’utilisateurs dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Attribuer des licences aux utilisateurs et groupes
Avec un abonnement actif, vous devez d’abord vous attribuer une licence et actualiser votre navigateur afin de bien afficher toutes les fonctionnalités attendues incluses dans votre abonnement. L’étape suivante consiste à attribuer des licences aux utilisateurs qui doivent accéder aux fonctionnalités Azure AD payantes. Un moyen simple d’attribuer des licences consiste à les attribuer à des groupes d’utilisateurs plutôt qu’à des personnes. Quand vous attribuez des licences à un groupe, tous les membres de ce groupe disposent d’une licence. Si des utilisateurs sont ajoutés au groupe ou en sont supprimés, la licence appropriée leur est automatiquement attribuée ou retirée. 

> [!NOTE]
> Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété **Emplacement d’utilisation** pour l’utilisateur. Vous pouvez définir cette propriété sous **Utilisateur** &gt; **Profil** &gt; **Paramètres** dans le portail Azure. Quand vous attribuez une licence à un groupe, tout utilisateur dont l’emplacement d’utilisation n’est pas spécifié hérite de l’emplacement de l’annuaire.

Pour attribuer une licence, sous **Azure Active Directory** &gt; **Licences** &gt; **Tous les produits**, sélectionnez un ou plusieurs produits, puis sélectionnez **Affecter** dans la barre de commandes.

![Sélectionner une licence à attribuer](media/license-users-groups/select-license-to-assign.png)

Vous pouvez utiliser le panneau **Utilisateurs et groupes** pour sélectionner plusieurs utilisateurs ou groupes ou pour désactiver des plans de service dans le produit. Utilisez la zone de recherche en haut pour rechercher des noms d’utilisateurs et de groupes.

![Sélectionner un utilisateur ou un groupe pour l’attribution de licences](media/license-users-groups/select-user-for-license-assignment.png)

Quand vous attribuez une licence au groupe, un certain temps peut être nécessaire pour que tous les utilisateurs héritent de la licence, en fonction de la taille du groupe. Vous pouvez suivre l’avancement du traitement dans le panneau **Groupe**, sous la vignette **Licences**.

![Statut d’affectation de licence](media/license-users-groups/license-assignment-status.png)

Des erreurs d’affectation peuvent se produire lors de l’affectation des licences Azure AD, mais elles restent relativement rares lors de la gestion des produits Azure AD et Enterprise Mobility + Security. Les erreurs d’attribution se limitent aux problèmes suivants :
- Conflit d’attribution : lorsqu’un utilisateur a précédemment reçu une licence incompatible avec la licence actuelle. Dans ce cas, l’affectation de la nouvelle licence nécessite la suppression de la licence active.
- Dépassement du nombre de licences disponibles : quand le nombre d’utilisateurs des groupes attribués dépasse le nombre de licences disponibles, le statut d’affectation d’un utilisateur reflète l’échec de l’affectation du fait de licences manquantes.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Affectation de licences Azure Active Directory B2B Collaboration

La collaboration B2B vous permet d’inviter des utilisateurs invités dans votre locataire Azure AD pour fournir l’accès à des services Azure AD et à toutes ressources Azure que vous mettez à disposition.  

L’invitation d’utilisateurs B2B et leur attribution à une application dans Azure AD n’entraînent aucun frais. Jusqu’à 10 applications par utilisateur invité et trois rapports de base sont également gratuits pour les utilisateurs B2B Collaboration. Si des licences appropriées sont affectées à votre utilisateur dans le locataire Azure AD du partenaire, il disposera également d’une licence dans votre locataire.

Cela n’est pas obligatoire, mais si vous souhaitez fournir l’accès à des fonctionnalités Azure AD payantes, vous devez accorder à ces utilisateurs invités B2B des licences Azure AD appropriées. Un locataire invitant avec une licence Azure payée peut accorder des droits d’utilisateur B2B Collaboration à cinq utilisateurs invités supplémentaires. Pour plus d’informations et pour voir des scénarios, consultez le [Guide d’attribution de licences pour Azure Active Directory B2B Collaboration](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Visualiser les licences attribuées

Un récapitulatif des licences attribuées et disponibles est disponible sous **Azure Active Directory** &gt; **Licences** &gt; **Tous les produits**.

![Afficher le récapitulatif des licences](media/license-users-groups/view-license-summary.png)

Une liste détaillée des utilisateurs et groupes attribués est disponible quand vous sélectionnez un produit spécifique. La liste **Utilisateurs sous licence** affiche tous les utilisateurs consommant actuellement une licence et indique si la licence a été attribuée directement à l’utilisateur ou si elle est héritée d’un groupe.

![Afficher le détail des licences](media/license-users-groups/view-license-detail.png)

De même, la liste **Groupes sous licence** montre tous les groupes auxquels des licences ont été attribuées. Sélectionnez un utilisateur ou un groupe pour ouvrir le panneau **Licences**, qui montre toutes les licences attribuées à cet objet.

## <a name="remove-a-license"></a>Supprimer une licence

Pour supprimer une licence, accédez à l’utilisateur ou au groupe et ouvrez la vignette **Licences**. Sélectionnez la licence, puis cliquez sur **Supprimer**.

![Supprimer une licence](media/license-users-groups/remove-license.png)

Les licences héritées par l’utilisateur à partir d’un groupe ne peuvent pas être supprimées directement. Au lieu de cela, supprimez l’utilisateur du groupe à partir duquel il a hérité de la licence.


## <a name="next-steps"></a>Étapes suivantes
Ce guide de démarrage rapide vous a expliqué comment attribuer des licences aux utilisateurs et aux groupes dans l’annuaire Azure AD. 

Vous pouvez utiliser le lien suivant pour configurer les attributions de licences d’abonnement dans Azure AD à travers le portail Azure.

> [!div class="nextstepaction"]
> [Attribuer des licences Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 