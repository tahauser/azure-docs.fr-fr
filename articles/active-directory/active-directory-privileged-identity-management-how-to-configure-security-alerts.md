---
title: Configuration des alertes de sécurité | Microsoft Docs
description: Découvrez comment configurer des alertes de sécurité pour l’extension Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8037942cb3700f8e46d3be24b5fed04004333335
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Comment configurer les alertes de sécurité dans Azure AD Privileged Identity Management
## <a name="security-alerts"></a>Alertes de sécurité
Azure Privileged Identity Management (PIM) génère des alertes en cas d’activité suspecte ou non fiable dans votre environnement. Lorsqu’une alerte est déclenchée, elle s’affiche sur le tableau de bord PIM. Sélectionnez l’alerte pour obtenir un rapport qui répertorie les utilisateurs ou les rôles à l’origine de son déclenchement.

![Alertes de sécurité du tableau de bord PIM - capture d’écran][1]

| Alerte | Niveau de gravité | Déclencheur | Recommandation |
| --- | --- | --- | --- |
| **Les rôles sont affectés en dehors de PIM** |Élevé |Un administrateur a été définitivement affecté à un rôle privilégié en dehors de l’interface PIM. |Passez en revue les utilisateurs de la liste et supprimez le rôle privilégié qui leur a été attribué en dehors de PIM. |
| **Les rôles sont activés trop fréquemment.** |Moyenne |Un trop grand nombre de réactivations du même rôle ont eu lieu par rapport à la durée autorisée dans les paramètres. |Contactez l’utilisateur pour savoir pourquoi il a activé le rôle autant de fois. Le délai est peut-être trop court pour lui permettre de finaliser les tâches en cours, ou il utilise peut-être des scripts pour activer automatiquement un rôle. Vérifiez que la durée d’activation de leur rôle est suffisante pour qu’ils puissent effectuer leurs tâches. |
| **Les rôles ne nécessitent pas l’authentification multifacteur pour l’activation** |Moyenne |Les paramètres comportent des rôles sans authentification MFA. |Nous exigeons l’authentification multifacteur pour les rôles les plus privilégiés, mais nous vous conseillons vivement d’activer l’authentification multifacteur pour l’activation de tous les rôles. |
| **Les utilisateurs n’utilisent par leurs rôles privilégiés** |Faible |Certains administrateurs éligibles n’ont pas activé leurs rôles récemment. |Démarrez une vérification d’accès pour identifier les utilisateurs qui n’ont plus besoin d’un accès. |
| **Trop d'administrateurs généraux** |Faible |Le nombre d’administrateurs généraux est supérieur au nombre recommandé. |Si vous avez un grand nombre d’administrateurs généraux, il est probable que les utilisateurs reçoivent plus d’autorisations que nécessaire. Affectez aux utilisateurs des rôles moins privilégiés, ou rendez certains d’entre eux éligibles au rôle au lieu de leur affecter le rôle de façon permanente. |

### <a name="severity"></a>Niveau de gravité
* **Élevé** : nécessite une action immédiate, car il s’agit d’une violation de stratégie. 
* **Moyen** : ne nécessite pas d’action immédiate, mais signale une violation potentielle de la stratégie.
* **Faible** : ne nécessite pas d’action immédiate, mais suggère une modification de la stratégie.

## <a name="configure-security-alert-settings"></a>Configurez les paramètres d'alerte de sécurité
Vous pouvez personnaliser certaines alertes de sécurité dans PIM selon votre environnement et vos objectifs de sécurité. Procédez comme suit pour accéder au panneau des paramètres :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et sélectionnez la mosaïque **Azure AD Privileged Identity Management** dans le tableau de bord.
2. Sélectionnez **Rôles privilégiés gérés** > **Paramètre** > **Paramètres des alertes**.
   
    ![Accès aux paramètres des alertes de sécurité][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerte « Les rôles sont activés trop fréquemment »
Cette alerte se déclenche si un utilisateur active le même rôle privilégié plusieurs fois pendant une période spécifiée. Vous pouvez configurer la période et le nombre d’activations.

* **Période de renouvellement des activations**: spécifiez en jours, heures, minutes et secondes la période que vous souhaitez utiliser pour effectuer le suivi des renouvellements suspects.
* **Nombre de renouvellements de l’activation**: spécifiez le nombre d’activations (de 2 à 100) qui vous semblent devoir déclencher une alerte, dans l’intervalle de temps que vous avez choisi. Vous pouvez modifier ce paramètre en déplaçant le curseur ou en tapant un nombre dans la zone de texte.

### <a name="there-are-too-many-global-administrators-alert"></a>Alerte « Trop d'administrateurs généraux »
PIM déclenche cette alerte si deux critères correspondent, et vous pouvez configurer ces deux valeurs. Vous devez tout d’abord atteindre un certain seuil d’administrateurs généraux. Puis vous devez réserver un certain pourcentage du total de vos affectations à des rôles d’administrateur général. L’alerte ne s’affiche pas si vous ne remplissez qu’un seul de ces critères.  

* **Nombre minimal d’administrateurs généraux**: spécifiez, de 2 à 100, le nombre d’administrateurs généraux qui, selon vous, présenterait un risque.
* **Pourcentage d'administrateurs généraux**: indiquez, de 0 % à 100 %, le pourcentage d'administrateurs généraux par rapport au nombre total d’administrateurs qui, selon vous, présenterait un risque pour votre environnement.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>Alerte « Les administrateurs n’utilisent pas leurs rôles privilégiés »
Cette alerte se déclenche si un utilisateur reste un certain temps sans activer un rôle.

* **Nombre de jours**: spécifiez, de 0 à 100, le nombre de jours pendant lesquels un utilisateur peut rester sans activer un rôle.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
