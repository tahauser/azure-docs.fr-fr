---
title: Reprotéger des machines virtuelles depuis Azure vers un site local | Microsoft Docs
description: Après avoir automatiquement restauré des machines virtuelles vers Azure, vous pouvez restaurer ces machines virtuelles vers votre site local. Apprenez à reprotéger des machines virtuelles avant une restauration automatique.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: cd5e53b49a850acf851e8351b5e14e2993176435
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Reprotéger des machines depuis Azure vers un site local

Après un [basculement](site-recovery-failover.md) de machines virtuelles VMware ou de serveurs physiques locaux vers Azure, la première étape de restauration automatique vers votre site local implique de protéger les machines virtuelles Azure créées pendant le basculement. Cet article décrit ce processus de reprotection. 

Pour une vue d’ensemble, regardez la vidéo suivante sur la procédure de basculement d’Azure vers un site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Avant de commencer

Si vous avez utilisé un modèle pour créer vos machines virtuelles, assurez-vous que chaque machine virtuelle possède son UUID pour les disques. Si l’UUID de la machine virtuelle locale est en conflit avec celui du serveur cible maître (car ceux-ci ont été créés à partir du même modèle), la reprotection échoue. Déployez un autre serveur cible maître qui n’a pas été créé à partir du même modèle.
- Si vous tentez d’effectuer une restauration automatique vers un autre serveur vCenter, vérifiez que le nouveau serveur vCenter et le serveur cible maître sont détectés. Un symptôme courant est que les banques de données ne sont pas accessibles/visibles dans la boîte de dialogue **Reprotéger**.
- Pour effectuer une réplication en retour vers le site local, vous avez besoin d’une stratégie de restauration automatique. Cette stratégie est automatiquement créée lorsque vous créez une stratégie dans le sens initial. Notez les points suivants :
    - Cette stratégie est automatiquement associée au serveur de configuration lors de la création.
    - Cette stratégie n’est pas modifiable.
    - Les valeurs définies de la stratégie sont (Seuil d’objectif de point de récupération = 15 minutes, Rétention de point de récupération = 24 heures, Fréquence des captures instantanées de cohérence d’application = 60 minutes)
- Le serveur de configuration local doit être en cours d’exécution et connecté pendant la reprotection et la restauration automatique.
- Si un serveur vCenter gère les machines virtuelles vers lesquelles vous allez effectuer une restauration automatique, vérifiez que vous disposez des [autorisations nécessaires](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) pour la détection des machines virtuelles sur les serveurs vCenter.
- Supprimez les captures instantanées sur le serveur cible maître avant la reprotection. Si des captures instantanées sont présentes sur le serveur cible maître local ou la machine virtuelle, la reprotection échoue. Les captures instantanées sur la machine virtuelle sont fusionnées automatiquement lors d’un travail de reprotection.
- Toutes les machines virtuelles d’un groupe de réplication doivent être du même type de système d’exploitation (toutes Windows ou toutes Linux). Un groupe de réplication avec des systèmes d’exploitation mixtes n’est pas pris en charge actuellement pour la reprotection et la restauration automatique locale. Ceci est dû au fait que le serveur cible maître doit exécuter le même système d’exploitation que la machine virtuelle, et toutes les machines virtuelles d’un groupe de réplication doivent avoir le même serveur cible maître. 
- Un serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échoue. Veillez à effectuer des sauvegardes régulières de votre serveur de configuration. En cas d’incident, restaurez le serveur avec la même adresse IP pour que la restauration automatique réussisse.
- La reprotection et la restauration automatique nécessitent un VPN de site à site pour répliquer les données. Spécifiez le réseau de sorte que les machines virtuelles basculées dans Azure puissent atteindre (avec une requête ping) le serveur de configuration local. Vous pouvez également déployer un serveur de processus dans le réseau Azure de la machine virtuelle basculée. Ce serveur de processus doit également être en mesure de communiquer avec le serveur de configuration local.
- Vérifiez que vous ouvrez les ports suivants pour le basculement et la restauration automatique.

    ![Ports pour le basculement et la restauration automatique](./media/vmware-azure-reprotect/failover-failback.png)

## <a name="deploy-a-process-server-in-azure"></a>Déployer un serveur de processus dans Azure

