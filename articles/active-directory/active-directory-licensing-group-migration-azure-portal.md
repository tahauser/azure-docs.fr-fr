---
title: "Migration des utilisateurs individuels sous licence vers une licence basée sur le groupe dans Azure Active Directory | Microsoft Docs"
description: "Comment passer de licences utilisateur individuelles à des licences basées sur le groupe avec Azure Active Directory"
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
ms.date: 01/14/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: 47d7bf5a41c140c6d6769b549163a3dadfa5ef1c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Ajout d’utilisateurs sous licence à un groupe de licences dans Azure Active Directory

Vous pouvez disposer de licences existantes déployées pour les utilisateurs dans les organisations par le biais d’une « attribution directe », autrement dit utiliser des scripts PowerShell ou d’autres outils pour attribuer des licences utilisateur individuelles. Si vous souhaitez utiliser une licence basée sur le groupe pour gérer les licences de votre organisation, vous devez recourir à un plan de migration afin de remplacer en toute transparence les solutions existantes par une licence basée le groupe.

La chose la plus importante à retenir est que vous devez éviter toute situation dans laquelle la migration vers une licence basée sur le groupe amène les utilisateurs à perdre de manière temporaire les licences qui leur sont actuellement attribuées. Tous les processus susceptibles d’entraîner le retrait de licences doivent être évités afin d’éliminer le risque pour les utilisateurs de perdre l’accès aux services et à leurs données.

## <a name="recommended-migration-process"></a>Processus de migration recommandé

1. Votre gestion de l’attribution et de la suppression des licences utilisateur est actuellement automatisée (par exemple, avec PowerShell). Conservez ce paramétrage.

2. Créez un groupe de gestion des licences (ou choisissez les groupes existants à utiliser) et vérifiez que tous les utilisateurs requis sont ajoutés en tant que membres.

3. Attribuez les licences nécessaires à ces groupes. Vous devez refléter l’état de licence que votre procédure de gestion automatisée existante (par exemple, avec PowerShell) applique à ces utilisateurs.

4. Vérifiez que les licences ont bien été appliquées à tous les utilisateurs de ces groupes. Pour ce faire, vérifiez l’état de traitement de chaque groupe, ainsi que les journaux d’audit.

  - Vous pouvez vérifier ponctuellement certains utilisateurs individuels en examinant les détails de leur licence. Vous constaterez qu’ils disposent des mêmes licences attribuées « directement » et « héritées » de groupes.

  - Vous pouvez exécuter un script PowerShell pour [vérifier les méthodes d’attribution des licences aux utilisateurs](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Lorsque la même licence produit est attribuée à l’utilisateur à la fois directement et par le biais d’un groupe, une seule licence est employée par l’utilisateur. Par conséquent, aucune licence supplémentaire n’est requise pour la migration.

5. Assurez-vous qu’aucune attribution de licence n’a échoué en vérifiant les utilisateurs en état d’erreur dans chaque groupe. Pour plus d’informations, consultez [Identification et résolution des problèmes de licence pour un groupe](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Supprimez les attributions directes initiales. Vous pouvez le faire graduellement, par « vagues » afin de superviser au préalable le résultat sur un sous-ensemble d’utilisateurs.

  Vous pouvez laisser les attributions directes aux utilisateurs. Toutefois, si les utilisateurs quittent leurs groupes sous licence, cela signifie qu’ils conserveront la licence initiale, ce que vous ne souhaitez peut-être pas.

## <a name="an-example"></a>exemple

Une organisation compte 1 000 utilisateurs. Tous les utilisateurs ont besoin d’une licence Enterprise Mobility + Security (EMS). 200 utilisateurs travaillent dans le service financier et ont besoin d’une licence Office 365 Entreprise E3. Un script PowerShell s’exécute en local afin d’ajouter et de supprimer les licences des utilisateurs qui arrivent ou s’en vont. Nous souhaitons remplacer ce script par une licence basée sur le groupe afin que les licences soient gérées automatiquement par Azure AD.

Voici ce à quoi le processus de migration peut ressembler :

1. Sur le Portail Azure, attribuez la licence EMS au groupe **Tous les utilisateurs** dans Azure AD. Attribuez la licence E3 au groupe **Service financier** qui contient tous les utilisateurs concernés.

2. Dans chaque groupe, vérifiez que l’attribution de licence est effectuée pour tous les utilisateurs. Accédez au panneau de chaque groupe, sélectionnez **Licences**, puis vérifiez l’état de traitement en haut du panneau **Licences**.

  - Recherchez « Les dernières modifications de licence ont été appliquées à tous les utilisateurs » afin de confirmer la fin de l’opération.

  - Recherchez une notification en haut de la page, indiquant les utilisateurs pour lesquels des licences n’ont peut-être pas été correctement attribuées. Avons-nous manqué de licences pour certains utilisateurs ? Certains utilisateurs présentent-ils des références de licence en conflit qui les empêchent d’hériter des licences de groupe ?

3. Vérifiez ponctuellement certains utilisateurs afin de veiller à ce qu’à la fois les licences directes et les licences de groupe leur soient bien appliquées. Accédez au panneau relatif à un utilisateur, sélectionnez **Licences**, puis examinez l’état des licences.

  - Voici l’état utilisateur attendu lors de la migration :

      ![État utilisateur attendu](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Cela confirme que l’utilisateur possède des licences directes et héritées. Il apparaît que des licences **EMS** et **E3** sont attribuées.

  - Sélectionnez chaque licence pour afficher les détails portant sur les services actifs. Cette procédure peut être utilisée pour vérifier si les licences directes et les licences de groupe activent exactement les mêmes plans de service pour l’utilisateur.

      ![Vérification des plans de service](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Après avoir confirmé l’équivalence des licences directes et des licences de groupe, vous pouvez commencer à supprimer les licences directes des utilisateurs. Vous pouvez tester cette procédure en supprimant ces licences pour des utilisateurs individuels sur le portail, puis exécuter des scripts d’automatisation afin de les supprimer en bloc. Voici l’exemple d’un même utilisateur dont les licences directes ont été supprimées par le biais du portail. L’état de licence reste inchangé, mais les attributions directes n’apparaissent plus.

  ![Licences directes supprimées](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des groupes, consultez :

* [Assigning licenses to a group in Azure Active Directory (Attribution de licences à un groupe dans Azure Active Directory)](active-directory-licensing-group-assignment-azure-portal.md)
* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory group-based licensing additional scenarios (Autres scénarios de licence basée sur le groupe Azure Active Directory)](active-directory-licensing-group-advanced.md)
