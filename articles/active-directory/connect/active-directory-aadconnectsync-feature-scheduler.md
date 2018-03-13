---
title: Planificateur Azure AD Connect Sync | Microsoft Docs
description: "Cette rubrique décrit la fonctionnalité de planificateur intégré dans Azure AD Connect Sync."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 6b1a598f-89c0-4244-9b20-f4aaad5233cf
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: df7b16157ccb47a5463570c3ed58b9c44f0b67d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-scheduler"></a>Planificateur Azure AD Connect Sync
Cette rubrique décrit le planificateur intégré dans Azure AD Connect Sync (également appelé moteur de synchronisation).

Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée en février 2016).

## <a name="overview"></a>Vue d’ensemble
La synchronisation Azure AD Connect synchronise les modifications dans votre répertoire local à l’aide d’un planificateur. Il existe deux processus de planificateur, l’un pour la synchronisation de mot de passe et l’autre pour la synchronisation d’attribut/d’objet, ainsi que des tâches de maintenance. Cette rubrique couvre ce dernier.

Dans les versions antérieures, le planificateur utilisé pour les objets et attributs était externe au moteur de synchronisation. Il utilisait le Planificateur de tâches Windows ou un service Windows distinct pour déclencher le processus de synchronisation. Le planificateur de la version 1.1 est intégré au moteur de synchronisation et permet de personnaliser certains aspects. La nouvelle valeur de la fréquence de synchronisation par défaut est de 30 minutes.

Le planificateur supervise deux tâches :

* **Cycle de synchronisation**. Processus d’importation, de synchronisation et d’exportation des modifications.
* **Tâches de maintenance**. Renouvelle les clés et les certificats pour la réinitialisation de mot de passe et le service DRS (Device Registration Service). Vide les anciennes entrées dans le journal des opérations.

Le planificateur est toujours en cours d’exécution, mais il peut être configuré pour exécuter uniquement une tâche, ou bien aucune tâche. Par exemple, si vous avez besoin d’avoir votre propre processus de cycle de synchronisation, vous pouvez désactiver cette tâche dans le planificateur, mais toujours exécuter la tâche de maintenance.

## <a name="scheduler-configuration"></a>Configuration du planificateur
Pour afficher vos paramètres de configuration en cours, accédez à PowerShell et exécutez `Get-ADSyncScheduler`. Vous obtenez un écran semblable à celui-ci :

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings2016.png)

Si le message **La commande de synchronisation ou l’applet de commande n’est pas disponible** apparaît lorsque vous exécutez cette applet de commande, le module PowerShell n'est pas chargé. Ce problème peut se produire si vous exécutez Azure AD Connect sur un contrôleur de domaine ou sur un serveur avec des niveaux de restriction PowerShell plus élevés que les paramètres par défaut. Si vous recevez cette erreur, exécutez `Import-Module ADSync` pour libérer l’applet de commande.

* **AllowedSyncCycleInterval**. L’intervalle de temps le plus court entre les cycles de synchronisation autorisé par Azure AD. Vous ne pouvez pas synchroniser plus fréquemment que ne le spécifie ce paramètre tout en maintenant la prise en charge.
* **CurrentlyEffectiveSyncCycleInterval**. Planificateur actuellement en vigueur. La valeur est identique à celle de CustomizedSyncInterval (si ce paramètre est défini) si la fréquence n’est pas supérieure à AllowedSyncInterval. Si vous utilisez une version antérieure à 1.1.281 et que vous modifiez CustomizedSyncCycleInterval, cette modification prendra effet après le prochain cycle de synchronisation. À partir de la version 1.1.281, la modification prend effet immédiatement.
* **CustomizedSyncCycleInterval**. Si vous souhaitez que le planificateur s’exécute à une fréquence autre que celle par défaut de 30 minutes, vous devez configurer ce paramètre. Dans l’image ci-dessus, le planificateur a été défini pour s’exécuter toutes les heures. Si vous choisissez une valeur inférieure à AllowedSyncInterval, ce dernier sera utilisé.
* **NextSyncCyclePolicyType**. Delta ou Initial. Indique si la prochaine exécution doit uniquement traiter les modifications delta du processus ou si elle doit effectuer une importation et une synchronisation intégrales, ce qui aura pour effet de retraiter les nouvelles règles ou les règles modifiées.
* **NextSyncCycleStartTimeInUTC**. Heure à laquelle le planificateur démarrera le prochain cycle de synchronisation.
* **PurgeRunHistoryInterval**. Durée pendant laquelle les journaux des opérations doivent être conservés. Ces journaux peuvent être consultés dans Synchronization Service Manager. La valeur de conservation des journaux par défaut est de 7 jours.
* **SyncCycleEnabled**. Indique si le planificateur exécute les processus d’importation, de synchronisation et d’exportation dans le cadre de son fonctionnement.
* **MaintenanceEnabled**. Indique si le processus de maintenance est activé. Met à jour les certificats/clés et vide le journal des opérations.
* **StagingModeEnabled**. Indique si le [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode) est activé. Si ce paramètre est activé, les exportations ne sont plus exécutées. Cependant, l’importation et la synchronisation sont toujours exécutées.
* **SchedulerSuspended**. Défini par Connect pendant une mise à niveau pour bloquer temporairement le l’exécution du planificateur.

