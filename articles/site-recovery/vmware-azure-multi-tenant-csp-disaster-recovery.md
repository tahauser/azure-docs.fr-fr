---
title: Configurer la réplication VMware sur Azure dans un environnement multilocataire à l’aide de Site Recovery et du programme du fournisseur de solutions cloud | Microsoft Docs
description: Décrit comment créer et gérer des abonnements de locataires via CSP et déployer Azure Site Recovery dans une configuration d’architecture mutualisée
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 25591acb3f046744400f5dcf20a7ea651a7bcf54
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="set-up-vmware-replication-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurer la réplication VMware dans un environnement multilocataire avec le programme du fournisseur de solutions cloud

Le [programme CSP](https://partner.microsoft.com/en-US/cloud-solution-provider) met en avant des témoignages de collaboration pour tous les services cloud de Microsoft, notamment Office 365, Enterprise Mobility Suite et Microsoft Azure. Grâce au fournisseur de solutions cloud, les partenaires établissent une relation de bout en bout avec leurs clients et deviennent leur point de contact principal. Les partenaires peuvent déployer des abonnements Azure pour les clients et combiner ces abonnements avec leurs propres offres personnalisées à valeur ajoutée.

Avec [Azure Site Recovery](site-recovery-overview.md), en tant que partenaires, vous pouvez gérer la récupération d’urgence pour les clients directement par le biais du fournisseur de solutions cloud. Vous pouvez également utiliser le fournisseur de solutions cloud pour configurer des environnements Site Recovery et permettre aux clients de gérer leurs propres besoins de récupération d’urgence en libre-service. Dans les deux cas, les partenaires jouent le rôle de liaison entre la récupération de site et leurs clients. Les partenaires assument la relation client et facturent l’utilisation de Site Recovery.

Cet article décrit comment un partenaire peut créer et gérer des abonnements de locataires par le biais du fournisseur de solutions cloud, dans le cadre d’un scénario de réplication VMware multilocataire.

## <a name="prerequisites"></a>Prérequis


Pour configurer la réplication VMware, vous avez besoin d’effectuer les opérations suivantes :

- [Préparer](tutorial-prepare-azure.md) des ressources Azure, notamment un abonnement Azure, un réseau virtuel Azure et un compte de stockage.
- [Préparer](vmware-azure-tutorial-prepare-on-premises.md) des serveurs et machines virtuelles VMware locaux. 
- Pour chaque locataire, créez un serveur d’administration distinct capable de communiquer avec les machines virtuelles du locataire et vos serveurs vCenter. Vous seul en tant que partenaire devez avoir des droits d’accès à ce serveur d’administration. Découvrez-en plus sur les [environnements multilocataires](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Créer un compte locataire

1. Via [Microsoft Partner Center](https://partnercenter.microsoft.com/), connectez-vous à votre compte CSP.
2. Dans le menu **Tableau de bord**, sélectionnez **Clients**.
3. Sur la page qui s’affiche, cliquez sur le bouton **Ajouter un client**.
4. Dans la page **Nouveau client**, renseignez les informations du compte du locataire. 

    ![La page Informations du compte](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Ensuite, cliquez sur **Suivant : Abonnements**.
6. Dans la page de sélection des abonnements, cochez la case **Microsoft Azure**. Vous pouvez ajouter d’autres abonnements maintenant ou à tout moment.
7. Sur la page **Révision**, vérifiez les détails du locataire, puis cliquez sur **Envoyer**.
8. Une fois que vous avez créé le compte locataire, une page de confirmation affichant les détails du compte par défaut et le mot de passe de cet abonnement apparaît. Enregistrez les informations et modifiez le mot de passe ultérieurement si nécessaire, par le biais de la page de connexion au portail Azure.

Vous pouvez partager ces informations avec le locataire en l’état, ou vous pouvez créer et partager un compte distinct si nécessaire.

## <a name="access-the-tenant-account"></a>Accéder au compte locataire

Vous pouvez accéder à l’abonnement du locataire par le biais du tableau de bord du Microsoft Partner Center.

1. Dans la page **Clients**, cliquez sur le nom du compte locataire.
2. Dans la page **Abonnements** du compte locataire, vous pouvez surveiller les abonnements de compte existants et en ajouter d’autres si nécessaire.
3. Pour gérer les opérations de récupération d’urgence du locataire, sélectionnez **Toutes les ressources (portail Azure)**. Cela vous permet d’accéder aux abonnements Azure du locataire.

    ![Le lien Toutes les ressources](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Vous pouvez vérifier l’accès en cliquant sur le lien Azure Active Directory en haut à droite du portail Azure.

    ![Lien Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Vous pouvez maintenant effectuer et gérer toutes les opérations Site Recovery pour le locataire dans le portail Azure. Pour accéder à l’abonnement locataire via CSP pour la récupération d’urgence gérée, suivez la procédure décrite précédemment.

## <a name="deploy-resources-to-the-tenant-subscription"></a>Déployer des ressources sur l’abonnement locataire

1. Sur le portail Azure, créez un groupe de ressources, puis déployez un coffre Recovery Services en suivant la procédure habituelle.
2. Téléchargez la clé d’inscription du coffre.
3. Inscrivez le serveur de configuration du locataire à l’aide de la clé d’inscription du coffre.

4. Entrez les informations d’identification des deux comptes, le compte permettant d’accéder au serveur vCenter et le compte permettant d’accéder à la machine virtuelle.

    ![Comptes serveur de configuration de gestionnaire](./media/vmware-azure-multi-tenant-csp-disaster-recovery/config-server-account-display.png)

## <a name="register-servers-in-the-vault"></a>Inscrire les serveurs dans le coffre

1. Dans le portail Azure, dans le coffre que vous avez créé précédemment, inscrivez le serveur vCenter auprès du serveur de configuration, en utilisant le compte vCenter que vous avez créé. 
2. Terminez la « Préparation de l’infrastructure » pour la récupération de site en suivant la procédure habituelle.
3. Les machines virtuelles sont maintenant prêtes pour la réplication. Vérifiez que seuls les machines virtuelles du locataire apparaissent dans **Répliquer** > **Sélectionner des machines virtuelles**.


## <a name="assign-tenant-access-to-the-subscription"></a>Affecter un accès locataire à l’abonnement

1. Vérifiez que l’infrastructure de récupération d’urgence est configurée. Notez que vous devez accéder aux abonnements du locataire par le biais du portail du fournisseur de solutions cloud, que la récupération d’urgence soit gérée ou en libre-service. Vous devez configurer votre coffre, puis inscrire l’infrastructure auprès des abonnements du locataire.
2. Fournir au locataire le [compte que vous avez créé](#create-a-tenant-account)
3. Vous pouvez ajouter un nouvel utilisateur à l’abonnement du locataire par le biais du portail du fournisseur de solutions cloud comme suit :

    a) Accédez à la page d’abonnement du fournisseur de solutions cloud du locataire, puis sélectionnez l’option **Utilisateurs et licences**.

        ![The tenant's CSP subscription page](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Créez maintenant un utilisateur en entrant les informations adéquates et en sélectionnant des autorisations ou en chargeant la liste des utilisateurs dans un fichier CSV.
    c) Une fois que vous avez créé un utilisateur, revenez au portail Azure. Dans la page **Abonnement**, sélectionnez l’abonnement approprié.
    d) Sélectionnez **Contrôle d’accès (IAM)**, puis cliquez sur **Ajouter** pour ajouter un utilisateur avec le niveau d’accès approprié. Les utilisateurs créés par le biais du portail du fournisseur de solutions cloud apparaissent automatiquement dans la page qui s’ouvre après avoir cliqué sur un niveau d’accès.

        ![Add a user](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Le rôle *Contributeur* suffit pour la plupart des opérations de gestion. Les utilisateurs disposant de ce niveau d’accès peuvent effectuer toutes les opérations sur un abonnement, à l’exception du changement de niveau d’accès (pour cette opération, le niveau d’accès *Propriétaire* est nécessaire).
- Site Recovery propose en outre trois [rôles d’utilisateur prédéfinis](site-recovery-role-based-linked-access-control.md), qui peuvent être utilisés pour limiter davantage les niveaux d’accès en fonction des besoins.

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez plus en détails](site-recovery-role-based-linked-access-control.md) le contrôle d’accès en fonction du rôle pour gérer les déploiements Azure Site Recovery.
- [Découvrez plus en détails](vmware-azure-architecture.md) l’architecture de la réplication VMware sur Azure.
- [Passez en revue le tutoriel](vmware-azure-tutorial.md) pour répliquer des machines virtuelles VMware sur Azure.
