---
title: "Bien démarrer avec Azure Active Directory Domain Services | Microsoft Docs"
description: "Activer Azure Active Directory Domain Services à l’aide du portail Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 2a171490faf9804196c93d33c5ee74d22533c044
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Activer Azure Active Directory Domain Services à l’aide du portail Azure


## <a name="task-3-configure-administrative-group"></a>Tâche 3 : configurer le groupe d’administration
Dans cette tâche de configuration, vous allez créer un groupe d’administration dans votre répertoire Azure AD. Ce groupe d’administration spécial est appelé *AAD DC Administrators*. Les membres de ce groupe bénéficient d’autorisations d’administration sur les machines jointes au domaine managé. Sur les ordinateurs joints au domaine, ce groupe est ajouté au groupe « Administrateurs ». En outre, les membres de ce groupe sont également autorisés à utiliser le Bureau à distance pour se connecter à distance aux ordinateurs joints au domaine.

> [!NOTE]
> Vous ne bénéficiez pas des autorisations d’administrateur de domaine ou d’administrateur d’entreprise sur le domaine géré que vous avez créé à l’aide des services de domaine Azure Active Directory. Dans un domaine géré, ces autorisations sont réservées par le service et ne sont pas accessibles aux utilisateurs au sein du client. Toutefois, vous pouvez utiliser le groupe d’administrateurs spécial créé dans cette tâche de configuration, afin d’exécuter des opérations privilégiées. Ces opérations comprennent l’ajout d’ordinateurs au domaine, l’appartenance au groupe Administrateurs sur les ordinateurs joints au domaine, la configuration de stratégie de groupe.
>

L’Assistant crée automatiquement le groupe d’administration dans votre répertoire Azure AD. Ce groupe est appelé « Administrateurs AAD DC ». Si vous disposez d’un groupe du même nom dans votre répertoire Azure AD, l’Assistant sélectionne ce groupe. Vous pouvez configurer l’appartenance au groupe au moyen de la page **Groupe des administrateurs** de l’Assistant.

1. Pour configurer l’appartenance au groupe, cliquez sur **Administrateurs AAD DC**.

    ![Configurer l’appartenance au groupe](./media/getting-started/domain-services-blade-admingroup.png)

2. Cliquez sur le bouton **Ajouter des membres** pour ajouter des utilisateurs au groupe Administrateurs à partir de votre répertoire Azure AD.

3. Lorsque vous avez terminé, cliquez sur **OK** pour accéder à la page **Résumé** de l’Assistant.

4. Dans la page **Résumé** de l’Assistant, examinez les paramètres de configuration du domaine managé. Si nécessaire, vous pouvez revenir à une étape quelconque de l’Assistant pour y apporter des modifications. Lorsque vous avez terminé, cliquez sur **OK** pour créer le domaine managé.

    ![Résumé](./media/getting-started/domain-services-blade-summary.png)

5. Vous voyez une notification indiquant la progression de votre déploiement Azure AD Domain Services. Cliquez sur la notification pour afficher la progression détaillée du déploiement.

    ![Notification - Déploiement en cours](./media/getting-started/domain-services-blade-deployment-in-progress.png)


## <a name="provision-your-managed-domain"></a>Approvisionner votre domaine managé
Le processus d’approvisionnement de votre domaine managé peut prendre jusqu’à une heure.

1. Pendant que votre déploiement est en cours, vous pouvez rechercher « Domain Services » dans la zone de recherche **Rechercher des ressources**. Sélectionnez **Azure AD Domain Services** dans les résultats de la recherche. Le panneau **Azure AD Domain Services** affiche le domaine managé en cours d’approvisionnement.

    ![Rechercher le domaine managé en cours d’approvisionnement](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Pour plus d’informations sur le domaine, cliquez sur le nom du domaine géré (par exemple, « contoso100.com »).

    ![Domain Services - État d’approvisionnement](./media/getting-started/domain-services-provisioning-state.png)

3. L’onglet **Vue d’ensemble** montre que le domaine géré est actuellement en cours d’approvisionnement. Vous ne pouvez pas configurer le domaine managé tant qu’il n’est pas entièrement approvisionné. L’approvisionnement complet de votre domaine managé peut prendre jusqu’à une heure.

    ![Domain Services - Onglet Vue d’ensemble durant l’approvisionnement ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Lorsque le domaine managé est entièrement approvisionné, l’onglet **Vue d’ensemble** affiche l’état du domaine comme **En cours d’exécution**.

    ![Domain Services - Onglet Vue d’ensemble une fois la configuration terminée](./media/getting-started/domain-services-provisioned.png)

5. Sous l’onglet **Propriétés**, vous voyez deux adresses IP auxquelles des contrôleurs de domaine sont disponibles pour le réseau virtuel.

    ![Domain Services - Onglet Propriétés après un approvisionnement complet](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Vous avez besoin d’aide ?
L’approvisionnement de votre domaine géré peut prendre une ou deux heures pour chaque contrôleur de domaine. Si votre déploiement a échoué ou est bloqué à l’état « En attente » pendant plusieurs heures, n’hésitez pas à [contactez l’équipe de produit pour obtenir de l’aide](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Étape suivante
[Tâche 4 : Mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md)
