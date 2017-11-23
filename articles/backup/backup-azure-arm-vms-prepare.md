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
ms.date: 9/3/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: b8a770323d115390d323352826457eee62be5f6f
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Préparation de votre environnement pour la sauvegarde des machines virtuelles Resource Manager
> [!div class="op_single_selector"]
> * [Modèle Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modèle classique](backup-azure-vms-prepare.md)
>
>

Cet article fournit les étapes de préparation de votre environnement pour la sauvegarde d’une machine virtuelle Azure déployée avec le modèle Resource Manager. Les étapes indiquées dans les procédures utilisent le portail Azure.  

Le service Azure Backup comprend deux types de coffres (coffres de sauvegarde et coffres Recovery Services) pour la protection de vos machines virtuelles. Un coffre de sauvegarde protège les machines virtuelles déployées à l'aide du modèle de déploiement classique. Un coffre Recovery Services protège les **machines virtuelles déployées à l’aide du modèle Classic et à l’aide du modèle Resource Manager**. Vous devez utiliser un coffre Recovery Services pour protéger une machine virtuelle déployée à l’aide du modèle Resource Manager.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et Azure Classic](../azure-resource-manager/resource-manager-deployment-model.md). Consultez la page [Préparer votre environnement pour la sauvegarde des machines virtuelles Azure](backup-azure-vms-prepare.md) pour plus d’informations sur l’utilisation des machines virtuelles avec le modèle de déploiement Classic.
>
>

Avant de pouvoir protéger ou sauvegarder une machine virtuelle déployée à l’aide du modèle Resource Manager, vérifiez que ces les conditions préalables suivantes sont remplies :

