---
title: "Affecter des licences à un groupe dans Azure Active Directory | Microsoft Docs"
description: "Comment affecter des licences à l’aide d’une gestion des licences de groupe Azure Active Directory"
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f24daaf5690eb1d9a0ab3b27a3626d03e6021d99
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Affecter des licences aux utilisateurs par appartenance aux groupes dans Azure Active Directory

Cet article présente l’affectation de licences de produit à un groupe d’utilisateurs avec Azure Active Directory (Azure AD), puis la vérification que les licences ont été correctement reçues.

Dans cet exemple, le locataire contient un groupe de sécurité appelé **HR Department**. Ce groupe inclut tous les membres du département des ressources humaines (environ 1 000 utilisateurs). Vous souhaitez attribuer des licences Office 365 Entreprise E3 à l’ensemble du département. Le service Yammer Enterprise inclus dans le produit doit être temporairement désactivé jusqu’à ce que le département soit prêt à l’utiliser. L’administrateur souhaite également déployer des licences Enterprise Mobility + Security vers le même groupe d’utilisateurs.

> [!NOTE]
> Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété Emplacement d’utilisation sur l’utilisateur.

> Pour l’affectation d’une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire. Si vos utilisateurs sont situés à plusieurs emplacements, nous vous recommandons de définir systématiquement l’emplacement d’utilisation dans le cadre de votre flux de création d’utilisateur dans Azure AD (par exemple, par le biais d’une configuration AAD Connect). Cela garantit que le résultat de l’affectation de licence est toujours correct et que les utilisateurs ne reçoivent pas de services à des emplacements non autorisés.

## <a name="step-1-assign-the-required-licenses"></a>Étape 1 : Affecter les licences requises

