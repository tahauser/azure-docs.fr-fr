---
title: "Gérer une architecture mutualisée dans Azure Site Recovery avec le programme Fournisseur de solutions Cloud (CSP) | Microsoft Docs"
description: "Décrit comment créer et gérer des abonnements de locataires via CSP et déployer Azure Site Recovery dans une configuration d’architecture mutualisée"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>Gérer une architecture mutualisée avec le programme Fournisseur de solutions Cloud (CSP)

Le [programme CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) met en avant des témoignages de collaboration pour tous les services cloud de Microsoft, notamment Office 365, Enterprise Mobility Suite et Microsoft Azure. Grâce à CSP, les partenaires établissent une relation de bout en bout avec les clients et deviennent le point de contact principal. Les partenaires peuvent déployer des abonnements Azure pour les clients et combiner les abonnements avec leurs propres offres personnalisées à valeur ajoutée.

Grâce à Azure Site Recovery, les partenaires peuvent gérer la solution de récupération d’urgence complète pour les clients directement via le CSP. Ils peuvent également utiliser le CSP pour configurer des environnements de récupération de site et permettre aux clients de gérer leurs propres besoins de récupération d’urgence en libre-service. Dans les deux cas, les partenaires jouent le rôle de liaison entre la récupération de site et leurs clients. Les partenaires assument la relation client et facturent les clients pour l’utilisation de la récupération de site.

Cet article décrit comment un partenaire peut créer et gérer des abonnements de locataires via CSP pour une configuration d’environnement VMware mutualisé.

## <a name="prerequisites"></a>Prérequis

- [Préparer](tutorial-prepare-azure.md) des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
- [Préparer](tutorial-prepare-on-premises-vmware.md) des serveurs VMware et des machines virtuelles sur site.
- Pour chaque locataire, créez un serveur d’administration distinct qui peut communiquer avec les machines virtuelles du locataire et le vCenter du partenaire. Seul le partenaire dispose de droits d’accès à ce serveur. Pour plus d’informations sur les différents environnements d’architecture mutualisée, reportez-vous aux conseils sur [l’environnement VMware mutualisé](site-recovery-multi-tenant-support-vmware-using-csp.md).

## <a name="create-a-tenant-account"></a>Créer un compte locataire

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), connectez-vous à votre compte CSP.

