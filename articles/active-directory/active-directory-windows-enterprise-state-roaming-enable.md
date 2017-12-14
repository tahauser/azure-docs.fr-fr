---
title: "Activer Enterprise State Roaming dans Azure Active Directory | Microsoft Docs"
description: "Foire aux questions sur les paramètres Enterprise State Roaming sur les appareils Windows. Enterprise State Roaming fournit aux utilisateurs une expérience unifiée sur leurs appareils Windows et réduit le temps nécessaire à la configuration d’un nouveau périphérique."
services: active-directory
keywords: enterprise state roaming, cloud windows, comment activer enterprise state roaming
documentationcenter: 
author: tanning
manager: mtillman
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.openlocfilehash: 3a8714ddbda10d8e1b4a8de35711101f4c8a0106
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Activer Enterprise State Roaming dans Azure Active Directory
Enterprise State Roaming est disponible pour toute organisation disposant d’une licence Azure AD Premium ou Enterprise Mobility + Security (EMS). Pour plus d’informations sur l’obtention d’un abonnement Azure AD, consultez la page [du produit Azure AD](https://azure.microsoft.com/services/active-directory) .

Lorsque vous activez Enterprise State Roaming, votre organisation reçoit automatiquement une licence gratuite à utilisation limitée pour Azure Rights Management. Cet abonnement gratuit est limité au chiffrage et au déchiffrage des paramètres d’entreprise et des données d’application synchronisés par Enterprise State Roaming. Vous devez avoir [un abonnement payant](https://azure.microsoft.com/pricing/details/active-directory/) pour utiliser toutes les fonctionnalités de Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Activer Enterprise State Roaming

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com/).

2. Sélectionnez **Azure Active Directory** &gt; **Appareils** &gt; **Paramètres de l’appareil**.

3. Sélectionnez **Les utilisateurs peuvent synchroniser les paramètres et les données d’application entre les appareils**. Pour plus d’informations, consultez [Comment configurer les paramètres de l’appareil](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Image du paramètre d’appareil «Les utilisateurs peuvent synchroniser les paramètres et les données d’application sur différents appareils»](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Pour qu’un appareil Windows 10 utilise le service Enterprise State Roaming, l’appareil doit s’authentifier à l’aide d’une identité Azure AD. Pour les appareils joints à Azure AD, l’identité de connexion principale de l’utilisateur est l’identité Azure AD. Ainsi, aucune configuration supplémentaire n’est requise. Pour les appareils qui utilisent un annuaire Active Directory local, l’administrateur informatique doit [connecter les appareils joints à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Stockage des données
Les données Enterprise State Roaming sont hébergées dans une ou plusieurs [régions Azure](https://azure.microsoft.com/regions/) qui correspondent le plus à la valeur de pays/région définie dans l’instance Azure Active Directory. Les données Enterprise State Roaming sont partitionnées selon les trois principales régions : Amérique du Nord, EMEA et Asie-Pacifique. Les données Enterprise State Roaming pour le client sont situées localement dans la région géographique, et ne sont pas répliquées entre régions.  Par exemple :
Valeur Pays/région | Emplacement de l’hébergement des données
---------------------|-------------------------
Un pays EMEA tel que « France » ou « Zambie » | un ou plusieurs régions Azure au sein de l’Europe 
Un pays d’Amérique du Nord, tel que « États-Unis » ou « Canada » | un ou plusieurs régions Azure au sein des États-Unis
Un pays de l’Asie-Pacifique tel que « Australie » ou « Nouvelle-Zélande » | un ou plusieurs régions Azure au sein de l’Asie
Régions Amérique du Sud et Antarctique | un ou plusieurs régions Azure au sein des États-Unis

La valeur de pays/région est définie dans le cadre du processus de création d’un répertoire Azure AD et ne peut pas être modifiée par la suite. Pour plus d’informations sur votre emplacement de stockage des données, soumettez un ticket au [support technique Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Afficher l’état de synchronisation d’appareil par utilisateur
Procédez comme suit pour afficher un rapport d’état de synchronisation d’appareil par utilisateur.

1. Connectez-vous au [Centre d’administration Azure AD](https://aad.portal.azure.com/).

2. Sélectionnez **Azure Active Directory** &gt; **Utilisateurs et groupes** &gt; **Tous les utilisateurs**.

3. Sélectionnez l’utilisateur, puis **Appareils**.

4. Sous **Afficher**, sélectionnez **Paramètres de synchronisation des appareils et données d’application** pour afficher l’état de synchronisation.
  
  ![image du paramètre de synchronisation d’appareils et des données](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Si des appareils se synchronisent pour cet utilisateur, vous les verrez comme illustré ici.
  
  ![image de la colonne de synchronisation d’appareils et des données](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Conservation des données
Les données synchronisées à Azure à l’aide de Enterprise State Roaming sont conservées jusqu’à leur suppression manuelle ou bien jusqu’à ce qu’elles soient considérées comme obsolètes. 

### <a name="explicit-deletion"></a>Suppression explicite
La suppression explicite est lorsqu’un administrateur Azure supprime un utilisateur ou un répertoire, ou qu’il demande explicitement que les données soient supprimées.

* **Suppression d’un utilisateur**: lorsqu’un utilisateur est supprimé dans Azure AD, les données d’itinérance de l’utilisateur sont supprimées après 90 à 180 jours. 
* **Suppression d’un répertoire**: la suppression d’un répertoire entier dans Azure AD est une opération à effet immédiat. Toutes les données de paramètres associées à ce répertoire sont supprimées après 90 à 180 jours. 
* **Suppression à la demande**: si l’administrateur Azure AD souhaite supprimer manuellement des données d’utilisateur ou de paramètre spécifiques, il peut soumettre un ticket au [support technique Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Suppression des données obsolètes
Les données qui n’ont pas été utilisées depuis un an (« la période de rétention ») sont considérées comme obsolètes et peuvent être supprimées de Azure. La période de rétention est susceptible de changer, mais ne sera pas inférieure à 90 jours. Il peut s’agir d’un ensemble spécifique de paramètres Windows/d’application ou de tous les paramètres d’un utilisateur. Par exemple :

* Si une collection de paramètres spécifique n’est utilisée par aucun appareil (par exemple, si une application est supprimée de l’appareil ou qu’un groupe de paramètres tels que « Thème » est désactivé pour tous les appareils d’un utilisateur), elle devient obsolète à la fin de la période de rétention et peut être supprimée. 
* Si un utilisateur a désactivé la synchronisation des paramètres sur tous ses appareils, aucune donnée de paramètres n’est utilisée. De plus, toutes les données de paramètres pour cet utilisateur deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 
* Si l’administrateur du répertoire Azure AD désactive Enterprise State Roaming pour l’ensemble du répertoire, la synchronisation des paramètres cessera pour tous les utilisateurs du répertoire. De plus, toutes les données de paramètres de tous les utilisateurs deviennent obsolètes et peuvent être supprimées à la fin de la période de rétention. 

### <a name="deleted-data-recovery"></a>Récupération de données supprimées
La stratégie de rétention de données n’est pas configurable. Les données définitivement supprimées ne sont pas récupérables. Toutefois, les données de paramètres sont supprimées uniquement sur Azure, pas sur l’appareil de l’utilisateur final. Si un appareil se reconnecte au service Enterprise State Roaming, les paramètres sont alors à nouveau synchronisés et stockés dans Azure.

## <a name="related-topics"></a>Rubriques connexes
* [Présentation d’Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [FAQ sur l’itinérance des paramètres et des données](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Paramètres de stratégie de groupe et de MDM pour la synchronisation des paramètres](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Référence des paramètres d’itinérance Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Dépannage](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
