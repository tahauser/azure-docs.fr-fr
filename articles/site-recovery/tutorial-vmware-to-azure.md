---
title: "Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence vers Azure de machines virtuelles VMware locales avec le service Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5810ff908d48fc4ff742d734e7c2457fdfe8cb03
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales

Ce didacticiel vous montre comment configurer la récupération d’urgence sur Azure pour des machines virtuelles VMware locales exécutant Windows. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Spécifier la source et la cible de réplication.
> * Configurer l’environnement de réplication source, y compris les composants Site Recovery locaux, et l’environnement de réplication cible.
> * Créer une stratégie de réplication
> * Activer la réplication pour une machine virtuelle

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer des machines virtuelles VMware locales](tutorial-prepare-on-premises-vmware.md)

Avant de commencer, il est utile [d’examiner l’architecture](concepts-vmware-to-azure-architecture.md) pour le scénario de récupération d’urgence.


## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

1. Dans **Coffres Recovery Services**, cliquez sur le nom du coffre, **ContosoVMVault**.
2. Dans **Prise en main**, cliquez sur Site Recovery. Cliquez ensuite sur **Préparer l’infrastructure**.
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Sur quelle cible souhaitez-vous répliquer vos machines, sélectionnez **Sur Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Cliquez ensuite sur **OK**.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Pour configurer l’environnement source, vous téléchargez le fichier du programme d’installation unifiée de Site Recovery. Vous exécutez le programme d’installation pour installer les composants Site Recovery locaux, inscrivez les serveurs VMware dans le coffre et détectez les machines virtuelles locales.

### <a name="verify-on-premises-site-recovery-requirements"></a>Vérifier les conditions exigées pour Site Recovery au niveau local

Vous avez besoin d’une seule machine virtuelle VMware locale à haut niveau de disponibilité pour héberger les composants Site Recovery locaux. Ces composants englobent le serveur de configuration, le serveur de processus et le serveur cible maître.

- Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- Le serveur de processus fait office de passerelle de réplication. Reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure. De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.
- Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

La machine virtuelle doit répondre aux exigences répertoriées ici.

| **Prérequis** | **Détails** |
|-----------------|-------------|
| Nombre de cœurs de processeur| 8 |
| RAM | 12 Go |
| Nombre de disques | 3 - disque du système d’exploitation, disque de cache du serveur de processus, lecteur de conservation (pour la restauration automatique) |
| Espace disque disponible (cache du serveur de traitement) | 600 Go |
| Espace disque disponible (disque de rétention) | 600 Go |
| Version de système d’exploitation | Windows Server 2012 R2 |
| Paramètres régionaux du système d’exploitation | Anglais (en-us) |
| Version VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Rôles Windows Server | N’activez pas ces rôles : Active Directory Domain Services, Internet Information Services, Hyper-V |
| Type de carte réseau | VMXNET3 |
| Type d’adresse IP | statique |
| Ports | 443 (Orchestration du canal de contrôle)<br/>9443 (Transport de données)|

Par ailleurs : 
- Vérifiez que l’horloge système de la machine virtuelle est synchronisée avec un serveur de temps. L’heure doit être synchronisée dans une plage de 15 minutes. Si elle est supérieure, l’installation échoue.
l’installation échoue.
- Vérifiez que la machine virtuelle du serveur de configuration peut accéder à ces URL :

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Assurez-vous que les règles de pare-feu basées sur une adresse IP autorisent les communications vers Azure.
    - Autorisez les [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), le port 443 (HTTPS) et le port 9443 (réplication de données).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).


### <a name="download-the-site-recovery-unified-setup-file"></a>Télécharger le fichier du programme d’installation unifiée de Site Recovery