1. Connectez-vous au [**portail Azure**](https://portal.azure.com) avec un compte administrateur. Pour gérer les licences, le compte doit être un rôle Administrateur général ou Administrateur des comptes d’utilisateur.

2. Sélectionnez **Tous les services** dans le volet de navigation de gauche, puis sélectionnez **Azure Active Directory**. Vous pouvez ajouter ce volet aux Favoris ou l’épingler au tableau de bord du portail.

3. Dans le volet **Azure Active Directory**, sélectionnez **Licences** afin d’ouvrir un volet vous permettant de visualiser et de gérer tous les produits sous licence du locataire.

4. Sous **Tous les produits**, sélectionnez Office 365 Enterprise E3 et Enterprise Mobility + Security en sélectionnant les noms de produits. Pour démarrer l’affectation, sélectionnez **Affecter** en haut du volet.

   ![Tous les produits, affecter des licences](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Dans le volet **Affecter une licence**, cliquez sur **Utilisateurs et groupes** pour ouvrir le volet **Utilisateurs et groupes**. Recherchez le nom du groupe *HR Department*, sélectionnez le groupe, puis veillez à confirmer en cliquant sur **Sélectionner** au bas du volet.

   ![Sélectionner un groupe](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Dans le volet **Affecter une licence**, cliquez sur **Options d’affectation (facultatif)** qui affiche tous les plans de services inclus dans les deux produits que nous avons sélectionnés précédemment. Recherchez **Yammer Enterprise** et définissez-le sur **Désactivé** pour désactiver ce service à partir de la licence du produit. Confirmez en cliquant sur **OK** en bas des **options d’affectation**.

   ![Options d’affectation](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Pour terminer l’affectation, dans le volet **Affecter une licence**, cliquez sur **Affecter** au bas du volet.

8. Une notification indiquant l’état et le résultat du processus s’affiche dans l’angle supérieur droit. Si l’affectation au groupe n’a pas pu être effectuée (par exemple en raison de licences existantes dans le groupe), cliquez sur la notification pour afficher les détails de l’échec.

Nous avons maintenant spécifié un modèle de licence sur le groupe HR Department. Un processus en arrière-plan dans Azure AD a été démarré pour traiter tous les membres existants de ce groupe. Cette opération initiale peut prendre un certain temps en fonction de la taille du groupe. L’étape suivante décrit comment vérifier que le processus est terminé, et comment déterminer si une attention supplémentaire est requise pour résoudre des problèmes.

> [!NOTE]
> Vous pouvez démarrer la même affectation à partir d’un autre emplacement : **Utilisateurs et groupes** dans Azure AD. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes**. Recherchez le groupe, sélectionnez-le et cliquez sur l’onglet **Licences**. Le bouton **Affecter** en haut du volet permet d’ouvrir le volet d’affectation des licences.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Étape 2 : Vérifier que l’affectation initiale est terminée

1. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes**. Recherchez le groupe **HR Department** auquel ont été affectées les licences.

2. Dans le volet du groupe **HR Department**, sélectionnez **Licences**. Cela vous permet de vérifier rapidement si les licences ont été entièrement affectées aux utilisateurs et s’il existe des erreurs que vous devez examiner. Les informations suivantes sont disponibles :

   - Liste des licences du produit qui sont actuellement affectées au groupe. Sélectionnez une entrée pour afficher les services spécifiques qui ont été activés et pour apporter des modifications.

   - État des dernières modifications de licences effectuées sur le groupe (si les modifications sont en cours de traitement ou si le traitement a été effectué sur tous les membres de l’utilisateur).

   - Informations sur les utilisateurs qui se trouvent en état d’erreur parce qu’il n’a pas été possible de leur affecter des licences.

   ![Options d’affectation](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Pour des informations plus détaillées sur le traitement des licences, consultez **Azure Active Directory** > **Utilisateurs et groupes** > *nom du groupe* > **Journaux d’audit**. Notez les activités suivants :

   - Activité : **Commencer à appliquer une licence basée sur le groupe aux utilisateurs**. Elle est consignée lorsque le système sélectionne la modification d’affectation de licences sur le groupe et commence à l’appliquer à tous les membres utilisateur. Elle contient des informations sur la modification qui a été apportée.

   - Activité : **Terminer l’application d’une licence basée sur le groupe aux utilisateurs**. Elle est consignée lorsque le système termine le traitement de tous les utilisateurs du groupe. Elle contient un résumé du nombre d’utilisateurs qui ont été traités avec succès et du nombre d’utilisateurs auxquels les licences du groupe n’ont pas pu être affectées.

   [Lisez cette section](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) pour en savoir plus sur l’utilisation des journaux d’audit pour analyser les modifications apportées par une gestion des licences basée sur un groupe.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Étape 3 : Rechercher des problèmes de licences et les résoudre

1. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes** et recherchez le groupe **HR Department** auquel des licences ont été affectées.
2. Dans le volet du groupe **HR Department**, sélectionnez **Licences**. La notification en haut du volet indique que des licences n’ont pas pu être affectées à 10 utilisateurs. En cliquant sur cette notification, vous accédez à une liste de tous les utilisateurs associés à une erreur de licence pour ce groupe.
3. La colonne **Échec d’affectations** indique que les deux licences de produit n’ont pas pu être affectées aux utilisateurs. La colonne **Raison principale de l’échec** contient la cause de l’échec. Dans ce cas, il s’agit de **Plans de service en conflit**.

   ![Échec d’affectations](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Sélectionnez un utilisateur pour ouvrir le volet **Licences**. Ce volet répertorie toutes les licences qui sont actuellement affectées à l’utilisateur. Dans cet exemple, l’utilisateur a hérité de la licence Office 365 Enterprise E1 du groupe **Kiosk users**. Cette licence est en conflit avec la licence E3 que le système a tenté d’appliquer à partir du groupe **HR Department**. Par conséquent, aucune des licences de ce groupe n’a été assignée à l’utilisateur.

   ![Afficher les licences pour un utilisateur](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Pour résoudre ce conflit, nous supprimons l’utilisateur du groupe **Kiosk users**. Lorsqu’Azure AD a traité la modification, les licences **HR Department** sont affectées correctement.

   ![Licence affectée correctement](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’ensemble de fonctionnalités de gestion des licences par le biais des groupes, consultez les articles suivants :

* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](active-directory-licensing-group-advanced.md)
