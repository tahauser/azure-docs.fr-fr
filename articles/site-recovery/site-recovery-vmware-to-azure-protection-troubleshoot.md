---
title: "Corriger les défauts de protection VMware/physiques dans Azure | Microsoft Docs"
description: "Cet article décrit les échecs de réplication d’ordinateur VMware courants et comment les résoudre"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: asgang
ms.openlocfilehash: 9e0c602646009b20c8d4f8a29d55b7f44a089131
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Résoudre les problèmes locaux de réplication VMware/du serveur physique
Vous pouvez recevoir un message d’erreur spécifique lorsque vous protégez vos machines virtuelles VMware ou les serveurs physiques à l’aide d’Azure Site Recovery. Cet article décrit en détail certains des messages d’erreur les plus couramment rencontrés, ainsi que les étapes de dépannage à suivre pour les résoudre.


## <a name="initial-replication-is-stuck-at-0"></a>La réplication initiale est bloquée à 0 %
La plupart des échecs de réplication initiale que nous rencontrons au niveau du support sont dus à des problèmes de connectivité entre le serveur source et le serveur de traitement ou le serveur de traitement et Azure.
Dans la plupart des cas, vous pouvez résoudre ces problèmes en suivant les étapes indiquées ci-dessous.

###<a name="check-the-following-on-source-machine"></a>Vérifiez les points suivants sur la MACHINE SOURCE
* À partir de la ligne de commande du serveur source, utilisez Telnet pour effectuer un test Ping sur le serveur de traitement avec le port https (par défaut 9443) comme indiqué ci-dessous. Cela vous indiquera s’il existe des problèmes de connectivité réseau ou des problèmes de blocage de port par le pare-feu.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Utilisez Telnet et non PING pour tester la connectivité.  Si Telnet n’est pas installé, suivez les étapes présentées [ici](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Si vous ne parvenez pas à vous connecter, autorisez le port entrant 9443 sur le serveur de traitement et vérifiez si le problème persiste. Dans certains cas, le serveur de traitement se trouvait derrière une zone DMZ, ce qui était à l’origine de ce problème.

* Vérifiez l’état du service `InMage Scout VX Agent – Sentinel/OutpostStart` s’il n’est pas en cours d’exécution et vérifiez si le problème persiste.   

###<a name="check-the-following-on-process-server"></a>Vérifiez les éléments suivants sur le SERVEUR DE TRAITEMENT

* **Vérifiez si le serveur de traitement transmet activement des données à Azure**

Sur le serveur de traitement, ouvrez le Gestionnaire des tâches (Ctrl-Maj-Échap). Accédez à l’onglet Performances, puis cliquez sur le lien « Open Resource Monitor » (Ouvrir le moniteur de ressource). Dans le Gestionnaire des ressources, accédez à l’onglet Réseau. Vérifiez si cbengine.exe dans « Processes with Network Activity » (Processus avec activité réseau) envoie activement de gros volume de données (en Mo).

![Activer la réplication](./media/site-recovery-protection-common-errors/cbengine.png)

Si ce n’est pas le cas, suivez les étapes détaillées ci-dessous :

* **Vérifiez si le serveur de traitement est en mesure de se connecter à Azure Blob** : sélectionnez et vérifiez cbengine.exe pour afficher les connexions TCP et voir s’il existe une connectivité du serveur de traitement à l’URL d’objet blob Azure Storage.

![Activer la réplication](./media/site-recovery-protection-common-errors/rmonitor.png)

Si ce n’est pas le cas, accédez au Panneau de configuration > Services et vérifiez si les services suivants sont en cours d’exécution :

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re)démarrez les services qui ne sont pas en cours d’exécution et vérifiez si le problème persiste.

* **Vérifiez si le serveur de traitement est en mesure de se connecter à l’adresse IP publique Azure via le port 443**

Ouvrez le dernier fichier CBEngineCurr.errlog sous `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` et recherchez « 443 » et « connection attempt failed ».

![Activer la réplication](./media/site-recovery-protection-common-errors/logdetails1.png)

Si vous rencontrez des problèmes, dans la ligne de commande du serveur de traitement, utilisez telnet pour effectuer un test ping avec votre adresse IP publique Azure (masquée dans l’image ci-dessus) trouvée dans le fichier CBEngineCurr.currLog via le port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Si vous ne parvenez pas à vous connecter, vérifiez si le problème d’accès est dû au pare-feu ou au proxy, comme décrit dans l’étape suivante.


* **Vérifiez si le pare-feu basé sur l’adresse IP du serveur de traitement ne bloque pas l’accès** : si vous utilisez des règles de pare-feu basées sur l’adresse IP sur le serveur, téléchargez la liste complète des plages d’IP du centre de données Microsoft Azure [ici](https://www.microsoft.com/download/details.aspx?id=41653) et ajoutez-les à la configuration de votre pare-feu pour vous assurer que la communication avec Azure (et le port HTTPS (443)) est autorisée.  Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

* **Vérifiez si le pare-feu basé sur l’URL du serveur de traitement ne bloque pas l’accès** : si vous utilisez des règles de pare-feu basées sur l’URL sur le serveur, vérifiez que les URL suivantes figurent dans la configuration du pare-feu.

  `*.accesscontrol.windows.net:` : élément utilisé pour la gestion des identités et le contrôle d’accès.

  `*.backup.windowsazure.com:` : élément utilisé pour l’orchestration et le transfert des données de réplication.

  `*.blob.core.windows.net:` : élément utilisé pour l’accès au compte de stockage qui stocke les données répliquées

  `*.hypervrecoverymanager.windowsazure.com:` : élément utilisé pour l’orchestration et l’administration des opérations de gestion de la réplication.

  `time.nist.gov` et `time.windows.com` : éléments utilisés pour vérifier la synchronisation horaire entre l’horloge système et l’heure globale.

URL du **cloud Azure Government** :

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Vérifiez si les paramètres de proxy sur le serveur de traitement ne bloque pas l’accès**.  Si vous utilisez un serveur proxy, vérifiez que le nom du serveur proxy résout le nom du serveur DNS.
Pour vérifier les informations que vous avez fournies au moment de la configuration du serveur de configuration. Accédez à la clé de Registre

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Assurez-vous maintenant que les mêmes paramètres sont utilisés par l’agent Azure Site Recovery pour envoyer des données.
Rechercher dans la sauvegarde Microsoft Azure

![Activer la réplication](./media/site-recovery-protection-common-errors/mab.png)

Ouvrez-la et cliquez sur Action > Modifier les propriétés. Sous l’onglet Configuration du proxy, vous devez voir l’adresse du proxy, qui doit être la même que celle figurant dans les paramètres du Registre. Si ce n’est pas le cas, remplacez-la par la même adresse.

![Activer la réplication](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Vérifiez si la bande passante de limitation n’est pas limitée sur le serveur de traitement** : augmentez la bande passante et vérifiez si le problème persiste.

##<a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’aide, posez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Nous avons une communauté active et un de nos ingénieurs pourra vous aider.
