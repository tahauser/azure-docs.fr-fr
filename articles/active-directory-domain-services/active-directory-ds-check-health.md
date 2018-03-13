---
title: "Azure AD Domain Services - Vérifier l’intégrité de votre domaine managé | Microsoft Docs"
description: "Vérifiez l’intégrité de votre domaine managé à l’aide de la page d’intégrité dans le portail Azure."
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: a9421ace7abf1f3d45b1f8cd810067d79faa92ec
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>Vérifier l’intégrité d’un domaine managé Azure AD Domain Services

## <a name="overview-of-the-health-page"></a>Vue d’ensemble de la page d’intégrité
À l’aide de la page d’intégrité sur le portail Azure, vous pouvez rester au courant de ce qui se passe sur votre domaine managé. Cet article présente les éléments de la page d’intégrité.

### <a name="how-to-view-the-health-of-your-managed-domain"></a>Comment afficher l’intégrité de votre domaine managé
1. Accédez à la [page Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) dans le portail Azure.
2. Cliquez sur le domaine dont vous souhaitez afficher l’intégrité.
3. Dans le volet de navigation de gauche, cliquez sur **Intégrité**.

L’image suivante illustre un exemple de page d’intégrité : ![exemple de page d’intégrité](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> L’intégrité de votre domaine managé est évaluée toutes les heures. Après avoir apporté des modifications à votre domaine managé, attendez le prochain cycle d’évaluation pour afficher l’intégrité mise à jour de votre domaine managé. L’horodateur de « Dernière évaluation » dans l’angle supérieur droit indique à quel moment l’intégrité de votre domaine managé a été évaluée pour la dernière fois.
>

### <a name="status-of-your-managed-domain"></a>État de votre domaine managé
L’état affiché dans le coin supérieur droit de votre page d’intégrité indique l’intégrité globale de votre domaine managé. L’état prend en compte toutes les alertes existantes de votre domaine. Vous pouvez également afficher l’état de votre domaine sur la page Vue d’ensemble d’Azure AD Domain Services.

| Statut | Icône | Explication |
| --- | :----: | --- |
| Exécution | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | Votre domaine managé fonctionne normalement et n’a pas d’alertes critiques ou d’avertissement. Ce domaine peut avoir des alertes d’information. |
| Doit être surveillé (avertissement) | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | Il n’existe aucune alerte critique sur votre domaine managé, mais il existe une ou plusieurs alertes d’avertissement qui doivent être traitées. |
| Doit être surveillé (critique) | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | Il existe une ou plusieurs alertes critiques sur votre domaine managé. Vous avez peut-être également des alertes d’avertissement et/ou d’information. |
| Déploiement en cours | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | Votre domaine est en cours de déploiement. |

## <a name="monitors"></a>Analyses
Les analyses sont les aspects de votre domaine managé qu’Azure AD Domain Services surveille régulièrement. La meilleure façon de conserver vos analyses dans un état sain consiste à résoudre toutes les alertes actives pour votre domaine managé.

Azure AD Domain Services surveille actuellement les éléments suivants :
 - Sauvegarde
 - Synchronisation avec Azure AD

### <a name="the-backup-monitor"></a>Analyse de sauvegarde
Cette analyse permet de savoir si des sauvegardes régulières de votre domaine managé sont effectuées. Le tableau suivant explique à quoi s’attendre dans la colonne Détails de l’analyse de sauvegarde :

| Valeur de détail | Explication |
| --- | --- |
|« Jamais sauvegardé » | Cet état est normal pour un domaine managé qui vient d’être créé. En règle générale, la première sauvegarde est créée 24 heures après la configuration de votre domaine managé. Si votre domaine managé n’a pas été créé récemment ou si vous voyez cet état pendant une durée anormale, [contactez le support technique](active-directory-ds-contact-us.md). |
| La dernière sauvegarde a été effectuée entre 1 et 14 jours auparavant. | En règle générale, cette valeur est attendue pour le moniteur de sauvegarde. |
| La dernière sauvegarde a été effectuée il y a plus de 14 jours. | Toute durée supérieure à deux semaines est une durée anormalement longue depuis votre dernière sauvegarde. Les alertes critiques actives peuvent empêcher votre domaine managé d’être sauvegardé régulièrement. Tout d’abord, résolvez toutes les alertes actives pour votre domaine managé, et si le problème persiste, [contactez le support technique](active-directory-ds-contact-us.md). |


### <a name="the-synchronization-with-azure-ad-monitor"></a>Analyse de synchronisation avec Azure AD
Microsoft surveille la fréquence à laquelle votre domaine managé est synchronisé avec Azure Active Directory. Le nombre d’objets (utilisateurs et groupes) et le nombre de modifications effectuées dans votre annuaire Azure AD depuis la dernière synchronisation peuvent tous deux affecter la durée de la période de synchronisation. Si votre domaine managé a été synchronisé la dernière fois il y a plus de trois jours [contactez le support technique](active-directory-ds-contact-us.md).

## <a name="alerts"></a>Alertes
Les alertes sont générées pour les problèmes sur votre domaine managé qui doivent être traités pour qu’Azure AD Domain Services puisse fonctionner. Chaque alerte explique le problème et fournit une URL de résolution qui décrit les étapes spécifiques pour résoudre le problème. Pour afficher toutes les alertes et leur résolution, visitez l’article [Dépanner les alertes](active-directory-ds-troubleshoot-alerts.md).

### <a name="alert-severity"></a>Gravité des alertes
Les alertes sont classées en trois niveaux de gravité : critique, avertissement et information.

 * Les **alertes critiques** sont des problèmes ayant un impact sérieux sur votre domaine managé. Ces alertes doivent être traitées immédiatement, car Microsoft ne peut pas surveiller, gérer, mettre à jour et synchroniser votre domaine managé. 
 * Les **alertes d’avertissement** vous avertissent des problèmes qui peuvent avoir un impact sur votre domaine managé à l’avenir. Ces alertes fournissent des recommandations pour sécuriser votre domaine managé.
 * Les **alertes d’information** sont des notifications qui n’ont pas effets négatifs sur votre domaine. Les alertes d’information sont conçues pour vous tenir informé de ce qui se passe dans votre domaine et Azure AD Domain Services.

## <a name="next-steps"></a>Étapes suivantes
- [Résoudre les alertes sur votre domaine managé](active-directory-ds-troubleshoot-alerts.md)
- [En savoir plus sur Azure AD Domain Services](active-directory-ds-overview.md)
- [Contacter l’équipe produit](active-directory-ds-contact-us.md)