Vous aurez peut-être besoin d’un serveur de processus dans Azure avant de restaurer automatiquement votre site local :
- Le serveur de processus reçoit des données de la machine virtuelle protégée dans Azure, puis envoie des données au site local.
- Un réseau à faible latence est requis entre le serveur de processus et la machine virtuelle protégée. En règle générale, vous devez tenir compte de la latence pour déterminer si vous avez besoin d’un serveur de processus dans Azure :
    - Si vous disposez d’une connexion ExpressRoute, vous pouvez utiliser un serveur de processus local pour envoyer des données, car la latence entre la machine virtuelle et le serveur de processus est faible.
    - Toutefois, si vous disposez uniquement d’un VPN S2S, nous vous recommandons de déployer le serveur de processus dans Azure.
    - Nous vous recommandons d’utiliser un serveur de processus basé sur Azure lors de la restauration automatique. Les performances de réplication sont plus élevées si le serveur de processus est plus proche de la machine virtuelle de réplication (machine basculée dans Azure). À titre de preuve de concept, vous pouvez utiliser le serveur de processus local en complément d’ExpressRoute avec un appairage privé.

Effectuez le déploiement comme suit :

1. Si vous avez besoin de déployer un serveur de processus dans Azure, suivez [ces instructions](vmware-azure-set-up-process-server-azure.md)
2. Les machines virtuelles Azure envoient des données de réplication au serveur de processus. Configurez les réseaux de sorte que les machines virtuelles Azure puissent atteindre le serveur de processus.
3. N’oubliez pas que la réplication d’Azure vers un emplacement local peut s’effectuer uniquement sur le VPN S2S ou via l’appairage privé de votre réseau ExpressRoute. Assurez-vous de disposer de suffisamment de bande passante sur ce réseau.

## <a name="deploy-a-separate-master-target-server"></a>Déployer un serveur cible maître distinct

Le serveur cible maître reçoit les données de la restauration automatique. Par défaut, le serveur cible maître s’exécute sur le serveur de configuration local. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique. Voici comment en créer un :

    * [Créez un serveur cible maître Linux](vmware-azure-install-linux-master-target.md) pour la restauration automatique des machines virtuelles Linux. Cela est nécessaire.
    * Créez éventuellement un serveur cible maître distinct pour la restauration automatique des machines virtuelles Windows. Pour cela, réexécutez le programme d’installation unifiée et choisissez de créer un serveur cible maître. [Plus d’informations](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup)

Une fois que vous avez créé un serveur cible maître, effectuez les étapes suivantes :

- Si la machine virtuelle est présente en local sur le serveur vCenter, le serveur cible maître a besoin d’accéder au VMDK de la machine virtuelle locale. Cet accès est nécessaire pour écrire les données répliquées sur des disques de la machine virtuelle. Assurez-vous que le magasin de données de la machine virtuelle locale est monté sur l’hôte du serveur cible maître avec accès en lecture/écriture.
- Si la machine virtuelle n’est pas présente en local sur le serveur vCenter, le service Site Recovery doit créer une machine virtuelle durant la reprotection. Cette machine virtuelle est créée sur l’hôte ESX sur lequel vous créez le serveur cible maître. Sélectionnez bien l’hôte ESX afin que la machine virtuelle de la restauration automatique soit créée sur l’hôte de votre choix.
- Vous ne pouvez pas utiliser Storage vMotion pour le serveur cible maître*. Cela peut provoquer l’échec de la restauration. La machine virtuelle ne peut pas démarrer, car elle n’a pas accès aux disques. Pour éviter ce problème, excluez les serveurs cibles maîtres de votre liste vMotion.
- Si un serveur cible maître subit une tâche Storage vMotion après la reprotection, les disques des machines virtuelles protégés qui sont attachés au serveur cible maître sont migrés vers la cible de la tâche vMotion. Si vous essayez d’effectuer une restauration automatique après cela, le détachement des disques échoue car les disques sont introuvables. Cela devient difficile de trouver les disques dans vos comptes de stockage. Vous devez les rechercher manuellement et les attacher à la machine virtuelle. Après quoi, vous pouvez démarrer la machine virtuelle locale.
- Ajoutez un lecteur de rétention à votre serveur cible maître Windows existant. Ajoutez un disque et formatez le lecteur. Le lecteur de rétention est utilisé pour arrêter les points dans le temps où la machine virtuelle est répliquée vers le site local. Vous trouverez ci-après quelques critères d’un lecteur de rétention. Sans ces critères, le lecteur ne s’affichera pas pour le serveur cible maître.
    - Le volume n’est pas utilisé à d’autres fins, notamment comme cible de réplication.
    - Le volume n’est pas en mode de verrouillage.
    - Le volume n’est pas un volume de cache. Aucune installation de serveur maître cible ne doit exister sur ce volume. Le volume d’installation personnalisée pour le serveur de processus et le serveur cible maître n’est pas éligible comme volume de rétention. Lorsque le serveur de processus et le serveur cible maître sont installés sur un volume, ce volume est un volume de cache du serveur cible maître.
    - Le type de système de fichiers du volume n’est pas FAT ni FAT32.
    - La capacité du volume est différente de zéro.
    - Le volume de rétention par défaut pour Windows est le volume R.
    - Le volume de rétention par défaut pour Linux est /mnt/retention.