1. Dans le coffre > **Préparer l’infrastructure**, cliquez sur **Source**.
1. Dans **Préparer la source**, cliquez sur **+ Serveur de configuration**.
2. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
3. Téléchargez le fichier d’installation unifiée de Site Recovery.
4. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lors de l’exécution du programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

   ![Configurer la source](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>Configurer le serveur de configuration

1. Exécutez le fichier d’installation unifiée.
2. Dans **Avant de commencer**, sélectionnez **Installer le serveur de configuration et le serveur de processus**, puis cliquez sur **Suivant**.

3. Dans **Licence de logiciel tiers**, cliquez sur **J’accepte** pour télécharger et installer MySQL, puis cliquez sur **Suivant**.

4. Dans **Inscription**, sélectionnez la clé d’inscription que vous avez téléchargée à partir du coffre.

5. Dans **Paramètres Internet**, indiquez de quelle manière le fournisseur qui s’exécute sur le serveur de configuration doit se connecter à Azure Site Recovery par le biais d’Internet.

   - Si vous voulez vous connecter avec le proxy actuellement configuré sur la machine, sélectionnez **Se connecter à Azure Site Recovery avec un serveur proxy**.
   - Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**.
   - Si le proxy existant nécessite une authentification, ou si vous voulez utiliser un proxy personnalisé pour la connexion au fournisseur, sélectionnez **Se connecter avec des paramètres de proxy personnalisés**, et spécifiez l’adresse, le port et les informations d’authentification.

   ![Pare-feu](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. Dans **Vérification de la configuration requise**, le programme d’installation procède à une vérification afin de garantir le bon déroulement de l’installation. Si un avertissement s’affiche à propos de la **vérification de la synchronisation globale**, vérifiez que l’heure de l’horloge système (paramètres **Date et heure**) est identique à celle du fuseau horaire.

   ![Prérequis](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. Dans **Configuration MySQL**, créez des informations d’identification pour vous connecter à l’instance de serveur MySQL installée.

8. Dans **Détails de l’environnement**, sélectionnez **Oui** pour protéger les machines virtuelles VMware. Le programme d’installation vérifie que PowerCLI 6.0 est installé.

9. Dans **Emplacement d’installation**, sélectionnez l’emplacement où vous voulez installer les fichiers binaires et stocker le cache. Le lecteur sélectionné doit présenter au moins 5 Go d’espace disque disponible. Toutefois, nous vous recommandons d’utiliser un lecteur de cache présentant au moins 600 Go d’espace disponible.

10. Dans **Sélection du réseau**, spécifiez l’écouteur (carte réseau et port SSL) à partir duquel le serveur de configuration envoie et reçoit les données de réplication. Le port 9443 est le port utilisé par défaut pour envoyer et recevoir le trafic de réplication, mais vous pouvez le modifier en fonction des exigences de votre environnement. Nous ouvrons aussi le port 443, qui est utilisé pour orchestrer les opérations de réplication. N’utilisez pas le port 443 pour envoyer ou recevoir le trafic de réplication.

11. Dans **Résumé**, passez en revue les informations, puis cliquez sur **Installer**. Le programme d’installation installe le serveur de configuration et l’inscrit auprès du service Azure Site Recovery.

    ![Résumé](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Une fois l’installation terminée, une phrase secrète est générée. Vous en aurez besoin lorsque vous activerez la réplication. Par conséquent, copiez-la et conservez-la en lieu sûr. Le serveur est affiché sur le volet **Paramètres** > **Serveurs** dans le coffre.

### <a name="configure-automatic-discovery"></a>Configurer la découverte automatique

Pour découvrir les machines virtuelles, le serveur de configuration doit se connecter aux serveurs VMware locaux. Pour les besoins de ce didacticiel, ajoutez le serveur vCenter ou les hôtes vSphere en utilisant un compte qui dispose des privilèges d’administrateur sur le serveur. Vous avez créé ce compte dans le [précédent didacticiel](tutorial-prepare-on-premises-vmware.md). 

Pour ajouter le compte :

1. Sur la machine virtuelle du serveur de configuration, lancez **CSPSConfigtool.exe**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier *emplacement d’installation*\home\svsystems\bin.

2. Cliquez sur **Gérer les comptes** > **Ajouter un compte**.

   ![Ajouter un compte](./media/tutorial-vmware-to-azure/credentials1.png)

3. Dans **Détails du compte**, ajoutez le compte qui sera utilisé pour la découverte automatique.

   ![Détails](./media/tutorial-vmware-to-azure/credentials2.png)

Pour ajouter le serveur VMware :

1. Ouvrez le [portail Azure](https://portal.azure.com), puis cliquez sur **Toutes les ressources**.
2. Cliquez sur le coffre Recovery Services nommé **ContosoVMVault**.
3. Cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Source**.
4. Sélectionnez **+vCenter** pour vous connecter à un serveur vCenter ou à un hôte ESXi vSphere.
5. Dans **Ajouter un serveur vCenter**, spécifiez un nom convivial pour le serveur. Ensuite, spécifiez l’adresse IP ou le nom de domaine complet.
6. Laissez le port défini sur 443, sauf si vos serveurs VMware écoutent les demandes sur un autre port.
7. Sélectionnez le compte à utiliser pour la connexion au serveur. Cliquez sur **OK**.

Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés, et découvre les machines virtuelles.

> [!NOTE]
> L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, cliquez sur **Serveurs de configuration** > ***nom du serveur*** > **Actualiser le serveur**.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez si votre modèle de déploiement cible est basé sur Resource Manager ou est de type classique.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes de stockage Azure compatibles.

   ![Cible](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

1. Ouvrez le [portail Azure](https://portal.azure.com), puis cliquez sur **Toutes les ressources**.
2. Cliquez sur le coffre Recovery Services nommé **ContosoVMVault**.
3. Pour créer une stratégie de réplication, cliquez sur **Infrastructure Site Recovery** > **Stratégies de réplication** > **+Stratégie de réplication**.
4. Dans **Créer une stratégie de réplication**, spécifiez le nom de stratégie **VMwareRepPolicy**.
5. Dans **Seuil RPO**, utilisez la valeur par défaut de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
6. Dans **Rétention des points de récupération**, utilisez la valeur par défaut de 24 heures pour la durée de la fenêtre de conservation pour chaque point de récupération. Pour ce didacticiel, nous sélectionnons 72 heures. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre.
7. Dans **Fréquence des captures instantanées de cohérence d’application**, utilisez la valeur par défaut de 60 minutes pour la fréquence de création des captures instantanées de cohérence d’application. Cliquez sur **OK** pour créer la stratégie.

   ![Stratégie](./media/tutorial-vmware-to-azure/replication-policy.png)

La stratégie est automatiquement associée au serveur de configuration. Par défaut, une stratégie de correspondance est automatiquement créée pour la restauration automatique. Par exemple, si la stratégie de réplication est **rep-policy**, la stratégie de restauration automatique correspond alors à **rep-policy-failback**. Cette stratégie n’est utilisée qu’à partir du moment où vous initiez une restauration automatique à partir d’Azure.

## <a name="enable-replication"></a>Activer la réplication

Site Recovery installe automatiquement le service Mobilité quand la réplication est activée pour une machine virtuelle. Il peut être nécessaire d’attendre 15 minutes ou plus avant que les modifications prennent effet et qu’elles apparaissent dans le portail.

Activez la réplication comme suit :

1. Cliquez sur **Répliquer l’application** > **Source**.
2. Dans **Source**, sélectionnez le serveur de configuration.
3. Dans **Type de machine**, sélectionnez **Machines virtuelles**.
4. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte.
5. Sélectionnez le serveur de processus (serveur de configuration). Cliquez ensuite sur **OK**.
6. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles basculées.
7. Sélectionnez le compte de stockage Azure que vous souhaitez utiliser pour les données de réplication.
8. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.
9. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine.
10. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.
11. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte à utiliser par le serveur de processus pour installer automatiquement le service Mobilité sur la machine.
12. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée.
13. Cliquez sur **Activer la réplication**.

Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

Pour surveiller les machines virtuelles que vous ajoutez, vous pouvez consulter l’heure de la dernière découverte pour les machines virtuelles dans **Serveurs de configuration**
> **Dernier contact à**. Pour ajouter des machines virtuelles sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans cliquer dessus) et cliquez sur **Actualiser**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter une simulation de récupération d'urgence](site-recovery-test-failover-to-azure.md)
