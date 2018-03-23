---
title: Gérer un serveur de processus dans Azure Site Recovery | Microsoft Docs
description: Cet article explique comment gérer un serveur de processus configuré pour la réplication d’une machine virtuelle VMware et d’un serveur physique dans Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: 096b2890d41402448809ae87759fcd6b67bee2fe
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-process-servers"></a>Gérer des serveurs de processus

Par défaut, le serveur de processus utilisé lorsque vous répliquez des machines virtuelles VMware ou des serveurs physiques dans Azure est installé sur l’ordinateur du serveur de configuration local. Il existe deux cas de figure qui doivent vous amener à configurer un serveur de processus distinct :

- Pour les déploiements à grande échelle, il peut être nécessaire de faire évoluer la capacité de vos serveurs de processus locaux.
- Pour la restauration automatique, vous avez besoin d’un serveur de processus temporaire configuré dans Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée. 

Cet article résume les tâches de gestion courantes pour ces serveurs de processus supplémentaires.

## <a name="upgrade-a-process-server"></a>Mettre à niveau un serveur de processus

Pour mettre à niveau un serveur de processus exécuté en local ou dans Azure (en vue d’une restauration automatique), procédez comme suit :

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  En règle générale, lorsque vous utilisez l’image de la galerie Azure pour créer un serveur de processus dans Azure, dans le cadre d’une restauration automatique, elle utilise la version la plus récente. L’équipe Site Recovery publie régulièrement des correctifs et des améliorations, aussi nous vous recommandons de mettre à jour vos serveurs de processus.



## <a name="reregister-a-process-server"></a>Réinscrire un serveur de processus

Si vous avez besoin de réinscrire un serveur de processus exécuté en local ou dans Azure, procédez comme suit au niveau du serveur de configuration :

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Une fois les paramètres enregistrés, procédez comme suit :

1. Sur le serveur de processus, ouvrez une invite de commandes administrateur.
2. Accédez au dossier **%PROGRAMDATA%\ASR\Agent**, puis exécutez la commande suivante :

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modifier les paramètres de proxy pour un serveur de processus local

Si le serveur de processus utilise un proxy pour se connecter à Site Recovery dans Azure, utilisez cette procédure si vous devez modifier les paramètres de proxy existants.

1. Ouvrez une session sur l’ordinateur du serveur de processus. 
2. Ouvrez une fenêtre de commande d’administration PowerShell et exécutez la commande suivante :
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Accédez au dossier **%PROGRAMDATA%\ASR\Agent** et exécutez la commande suivante :
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Supprimer un serveur de processus

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