- Vous devez ajouter un nouveau lecteur si vous utilisez une machine de serveur de configuration /un serveur de processus existant ou un serveur de traitement ou de mise à l’échelle/une machine de serveur cible maître. Le nouveau lecteur doit respecter les conditions ci-dessus. Si le lecteur de rétention n’est pas présent, il n’apparaît pas dans la liste de sélection déroulante sur le portail. Une fois que vous avez ajouté un lecteur au serveur cible maître local, 15 minutes sont nécessaires pour que le lecteur apparaisse dans la sélection sur le portail. Vous pouvez également actualiser le serveur de configuration si le lecteur n’apparaît pas au bout de 15 minutes.
- Installez les outils VMware sur le serveur cible maître. Sans les outils VMware, les magasins de données sur l’hôte ESXi du serveur cible maître ne peuvent pas être détectés.
- Définissez le paramètre `disk.EnableUUID=true` dans les paramètres de configuration de la machine virtuelle cible maître dans VMware. Si cette ligne n’existe pas, ajoutez-la. Ce paramètre est nécessaire pour fournir un UUID cohérent au disque de machine virtuelle (VMDK) et lui assurer ainsi un montage correct.
- Au moins une banque de données VMFS doit être attaché au serveur cible maître. S’il n’y en a aucun, l’entrée **Banque de données** sur la page de reprotection sera vide et vous ne pouvez pas continuer.
- Le serveur cible maître ne peut pas avoir de captures instantanées sur les disques. S’il existe des captures instantanées, l’opération de reprotection et de restauration automatique échoue.
- Le serveur cible maître ne peut pas présenter de contrôleur SCSI Paravirtual. Il peut uniquement s’agir d’un contrôleur LSI Logic. Sans contrôleur LSI Logic, la reprotection échoue.
- Sur toute instance donnée, le serveur cible maître peut avoir au plus 60 disques attachés. Si le nombre de machines virtuelles en cours de reprotection sur le serveur cible maître local a un nombre total de disques supérieur à 60, les reprotections vers le serveur cible maître commencent à échouer. Veillez à avoir assez d’emplacements de disque sur le serveur cible maître, ou déployez des serveurs cibles maîtres supplémentaires.
    

## <a name="enable-reprotection"></a>Activer la reprotection

Après le démarrage d’une machine virtuelle dans Azure, il faut un certain temps à l’agent pour se réinscrire auprès du serveur de configuration (jusqu’à 15 minutes). Pendant ce temps, vous ne parvenez pas à effectuer la reprotection et un message d’erreur indique que l’agent n’est pas installé. Le cas échéant, patientez quelques minutes avant de relancer la reprotection.


1. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit de la souris sur la machine virtuelle ayant basculé et sélectionnez **Reprotéger**. Vous pouvez également cliquer sur la machine et sélectionner **Reprotéger** à partir des boutons de commande.
2. Vérifiez que la direction de protection **Azure à local** est déjà sélectionnée.
3. Dans les champs **Serveur cible maître** et **Serveur de processus**, sélectionnez le serveur cible maître local et le serveur de processus.
4. Pour **Banque de données**, sélectionnez la banque de données dans laquelle vous souhaitez récupérer les disques locaux. Cette option est utilisée lorsque la machine virtuelle locale est supprimée et que vous devez créer de nouveaux disques. Cette option est ignorée si les disques existent déjà, mais vous devez toujours spécifier une valeur.
5. Choisissez le lecteur de rétention.
6. La stratégie de restauration automatique est sélectionnée automatiquement.
7. Cliquez sur **OK** pour commencer l’opération de reprotection. Un travail est lancé pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Tâches** . Une fois la reprotection réussie, la machine virtuelle passera à l’état protégé.

Notez les points suivants :
- Si vous souhaitez procéder à la récupération vers un autre emplacement (si la machine virtuelle locale est supprimée), sélectionnez le lecteur de rétention et la banque de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent la même banque de données que le serveur cible maître. Une nouvelle machine virtuelle est créée dans vCenter.
- Si vous souhaitez récupérer la machine virtuelle Azure sur une machine virtuelle locale existante, montez les banques de données de la machine virtuelle locale avec un accès en lecture/écriture sur l’hôte ESXi du serveur cible maître.
    ![Boîte de dialogue Reprotéger](./media/vmware-azure-reprotect/reprotectinputs.png)