2. Dans le menu **Tableau de bord**, sélectionnez **Clients**.

    ![Le lien Clients de Microsoft Partner Center](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. Sur la page qui s’affiche, cliquez sur le bouton **Ajouter un client**.

    ![Le bouton Ajouter un client](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. Sur la page **Nouveau client**, renseignez les informations sur le compte pour le locataire, puis cliquez sur **Suivant : Abonnements**.

    ![La page Informations du compte](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. Sur la page de sélection des abonnements, sélectionnez la case à cocher **Microsoft Azure**. Vous pouvez ajouter d’autres abonnements maintenant ou à tout moment.

    ![La case à cocher d’abonnement Microsoft Azure](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. Sur la page **Révision**, vérifiez les détails du locataire, puis cliquez sur **Envoyer**.

    ![La page Révision](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    Une fois que vous avez créé le compte locataire, une page de confirmation affichant les détails du compte par défaut et le mot de passe de cet abonnement apparaît.

7. Enregistrez les informations et modifiez le mot de passe ultérieurement si nécessaire via la page de connexion au portail Azure.  

    Vous pouvez partager ces informations avec le locataire en l’état, ou vous pouvez créer et partager un compte distinct si nécessaire.

## <a name="access-the-tenant-account"></a>Accéder au compte locataire

Vous pouvez accéder à l’abonnement du locataire via le tableau de bord du Microsoft Partner Center, comme décrit dans « Étape 1 : Créer un compte locataire. »

1. Accédez à la page **Clients**, puis cliquez sur le nom du compte locataire.

2. Sur la page **Abonnements** du compte locataire, vous pouvez surveiller les abonnements de compte existants et ajouter d’autres abonnements si nécessaire. Pour gérer les opérations de récupération d’urgence du locataire, sélectionnez **Toutes les ressources (portail Azure)**.

    ![Le lien Toutes les ressources](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    Cliquer sur **Toutes les ressources** vous permet d’accéder aux abonnements Azure du locataire. Vous pouvez vérifier l’accès en cliquant sur le lien Azure Active Directory en haut à droite du portail Azure.

    ![Lien Azure Active Directory](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

Vous pouvez maintenant effectuer toutes les opérations de récupération de site pour le locataire via le portail Azure et gérer les opérations de récupération d’urgence. Pour accéder à l’abonnement locataire via CSP pour la récupération d’urgence gérée, suivez la procédure décrite précédemment.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Déployer des ressources sur l’abonnement locataire
1. Sur le portail Azure, créez un groupe de ressources, puis déployez un coffre Recovery Services en suivant la procédure habituelle.

2. Téléchargez la clé d’inscription du coffre.

3. Inscrivez le serveur de configuration du locataire à l’aide de la clé d’inscription du coffre.

4. Entrez les informations d’identification pour les deux comptes d’accès : compte d’accès vCenter et compte d’accès de machine virtuelle.

    ![Comptes serveur de configuration de gestionnaire](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Inscrire l’infrastructure de récupération de site dans le coffre Recovery Services
1. Dans le portail Azure, dans le coffre que vous avez créé précédemment, inscrivez le serveur vCenter sur le CS que vous avez inscrit dans « Étape 3 : Déployer des ressources sur l’abonnement locataire. » Pour cela, utilisez le compte d’accès vCenter.
2. Terminez la « Préparation de l’infrastructure » pour la récupération de site en suivant la procédure habituelle.
3. Les machines virtuelles sont maintenant prêtes pour la réplication. Vérifiez que seules les machines virtuelles du locataire sont affichées dans le panneau **Sélectionner des machines virtuelles** sous l’option **Répliquer**.

    ![Liste des machines virtuelles de locataire dans le panneau Sélectionner des machines virtuelles](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>Affecter un accès locataire à l’abonnement

Pour la récupération d’urgence en libre-service, fournissez au locataire les détails du compte, comme indiqué à l’étape 6 de la section « Étape 1 : Créer un compte locataire ». Effectuez cette action une fois que le partenaire a défini l’infrastructure de récupération d’urgence. Si le type de récupération d’urgence est géré ou en libre-service, les partenaires doivent accéder aux abonnements locataire via le portail CSP. Ils configurent le coffre appartenant à un partenaire et inscrivent l’infrastructure dans les abonnements locataire.

Les partenaires peuvent également ajouter un nouvel utilisateur à l’abonnement locataire via le portail CSP en procédant comme suit :

1. Accédez à la page d’abonnement CSP du locataire, puis sélectionnez l’option **Utilisateurs et licences**.

    ![La page d’abonnement CSP du locataire](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    Vous pouvez maintenant créer un nouvel utilisateur en entrant les informations utiles et en sélectionnant des autorisations ou en téléchargeant la liste des utilisateurs dans un fichier CSV.

2. Lorsque vous avez créé un nouvel utilisateur, revenez au portail Azure puis, sur le panneau **Abonnement**, sélectionnez l’abonnement approprié.

3. Sur le panneau qui s’affiche, sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **Ajouter** pour ajouter un utilisateur avec le niveau d’accès approprié.      
    Les utilisateurs qui ont été créés via le portail CSP apparaissent automatiquement dans le panneau qui s’affiche après avoir cliqué sur un niveau d’accès.

    ![Ajouter un utilisateur](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    Le rôle *Contributeur* suffit pour la plupart des opérations de gestion. Les utilisateurs disposant de ce niveau d’accès peuvent effectuer toutes les opérations sur un abonnement, à l’exception du changement de niveau d’accès (pour cette opération, le niveau d’accès *Propriétaire* est nécessaire).

  Azure Site Recovery propose en outre trois [rôles d’utilisateur prédéfinis](site-recovery-role-based-linked-access-control.md), qui peuvent être utilisés pour limiter davantage les niveaux d’accès en fonction des besoins.

## <a name="next-steps"></a>étapes suivantes
  [Découvrez plus en détails](site-recovery-role-based-linked-access-control.md) le contrôle d’accès en fonction du rôle pour gérer les déploiements Azure Site Recovery.

  [Gérer les environnements VMware mutualisés](site-recovery-multi-tenant-support-vmware-using-csp.md)
