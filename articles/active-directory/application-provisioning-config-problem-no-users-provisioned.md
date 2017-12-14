---
title: "Aucun utilisateur n’est approvisionné dans une application de la galerie Azure AD | Microsoft Docs"
description: "Résolution des problèmes courants rencontrés lorsque les utilisateurs ne figurent pas dans une application de la galerie Azure AD pour que vous avez configurée pour l’approvisionnement d’utilisateurs avec Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: asteen
ms.openlocfilehash: a36d60b8915ae0b46226bb2127829a8f1767daba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Aucun utilisateur n’est approvisionné dans une application de la galerie Azure AD

Une fois que l’approvisionnement automatique a été configuré pour une application (avec notamment la vérification des informations d’identification de l’application fournies à Azure AD pour la connexion à l’application), l’approvisionnement des utilisateurs et/ou des groupes dans l’application est déterminé par les éléments suivants :

-   Quels utilisateurs et groupes ont été **affectés** à l’application. Pour plus d’informations sur l’affectation, consultez [Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

-   Les **mappages d’attributs** sont-ils activés et configurés pour synchroniser les attributs valides d’Azure AD avec l’application. Pour plus de détails sur les mappages d’attributs, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

-   Un **filtre d’étendue** permet-il de filtrer les utilisateurs en fonction de valeurs d’attribut spécifiques. Pour plus d’informations sur les filtres d’étendue, consultez [Approvisionnement d’applications basé sur les attributs avec filtres d’étendue](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

Si vous constatez que les utilisateurs ne sont pas approvisionnés, consultez les journaux d’audit dans Azure AD et recherchez les entrées de journal relatives à un utilisateur spécifique.

Les journaux d’audit d’approvisionnement sont accessibles dans le portail Azure, sous l’onglet **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt; Journaux d’audit**. Filtrez les journaux sur la catégorie **Approvisionnement des comptes** pour afficher uniquement les événements d’approvisionnement de cette application. Vous pouvez rechercher des utilisateurs en fonction de l’ID de correspondance qui a été configuré pour eux dans les mappages d’attributs. Par exemple, si vous avez configuré le nom d’utilisateur principal ou l’adresse de messagerie en tant qu’attribut correspondant côté Azure AD, et si l’utilisateur qui n’est pas approvisionné a la valeur « audrey@contoso.com », recherchez les journaux d’audit correspondant à « audrey@contoso.com » et passez en revue les entrées renvoyées.

Les journaux d’audit d’approvisionnement enregistrent toutes les opérations effectuées par le service d’approvisionnement, y compris l’interrogation d’Azure AD concernant les utilisateurs qui se trouvent dans l’étendue de l’approvisionnement, l’interrogation de l’application cible concernant l’existence de ces utilisateurs, et la comparaison des objets utilisateur du système. Ajoutez ensuite, mettez à jour ou désactivez le compte d’utilisateur dans le système cible en fonction de cette comparaison.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Problèmes généraux d’approvisionnement à prendre en compte

Voici une liste des problèmes généraux que vous pouvez explorer si vous savez par où commencer.

* [Le service d’approvisionnement ne semble pas démarrer](#provisioning-service-does-not-appear-to-start)
* [Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, bien qu’ils soient affectés](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Le service d’approvisionnement ne semble pas démarrer

Si vous définissez le paramètre **État de la configuration** sur **Activé** dans la section **Azure Active Directory &gt; Applications Entreprise &gt; \[Nom de l’application\] &gt;Approvisionnement** du portail Azure. Cependant, si aucune autre information d’état n’est affichée sur cette page après de nouveaux chargements, il est probable que le service est en cours d’exécution mais qu’il n’a pas encore terminé la synchronisation initiale. Vérifiez les **journaux d’audit** décrits ci-dessus pour déterminer les opérations effectuées par le service, et la présence éventuelle d’erreurs.

>[!NOTE]
>Une synchronisation initiale peut prendre de 20 minutes à plusieurs heures, en fonction de la taille de l’annuaire Azure AD et du nombre d’utilisateurs présents dans l’étendue de l’approvisionnement. Les synchronisations qui suivent la synchronisation initiale sont plus rapides, car le service d’approvisionnement stocke les filigranes qui représentent l’état des deux systèmes après la synchronisation initiale. Cela améliore les performances des synchronisations suivantes.
>
>

## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Les journaux d’audit indiquent que les utilisateurs sont ignorés et non approvisionnés, bien qu’ils soient affectés

Lorsqu’un utilisateur apparaît comme « ignoré » dans les journaux d’audit, il est essentiel de consulter les détails du message du journal pour en déterminer la raison. Voici les raisons les plus courantes et les solutions correspondantes :

-   **Un filtre d’étendue a été configuré****et exclut l’utilisateur en fonction d’une valeur d’attribut**. Pour plus d’informations sur les filtres d’étendue, consultez <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

-   **L’utilisateur n’est pas « autorisé de manière effective ».** Ce message d’erreur indique un problème concernant l’enregistrement d’affectation d’utilisateurs stocké dans Azure AD. Pour résoudre ce problème, supprimez l’affectation de l’utilisateur (ou du groupe) à partir de l’application, puis réaffectez l’utilisateur. Pour plus d’informations sur l’affectation, consultez <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

-   **Un attribut requis manque ou n’est pas indiqué pour un utilisateur.** Lors de la configuration de l’approvisionnement, il est important de vérifier et configurer les mappages d’attributs et les workflows qui définissent les propriétés de l’utilisateur (ou du groupe) passant d’Azure AD à l’application. Cela inclut la définition d’une « propriété correspondante » réservée à l’utilisation et à la mise en correspondance des utilisateurs/groupes entre les deux systèmes. Pour plus de détails sur ce processus important, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings).

  * **Mappages d’attributs pour les groupes :** approvisionnement du nom du groupe et des détails du groupe, en plus des membres, si la prise en charge est effective pour certaines applications. Vous pouvez activer ou désactiver cette fonctionnalité en activant ou désactivant le **mappage** pour les objets de groupe affichés dans l’onglet **Approvisionnement**. Si les groupes d’approvisionnement sont activés, veillez à passer en revue les mappages d’attributs afin de vous assurer qu’un champ approprié est utilisé pour l’« ID correspondant ». Il peut s’agir du nom d’affichage ou de l’alias de courrier électronique dans la mesure où le groupe et ses membres ne sont pas approvisionnés si la propriété correspondante est vide ou n’est pas remplie pour un groupe dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes
[Azure AD Connect Sync : présentation de l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md)

