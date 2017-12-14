---
title: "Azure AD Connect : Étapes suivantes et gestion Azure AD Connect | Microsoft Docs"
description: "Apprenez à étendre la configuration par défaut et les tâches opérationnelles pour Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e898ee203276b072605fe0f21af633ecfdaae1fe
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Étapes suivantes et gestion d’Azure AD Connect
Utilisez les procédures opérationnelles dans cet article pour personnaliser Azure Active Directory (Azure AD) Connect pour l’adapter aux besoins et aux spécifications de votre organisation.  

## <a name="add-additional-sync-admins"></a>Ajout d’administrateurs de synchronisation supplémentaires
Par défaut, seul l’utilisateur qui a effectué l’installation et les administrateurs locaux est en mesure de gérer le moteur de synchronisation installé. Pour que d’autres personnes puissent accéder au moteur de synchronisation et en assurer la gestion, localisez le groupe nommé ADSyncAdmins sur le serveur local et ajoutez ces personnes à ce groupe.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Attribution des licences aux utilisateurs Azure AD Premium et Enterprise Mobility Suite
Maintenant que vos utilisateurs ont été synchronisés dans le cloud, vous devez leur attribuer une licence afin qu’ils puissent commencer à utiliser les applications cloud telles qu’Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Pour l’attribution d’une licence Azure AD Premium ou Enterprise Mobility Suite

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory**à gauche.
3. Sur la page **Active Directory**, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez configurer.
4. En haut de la page du répertoire, sélectionnez **Licences**.
5. Sur la page **Licences**, sélectionnez **Active Directory Premium** ou **Enterprise Mobility Suite**, puis cliquez sur **Attribuer**.
6. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l'icône de coche pour enregistrer les modifications.

## <a name="verify-the-scheduled-synchronization-task"></a>Vérification de la tâche de synchronisation planifiée
Utilisez le portail Azure pour vérifier l’état d’une synchronisation.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Pour la vérification de la tâche de synchronisation planifiée
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory**à gauche.
3. Sur la page **Active Directory**, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez configurer.
4. En haut de la page du répertoire, sélectionnez **Intégration du répertoire**.
5. Sous **l’intégration à un Active Directory local**, notez l’heure de la dernière synchronisation.

<center>![Heure de synchronisation d’annuaires](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="start-a-scheduled-synchronization-task"></a>Lancement d’une tâche de synchronisation planifiée
Si vous devez exécuter une tâche de synchronisation, vous pouvez le faire en l’exécutant une nouvelle fois depuis l’Assistant Azure AD Connect.  Vous devez fournir vos informations d’identification Azure AD.  Dans l’Assistant, sélectionnez la tâche **Personnalisation des options de synchronisation** et cliquez sur **Suivant** pour terminer l’Assistant. À la fin, vérifiez que la case **Démarrez le processus de synchronisation dès que la configuration initiale est terminée** est cochée.

<center>![Démarrage de la synchronisation](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Pour plus d’informations sur le planificateur de synchronisation Azure AD Connect, consultez [Planificateur Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md).

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tâches supplémentaires disponibles dans Azure AD Connect
Après l’installation initiale d’Azure AD Connect, vous pouvez toujours redémarrer l’Assistant depuis la page de démarrage ou le raccourci du bureau Azure AD Connect.  Vous remarquerez qu’en passant à nouveau par l’Assistant de nouvelles options apparaissent sous forme de tâches supplémentaires.  

Le tableau suivant fournit un résumé de ces tâches et une brève description de chacune d’elles.

![Liste des tâches supplémentaires](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Tâche supplémentaire | Description |
| --- | --- |
| **Afficher le scénario sélectionné** |Afficher votre solution Azure AD Connect actuelle.  Cela inclut les paramètres généraux, les répertoires synchronisés et les paramètres de synchronisation. |
| **Personnalisation des options de synchronisation** |Modifier la configuration actuelle, notamment par l’ajout de forêts Active Directory supplémentaires à la configuration ou l’activation d’options de synchronisation telles que l’utilisateur, le groupe, l’appareil ou le mot de passe en écriture différée. |
| **Activation du mode intermédiaire** |Réalisez un déploiement intermédiaire des informations qui ne sont pas immédiatement synchronisées et ne sont pas exportées vers Azure AD ou l’Active Directory local.  Cette fonctionnalité vous permet d’afficher un aperçu des synchronisations avant qu’elles ne s’effectuent. |

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
