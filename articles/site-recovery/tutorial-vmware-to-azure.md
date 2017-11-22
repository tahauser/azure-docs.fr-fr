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
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 461feb952f7e2eddba9c7218b3463868e8cb7965
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales

Ce didacticiel vous montre comment configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales exécutant Windows. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un coffre Recovery Services pour Site Recovery
> * Configurer les environnements de réplication source et cible
> * Créer une stratégie de réplication
> * Activer la réplication pour une machine virtuelle

Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer des machines virtuelles VMware locales](tutorial-prepare-on-premises-vmware.md)

Avant de commencer, il est utile [d’examiner l’architecture](concepts-vmware-to-azure-architecture.md) pour le scénario de récupération d’urgence.

## <a name="configure-vmware-account-permissions"></a>Configurer les autorisations du compte VMware

1. Créez un rôle au niveau du vCenter. Nommez le rôle **Azure_Site_Recovery**.
2. Affectez des autorisations suivantes au rôle **Azure_Site_Recovery**.

   **Tâche** | **Rôle/Autorisations** | **Détails**
   --- | --- | ---
   **Détection des machines virtuelles** | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | Au moins un utilisateur en lecture seule.<br/><br/> L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
   **Réplication complète, basculement, restauration automatique** |  Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

3. Créez un utilisateur sur le serveur vCenter ou sur l’hôte vSphere. Affectez le rôle à l’utilisateur.

## <a name="specify-what-you-want-to-replicate"></a>Spécifier ce que vous voulez répliquer

Le service Mobility doit être installé sur chaque machine que vous souhaitez répliquer. Site Recovery installe ce service automatiquement quand vous activez la réplication pour la machine virtuelle. Pour une installation automatique, vous devez préparer un compte qui sera utilisé par Site Recovery pour accéder à la machine virtuelle.

Vous pouvez utiliser un compte local ou de domaine. Pour les machines virtuelles Linux, le compte doit être root sur le serveur Linux source. Pour les machines virtuelles Windows, si vous n’utilisez pas un compte de domaine, désactivez le contrôle d’accès des utilisateurs distants sur la machine locale :

  - Dans le Registre, sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** et définissez sa valeur sur 1.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

La configuration de l’environnement source consiste à télécharger le programme d’installation unifiée de Site Recovery, à configurer le serveur de configuration et à l’inscrire dans le coffre, et à découvrir les machines virtuelles.

Le serveur de configuration est une machine virtuelle VMware locale qui héberge tous les composants de Site Recovery. Cette machine virtuelle exécute le serveur de configuration, le serveur de processus et le serveur cible maître.

- Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- Le serveur de processus fait office de passerelle de réplication. Reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure. En outre, le serveur de traitement installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la découverte automatique des machines virtuelles sur les serveurs VMware locaux.
- Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

La machine virtuelle du serveur de configuration doit être une machine virtuelle VMware à haute disponibilité répondant aux spécifications suivantes :

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

Sur la machine virtuelle du serveur de configuration, vérifiez que l’horloge système est synchronisée avec un serveur de temps.
L’heure doit être synchronisée dans une plage de 15 minutes. Si la différence d’heure est supérieure à 15 minutes, l’installation échoue.

Vérifiez que le serveur de configuration peut accéder à ces URL :

   [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Toutes les règles de pare-feu basées sur une adresse IP doivent autoriser les communications vers Azure.

- Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
    - Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

Toutes les règles de pare-feu basées sur une adresse IP doivent autoriser la communication aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), et aux ports 443 (HTTPS) et 9443 (réplication des données). Veillez à autoriser les plages d’adresses IP pour la région de votre abonnement Azure et pour la région des États-Unis de l’Ouest (utilisée pour la gestion du contrôle d’accès et des identités).

### <a name="download-the-site-recovery-unified-setup"></a>Télécharger le programme d’installation unifiée de Site Recovery

1. Ouvrez le [portail Azure](https://portal.azure.com), puis cliquez sur **Toutes les ressources**.
2. Cliquez sur le coffre Recovery Services nommé **ContosoVMVault**.
3. Cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
4. Sélectionnez **Local** pour l’endroit où se trouvent vos machines, **Vers Azure** pour l’endroit où vous voulez répliquer vos machines, et **Oui, avec l’hyperviseur VMware vSphere**. Cliquez ensuite sur **OK**.
5. Dans le volet Préparer la source, cliquez sur **+ Serveur de configuration**.
6. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
7. Téléchargez le fichier d’installation unifiée de Site Recovery.
8. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lors de l’exécution du programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

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

   ![Composants requis](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. Dans **Configuration MySQL**, créez des informations d’identification pour vous connecter à l’instance de serveur MySQL installée.

8. Dans **Détails de l’environnement**, sélectionnez **Oui** pour protéger les machines virtuelles VMware. Le programme d’installation vérifie que PowerCLI 6.0 est installé.

9. Dans **Emplacement d’installation**, sélectionnez l’emplacement où vous voulez installer les fichiers binaires et stocker le cache. Le lecteur sélectionné doit présenter au moins 5 Go d’espace disque disponible. Toutefois, nous vous recommandons d’utiliser un lecteur de cache présentant au moins 600 Go d’espace disponible.

10. Dans **Sélection du réseau**, spécifiez l’écouteur (carte réseau et port SSL) à partir duquel le serveur de configuration envoie et reçoit les données de réplication. Le port 9443 est le port utilisé par défaut pour envoyer et recevoir le trafic de réplication, mais vous pouvez le modifier en fonction des exigences de votre environnement. Nous ouvrons aussi le port 443, qui est utilisé pour orchestrer les opérations de réplication. N’utilisez pas le port 443 pour envoyer ou recevoir le trafic de réplication.

11. Dans **Résumé**, passez en revue les informations, puis cliquez sur **Installer**. Le programme d’installation installe le serveur de configuration et l’inscrit auprès du service Azure Site Recovery.

    ![Résumé](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    Une fois l’installation terminée, une phrase secrète est générée. Vous en aurez besoin lorsque vous activerez la réplication. Par conséquent, copiez-la et conservez-la en lieu sûr. Le serveur est affiché sur le volet **Paramètres** > **Serveurs** dans le coffre.

### <a name="configure-automatic-discovery"></a>Configurer la découverte automatique

Pour découvrir les machines virtuelles, le serveur de configuration doit se connecter aux serveurs VMware locaux. Pour les besoins de ce didacticiel, ajoutez le serveur vCenter ou les hôtes vSphere en utilisant un compte qui dispose des privilèges d’administrateur sur le serveur.

1. Sur votre serveur de configuration, lancez **CSPSConfigtool.exe**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier *emplacement d’installation*\home\svsystems\bin.

2. Cliquez sur **Gérer les comptes** > **Ajouter un compte**.

   ![Ajouter un compte](./media/tutorial-vmware-to-azure/credentials1.png)

3. Dans **Détails du compte**, ajoutez le compte qui sera utilisé pour la découverte automatique.

   ![Détails](./media/tutorial-vmware-to-azure/credentials2.png)

Pour ajouter un serveur :

1. Ouvrez le [portail Azure](https://portal.azure.com), puis cliquez sur **Toutes les ressources**.
2. Cliquez sur le coffre Recovery Services nommé **ContosoVMVault**.
3. Cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Source**.
4. Sélectionnez **+ vCenter** pour vous connecter un serveur vCenter ou à un hôte ESXi vSphere.
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
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

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
