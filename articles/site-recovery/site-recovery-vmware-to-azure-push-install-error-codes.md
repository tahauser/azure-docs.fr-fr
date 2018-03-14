---
title: "Résolution des problèmes Azure Site Recovery depuis VMware vers Azure | Microsoft Docs"
description: "Corrigez les erreurs que vous rencontrez lorsque vous répliquez les machines virtuelles Azure."
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: anoopkv
ms.openlocfilehash: a03766f8a22399f7d72a01f8d744bfd1cef90ac3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Résoudre les problèmes d’installation Push du service Mobilité

Cet article décrit les problèmes courants que vous pouvez rencontrer lorsque vous tentez d’installer le service Mobilité d’Azure Site Recovery sur le serveur source en vue d’activer la protection.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Erreur 78007 - Impossible d’achever l’opération
Cette erreur peut être signalée par le service pour plusieurs raisons. Choisissez l’erreur du fournisseur correspondant pour procéder au dépannage.

* [Erreur 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Erreur 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Erreur 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Erreur 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Erreur 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Erreur 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Erreur 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Erreur 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Erreur 95105 - Impossible d’activer la protection (EP0856)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95105 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0856**. <br> Soit le **Partage de fichiers et d’imprimantes** n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source.| Le **Partage de fichiers et d’imprimantes** n’est pas activé. | Autorisez le **Partage de fichiers et d’imprimantes** sur la machine source dans le pare-feu Windows. Sur la machine source, sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows** , sélectionnez **Partage de fichiers et d’imprimantes pour tous les profils**. </br> En outre, vérifiez que les prérequis suivants sont présents pour garantir le bon déroulement de l’installation Push.<br> En savoir plus sur le [dépannage des problèmes WMI](#troubleshoot-wmi-issues)


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Erreur 95107 - Impossible d’activer la protection (EP0858)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95107 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0858**. <br> Soit les informations d’identification fournies pour installer le service Mobilité sont incorrectes, soit le compte d’utilisateur ne dispose pas de privilèges suffisants. | Les informations d’identification de l’utilisateur fournies pour installer le service Mobilité sur la machine source sont incorrectes. | Vérifiez que les informations d’identification fournies pour la machine source sur le serveur de configuration sont correctes. <br> Pour ajouter ou modifier les informations d’identification de l’utilisateur : accédez au serveur de configuration, puis sélectionnez **Cspsconfigtool** > **Gérer le compte**. </br> En outre, vérifiez que les [prérequis](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) suivants sont présents pour garantir le bon déroulement de l’installation Push.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Erreur 95117 - Impossible d’activer la protection (EP0865)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95117 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0865**. <br> Soit la machine source n’est pas en cours d’exécution, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source. | Problèmes de connectivité réseau entre le serveur de processus et le serveur source. | Vérifiez la connectivité réseau entre le serveur de processus et le serveur source. </br> En outre, vérifiez que les [prérequis](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) suivants sont présents pour garantir le bon déroulement de l’installation Push.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Erreur 95103 - Impossible d’activer la protection (EP0854)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95103 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0854**. <br> Soit WMI (Windows Management Instrumentation) n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source.| WMI est bloqué dans le pare-feu Windows. | Autorisez WMI dans le pare-feu Windows. Sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows**, sélectionnez **WMI pour tous les profils**. </br> En outre, vérifiez que les [prérequis](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery) suivants sont présents pour garantir le bon déroulement de l’installation Push.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Erreur 95213 - Impossible d’activer la protection (EP0874)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95213 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source %SourceIP avec le code d’erreur **EP0874**. <br> | La version du système d’exploitation sur la machine source n’est pas prise en charge. <br>| Assurez-vous que la version du système d’exploitation de la machine source est prise en charge. Examinez la [matrice de prise en charge](https://aka.ms/asr-os-support). </br> En outre, vérifiez que les [prérequis](https://aka.ms/pushinstallerror) suivants sont présents pour garantir le bon déroulement de l’installation Push.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Erreur 95108 - Impossible d’activer la protection (EP0859)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95108 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0859**. <br>| Soit les informations d’identification fournies pour installer le service Mobilité sont incorrectes, soit le compte d’utilisateur ne dispose pas de privilèges suffisants. <br>| Assurez-vous que les informations d’identification fournies sont les informations d’identification du compte **racine**. Pour ajouter ou modifier des informations d’identification de l’utilisateur, accédez au serveur de configuration, puis sélectionnez l’icône de raccourci **Cspsconfigtool** sur le Bureau. Sélectionnez **Gérer le compte** pour ajouter ou modifier les informations d’identification.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Erreur 95265 - Impossible d’activer la protection (EP0902)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95265 </br>**Message :** L’installation Push du service Mobilité a été effectuée sur la machine source, mais la machine source (%SourceIP;) doit être redémarrée pour que certaines modifications système prennent effet. <br>| Une version antérieure du service mobilité a déjà été installée sur le serveur.| La réplication de la machine virtuelle se poursuit en toute transparence.<br> Redémarrez le serveur lors de votre prochaine fenêtre de maintenance afin de tirer profit des nouvelles améliorations du service Mobilité.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Erreur 95224 - Impossible d’activer la protection (EP0883)

**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95224 </br>**Message :** Échec de l’installation Push du service Mobilité sur la machine source %SourceIP;. Code d’erreur : **EP0883**. Un redémarrage du système à partir d’une précédente installation ou mise à jour est en attente.| Le système n’a pas été redémarré après la désinstallation d’une version antérieure ou incompatible du service Mobilité.| Vérifiez qu’aucune version du service Mobilité ne se trouve sur le serveur. <br> Redémarrez le serveur, puis réexécutez la tâche d’activation de la protection.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Ressource pour résoudre les problèmes d’installation Push

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Résoudre les problèmes de partage de fichiers et d’impression
* [Activer ou désactiver le partage de fichiers avec la stratégie de groupe](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Activer le partage de fichiers et d’impression via le Pare-feu Windows](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Résoudre les problèmes WMI
* [Test de base WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Résolution des problèmes WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Résolution des problèmes liés aux scripts et aux services WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>étapes suivantes

[Découvrez comment](tutorial-vmware-to-azure.md) configurer la récupération d’urgence de machines virtuelles VMware.