* Créez un coffre Recovery Services (ou identifiez un coffre Recovery Services existant) *dans le même emplacement que votre machine virtuelle*.
* Sélectionnez un scénario, définissez la stratégie de sauvegarde et définissez les éléments à protéger.
* Vérifiez l’installation de l’Agent de machine virtuelle sur la machine virtuelle.
* Vérifiez la connectivité réseau
* Pour les machines virtuelles Linux, au cas où vous souhaitez personnaliser votre environnement de sauvegarde pour des sauvegardes cohérentes avec les applications, suivez les [étapes permettant de configurer des scripts pré- et post-capture instantanée](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Si vous savez que ces conditions existent déjà dans votre environnement, passez à [l’article traitant de la sauvegarde de vos machines virtuelles](backup-azure-vms.md). Si vous avez besoin de définir ou de vérifier l’une de ces conditions préalables, cet article vous guide à travers les étapes nécessaires pour préparer la condition préalable requise.

##<a name="supported-operating-system-for-backup"></a>Versions du système d’exploitation prises en charge pour une sauvegarde
 * **Linux**: Azure Backup prend en charge [une liste de distributions approuvées par Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , à l’exception de CoreOS Linux. _D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge. Toutefois, nous n’approuvons pas ces distributions pour la sauvegarde._
 * **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limites lors de la sauvegarde et la restauration d’une machine virtuelle
Avant de préparer votre environnement, notez les limitations.

* La sauvegarde de machines virtuelles ayant plus de 16 disques de données n’est pas prise en charge.
* La sauvegarde de machines virtuelles avec des tailles de disque de données supérieures à 1 023 Go n’est pas prise en charge.

> [!NOTE]
> Nous avons une préversion privée pour prendre en charge les sauvegardes des machines virtuelles dotées de disques non managés de >1 To. Pour plus d’informations, consultez [Préversion privée pour la prise en charge de la sauvegarde des machines virtuelles dotées de disques volumineux](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

* La sauvegarde de machines virtuelles avec une adresse IP réservée et sans point de terminaison n’est pas prise en charge.
* La sauvegarde des machines virtuelles chiffrées BEK seulement n’est pas prise en charge. La sauvegarde des machines virtuelles Linux chiffrées LUKS n’est pas prise en charge.
* La sauvegarde des machines virtuelles contenant une configuration de volumes partagés de cluster (CSV) ou de serveur de fichiers avec montée en puissance parallèle n’est pas recommandée car elle exige d’impliquer toutes les machines virtuelles incluses dans la configuration du cluster pendant la tâche de capture instantanée. Sauvegarde Azure ne prend pas en charge la cohérence multimachine virtuelle. 
* Les données de sauvegarde n’incluent pas les lecteurs réseau montés attachés à la machine virtuelle.
* Le remplacement d’une machine virtuelle existante pendant la restauration n’est pas pris en charge. Si vous tentez de restaurer la machine virtuelle alors que celle-ci existe, l’opération de restauration échoue.
* La sauvegarde et la restauration entre différentes régions ne sont pas prises en charge.
* Vous pouvez sauvegarder des machines virtuelles dans toutes les régions publiques d’Azure (voir la [liste](https://azure.microsoft.com/regions/#services) des régions prises en charge). Si la région que vous recherchez n’est pas prise en charge aujourd’hui, elle n’apparaît pas dans la liste déroulante lors de la création de l’archivage.
* La restauration d’une machine virtuelle de contrôleur de domaine qui fait partie d’une configuration à plusieurs contrôleurs de domaine est prise en charge uniquement par le biais de PowerShell. En savoir plus sur la [restauration d’un contrôleur de domaine dans un environnement à plusieurs contrôleurs de domaine](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* La restauration de machines virtuelles qui ont des configurations réseau spéciales suivantes est prise en charge uniquement par le biais de PowerShell. Les machines virtuelles créées à l'aide du flux de travail de restauration dans l'interface utilisateur n'aura pas ces configurations réseau une fois l'opération de restauration terminée. Pour plus d’informations, consultez [Restauration de machines virtuelles avec des configurations de réseau spéciales](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Machines virtuelles avec configuration d’un équilibreur de charge (internes et externes)
  * Machines virtuelles avec plusieurs adresses IP réservées
  * Machines virtuelles avec plusieurs cartes réseau

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Création d’un coffre Recovery Services pour une machine virtuelle
Un coffre Recovery Services est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre Recovery Services contient également les stratégies de sauvegarde associées aux machines virtuelles protégées.

Pour créer un coffre Recovery Services :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Parcourir** et, dans la liste des ressources, tapez **Recovery Services**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Coffre Recovery Services**.

    ![Cliquez sur le bouton Parcourir, puis saisissez Recovery Services. Lorsque l’option du coffre Recovery Services apparaît, cliquez dessus afin d’ouvrir le panneau correspondant.](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    La liste des coffres Recovery Services est affichée.
3. Dans le menu **Coffres Recovery Services**, cliquez sur **Ajouter**.

    ![Créer un coffre Recovery Services - Étape 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Le panneau du coffre Recovery Services s’affiche et vous invite à renseigner les champs **Nom**, **Abonnement**, **Groupe de ressources** et **Emplacement**.

    ![Créer un archivage de Recovery Services - Étape 5](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
5. Cliquez sur **Abonnement** pour afficher la liste des abonnements disponibles. Si vous n’êtes pas sûr de l’abonnement à utiliser, utilisez l’abonnement par défaut (ou suggéré). Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.
6. Cliquez sur **Groupe de ressources** pour afficher la liste des groupes de ressources disponibles ou sur **Nouveau** pour en créer un. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Cliquez sur **Emplacement** pour sélectionner la région géographique du coffre. Le coffre **doit** se trouver dans la même région que les machines virtuelles que vous souhaitez protéger.

   > [!IMPORTANT]
   > Si vous ne savez pas où se trouve votre machine virtuelle, fermez la boîte de dialogue de création d’archivage et accédez à la liste des machines virtuelles dans le portail. Si vous avez des machines virtuelles dans plusieurs régions, vous devez créer un archivage de Recovery Services dans chaque région. Créez l’archivage dans le premier emplacement avant de passer à l'emplacement suivant. Il est inutile de spécifier des comptes de stockage dans lesquels héberger les données de sauvegarde : l’archivage de Recovery Services et le service Azure Backup gèrent cela automatiquement.
   >
   >

8. Cliquez sur **Create**. La création de l’archivage de Recovery Services peut prendre un certain temps. Surveillez les notifications d'état dans l'angle supérieur droit du portail. Une fois votre archivage créé, il apparaît dans la liste des archivages de Recovery Services. Si vous ne voyez pas votre coffre, cliquez sur **Actualiser** pour

    ![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Maintenant que vous avez créé votre archivage, découvrez comment définir la réplication du stockage.

## <a name="set-storage-replication"></a>Définir la réplication du stockage
L’option de réplication du stockage vous permet de choisir entre stockage géo-redondant et stockage localement redondant. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous utilisez Azure comme sauvegarde principale, laissez cette option inchangée. Choisissez Stockage localement redondant si vous souhaitez une option plus économique, mais moins durable.

Pour modifier le paramètre de réplication du stockage :

1. Dans le panneau **Coffres Recovery Services**, choisissez votre coffre.
    Lorsque vous cliquez sur votre coffre, le panneau Paramètres (*qui porte le nom du coffre en haut*) et le panneau des détails du coffre s’ouvrent.

    ![Choisissez votre coffre dans la liste des coffres de sauvegarde](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Dans le panneau **Paramètres**, utilisez le curseur vertical pour faire défiler vers le bas jusqu'à la section **Gérer**. Cliquez sur **Infrastructure de sauvegarde** pour ouvrir son panneau. Dans la section **Général**, cliquez sur **Configuration de la sauvegarde** pour ouvrir son panneau. Dans le panneau **Configuration de la sauvegarde** , choisissez l’option de réplication du stockage à appliquer à votre coffre. Par défaut, votre archivage utilise un stockage géo-redondant. Si vous modifiez le type de réplication de stockage, cliquez sur **Enregistrer**.

    ![Liste des archivages de sauvegarde](./media/backup-azure-arm-vms-prepare/full-blade.png)

     Si vous utilisez Azure comme principal point de terminaison du stockage de sauvegarde, laissez cette option inchangée. Si vous utilisez Azure comme point de terminaison du stockage de sauvegarde non principal, utilisez plutôt le stockage localement redondant. Pour en savoir plus sur les options de stockage [géo-redondant](../storage/common/storage-redundancy.md#geo-redundant-storage) et [localement redondant](../storage/common/storage-redundancy.md#locally-redundant-storage), consultez l’article [Réplication Stockage Azure](../storage/common/storage-redundancy.md).
    Après avoir sélectionné l’option de stockage pour votre archivage, vous pouvez associer la machine virtuelle à l’archivage. Pour commencer l’association, vous devez découvrir et enregistrer les machines virtuelles Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Sélectionner l’objectif d’une sauvegarde, définir la stratégie et définir les éléments à protéger
Avant d’enregistrer une machine virtuelle dans un coffre, lancez le processus de découverte pour vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont bien identifiées. Le processus interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement ainsi que des informations supplémentaires, comme le nom du service cloud et la région. Dans le portail Azure, l’objectif fait référence à ce que vous allez placer dans l’archivage de Recovery Services. La stratégie permet de planifier la fréquence et l’heure de la création des points de récupération. Elle inclut également la durée de rétention de ces derniers.

1. Si vous avez un archivage de Recovery Services ouvert, passez à l’étape 2. Si vous n’avez aucun coffre Recovery Services ouvert, ouvrez le [portail Azure](https://portal.azure.com/) et cliquez sur **Plus de services** dans le menu Hub.

   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure des caractères saisis, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.

     ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

     La liste des archivages de Recovery Services s’affiche. S’il n’y a aucun coffre dans votre abonnement, cette liste est vide.

    ![Affichage de la liste des coffres Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   * Dans la liste des coffres Recovery Services, sélectionnez un coffre pour ouvrir son tableau de bord.

     Le panneau Paramètres et son tableau de bord du coffre choisi s’ouvre.

     ![Ouvrir le panneau de l’archivage](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. Dans le menu du tableau de bord du coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarde.

    ![Ouvrir le panneau Sauvegarde](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Les panneaux Sauvegarde et Objectif de sauvegarde s’ouvrent.

    ![Ouvrir le panneau Scénario](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Dans le panneau Objectif de sauvegarde, définissez **Où s’exécute votre charge de travail ?** sur Azure et **Que souhaitez-vous sauvegarder ?** sur Machine virtuelle, puis cliquez sur **OK**.

    L’extension de machine virtuelle est inscrite sur le coffre. Le panneau Objectif de sauvegarde se ferme et le panneau **Stratégie de sauvegarde** s’ouvre.

    ![Ouvrir le panneau Scénario](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Dans le panneau Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre.

    ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Pour savoir comment définir une stratégie de sauvegarde, consultez la section [Définition d’une stratégie de sauvegarde](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Cliquez sur **OK** pour associer la stratégie de sauvegarde au coffre.

    Le panneau Stratégie de sauvegarde se ferme et le panneau **Sélectionner les machines virtuelles** s’ouvre.
5. Dans le panneau **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles à associer à la stratégie spécifiée, puis cliquez sur **OK**.

    ![Sélectionner la charge de travail](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    La machine virtuelle sélectionnée est validée. Si vous ne voyez pas les machines virtuelles que vous espériez voir, vérifiez qu’elles existent dans le même emplacement Azure que le coffre Recovery Services et qu’elles ne sont pas protégées dans un autre coffre. L’emplacement du coffre Recovery Services est indiqué dans le tableau de bord associé.

6. Maintenant que vous avez défini tous les paramètres du coffre, cliquez sur **Activer la sauvegarde** dans le panneau Sauvegarde. La stratégie est déployée dans l’archivage et les machines virtuelles. Cela ne crée pas le point de récupération initial pour la machine virtuelle.

    ![Activer la sauvegarde](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Après avoir activé la sauvegarde, votre stratégie de sauvegarde sera exécutée selon la planification. Si vous souhaitez générer une tâche de sauvegarde à la demande pour sauvegarder les machines virtuelles, consultez la rubrique [Déclenchement du travail de sauvegarde](./backup-azure-arm-vms.md#triggering-the-backup-job).

Si vous avez des problèmes lors de l’inscription de la machine virtuelle, consultez les informations suivantes sur l’installation de l’Agent de machine virtuelle et la connectivité réseau. Vous n’avez probablement pas besoin des informations suivantes si vous protégez des machines virtuelles créées dans Azure. Toutefois, si vous avez migré vos machines virtuelles dans Azure, assurez-vous d’avoir correctement installé l’agent de machine virtuelle et que votre machine virtuelle peut communiquer avec le réseau virtuel.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Installer l’agent de machine virtuelle sur la machine virtuelle
L’agent de machine virtuelle Azure doit être installé sur la machine virtuelle Azure pour permettre la prise en charge de l’extension Backup. Si votre machine virtuelle a été créée à partir de la galerie Azure, l’agent y est déjà installé. Ces informations sont fournies pour les situations dans lesquelles vous n’utilisez *pas* de machine virtuelle créée à partir de la galerie Azure : par exemple, lorsque vous avez migré une machine virtuelle à partir d’un centre de données local. Dans ce cas, l’Agent de machine virtuelle doit être installé afin de protéger la machine virtuelle. Apprenez-en davantage sur l’[agent de machine virtuelle](../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux).

Si vous rencontrez des problèmes de sauvegarde de la machine virtuelle Azure, vérifiez que son agent est correctement installé sur celle-ci (reportez-vous au tableau ci-dessous). Le tableau suivant fournit des informations supplémentaires sur l’agent de machine virtuelle pour les machines virtuelles Windows et Linux.

| **Opération** | **Windows** | **Linux** |
| --- | --- | --- |
| Installation de l’agent de machine virtuelle |Téléchargez et installez le fichier [MSI de l’agent](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. |<li> Installez l’[agent Linux](../virtual-machines/linux/agent-user-guide.md) le plus récent. Vous aurez besoin de privilèges d’administrateur pour terminer l’installation. Nous vous recommandons d’installer l’agent à partir de votre référentiel de distribution. Nous **déconseillons** d’installer l’agent de machine virtuelle Linux directement à partir de github.  |
| Mise à jour de l’agent de machine virtuelle |La mise à jour de l’agent de machine virtuelle est aussi simple que la réinstallation des [fichiers binaires de l’agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |Suivez les instructions fournies dans l’article [Mise à jour d’un agent de machine virtuelle Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Nous vous recommandons de mettre à jour l’agent à partir de votre référentiel de distribution. Nous **déconseillons** de mettre à jour l’agent de machine virtuelle Linux à partir de github.<br>Vérifiez qu’aucune opération de sauvegarde n’est en cours pendant la mise à jour de l’agent de machine virtuelle. |
| Validation de l’installation de l’agent de machine virtuelle |<li>Accédez au dossier *C:\WindowsAzure\Packages* sur la machine virtuelle Azure. <li>Le fichier WaAppAgent.exe doit être présent.<li> Cliquez avec le bouton droit sur le fichier, accédez à **Propriétés**, puis sélectionnez l’onglet **Détails**. Le champ Version du produit doit être défini sur 2.6.1198.718 ou une version ultérieure. |N/A |

### <a name="backup-extension"></a>Extension de sauvegarde
Une fois l’agent de machine virtuelle installé sur la machine virtuelle, le service Azure Backup installe l’extension de sauvegarde vers l’agent de machine virtuelle. Le service Azure Backup met à niveau et corrige en toute transparence l’extension de sauvegarde.

Le service Backup installe l’extension de sauvegarde que la machine virtuelle soit ou non en cours d’exécution. Une machine virtuelle en cours d’exécution offre le plus de chance d’obtenir un point de récupération d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de la machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée, autrement dit si la machine virtuelle est hors connexion. Dans ce cas, le point de récupération sera *cohérent en cas d’incident*.

## <a name="network-connectivity"></a>Connectivité réseau
Afin de gérer les instantanés de la machine virtuelle, l’extension de sauvegarde nécessite une connectivité vers les adresses IP publiques Azure. Sans une bonne connectivité Internet, les requêtes HTTP de la machine virtuelle expirent et l’opération de sauvegarde échoue. Si votre déploiement comporte des restrictions d’accès (via un groupe de sécurité réseau (NSG), par exemple), choisissez l’une de ces options pour fournir un chemin clair pour le trafic de sauvegarde :

* [Mettez sur liste approuvée les plages d’adresses IP des centres de données Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) : consultez l’article pour obtenir des instructions sur la mise sur liste blanche des adresses IP.
* Déployer un serveur de proxy HTTP pour acheminer le trafic.

Lors du choix de l’option à utiliser, le compromis se situe entre la facilité de gestion, le contrôle granulaire et le coût.

| Option | Avantages | Inconvénients |
| --- | --- | --- |
| Plages IP de liste blanche |Aucun coût supplémentaire<br><br>Pour l’ouverture d’accès à un groupe de sécurité réseau, utilisez l’applet de commande <i>Set-AzureNetworkSecurityRule</i>. |Difficile à gérer, car les plages IP concernées changent au fil du temps.<br><br>Fournit un accès à l’ensemble d’Azure et pas seulement au stockage. |
| Serveur proxy HTTP |Contrôle granulaire dans le proxy sur les URL de stockage autorisées.<br>Un seul point d’accès Internet aux machines virtuelles.<br>Non soumis aux modifications d’adresse IP Azure. |Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Mettez sur liste approuvée les plages IP du centre de données Azure
* Pour mettre sur liste approuvée les plages d’adresses IP des centres de données Azure, mais aussi obtenir plus d’informations sur les plages d’adresses IP et des instructions, voir le [site web Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) .
* Vous pouvez utiliser des balises de service pour autoriser les connexions au stockage de la région spécifique à l’aide de [Balises de service](../virtual-network/security-overview.md#service-tags). Vérifiez que la règle qui autorise l’accès au compte de stockage a une priorité plus élevée que la règle bloquant l’accès à internet. 

  ![Groupe de sécurité réseau avec des balises de stockage pour une région](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Les balises de stockage sont en préversion et disponibles uniquement dans des régions spécifiques. Pour obtenir la liste des régions, consultez [Balises de service pour le stockage](../virtual-network/security-overview.md#service-tags).

### <a name="using-an-http-proxy-for-vm-backups"></a>Utilisation d’un proxy HTTP pour les sauvegardes de machine virtuelle
Lorsque vous sauvegardez une machine virtuelle, l’extension de sauvegarde sur la machine virtuelle envoie les commandes de gestion de capture instantanée vers le stockage Azure à l’aide d’une API HTTPS. Acheminez le trafic de l’extension de sauvegarde via le proxy HTTP, car c’est le seul composant configuré pour l’accès à l’Internet public.

> [!NOTE]
> Il n’existe aucune recommandation pour le logiciel de serveur proxy qui doit être utilisé. Assurez-vous de choisir un proxy compatible avec les étapes de configuration ci-dessous.
>
>

L’image de l’exemple ci-dessous montre les trois étapes de configuration nécessaire pour utiliser un proxy HTTP :

* La machine virtuelle d’application achemine tout le trafic HTTP pour l’Internet public via la machine virtuelle proxy.
* La machine virtuelle proxy autorise le trafic entrant à partir de machines virtuelles sur le réseau virtuel.
* Le groupe de sécurité réseau (NSG) nommé NSF-lockdown a besoin d’une règle de sécurité autorisant le trafic Internet sortant à partir de la machine virtuelle proxy.

![Groupe de sécurité réseau avec diagramme de déploiement du proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Pour utiliser un proxy HTTP pour communiquer avec le réseau Internet public, procédez comme suit :

#### <a name="step-1-configure-outgoing-network-connections"></a>Étape 1. Configurer les connexions réseau sortantes
###### <a name="for-windows-machines"></a>Pour les machines Windows
Cela définit la configuration du serveur proxy pour le compte système local.

1. Téléchargez [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Exécutez la commande suivante à partir de l’invite de commandes avec élévation de privilèges.

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Une fenêtre Internet Explorer s’ouvre.
3. Accédez à Outils -> Options Internet -> Connexions -> Paramètres réseau.
4. Vérifiez les paramètres de proxy pour le compte système. Définissez l’adresse IP et le port de proxy.
5. Fermez Internet Explorer.

Cela permet d’installer une configuration de proxy au niveau de l’ordinateur et de l’utiliser pour le trafic HTTP/HTTPS sortant.

Si vous avez configuré un serveur proxy sur un compte d’utilisateur actuel (pas un compte système local), utilisez le script suivant pour les appliquer à SYSTEMACCOUNT :

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

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Étape 2. Autoriser les connexions entrantes sur le serveur proxy :
1. Ouvrez le Pare-feu Windows sur le serveur proxy. Pour accéder au pare-feu, le plus simple consiste à rechercher le Pare-feu Windows avec fonctions de sécurité avancées.

    ![Ouvrir le pare-feu](./media/backup-azure-vms-prepare/firewall-01.png)
2. Dans la boîte de dialogue Pare-feu Windows, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis cliquez sur **Nouvelle règle...**.

    ![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-02.png)
3. Dans l’**Assistant Nouvelle règle de trafic entrant**, choisissez l’option **personnalisée** comme **Type de règle**, puis cliquez sur **Suivant**.
4. Dans la page servant à sélectionner le **programme**, choisissez **Tous les programmes**, puis cliquez sur **Suivant**.
5. Dans la page **Protocole et ports**, entrez les informations suivantes, puis cliquez sur **Suivant** :

    ![Créer une nouvelle règle](./media/backup-azure-vms-prepare/firewall-03.png)

   * Pour *Type de protocole*, choisissez *TCP*.
   * Pour *Port local*, choisissez *Ports spécifiques* et dans le champ situé en dessous, spécifiez le ```<Proxy Port>``` qui a été configuré.
   * Pour *Port distant*, sélectionnez *Tous les ports*.

     Pour le reste de l’Assistant, cliquez jusqu’à la fin et donnez un nom à cette règle.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Étape 3. Ajouter une règle d’exception au groupe de sécurité réseau :
Dans une invite de commandes Azure PowerShell, saisissez la commande suivante :

La commande suivante ajoute une exception pour le groupe de sécurité réseau. Cette exception autorise le trafic TCP à partir d’un port 10.0.0.5 de n’importe quelle adresse Internet sur le port 80 (HTTP) ou 443 (HTTPS). Si vous avez besoin d’un port spécifique de l’Internet public, n’oubliez pas d’ajouter également ce port à ```-DestinationPortRange```.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*Ces étapes utilisent des noms et des valeurs spécifiques dans cet exemple. Utilisez les noms et les valeurs de votre déploiement lors de la saisie, lorsque vous coupez/collez les détails dans votre code.*

Maintenant que vous savez que vous disposez d’une connectivité réseau, vous êtes prêt à sauvegarder votre machine virtuelle. Voir [Sauvegarde des machines virtuelles déployées sur Azure Resource Manager (ARM)](backup-azure-arm-vms.md).

## <a name="questions"></a>Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Étapes suivantes
À présent que votre environnement est prêt pour sauvegarder votre machine virtuelle, l’étape logique suivante consiste à créer une sauvegarde. L’article sur la planification fournit des informations détaillées sur la sauvegarde des machines virtuelles.

* [Sauvegarde de machines virtuelles](backup-azure-vms.md)
* [Planification de votre infrastructure de sauvegarde de machines virtuelles](backup-azure-vms-introduction.md)
* [Gestion des sauvegardes de machines virtuelles](backup-azure-manage-vms.md)