- Vous pouvez également effectuer la reprotection au niveau du plan de récupération. Un groupe de réplication peut uniquement être reprotégé via un plan de récupération. Lorsque vous effectuez la reprotection via un plan de récupération, vous devez fournir les valeurs de chaque machine protégée.
- Utilisez le même serveur cible maître pour reprotéger un groupe de réplication. Si vous utilisez un autre serveur cible maître pour reprotéger un groupe de réplication, le serveur ne peut fournir aucun point dans le temps commun.
- La machine virtuelle locale est désactivée au cours de la reprotection. Cela permet de garantir la cohérence des données pendant la réplication. N’activez pas la machine virtuelle une fois la reprotection terminée.



## <a name="common-issues"></a>Problèmes courants


- Actuellement, Site Recovery prend en charge la restauration automatique uniquement vers une banque de données de système de fichiers de machine virtuelle (VMFS) ou vSAN. Les banques de données NFS ne sont pas prises en charge. En raison de cette limitation, l’entrée de sélection de banque de données dans l’écran de reprotection est vide s’il s’agit d’une banque de données NFS, ou affiche la banque de données vSAN mais échoue lors de l’exécution du travail. Si vous voulez une restauration automatique, vous pouvez créer une banque de données VMFS locale, puis opérer la restauration automatique sur celle-ci. Cette restauration automatique entraînera un téléchargement complet du VMDK.
- Si vous effectuez une découverte utilisateur vCenter en mode lecture seule et protégez des machines virtuelles, la protection réussit et le basculement fonctionne. Au cours de la reprotection, le basculement échoue car les banques de données ne peuvent pas être détectées. L’un des symptômes est que les banques de données ne sont pas répertoriées lors de la reprotection. Pour résoudre ce problème, vous pouvez mettre à jour les informations d’identification vCenter à l’aide d’un compte approprié disposant des autorisations requises, puis renouveler l’opération. 
- Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez en local, vous pouvez constater que le package du Gestionnaire de réseau est désinstallé de la machine. En effet, celui-ci est supprimé une fois la machine virtuelle récupérée dans Azure.
- Lorsqu’une machine virtuelle Linux est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l’adresse IP est obtenue à partir de DHCP. Lorsque vous basculez en local, la machine virtuelle continue d’utiliser DHCP pour obtenir l’adresse IP. Connectez-vous manuellement à l’ordinateur et redéfinissez l’adresse IP sur une adresse statique le cas échéant. Une machine virtuelle Windows peut récupérer son adresse IP fixe.
- Si vous utilisez la version gratuite d’ESXi 5.5 ou de vSphere 6 Hypervisor, le basculement aboutit, mais la restauration automatique ne fonctionnera pas. Vous devrez effectuer une mise à niveau vers une licence d’évaluation d’un des programmes pour activer la restauration automatique.
- Si vous ne pouvez pas communiquer avec le serveur de configuration à partir du serveur de processus, utilisez Telnet pour vérifier la connectivité au serveur de configuration sur le port 443. Vous pouvez également essayer d’exécuter un test ping sur le serveur de configuration à partir du serveur de processus. Un serveur de processus doit également avoir une pulsation lorsqu’il est connecté au serveur de configuration.
- Un serveur Windows Server 2008 R2 SP1 protégé comme serveur physique sur site ne peut pas être restauré localement à partir d’Azure.
- Vous ne pouvez pas effectuer de restauration automatique dans les circonstances suivantes :
    - Vous avez effectué une migration des machines vers Azure. [Plus d’informations](migrate-overview.md#what-do-we-mean-by-migration)
    - Vous avez déplacé une machine virtuelle vers un autre groupe de ressources.
    - Vous avez supprimé la machine virtuelle Azure.
    - Vous avez désactivé la protection de la machine virtuelle.
    - Vous avez créé la machine virtuelle manuellement dans Azure. Il faut que la machine ait été initialement protégée localement et qu’elle ait basculé vers Azure avant la reprotection.
    - Vous pouvez uniquement basculer vers un hôte ESXi. Vous ne pouvez pas restaurer automatiquement des machines virtuelles VMware ou des serveurs physiques vers des hôtes Hyper-V, des ordinateurs physiques ou des stations de travail VMware.


## <a name="next-steps"></a>Étapes suivantes

Une fois que la machine virtuelle est passée à l’état protégé, vous pouvez [commencer une restauration automatique](vmware-azure-failback.md). La restauration automatique arrêtera la machine virtuelle dans Azure et démarrera la machine virtuelle en local. Prévoyez un temps d’arrêt de l’application. Effectuez la restauration automatique lorsque l’application peut tolérer un temps d’arrêt.

