---
title: "Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment configurer la récupération d’urgence vers Azure de machines virtuelles VMware locales avec le service Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3d9248d2501c7fea0492bad2687b6bdfb0b903e8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales

Ce didacticiel vous montre comment configurer la récupération d’urgence sur Azure pour des machines virtuelles VMware locales exécutant Windows. Ce tutoriel vous montre comment effectuer les opérations suivantes :

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
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Cliquez ensuite sur **OK**.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Pour configurer un environnement source, vous avez besoin d’une seule machine locale à haut niveau de disponibilité pour héberger les composants Site Recovery locaux. Ces composants englobent le serveur de configuration, le serveur de processus et le serveur cible maître.

- Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- Le serveur de processus fait office de passerelle de réplication. Reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure. De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.
- Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

Pour configurer le serveur de configuration comme une machine virtuelle VMware hautement disponible, vous téléchargez un modèle OVF préparé et vous l’importez dans VMware pour créer la machine virtuelle. Après avoir configuré le serveur de configuration, vous l’inscrivez dans le coffre. Après l’inscription, Site Recovery détecte les machines virtuelles VMware locales.

### <a name="download-the-vm-template"></a>Télécharger le modèle de machine virtuelle

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, cliquez sur **+ Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
4. Téléchargez le modèle de format OVF (Open Virtualization) pour le serveur de configuration.

  > [!TIP]
  La dernière version du modèle de serveur de configuration peut être téléchargée directement à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).

## <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware

1. Ouvrez une session sur le serveur vCenter ou l’hôte vSphere ESXi de VMware, à l’aide du client vSphere de VMware.
2. Dans le menu **Fichiers**, sélectionnez **Déployer le modèle OVF** pour lancer l’assistant de déploiement du modèle OVF.  

     ![Modèle OVF](./media/tutorial-vmware-to-azure/vcenter-wizard.png)

3. Dans **Sélectionner une source**, spécifiez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, cliquez sur **Suivant**.
5. Dans **Sélectionner le nom et le dossier**, et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, et pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**.
4. Dans le reste des pages de l’assistant, acceptez les paramètres par défaut.
5. Dans **Prêt à finaliser** :
  - Pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.
  - Si vous souhaitez ajouter une interface réseau supplémentaire, désactivez **Mettre sous tension après le déploiement**, puis sélectionnez **Terminer**. Par défaut, le modèle de serveur de configuration est déployé avec une seule carte réseau, mais vous pouvez en ajouter d’autres après le déploiement.

  
## <a name="add-an-additional-adapter"></a>Ajouter une carte supplémentaire

Si vous souhaitez ajouter une carte réseau supplémentaire au serveur de configuration, faites-le avant d’inscrire le serveur dans le coffre. L’ajout de cartes supplémentaires n’est pas pris en charge après l’inscription.

1. Dans l’inventaire du client vSphere, faites un clic droit sur la machine virtuelle, puis sélectionnez **Modifier les paramètres**.
2. Dans **Matériel**, cliquez sur **Ajouter** > **Carte Ethernet**. Cliquez ensuite sur **Suivant**.
3. Sélectionnez un type de carte et un réseau. 
4. Pour connecter la carte réseau virtuelle lorsque la machine virtuelle est allumée, sélectionnez **Connecter à la mise sous tension**. Cliquez sur **Suivant** > **Terminer**, puis cliquez sur **OK**.


## <a name="register-the-configuration-server"></a>Inscrire le serveur de configuration 

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et spécifiez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.
4. L’outil de Configuration Azure Site Recovery se lance la première fois que vous vous connectez.
5. Spécifiez un nom utilisé pour inscrire le serveur de configuration avec Site Recovery. Cliquez ensuite sur **Suivant**.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, cliquez sur **Connecter** pour vous connecter à votre abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.
7. L’outil effectue des tâches de configuration, puis redémarre.
8. Connectez-vous de nouveau à la machine. L’assistant de gestion de serveur de configuration se lance automatiquement.

### <a name="configure-settings-and-connect-to-vmware"></a>Configurer les paramètres et se connecter à VMware

1. Dans l’assistant de gestion de serveur de configuration > **Configurer la connectivité**, sélectionnez la carte réseau qui recevra le trafic de réplication. Cliquez ensuite sur **Enregistrer**. Vous ne pouvez pas modifier ce paramètre une fois configuré.
2. Dans **Sélectionner le coffre Recovery Services**, sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre appropriés.
3. Dans **Installer un logiciel tiers**, acceptez le contrat de licence et cliquez sur **Télécharger et installer** pour installer MySQL Server.
4. Cliquez sur **Installer VMware PowerCLI**. Assurez-vous que toutes les fenêtres du navigateur sont fermées avant de continuer. Cliquez ensuite sur **Continuer**
5. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant de continuer.
6. Dans **Configurer le serveur vCenter/vSphere ESXi**, spécifiez le nom de domaine complet ou l’adresse IP du serveur vCenter, ou l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Spécifiez le port écouté par le serveur et un nom convivial à utiliser pour le serveur VMware dans le coffre.
7. Spécifiez les informations d’identification à utiliser par le serveur de configuration pour se connecter au serveur VMware. Site Recovery utilise ces informations d’identification pour détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Cliquez sur **Ajouter**, puis sur **Continuer**.
8. Dans **Configurer les informations d’identification de la machine virtuelle**, spécifiez le nom d’utilisateur et le mot de passe qui serviront à installer automatiquement le service Mobility sur les machines, une fois la réplication activée. Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer. Pour Linux, fournissez les détails du compte racine.
9. Cliquez sur **Finaliser la configuration** pour terminer l’inscription. 
10. Une fois l’inscription terminée, dans le portail Azure, vérifiez que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. Cliquez ensuite sur **OK** pour configurer les paramètres cibles.


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

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter une simulation de récupération d’urgence](site-recovery-test-failover-to-azure.md)