Vous pouvez modifier certains de ces paramètres avec `Set-ADSyncScheduler`. Les paramètres suivants peuvent être modifiés :

* CustomizedSyncCycleInterval
* NextSyncCyclePolicyType
* PurgeRunHistoryInterval
* SyncCycleEnabled
* MaintenanceEnabled

Dans les versions antérieures d’Azure AD Connect, **isStagingModeEnabled** était exposé dans Set-ADSyncScheduler. Elle **n’est pas prise en charge** pour la définition de cette propriété. La propriété **SchedulerSuspended** doit uniquement être modifiée par Connect. Elle **n’est pas prise en charge** pour la définition avec PowerShell directement.

La configuration du planificateur est stockée dans Azure AD. Si vous avez un serveur intermédiaire, toute modification sur le serveur principal a également un effet sur le serveur intermédiaire (à l’exception de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe : `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
d - jours, HH - heures, mm - minutes, ss - secondes

Exemple : `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
Modifie le planificateur pour qu’il s’exécute toutes les 3 heures.

Exemple : `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
Modifie le planificateur pour qu’il s’exécute tous les jours.

### <a name="disable-the-scheduler"></a>Désactivation du planificateur  
Si vous devez apporter des modifications de configuration, vous devez désactiver le planificateur. Par exemple, lorsque vous [configurez le filtrage](active-directory-aadconnectsync-configure-filtering.md) ou [apportez des modifications aux règles de synchronisation](active-directory-aadconnectsync-change-the-configuration.md).

Exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false` pour désactiver le planificateur.

![Désactivation du planificateur](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

Une fois vos modifications apportées, n’oubliez pas d’activer de nouveau le planificateur en exécutant `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="start-the-scheduler"></a>Démarrer le planificateur
Par défaut, le planificateur s’exécute toutes les 30 minutes. Dans certains cas, vous souhaiterez peut-être exécuter un cycle de synchronisation entre les cycles planifiés ou vous devrez exécuter un autre type de synchronisation.

**Cycle de synchronisation delta**  
Un cycle de synchronisation delta comprend les étapes suivantes :

* Importation delta sur tous les connecteurs
* Synchronisation delta sur tous les connecteurs
* Exportation sur tous les connecteurs

Cela peut indiquer une modification urgente à synchroniser immédiatement, nécessitant l’exécution manuelle d’un cycle. Si vous avez besoin d’exécuter un cycle manuellement, exécutez `Start-ADSyncSyncCycle -PolicyType Delta`à partir de PowerShell.

**Cycle de synchronisation complet**  
Si vous avez effectué l’une des modifications de configuration suivantes, vous devez exécuter un cycle de synchronisation complet (également appelé Initial) :

* Ajout de plusieurs objets ou attributs à importer à partir d’un répertoire source
* Modifications apportées aux règles de synchronisation
* Le [filtrage](active-directory-aadconnectsync-configure-filtering.md) étant modifié, un nombre différent d’objets doit être inclus

Si vous avez effectué l’une de ces modifications, vous devez exécuter un cycle de synchronisation complète, afin que le moteur de synchronisation ait la possibilité de reconsolider les espaces du connecteur. Un cycle de synchronisation complète comprend les étapes suivantes :

* Importation complète sur tous les connecteurs
* Synchronisation complète sur tous les connecteurs
* Exportation sur tous les connecteurs

Pour initier un cycle de synchronisation complète, exécutez `Start-ADSyncSyncCycle -PolicyType Initial` à partir d’une invite PowerShell. Cette commande démarre un cycle de synchronisation complet.

## <a name="stop-the-scheduler"></a>Arrêter le planificateur
Si le planificateur exécute un cycle de synchronisation, vous devrez l’arrêter. Par exemple, si vous démarrez l’Assistant d’installation et que vous obtenez l’erreur suivante :

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Lorsqu’un cycle de synchronisation est en cours d’exécution, vous ne pouvez pas modifier la configuration. Vous pouvez attendre jusqu’à ce que le planificateur ait terminé le processus, mais vous pouvez également l’arrêter afin d’implémenter immédiatement vos modifications. L’arrêt du cycle en cours n’est pas dangereux, et les modifications en attente seront traitées à la prochaine exécution.

1. Commencez par indiquer au planificateur d’arrêter son cycle en cours à l’aide de l’applet de commande PowerShell `Stop-ADSyncSyncCycle`.
2. Si vous utilisez une version antérieure à 1.1.281, l’arrêt du planificateur n’empêche pas le connecteur actuel de terminer sa tâche en cours. Pour forcer le connecteur à s’arrêter, prenez les mesures suivantes : ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
   * Démarrez le **Service de synchronisation** depuis le menu Démarrer. Accédez à **Connecteurs**, mettez en surbrillance le connecteur avec l’état **En cours d’exécution** et sélectionnez **Arrêter** dans les actions.

Le planificateur est toujours actif et redémarrera à la prochaine occasion.

## <a name="custom-scheduler"></a>Planificateur personnalisé
Les applets de commande décrites dans cette section sont uniquement disponibles dans le build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) et versions ultérieures.

Si le planificateur intégré ne satisfait pas à vos exigences, vous pouvez planifier les connecteurs à l'aide de PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Vous pouvez démarrer un profil pour un connecteur de cette façon :

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Les noms à utiliser pour [Noms de connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md) et [Exécuter les noms de profil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) se trouvent dans [l’interface utilisateur Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Appeler le profil d’exécution](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

L’applet de commande `Invoke-ADSyncRunProfile` est synchrone, c’est-à-dire qu’elle ne retournera pas de contrôle avant que le connecteur ait terminé l’opération, avec succès ou avec une erreur.

Lorsque vous planifiez vos connecteurs, il est recommandé de le faire dans l'ordre suivant :

1. (Intégral/Delta) Importation à partir de répertoires locaux, tels qu'Active Directory
2. (Intégral/Delta) Importation à partir d'Azure AD
3. (Intégral/Delta) Synchronisation à partir de répertoires locaux, tels qu'Active Directory
4. (Intégral/Delta) Synchronisation à partir d'Azure AD
5. Exportation vers Azure AD
6. Exportation vers des répertoires locaux, tels qu'Active Directory

Il s’agit de l’ordre dans lequel le planificateur intégré exécute les connecteurs.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Vous pouvez également surveiller le moteur de synchronisation pour voir s'il est occupé ou inactif. Cette applet de commande renvoie un résultat vide si le moteur de synchronisation est inactif et qu’il n’exécute pas un connecteur. Si un connecteur est en cours d’exécution, il retourne le nom du connecteur.

```
Get-ADSyncConnectorRunStatus
```

![État d’exécution du connecteur](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
Dans l'illustration ci-dessus, la première ligne est dans un état où le moteur de synchronisation est inactif. La deuxième ligne lorsque le Connecteur Azure AD est en cours d'exécution.

## <a name="scheduler-and-installation-wizard"></a>Planificateur et Assistant d’installation
Si vous démarrez l’Assistant d’installation, le planificateur est temporairement interrompu. Ce comportement repose sur l’hypothèse que vous apportez des modifications de configuration et le fait que ces paramètres ne peuvent pas être appliqués si le moteur de synchronisation s’exécute activement. Pour cette raison, ne laissez pas l’Assistant d’installation ouvert, car il empêche le moteur de synchronisation d’effectuer toutes les actions de synchronisation.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
