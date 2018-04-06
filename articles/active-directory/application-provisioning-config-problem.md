---
title: Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD | Microsoft Docs
description: Comment résoudre les problèmes courants rencontrés lors de la configuration l’approvisionnement des utilisateurs pour une application déjà répertoriée dans la galerie d’applications Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9fcfee9cbbeb01422e7d9e92d90b908e054f96da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problèmes lors de la configuration de l’approvisionnement des utilisateurs pour une application relevant de la galerie Azure AD

La configuration de [l’approvisionnement automatique des utilisateurs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) pour une application (lorsqu’il est pris en charge) implique de respecter des instructions spécifiques pour la préparation de l’application. Vous pouvez ensuite utiliser le Portail Azure afin de configurer le service d’approvisionnement pour synchroniser les comptes d’utilisateur avec l’application.

Vous devez toujours commencer par rechercher le didacticiel spécifique à la configuration de l’approvisionnement pour votre application. Suivez ensuite ces étapes afin de configurer l’application et Azure AD pour créer la connexion d’approvisionnement. Vous trouverez une liste de didacticiels pour les applications à la page [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Comment savoir si l’approvisionnement fonctionne 

Une fois que le service est configuré, la plupart des informations sur son fonctionnement peuvent être obtenues à deux emplacements :

-   **Journaux d’audit** : les journaux d’audit d’approvisionnement enregistrent toutes les opérations effectuées par le service d’approvisionnement, y compris l’interrogation d’Azure AD concernant les utilisateurs assignés qui se trouvent dans l’étendue de l’approvisionnement. Interrogez l’application cible concernant l’existence de ces utilisateurs en comparant les objets utilisateur du système. Puis, ajoutez, mettez à jour ou désactivez le compte d’utilisateur dans le système cible en fonction de cette comparaison. Les journaux d’audit d’approvisionnement sont accessibles dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt; Journaux d’audit**. Filtrez les journaux sur la catégorie **Approvisionnement de comptes** pour afficher uniquement les événements d’approvisionnement de cette application.

-   **État de l’approvisionnement :** un résumé de la dernière exécution de l’approvisionnement pour une application donnée est disponible sous **Azure Active Directory &gt; Applications d’entreprise &gt;\[Nom de l’application\]&gt;Approvisionnement**, en bas de l’écran, sous les paramètres du service. Cette section affiche le nombre d’utilisateurs (et/ou groupes) en cours de synchronisation entre les deux systèmes et indique les erreurs éventuelles. Les erreurs sont détaillées dans les journaux d’audit. Notez que l’état de l’approvisionnement n’est renseigné que lorsqu’une synchronisation initiale complète a été effectuée entre Azure AD et l’application.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Problèmes d’approvisionnement généraux à prendre en compte

Voici une liste des problèmes généraux que vous pouvez explorer si vous savez par où commencer.

* [Le service d’approvisionnement ne semble pas démarrer](#provisioning-service-does-not-appear-to-start)
* [Impossible d’enregistrer la configuration car les informations d’identification de l’application ne fonctionnent pas](#can’t-save-configuration-due-to-app-credentials-not-working)
* [Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, alors qu’ils sont affectés](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Le service d’approvisionnement ne semble pas démarrer

Si vous définissez le paramètre **État de l’approvisionnement** sur **Activé** dans la section **Azure Active Directory &gt; Applications d’entreprise &gt; \[Nom de l’application\] &gt;Approvisionnement** du Portail Azure. Cependant aucune autre information d’état n’apparaît sur cette page après de nouveaux chargements. Le service est probablement en cours d’exécution mais n’a pas encore terminé la synchronisation initiale. Vérifiez les **journaux d’audit** décrits ci-dessus pour déterminer les opérations effectuées par le service et la présence éventuelle d’erreurs.

>[!NOTE]
>Une synchronisation initiale peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs présents dans l’étendue de l’approvisionnement. Les synchronisations qui suivent la synchronisation initiale sont plus rapides, car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale, ce qui améliore les performances des synchronisations suivantes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Impossible d’enregistrer la configuration car les informations d’identification de l’application ne fonctionnent pas

Pour un fonctionnement correct de l’approvisionnement, Azure AD nécessite des informations d’identification valides qui lui permettent de se connecter à une API de gestion des utilisateurs fournie par cette application. Si ces informations d’identification ne fonctionnent pas ou si vous ne les connaissez pas, consultez le didacticiel dédié à la configuration de cette application (décrit précédemment).

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, alors qu’ils sont affectés

Lorsqu’un utilisateur apparaît comme « ignoré » dans les journaux d’audit, il est essentiel de consulter les détails du message du journal pour en déterminer la raison. Voici les raisons les plus courantes et les solutions correspondantes :

-   **Un filtre d’étendue a été configuré****et exclut l’utilisateur en fonction d’une valeur d’attribut**. Pour plus d’informations sur les filtres d’étendue, voir <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **L’utilisateur n’est pas « autorisé de manière effective ».** Ce message d’erreur indique un problème concernant l’enregistrement d’affectation d’utilisateurs stocké dans Azure AD. Pour résoudre ce problème, supprimez l’affectation de l’utilisateur (ou du groupe) à partir de l’application, puis réaffectez l’utilisateur. Pour plus d’informations sur l’affectation, voir <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Un attribut requis manque ou n’est pas indiqué pour un utilisateur.** Lors de la configuration de l’approvisionnement, il est important de vérifier et configurer les mappages d’attributs et les workflows qui définissent les propriétés de l’utilisateur (ou du groupe) passant d’Azure AD à l’application. Cela inclut la définition d’une « propriété correspondante » réservée à l’utilisation et à la mise en correspondance des utilisateurs/groupes entre les deux systèmes. Pour plus d’informations sur ce processus important, voir <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

   * **Mappages d’attributs pour les groupes :** approvisionnement du nom du groupe et des détails du groupe, en plus des membres, si la prise en charge est effective pour certaines applications. Vous pouvez activer ou désactiver cette fonctionnalité en activant ou désactivant le **mappage** pour les objets de groupe affichés dans l’onglet **Approvisionnement**. Si les groupes d’approvisionnement sont activés, veillez à passer en revue les mappages d’attributs afin de vous assurer qu’un champ approprié est utilisé pour l’« ID correspondant ». Il peut s’agir du nom d’affichage ou de l’alias de courrier électronique dans la mesure où le groupe et ses membres ne sont pas approvisionnés si la propriété correspondante est vide ou n’est pas remplie pour un groupe dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes
[Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)
