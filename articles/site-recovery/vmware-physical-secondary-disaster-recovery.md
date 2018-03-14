---
title: "Configurer la récupération d’urgence de machines virtuelles ou de serveurs physiques VMware sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence de machines virtuelles VMware, ou de serveurs physiques Windows et Linux, sur un site secondaire avec Azure Site Recovery."
services: site-recovery
author: nsoneji
manager: gauarvd
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/27/2018
ms.author: raynew
ms.openlocfilehash: c83f6142e70103ff4d99d9d2871c4ab747af8d99
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurer la récupération d’urgence de machines virtuelles ou de serveurs physiques VMware locaux sur un site secondaire

InMage Scout dans [Azure Site Recovery](site-recovery-overview.md) assure la réplication en temps réel entre des sites VMware locaux. InMage Scout est inclus dans les abonnements au service Azure Site Recovery. 


## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel :

- [Examinez](site-recovery-support-matrix-to-sec-site.md) les spécifications de prise en charge de tous les composants.
- Vérifiez que les machines à répliquer sont conformes à la [prise en charge des machines répliquées](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>création d'un coffre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Choisir un objectif en matière de protection

Sélectionnez ce qui doit être répliqué et où.

1. Cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
2. Sélectionnez **Vers le site de récupération** > **Oui, avec l’hyperviseur VMware vSphere**. Cliquez ensuite sur **OK**.
3. Dans **Installation de Scout**, téléchargez le logiciel InMage Scout 8.0.1 GA et la clé d’inscription. Les fichiers d’installation pour tous les composants figurent dans le fichier .zip téléchargé.

## <a name="download-and-install-component-updates"></a>Télécharger et installer les mises à jour des composants

 Examinez et installez les dernières [mises à jour](#updates). Les mises à jour doivent être installées sur les serveurs dans l’ordre suivant :

1. Serveur RX (le cas échéant)
2. Serveurs de configuration
3. Serveurs de traitement
4. Serveurs cibles maîtres
5. Serveurs vContinuum
6. Serveur source (serveurs Windows et Linux)

Installez les mises à jour comme suit :

> [!NOTE]
>Les versions à jour des fichiers de composants Scout peuvent être différentes dans le fichier zip de mise à jour. La version plus ancienne indique qu’il n’y a eu aucune modification dans le composant depuis la dernière mise à jour.

Téléchargez le fichier .zip de [mise à jour](https://aka.ms/asr-scout-update6). Le fichier contient les éléments suivants : 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update 4 bits pour RHEL5, OL5, OL6, SUSE 10, SUSE 11 : UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extrayez les fichiers .zip.
2. **Serveur RX** : copiez **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** sur le serveur RX, puis extrayez-le. Dans le dossier extrait, exécutez **/Install**.
3. **Serveur de configuration et serveur de processus** : copiez **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** sur le serveur de configuration et le serveur de processus. Double-cliquez dessus pour l’exécuter.<br>
4. **Serveur cible maître Windows** : pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** sur le serveur cible maître. Double-cliquez dessus pour l’exécuter. La même mise à jour de l’agent unifié s’applique aussi au serveur source. Si la source n’a pas été mise à jour vers Update 4, vous devez mettre à jour l’agent unifié.
  Vous n’avez pas besoin d’appliquer la mise à jour au serveur cible maître préparé avec **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, étant donné qu’il s’agit du nouveau programme d’installation avec toutes les dernières modifications.
5. **Serveur vContinuum** : copiez **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** sur le serveur.  Vérifiez que vous avez fermé l’Assistant vContinuum. Double-cliquez sur le fichier pour l’exécuter.
    Vous n’avez pas besoin d’appliquer la mise à jour au serveur cible maître préparé avec **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, étant donné qu’il s’agit du nouveau programme d’installation avec toutes les dernières modifications.
6. **Serveur cible maître Linux** : pour mettre à jour l’agent unifié, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur cible maître et extrayez-le. Dans le dossier extrait, exécutez **/Install**.
7. **Serveur source Windows** : pour mettre à jour l’agent unifié, copiez **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** sur le serveur source. Double-cliquez sur le fichier pour l’exécuter. 
    Vous n’avez pas besoin d’installer l’agent Update 5 sur le serveur source si ce dernier a déjà été mis à jour avec l’Update 4 ou si l’agent source est installé avec le programme d’installation le plus récent **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Serveur source Linux** : pour mettre à jour l’agent unifié, copiez la version correspondante du fichier de l’agent unifié sur le serveur Linux et extrayez-le. Dans le dossier extrait, exécutez **/Install**.  Par exemple : pour le serveur RHEL 6.7 64 bits, copiez **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur et extrayez-le. Dans le dossier extrait, exécutez **/Install**.

## <a name="enable-replication"></a>Activer la réplication

1. Configurez la réplication entre les sites VMware sources et cibles.
2. Consultez les documents suivants pour en savoir plus sur l’installation, la protection et la récupération :

   * [Notes de publication](https://aka.ms/asr-scout-release-notes)
   * [Matrice de compatibilité](https://aka.ms/asr-scout-cm)
   * [Guide d’utilisation](https://aka.ms/asr-scout-user-guide)
   * [Guide d’utilisation de RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guide d'installation rapide](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Mises à jour

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Mise à jour : 12 octobre 2017

Téléchargez [Scout Update 6](https://aka.ms/asr-scout-update6).

Scout Update 6 est une mise à jour cumulative. Elle contient tous les correctifs d’Update 1 à Update 5, ainsi que les nouveaux correctifs et améliorations décrits ci-dessous. 

#### <a name="new-platform-support"></a>Prise en charge de nouvelles plateformes
* Ajout de la prise en charge de Source Windows Server 2016
* Ajout de la prise en charge des systèmes d’exploitation Linux suivants :
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Ajout de la prise en charge de VMware Center 6.5

> [!NOTE]
> * Actualisation du programme d’installation d’agent unifié (UA) de base pour prendre en charge Windows Server 2016. Le nouveau programme d’installation **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** est disponible dans le package Scout GA de base (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Le même programme d’installation sera utilisé pour toutes les versions Windows prises en charge. 
> * Actualisation des programmes d’installation du serveur cible maître et de Windows vContinuum de base pour prendre en charge Windows Server 2016. Le nouveau programme d’installation **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** est disponible dans le package Scout GA de base (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Le même programme d’installation sera utilisé pour déployer les serveurs cibles maîtres Windows 2016 et Windows 2012R2.
> * Téléchargez le package GA à partir du portail, comme décrit dans [créer un coffre](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Résolutions de bogues et améliorations
- Échec de la protection de la restauration automatique pour la machine Linux. La liste des disques à répliquer est vide à la fin de la configuration.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 est une mise à jour cumulative. Elle contient tous les correctifs d’Update 1 à Update 4, ainsi que les nouveaux correctifs décrits ci-dessous.
- Les correctifs de Site Recovery Scout Update 4 à Update 5 sont spécifiquement destinés aux composants du serveur cible maître et de vContinuum.
- Si les serveurs sources, le serveur cible maître, et les serveurs de configuration, de processus et RX exécutent l’Update 4, appliquez-la seulement sur le serveur cible maître. 

#### <a name="new-platform-support"></a>Prise en charge de nouvelles plateformes
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 bits  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** est fourni avec le package Scout GA de base (**InMage_Scout_Standard_8.0.1 GA.zip**). Téléchargez le package GA à partir du portail, comme décrit dans [créer un coffre](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Résolutions de bogues et améliorations

* Correctifs pour améliorer la fiabilité de la prise en charge des clusters Windows :
    * Corrigé : certains disques de cluster P2V MSCS se changent en disques bruts après la récupération.
    * Corrigé : échec de la récupération de cluster P2V MSCS en raison d’une non-correspondance de l’ordre des disques.
    * Corrigé : l’opération d’ajout de disques au cluster MSCS échoue avec une erreur de non-correspondance de taille de disque.
    * Corrigé : échec de la vérification de la préparation au mappage du cluster MSCS source avec RDM LUN lors de la vérification de la taille.
    * Corrigé : échec de la protection du cluster à un seul nœud en raison d’un problème de non-correspondance SCSI. 
    * Corrigé : échec de la reprotection du serveur de cluster Windows P2V si les disques du cluster cible sont présents. 
    
* Corrigé : lors de la protection de la restauration automatique, si le serveur cible maître sélectionné ne se trouve pas sur le même serveur ESXi que la machine source protégée (lors de la protection du transfert), vContinuum sélectionne le mauvais serveur cible maître lors la récupération de la restauration automatique, ce qui entraîne l’échec de l’opération de récupération.

> [!NOTE]
> * Les correctifs de cluster P2V s’appliquent seulement aux clusters MSCS physiques qui sont nouvellement protégés avec Site Recovery Scout Update 5. Pour installer les correctifs de cluster sur des clusters P2V MSCS protégés avec des mises à jour plus anciennes, suivez les étapes de mise à niveau mentionnées dans la section 12 de [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes).
> * Si, au moment de la reprotection. le même jeu de disques est actif sur chaque nœud de cluster comme ils l’étaient quand ils ont été initialement protégés, la reprotection d’un cluster MSCS physique peut seulement réutiliser les disques cibles existants. Dans le cas contraire, utilisez la procédure manuelle mentionnée dans la section 12 de [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes) pour déplacer les disques côté cible dans le chemin du magasin de données adéquat, de façon à les réutiliser lors de la reprotection. Si vous reprotégez le cluster MSCS en mode P2V sans appliquer la procédure de mise à niveau, il crée un nouveau disque sur le serveur ESXi cible. Vous devrez supprimer manuellement les anciens disques du magasin de données.
> * Quand une source SLES11 ou SLES11 (avec n’importe quel Service Pack) est redémarré normalement, marquez manuellement les paires de réplications de disque **racine** pour la resynchronisation. Aucune notification n’est faite dans l’interface CX. Si vous ne marquez pas le disque racine pour la resynchronisation, des problèmes d’intégrité des données peuvent apparaître.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 est une mise à jour cumulative. Elle contient tous les correctifs d’Update 1 à Update 3, ainsi que les nouveaux correctifs décrits ci-dessous.

#### <a name="new-platform-support"></a>Prise en charge de nouvelles plateformes

* Ajout de la prise en charge pour vCenter/vSphere 6.0, 6.1 et 6.2
* Ajout de la prise en charge de ces systèmes d’exploitation Linux :
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 and 7.2
  * CentOS 7.0, 7.1 et 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** est fourni avec le package Scout GA de base **InMage_Scout_Standard_8.0.1 GA.zip**. Téléchargez le package Scout GA à partir du portail, comme décrit dans [créer un coffre](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Résolutions de bogues et améliorations

* Amélioration de la gestion de l’arrêt pour les systèmes d’exploitation Linux et les clones suivants pour éviter les problèmes indésirables liés à la resynchronisation :
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Pour Linux, les autorisations d’accès à tous les dossiers dans le répertoire d’installation de l’agent unifié sont à présent limitées à l’utilisateur local uniquement.
* Sur Windows, correction d’un problème d’expiration de délai lors de l’émission d’un signet de cohérence distribué commun sur les applications distribuées très chargées, comme les clusters SQL Server et SharePoint.
* Correctif relatif au journal dans le programme d’installation de base du serveur de configuration.
* Un lien de téléchargement vers VMware vCLI 6.0 a été ajouté au programme d’installation de base du serveur cible maître Windows.
* Des vérifications et des journaux supplémentaires ont été ajoutés pour les modifications de la configuration réseau lors des tests de basculement et de récupération d’urgence.
* Correctif pour un problème qui provoquait la non-communication des informations de conservation au serveur de configuration.  
* Pour les clusters physiques, correctif pour un problème qui provoquait l’échec du redimensionnement du volume dans l’Assistant vContinuum lors de la réduction du volume source.
* Correctif pour un problème de protection du cluster qui échouait avec l’erreur « Impossible de trouver la signature de disque » quand le disque de cluster était un disque PRDM.
* Correctif pour le blocage du serveur de transport cxps provoqué par une exception relative au dépassement des limites.
* Les colonnes Nom du serveur et Adresse IP sont à présent redimensionnables dans la page **Installation Push** de l’Assistant vContinuum.
* Améliorations de l’API RX :
  * Les cinq derniers points de cohérence communs sont désormais disponibles (seulement les étiquettes garanties).
  * Les détails sur la capacité et l’espace libre sont affichés pour tous les appareils protégés.
  * L’état du pilote Scout sur le serveur source est disponible.

> [!NOTE]
> * Le package de base **InMage_Scout_Standard_8.0.1_GA.zip** a :
    * Un programme d’installation de base du serveur de configuration mis à jour base (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Un programme d’installation de base du serveur cible maître Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Pour toutes les nouvelles installations, utilisez le nouveau serveur de configuration et les exécutables GA du serveur cible maître Windows.
> * Vous pouvez appliquer Update 4 directement sur 8.0.1 GA.
> * Les mises à jour du serveur de configuration et de RX ne peuvent pas être annulées après avoir été appliquées.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Toutes les mises à jour de Site Recovery sont cumulatives. Update 3 contient tous les correctifs d’Update 1 et d’Update 2. Vous pouvez directement appliquer l’Update3 à 8.0.1 GA. Les mises à jour du serveur de configuration et de RX ne peuvent pas être annulées après avoir été appliquées.

#### <a name="bug-fixes-and-enhancements"></a>Résolutions de bogues et améliorations
Update 3 résout les problèmes suivants :

* Le serveur de configuration et le serveur RX ne sont pas inscrits dans le coffre quand ils sont derrière le proxy.
* Le nombre d’heures pendant lesquelles l’objectif de point de récupération n’a pas été atteint n’est pas mis à jour dans le rapport d’intégrité.
* Le serveur de configuration ne se synchronise pas avec RX quand les détails du matériel ESX ou du réseau contiennent des caractères UTF-8.
* Les contrôleurs de domaine Windows Server 2008 R2 ne démarrent pas après la récupération.
* La synchronisation hors connexion ne fonctionne pas comme prévu.
* Après le basculement de la machine virtuelle, la suppression de la paire de réplication ne progresse pas dans la console du serveur de configuration pendant un long moment. Les utilisateurs ne peuvent pas terminer la restauration automatique ou reprendre les opérations.
* Les opérations de capture instantanée globale effectuées par la tâche de cohérence ont été optimisées pour réduire les déconnexions des applications, comme les clients SQL Server.
* Les performances de l’outil de cohérence (VACP.exe) ont été améliorées. L’utilisation de la mémoire nécessaire pour créer des captures instantanées sur Windows a été réduite.
* Le service d’installation Push se bloque quand le mot de passe comporte plus de 16 caractères.
* vContinuum ne vérifie pas et ne demande pas les nouvelles informations d’identification de vCenter quand elles sont modifiées.
* Sur Linux, le gestionnaire de cache cible maître (cachemgr) ne télécharge pas les fichiers à partir du serveur de processus. Ceci entraîne la limitation de la paire de réplication.
* Quand l’ordre des disques du cluster de basculement physique (MSCS) n’est pas le même sur tous les nœuds, la réplication n’est pas définie pour certains volumes du cluster. Le cluster doit être reprotégé pour tirer parti de ce correctif.  
* La fonctionnalité SMTP ne fonctionne pas comme attendu après la mise à niveau de RX de Scout 7.1 vers Scout 8.0.1.
* Des statistiques ont été ajoutées au journal pour l’opération de restauration, pour suivre la durée nécessaire à cette dernière.
* Ajout de la prise en charge des systèmes d’exploitation Linux sur le serveur source :
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* Les consoles du serveur de configuration et du serveur RX affichent désormais des notifications pour la paire, qui passe en mode bitmap.
* Les correctifs de sécurité suivants ont été ajoutés dans RX :
    * Contournement des autorisations via l’altération de paramètre : accès limité aux utilisateurs non applicables.
    * Falsification de requête intersites : le concept de jeton de page a été implémenté et il est généré aléatoirement pour chaque page. Cela signifie qu’il n’existe qu’une seule instance de connexion pour le même utilisateur et que l’actualisation de la page ne fonctionne pas. Au lieu de cela, elle redirige vers le tableau de bord.
    * Chargement de fichiers malveillants : les fichiers sont limités à des extensions spécifiques : z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml et zip.
    * Scripts intersites permanents : des validations des entrées ont été ajoutées.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Les révisions apportées dans Update 2 sont les suivantes :

* **Serveur de configuration** : problèmes empêchant le fonctionnement normal de la fonctionnalité de contrôle (gratuite pendant 31 jours) quand le serveur de configuration était inscrit dans Site Recovery.
* **Agent unifié** : résolution d’un problème dans Update 1 empêchant l’installation de la mise à jour sur le serveur cible maître lors de sa mise à niveau de la version 8.0 à la version 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 contient les correctifs de bogues et les nouvelles fonctionnalités suivants :

* 31 jours de protection gratuite par instance de serveur. Ceci vous permet de tester les fonctionnalités ou de procéder à une validation technique.
* Toutes les opérations sur le serveur, notamment le basculement et la restauration automatique, sont gratuites pendant les 31 premiers jours. Cette période démarre quand un serveur est protégé pour la première fois avec Site Recovery Scout. À compter du 32e jour, chaque serveur protégé est facturé au tarif d’une instance standard pour la protection Site Recovery sur un site appartenant au client.
* À tout moment, le nombre de serveurs protégés facturés est disponible sur le **tableau de bord** du coffre.
* La prise en charge de vSphere vCLI (Command-Line Interface) 5.5 Update 2 a été ajoutée.
* La prise en charge de ces systèmes d’exploitation Linux sur le serveur source a été ajoutée :
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Correctifs de bogues pour résoudre les problèmes suivants :
  * L’inscription du coffre échoue pour le serveur de configuration ou pour le serveur RX.
  * Les volumes du cluster n’apparaissent pas comme attendu quand les machines virtuelles en cluster sont reprotégées lors de leur reprise.
  * La restauration automatique échoue quand le serveur cible maître est hébergé sur un autre serveur ESXi que les machines virtuelles de production locales.
  * Les autorisations du fichier de configuration sont changées lors de la mise à niveau vers la version 8.0.1. Cette modification affecte la protection et les opérations.
  * Le seuil de resynchronisation n’est pas appliqué comme attendu, entraînant un comportement incohérent de la réplication.
  * Les paramètres RPO n’apparaissent pas correctement dans la console du serveur de configuration. La valeur de données non compressée apparaît par erreur comme valeur compressée.
  * L’opération Remove n’effectue pas la suppression attendue dans l’Assistant vContinuum, et la réplication n’est pas supprimée de la console du serveur de configuration.
  * Dans l’Assistant vContinuum, le disque est désactivé automatiquement quand vous cliquez sur **Détails** dans la vue des disques pendant la protection des machines virtuelles MSCS.
  * Dans le scénario physique-à-virtuel (P2V), les services HP nécessaires (comme CIMnotify et CqMgHost) ne passent pas à l’état manuel dans la récupération de la machine virtuelle. Ce problème allonge la durée du démarrage.
  * La protection des machines virtuelles Linux échoue quand le serveur cible maître contient plus de 26 disques.

