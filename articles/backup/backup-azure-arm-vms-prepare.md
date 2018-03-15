---
title: "Sauvegarde Azure : Préparation à la sauvegarde de machines virtuelles | Microsoft Docs"
description: "Assurez-vous que votre environnement est prêt pour la sauvegarde de machines virtuelles dans Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "sauvegardes ; sauvegarde ;"
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 62e047d706bdc42abbe44340c87267e59eb84369
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Préparation de votre environnement pour la sauvegarde des machines virtuelles Resource Manager

Cet article fournit les étapes de préparation de votre environnement pour la sauvegarde d’une machine virtuelle déployée avec le modèle Azure Resource Manager. Les étapes indiquées dans les procédures utilisent le portail Azure. Stockez les données de sauvegarde de la machine virtuelle dans un coffre Recovery Services. Le coffre conserve les données de sauvegarde des machines virtuelles classiques et déployées à l’aide du modèle Resource Manager.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Resource Manager et Classique](../azure-resource-manager/resource-manager-deployment-model.md).

Avant de protéger ou sauvegarder une machine virtuelle déployée à l’aide du modèle Resource Manager, vérifiez que les conditions préalables suivantes sont remplies :

* Créez un coffre Recovery Services (ou identifiez un coffre Recovery Services existant) *dans la même région que votre machine virtuelle*.
* Sélectionnez un scénario, définissez la stratégie de sauvegarde et définissez les éléments à protéger.
* Vérifiez l’installation d’un agent de machine virtuelle sur la machine virtuelle.
* Vérifiez la connectivité réseau.
* Pour les machines virtuelles Linux, si vous voulez personnaliser votre environnement de sauvegarde pour des sauvegardes cohérentes avec les applications, suivez les [étapes permettant de configurer des scripts avant et après les captures instantanées](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Si ces conditions existent déjà dans votre environnement, passez à [l’article traitant de la sauvegarde de vos machines virtuelles](backup-azure-arm-vms.md). Si vous avez besoin de définir ou de vérifier l’une de ces conditions préalables, cet article vous guide le long des étapes nécessaires.

## <a name="supported-operating-systems-for-backup"></a>Systèmes d’exploitation pris en charge pour la sauvegarde
 * **Linux** : Azure Backup prend en charge [une liste de distributions approuvées par Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), à l’exception de CoreOS Linux. 
 
    > [!NOTE] 
    > D’autres distributions « Bring-Your-Own-Linux » peuvent fonctionner, tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge. Les distributions ne sont toutefois pas prises en charge.
 * **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limites lors de la sauvegarde et la restauration d’une machine virtuelle
Avant de préparer votre environnement, assurez-vous de noter les limitations suivantes :

* La sauvegarde de machines virtuelles ayant plus de 16 disques de données n’est pas prise en charge.
* La sauvegarde de machines virtuelles avec des tailles de disque de données supérieures à 1 023 Go n’est pas prise en charge.

  > [!NOTE]
  > Nous avons une préversion privée qui prend en charge les sauvegardes des machines virtuelles dotées de disques de plus de 1 To. Pour plus d’informations, consultez [Préversion privée pour la prise en charge de la sauvegarde des machines virtuelles dotées de disques volumineux](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a).
  >

* La sauvegarde de machines virtuelles avec une adresse IP réservée et sans point de terminaison n’est pas prise en charge.
* La sauvegarde des machines virtuelles Linux chiffrées via le chiffrement LUKS (Linux Unified Key Setup) n’est pas prise en charge.
* La sauvegarde des machines virtuelles contenant une configuration de volumes partagés de cluster (CSV) ou de serveur de fichiers avec montée en puissance parallèle n’est pas recommandée. Cette sauvegarde exige d’impliquer toutes les machines virtuelles incluses dans la configuration du cluster pendant la tâche de capture instantanée. Sauvegarde Azure ne prend pas en charge la cohérence multimachine virtuelle. 
* Les données de sauvegarde n’incluent pas les lecteurs réseau montés attachés à une machine virtuelle.
* Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Si vous tentez de restaurer la machine virtuelle alors que celle-ci existe, l’opération de restauration échoue.
* La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
* La sauvegarde et la restauration de machines virtuelles à l’aide de disques non managés dans des comptes de stockage avec des règles de réseau ne sont pas prises en charge. 
* Lors de la configuration de la sauvegarde, assurez-vous que les paramètres du compte de stockage **Pare-feux et réseaux virtuels** permettent l’accès à partir de « Tous les réseaux ».
* Vous pouvez sauvegarder des machines virtuelles dans toutes les régions publiques d’Azure. (Consultez la [liste](https://azure.microsoft.com/regions/#services) des régions prises en charge.) Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de coffres.
* La restauration d’une machine virtuelle de contrôleur de domaine qui fait partie d’une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. Pour en savoir plus, consultez [Restauration d’un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* La restauration de machines virtuelles qui ont des configurations réseau spéciales suivantes est prise en charge uniquement par le biais de PowerShell. Les machines virtuelles créées à l’aide du flux de travail de restauration dans l’interface utilisateur n’ont pas ces configurations réseau une fois l’opération de restauration terminée. Pour plus d’informations, consultez [Restauration de machines virtuelles avec des configurations de réseau spéciales](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Machines virtuelles avec configuration d’un équilibreur de charge (internes et externes)
  * Machines virtuelles avec plusieurs adresses IP réservées
  * Machines virtuelles avec plusieurs cartes réseau

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Créer un coffre Recovery Services pour une machine virtuelle
Un coffre Recovery Services est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre Recovery Services contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

Pour créer un archivage de Recovery Services :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Dans le menu **Hub**, sélectionnez **Parcourir**, puis tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste des ressources est filtrée. Sélectionnez **Coffres Recovery Services**.

    ![Saisie dans la zone et sélection de « Coffres Recovery Services » dans les résultats](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    La liste des archivages de Recovery Services s’affiche.
3. Dans le menu **Coffres Recovery Services**, sélectionnez **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Le volet **Coffres Recovery Services** s’ouvre. Il vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Volet « Coffres Recovery Services »](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Sélectionnez **Abonnement** pour afficher la liste des abonnements disponibles. Si vous ne savez pas quel abonnement utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel ou scolaire est associé à plusieurs abonnements Azure.
6. Sélectionnez **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles, ou **Nouveau** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Sélectionnez **Emplacement** pour sélectionner la région géographique du coffre. Le coffre *doit* se trouver dans la même région que les machines virtuelles que vous souhaitez protéger.

   > [!IMPORTANT]
   > Si vous ne savez pas où se trouve votre machine virtuelle, fermez la boîte de dialogue de création de coffres et accédez à la liste des machines virtuelles dans le portail. Si vous avez des machines virtuelles dans plusieurs régions, vous devez créer un coffre Recovery Services dans chaque région. Créez l’archivage dans le premier emplacement avant de passer à l'emplacement suivant. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde. Le coffre Recovery Services et le service Azure Backup gèrent cela automatiquement.
   >
   >

8. Sélectionnez **Créer**. La création de l’archivage de Recovery Services peut prendre un certain temps. Surveillez les notifications d’état dans l’angle supérieur droit du portail. Une fois votre coffre créé, il apparaît dans la liste des coffres Recovery Services. Si vous ne voyez pas votre coffre, sélectionnez **Actualiser**.

    ![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Maintenant que vous avez créé votre archivage, découvrez comment définir la réplication du stockage.

## <a name="set-storage-replication"></a>Définir la réplication du stockage
L’option de réplication du stockage vous permet de choisir entre stockage géoredondant et stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez le stockage géoredondant comme sauvegarde principale, laissez cette option inchangée. Si vous souhaitez une option plus économique, mais moins durable, choisissez Stockage localement redondant.

Pour modifier le paramètre de réplication du stockage :

1. Dans le volet **Coffres Recovery Services**, sélectionnez votre coffre.
    Quand vous sélectionnez votre coffre, le volet **Paramètres** (qui affiche le nom du coffre en haut) et le volet d’informations du coffre s’ouvrent.

   ![Choisir votre coffre dans la liste des coffres de sauvegarde](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Dans le volet **Paramètres**, utilisez le curseur vertical pour faire défiler l’écran jusqu’à la section **Gérer**, puis sélectionnez **Infrastructure de sauvegarde**. Dans la section **Général**, sélectionnez **Configuration de la sauvegarde**. Dans le volet **Configuration de la sauvegarde**, choisissez l’option de réplication du stockage à appliquer à votre coffre. Par défaut, votre archivage utilise un stockage géo-redondant.

   ![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, continuez d’utiliser le stockage géoredondant. Si vous utilisez Azure comme point de terminaison du stockage de sauvegarde non principal, choisissez le stockage localement redondant. Pour en savoir plus sur les options de stockage, consultez [Réplication Stockage Azure](../storage/common/storage-redundancy.md).

3. Si vous avez modifié le type de réplication du stockage, sélectionnez **Enregistrer**.
    
Après avoir choisi l’option de stockage pour votre coffre, vous pouvez associer la machine virtuelle au coffre. Pour commencer l’association, vous devez découvrir et enregistrer les machines virtuelles Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Sélectionner l’objectif d’une sauvegarde, définir la stratégie et définir les éléments à protéger
Avant d’inscrire une machine virtuelle auprès d’un coffre Recovery Services, lancez le processus de découverte pour identifier les nouvelles machines virtuelles ajoutées à l’abonnement. Le processus de découverte interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement. Si de nouvelles machines virtuelles sont trouvées, le portail affiche le nom du service cloud et la région associée. Dans le portail Azure, le *scénario* correspond à ce que vous placez dans le coffre Recovery Services. La *stratégie* permet de planifier la fréquence et l’heure de la création des points de récupération. Elle inclut également la durée de rétention de ces derniers.

1. Si vous avez un archivage de Recovery Services ouvert, passez à l’étape 2. Si vous n’avez aucun coffre Recovery Services ouvert, ouvrez le [portail Azure](https://portal.azure.com/). Dans le menu **Hub**, sélectionnez **Plus de services**.

   a. Dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

      ![Saisie dans la zone et sélection de « Coffres Recovery Services » dans les résultats](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      La liste des archivages de Recovery Services s’affiche. S’il n’y a aucun coffre dans votre abonnement, cette liste est vide.

      ![Affichage de la liste des coffres Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.

      Le volet **Paramètres** et le tableau de bord du coffre choisi s’ouvrent.

      ![Volet Paramètres et tableau de bord du coffre](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Dans le menu du tableau de bord du coffre, sélectionnez **Sauvegarder**.

   ![Bouton de sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Les volets **Sauvegarde** et **Objectif de sauvegarde** s’ouvrent.

3. Dans le volet **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur la valeur **Azure** et **Que souhaitez-vous sauvegarder ?** sur la valeur **Machine virtuelle**. Sélectionnez ensuite **OK**.

   ![Volets Sauvegarde et Objectif de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Cette étape inscrit l’extension de machine virtuelle auprès du coffre. Le volet **Objectif de sauvegarde** se ferme et le volet **Stratégie de sauvegarde** s’ouvre.

   ![Volets Sauvegarde et Stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Dans le volet **Stratégie de sauvegarde**, sélectionnez la stratégie de sauvegarde à appliquer au coffre.

   ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Sélectionnez **OK** pour associer la stratégie de sauvegarde au coffre.

   Le volet **Stratégie de sauvegarde** se ferme et le volet **Sélectionner les machines virtuelles** s’ouvre.
5. Dans le volet **Sélectionner les machines virtuelles**, choisissez les machines virtuelles à associer à la stratégie spécifiée, puis sélectionnez **OK**.

   ![Volet Sélectionner les machines virtuelles](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   La machine virtuelle sélectionnée est validée. Si vous ne voyez pas les machines virtuelles que vous attendiez, vérifiez qu’elles se trouvent dans la même région Azure que le coffre Recovery Services. Si vous ne voyez toujours pas les machines virtuelles, vérifiez qu’elles ne sont pas déjà protégées par un autre coffre. Le tableau de bord du coffre indique la région dans laquelle le coffre Recovery Services se trouve.

6. Maintenant que vous avez défini tous les paramètres du coffre, sélectionnez **Activer la sauvegarde** dans le volet **Sauvegarde**. Cette étape déploie la stratégie sur le coffre et les machines virtuelles. Elle ne crée pas le point de récupération initial pour la machine virtuelle.

   ![Bouton Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde, votre stratégie de sauvegarde est exécutée selon la planification. Si vous voulez générer une tâche de sauvegarde à la demande pour sauvegarder les machines virtuelles, consultez la rubrique [Déclenchement du travail de sauvegarde](./backup-azure-arm-vms.md#triggering-the-backup-job).

Si vous avez des problèmes lors de l’inscription de la machine virtuelle, consultez les informations suivantes sur l’installation de l’agent de machine virtuelle et la connectivité réseau. Vous n’avez probablement pas besoin des informations suivantes si vous protégez des machines virtuelles créées dans Azure. Toutefois, si vous avez migré vos machines virtuelles dans Azure, vérifiez que vous avez correctement installé l’agent de machine virtuelle et que votre machine virtuelle peut communiquer avec le réseau virtuel.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installer l’agent de machine virtuelle sur la machine virtuelle
Pour que l’extension de sauvegarde fonctionne, [l’agent de machine virtuelle](../virtual-machines/windows/agent-user-guide.md) Azure doit être installé sur la machine virtuelle Azure. Si votre machine virtuelle a été créée à partir de Place de Marché Azure, l’agent y est déjà installé. 

Ces informations sont fournies pour les situations dans lesquelles vous n’utilisez *pas* de machine virtuelle créée à partir de Place de Marché Azure, par exemple, si vous avez migré une machine virtuelle à partir d’un centre de données local. Dans ce cas, l’agent de machine virtuelle doit être installé afin de protéger la machine virtuelle.

Si vous rencontrez des problèmes de sauvegarde de la machine virtuelle Azure, utilisez le tableau ci-dessous pour vérifier que l’agent de machine virtuelle Azure est correctement installé sur celle-ci. Le tableau fournit des informations supplémentaires sur l’agent de machine virtuelle pour les machines virtuelles Windows et Linux.

| **opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installer l’agent de machine virtuelle |Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin de privilèges d’administrateur pour terminer l’installation. |Installez l’[agent Linux](../virtual-machines/linux/agent-user-guide.md) le plus récent. Vous avez besoin de privilèges d’administrateur pour terminer l’installation. Nous vous recommandons d’installer l’agent à partir de votre référentiel de distribution. Nous *déconseillons* d’installer l’agent de machine virtuelle Linux directement à partir de GitHub.  |
| Mettre à jour l’agent de machine virtuelle |La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |Suivez les instructions fournies pour la [mise à jour d’un agent de machine virtuelle Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nous vous recommandons de mettre à jour l’agent à partir de votre référentiel de distribution. Nous *déconseillons* de mettre à jour l’agent de machine virtuelle Linux directement à partir de GitHub.<br><br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Valider l’installation de l’agent de machine virtuelle |1. Accédez au dossier C:\WindowsAzure\Packages sur la machine virtuelle Azure. <br><br>2. Recherchez le fichier WaAppAgent.exe. <br><br>3. Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ **Version du produit** doit indiquer 2.6.1198.718 ou une version ultérieure. |N/A |

### <a name="backup-extension"></a>Extension de sauvegarde
Une fois l’agent de machine virtuelle installé sur la machine virtuelle, le service Azure Backup installe l’extension de sauvegarde sur l’agent de machine virtuelle. Le service Azure Backup met à niveau et corrige de manière fluide l’extension de sauvegarde.

Il installe l’extension de sauvegarde, que la machine virtuelle soit ou non en cours d’exécution. Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de la machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée, autrement dit si la *machine virtuelle est hors connexion*. Dans ce cas, le point de récupération sera *cohérent en cas d’incident*.

## <a name="establish-network-connectivity"></a>Établir la connectivité réseau
Pour gérer les captures instantanées de la machine virtuelle, l’extension de sauvegarde nécessite une connectivité aux adresses IP publiques Azure. Sans une bonne connectivité Internet, les requêtes HTTP de la machine virtuelle expirent et l’opération de sauvegarde échoue. Si votre déploiement comporte des restrictions d’accès, par exemple via un groupe de sécurité réseau (NSG), choisissez l’une de ces options pour fournir un chemin clair pour le trafic de sauvegarde :

* [Mettez sur liste approuvée les plages d’adresses IP des centres de données Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Déployer un serveur de proxy HTTP pour acheminer le trafic.

Lors du choix de l’option à utiliser, le compromis se situe entre la facilité de gestion, le contrôle granulaire et le coût.

| Option | Avantages | Inconvénients |
| --- | --- | --- |
| Plages IP de liste blanche |Aucun coût supplémentaire<br><br>Pour l’ouverture d’accès à un groupe de sécurité réseau, utilisez l’applet de commande **Set-AzureNetworkSecurityRule**. |Difficile à gérer, car les plages d’adresses IP concernées changent au fil du temps.<br><br>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage. |
| Utiliser un proxy HTTP |Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé.<br><br>Un seul point d’accès Internet aux machines virtuelles.<br><br>Non soumis aux modifications d’adresse IP Azure. |Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Mettez sur liste approuvée les plages IP du centre de données Azure
Pour mettre sur liste approuvée les plages d’adresses IP des centres de données Azure, mais aussi obtenir plus d’informations sur les plages d’adresses IP et des instructions, consultez le [site web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653).

Vous pouvez autoriser les connexions au stockage de la région spécifique à l’aide de [balises de service](../virtual-network/security-overview.md#service-tags). Vérifiez que la règle qui autorise l’accès au compte de stockage a une priorité plus élevée que la règle bloquant l’accès à Internet. 

![Groupe de sécurité réseau avec des balises de stockage pour une région](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Les balises de service de stockage sont en préversion et disponibles uniquement dans certaines régions. Pour obtenir une liste de régions, consultez [Balises de service pour le stockage](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Utiliser un proxy HTTP pour les sauvegardes de machine virtuelle
Quand vous sauvegardez une machine virtuelle, l’extension de sauvegarde sur la machine virtuelle envoie les commandes de gestion de capture instantanée au stockage Azure à l’aide d’une API HTTPS. Acheminez le trafic de l’extension de sauvegarde via le proxy HTTP, car il s’agit du seul composant configuré pour l’accès Internet public.

> [!NOTE]
> Il n’existe aucune recommandation pour le logiciel de serveur proxy qui doit être utilisé. Assurez-vous de choisir un proxy compatible avec les étapes de configuration ci-dessous.
>
>

L’exemple d’image ci-dessous montre les trois étapes de configuration nécessaires pour utiliser un proxy HTTP :

* La machine virtuelle d’application achemine tout le trafic HTTP pour l’Internet public via la machine virtuelle proxy.
* La machine virtuelle proxy autorise le trafic entrant à partir de machines virtuelles sur le réseau virtuel.
* Le groupe de sécurité réseau nommé NSF-lockdown a besoin d’une règle de sécurité autorisant le trafic Internet sortant à partir de la machine virtuelle proxy.

Pour utiliser un proxy HTTP pour communiquer avec le réseau Internet public, effectuez les étapes suivantes.

> [!NOTE]
> Ces étapes utilisent des noms et des valeurs spécifiques dans cet exemple. Quand vous entrez (ou collez) des détails dans votre code, utilisez les noms et les valeurs de votre déploiement.

#### <a name="step-1-configure-outgoing-network-connections"></a>Étape 1 : Configurer les connexions réseau sortantes
###### <a name="for-windows-machines"></a>Pour les machines Windows
Cette procédure définit la configuration du serveur proxy pour le compte système local.

1. Téléchargez [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Ouvrez Internet Explorer en exécutant la commande suivante à partir d’une invite de commandes avec élévation de privilèges :

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. Dans Internet Explorer, accédez à **Outils** > **Options Internet** > **Connexions** > **Paramètres réseau**.
4. Vérifiez les paramètres de proxy pour le compte système. Définissez l’adresse IP et le port de proxy.
5. Fermez Internet Explorer.

Le script suivant définit une configuration de proxy au niveau de l’ordinateur et l’utilise pour le trafic HTTP ou HTTPS sortant. Si vous avez configuré un serveur proxy sur un compte d’utilisateur actuel (pas un compte système local), utilisez ce script pour les appliquer à SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Si vous voyez « (407)Authentification proxy requise » dans le journal du serveur proxy, vérifiez que votre authentification est correctement configurée.
>
>

###### <a name="for-linux-machines"></a>Pour les machines Linux
Ajoutez la ligne suivante au fichier ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Ajoutez les lignes suivantes au fichier ```/etc/waagent.conf``` :

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Étape 2 : Autoriser les connexions entrantes sur le serveur proxy
1. Ouvrez le Pare-feu Windows sur le serveur proxy. Pour accéder au pare-feu, le plus simple consiste à rechercher **Pare-feu Windows avec fonctions avancées de sécurité**.
2. Dans la boîte de dialogue **Pare-feu Windows avec fonctions avancées de sécurité**, cliquez avec le bouton droit sur **Règles de trafic entrant** et sélectionnez **Nouvelle règle**.
3. Dans l’Assistant Nouvelle règle de trafic entrant, sélectionnez l’option **personnalisée** dans la page **Type de règle**, puis **Suivant**.
4. Dans la page servant à sélectionner le **programme**, sélectionnez **Tous les programmes**, puis **Suivant**.
5. Dans la page **Protocole et ports**, entrez les informations suivantes, puis sélectionnez **Suivant** :
   * Pour **Type de protocole**, sélectionnez **TCP**.
   * Pour **Port local**, sélectionnez **Ports spécifiques**. Dans la zone suivante, spécifiez le numéro du port proxy qui a été configuré.
   * Pour **Port distant**, sélectionnez **Tous les ports**.

Pour le reste de l’Assistant, acceptez les paramètres par défaut jusqu’à ce que vous arriviez à la fin. Donnez ensuite un nom à cette règle. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Étape 3 : Ajouter une règle d’exception au groupe de sécurité réseau
La commande suivante ajoute une exception pour le groupe de sécurité réseau. Cette exception autorise le trafic TCP à partir d’un port 10.0.0.5 vers n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS). Si vous avez besoin d’un port spécifique de l’Internet public, n’oubliez pas d’ajouter ce port à ```-DestinationPortRange```.

Dans une invite de commandes Azure PowerShell, saisissez la commande suivante :

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>étapes suivantes
À présent que votre environnement est prêt pour sauvegarder votre machine virtuelle, l’étape logique suivante consiste à créer une sauvegarde. L’article sur la planification fournit des informations détaillées sur la sauvegarde des machines virtuelles.

* [Sauvegarde de machines virtuelles](backup-azure-arm-vms.md)
* [Planification de votre infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
* [Gestion des sauvegardes de machines virtuelles](backup-azure-manage-vms.md)
