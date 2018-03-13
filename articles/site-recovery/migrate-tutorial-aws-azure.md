---
title: Migrer des machines virtuelles depuis AWS vers Azure avec Azure Site Recovery | Microsoft Docs
description: "Cet article décrit comment migrer des machines virtuelles Windows s’exécutant dans Amazon Web Services (AWS) vers Azure en utilisant Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 59a09b5d67391f2b48d338d721369f14ed6b4ede
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrer des machines virtuelles Amazon Web Services (AWS) vers Azure

Ce didacticiel vous montre comment migrer des machines virtuelles Amazon Web Services (AWS) vers des machines virtuelles Azure en utilisant Site Recovery. Durant la migration d’instances EC2 vers Azure, les machines virtuelles sont traitées comme des ordinateurs locaux physiques. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Préparer les ressources Azure
> * Préparer les instances AWS EC2 pour la migration
> * Déployer un serveur de configuration
> * Activer la réplication pour les machines virtuelles
> * Tester le basculement pour vérifier que tout fonctionne bien
> * Exécuter un basculement unique vers Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prepare-azure-resources"></a>Préparer les ressources Azure

Vous devez disposer de quelques ressources prêtes dans Azure, que les instances EC2 migrées vont utiliser. Il s’agit d’un compte de stockage, d’un coffre et d’un réseau virtuel.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Les images des machines répliquées sont conservées dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure.

1. Dans le menu du [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Stockage** > **Compte de stockage**.
2. Entrez un nom pour votre compte de stockage. Pour ces didacticiels, nous utilisons le nom **awsmigrated2017**. Le nom doit être unique dans Azure, comprendre entre 3 et 24 caractères, et comporter uniquement des nombres et des lettres minuscules.
3. Conservez les valeurs par défaut pour **Modèle de déploiement**, **Type de compte**, **Performances** et **Transfert sécurisé requis**.
5. Sélectionnez la valeur par défaut **RA-GRS** pour **Réplication**.
6. Sélectionnez l’abonnement que vous voulez utiliser pour ce didacticiel.
7. Pour **Groupe de ressources**, sélectionnez **Créer**. Dans cet exemple, nous utilisons le nom **migrationRG**.
8. Sélectionnez l’emplacement **Europe de l’Ouest**.
9. Cliquez sur **Créer** pour créer le compte de stockage.

### <a name="create-a-vault"></a>création d'un coffre

1. Dans le [portail Azure](https://portal.azure.com), dans le volet de navigation de gauche, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Coffres Recovery Services**.
2. Dans la page Coffres Recovery Services, cliquez sur **+ Ajouter** en haut à gauche de la page.
3. Pour **Nom**, tapez *myVault*.
4. Pour **Abonnement**, sélectionnez l’abonnement approprié.
4. Pour **Groupe de ressources**, sélectionnez **Existant** et *migrationRG*.
5. Dans **Emplacement**, sélectionnez *Europe de l’Ouest*.
5. Pour accéder rapidement au nouveau coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord**.
7. Lorsque vous avez terminé, cliquez sur **Créer**.

Le nouveau coffre apparaît dans **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Quand les machines virtuelles Azure sont créées après la migration (basculement), elles sont jointes à ce réseau.

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Créer une ressource** > **Mise en réseau** >
   **Réseau virtuel**.
3. Pour **Nom**, tapez *myMigrationNetwork*.
4. Laissez la valeur par défaut pour **Espace d’adressage**.
5. Pour **Abonnement**, sélectionnez l’abonnement approprié.
6. Pour **Groupe de ressources**, sélectionnez **Existant** et choisissez *migrationRG* dans la liste déroulante.
7. Pour **Emplacement**, sélectionnez **Europe de l’Ouest**.
8. Laissez les valeurs par défaut pour **Sous-réseau**, à la fois le **Nom** et la **Plage d’adresses IP**.
9. Laissez **Points de terminaison du service** désactivé.
10. Lorsque vous avez terminé, cliquez sur **Créer**.


## <a name="prepare-the-ec2-instances"></a>Préparer les instances EC2

Vous avez besoin d’une ou plusieurs machines virtuelles à migrer. Ces instances EC2 doivent exécuter la version 64 bits de Windows Server 2008 R2 SP1 ou ultérieur, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 ou Red Hat Enterprise Linux 6.7 (uniquement pour les instances HVM virtualisées). Le serveur doit avoir seulement les pilotes Citrix PV ou AWS PV. Les instances exécutant des pilotes RedHat PV ne sont pas prises en charge.

Le service Mobility doit être installé sur chaque machine que vous souhaitez répliquer. Site Recovery installe ce service automatiquement quand vous activez la réplication pour la machine virtuelle. Pour une installation automatique, vous devez préparer un compte sur les instances EC2 qui sera utilisé par Site Recovery pour accéder à la machine virtuelle.

Vous pouvez utiliser un compte local ou de domaine. Pour les machines virtuelles Linux, le compte doit être root sur le serveur Linux source. Pour les machines virtuelles Windows, si vous n’utilisez pas un compte de domaine, désactivez le contrôle d’accès des utilisateurs distants sur la machine locale :

  - Dans le Registre, sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** et définissez sa valeur sur 1.

Vous avez aussi besoin d’une instance EC2 distincte que vous pouvez utiliser comme serveur de configuration Site Recovery. Cette instance doit exécuter Windows Server 2012 R2.


## <a name="prepare-the-infrastructure"></a>Préparer l’infrastructure

Dans la page du portail de votre coffre, sélectionnez **Site Recovery** dans la section **Bien démarrer**, puis cliquez sur **Préparer l’infrastructure**.

### <a name="1-protection-goal"></a>1 Objectif de protection

Sélectionnez les valeurs suivantes dans la page **Objectif de protection** :

|    |  |
|---------|-----------|
| Où se trouvent vos machines ? | **Local**|
| Sur quelle cible souhaitez-vous répliquer vos machines ? |**Vers Azure**|
| Vos machines sont-elles virtualisées ? | **Non virtualisée / Autre**|

Quand vous avez terminé, cliquez sur **OK** pour passer à la section suivante.

### <a name="2-source-prepare"></a>2 Préparer la source

Dans la page **Préparer la source**, cliquez sur **+ Serveur de configuration**.

1. Utilisez une instance EC2 exécutant Windows Server 2012 R2 pour créer un serveur de configuration et inscrivez-le auprès de votre coffre de récupération.

2. Configurez le proxy sur la machine virtuelle de l’instance EC2 que vous utilisez comme serveur de configuration, afin qu’il puisse accéder aux [URL des services](site-recovery-support-matrix-to-azure.md).

3. Téléchargez le programme [Installation unifiée de Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus). Vous pouvez le télécharger sur votre machine locale, puis le copier sur la machine virtuelle que vous utilisez comme serveur de configuration.

4. Cliquez sur le bouton **Télécharger** pour télécharger la clé d’inscription du coffre. Copiez le fichier téléchargé sur la machine virtuelle que vous utilisez comme serveur de configuration.

5. Sur la machine virtuelle, cliquez sur le programme d’installation que vous avez téléchargé pour **l’installation unifiée de Microsoft Azure Site Recovery** et sélectionnez **Exécuter en tant qu’administrateur**.

    1. Dans **Avant de commencer**, sélectionnez **Installer le serveur de configuration et le serveur de processus**, puis cliquez sur **Suivant**.
    2. Dans **Licence de logiciel tiers**, sélectionnez **J’accepte le contrat de licence tiers**. Cliquez ensuite sur **Suivant**.
    3. Dans **Inscription**, cliquez sur Parcourir et accédez à l’endroit où vous avez placé le fichier de clé d’inscription du coffre, puis cliquez sur **Suivant**.
    4. Dans **Paramètres Internet**, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**. Cliquez ensuite sur **Suivant**.
    5. Dans la page **Vérification de la configuration requise**, il effectue des vérifications pour plusieurs éléments. Quand la vérification est terminée, cliquez sur **Suivant**.
    6. Dans **Configuration de MySQL**, spécifiez les mots de passe nécessaires, puis cliquez sur **Suivant**.
    7. Dans **Détails de l’environnement**, sélectionnez **Non**, vous n’avez pas besoin de protéger des machines VMware, puis cliquez sur **Suivant**.
    8. Dans **Emplacement d’installation**, cliquez sur **Suivant** pour accepter la valeur par défaut.
    9. Dans **Sélection du réseau**, cliquez sur **Suivant** pour accepter la valeur par défaut.
    10. Dans **Récapitulatif**, cliquez sur **Installer**.
    11. **Progression de l’installation** vous montre des informations sur l’avancement du processus d’installation. Quand l’installation est terminée, cliquez sur **Terminé**. Vous voyez une fenêtre contextuelle indiquant qu’il peut être nécessaire de redémarrer. Cliquez sur **OK**. Vous voyez aussi une fenêtre contextuelle à propos de la phrase secrète de la connexion du serveur de configuration. Copiez-la dans le Presse-papiers et enregistrez-la à un emplacement sûr.

6. Sur la machine virtuelle, exécutez **cspsconfigtool.exe** pour créer un ou plusieurs comptes de gestion sur le serveur de configuration. Vérifiez que les comptes de gestion disposent des autorisations d’administrateur sur les instances EC2 que vous voulez migrer.

Quand vous avez terminé la configuration du serveur de configuration, revenez au portail et sélectionnez le serveur que vous venez de créer pour le **serveur de configuration** et cliquez sur *OK** pour passer à l’étape 3 Préparer la cible.

### <a name="3-target-prepare"></a>3 Préparer la cible

Dans cette section, vous entrez des informations sur les ressources que vous avez créées quand vous avez effectué les actions de la section[Préparer les ressources Azure](#prepare-azure-resources), plus tôt dans ce didacticiel.

1. Dans **Abonnement**, sélectionnez l’abonnement Azure que vous avez utilisé pour le didacticiel [Préparer Azure](tutorial-prepare-azure.md).
2. Sélectionnez **Resource Manager** comme modèle de déploiement.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles. Il doit s’agir des ressources que vous avez créées quand vous avez effectué les actions de la section [Préparer les ressources Azure](#prepare-azure-resources), plus tôt dans ce didacticiel.
4. Une fois ces opérations effectuées, cliquez sur **OK**.


### <a name="4-replication-settings-prepare"></a>4 Préparer les paramètres de la réplication

Vous devez créer une stratégie de réplication avant de pouvoir activer la réplication.

1. Cliquez sur **+ Répliquer et associer**.
2. Dans **Nom**, tapez **myReplicationPolicy**.
3. Conservez les autres paramètres par défaut et cliquez sur **OK** pour créer la stratégie. La nouvelle stratégie est automatiquement associée au serveur de configuration.

### <a name="5-deployment-planning-select"></a>5 Sélectionner la planification du déploiement

Dans **Avez-vous effectué la planification du déploiement ?**, sélectionnez **Je le ferai plus tard** dans la liste déroulante, puis cliquez sur **OK**.

Quand vous avez terminé les 5 sections de **Préparer l’infrastructure**, cliquez sur **OK**.


## <a name="enable-replication"></a>Activer la réplication

Activez la réplication pour chaque machine virtuelle que vous voulez migrer. Quand la réplication est activée, Site Recovery installe automatiquement le service Mobilité.

1. Ouvrez le [portail Azure](htts://portal.azure.com).
1. Dans la page correspondant à votre coffre, sous **Bien démarrer**, cliquez sur **Site Recovery**.
2. Sous **Pour les ordinateurs locaux et les machines virtuelles Azure**, cliquez sur **Étape 1 : Répliquer l’application**. Complétez les pages de l’Assistant avec les informations suivantes et cliquez sur **OK** dans chaque page quand vous avez terminé :
    - 1 Configurer la source :

    |  |  |
    |-----|-----|
    | Source : | **Locale**|
    | Emplacement source :| Nom de votre instance EC2 du serveur de configuration.|
    |Type de machine : | **Machines physiques**|
    | Serveur de processus : | Sélectionnez le serveur de configuration dans la liste déroulante.|

    - 2 Configurer la cible

    |  |  |
    |-----|-----|
    | Cible : | Conservez la valeur par défaut.|
    | Abonnement : | Sélectionnez l’abonnement que vous avez utilisé.|
    | Groupe de ressources de post-basculement :| Utilisez le groupe de ressources que vous avez créé dans la section [Préparer les ressources Azure](#prepare-azure-resources).|
    | Modèle de déploiement post-basculement : | Choisissez **Resource Manager**.|
    | Compte de stockage : | Choisissez le compte de stockage que vous avez créé dans la section [Préparer les ressources Azure](#prepare-azure-resources).|
    | Réseau Azure : | Choisissez **Effectuez maintenant la configuration pour les machines sélectionnées**.|
    | Réseau Azure post-basculement : | Choisissez le réseau que vous avez créé dans la section [Préparer les ressources Azure](#prepare-azure-resources).|
    | Sous-réseau : | Sélectionnez la **valeur par défaut** dans la liste déroulante.|

    - 3 Sélectionner les machines physiques

        Cliquez sur **+ Machine physique**, puis entrez le **Nom**, **l’adresse IP** et le **Type de système d’exploitation** de l’instance EC2 que vous voulez migrer, puis cliquez sur **OK**.

    - 4 Configurer les propriétés

        Sélectionnez le compte que vous avez créé sur le serveur de configuration dans la liste déroulante et cliquez sur **OK**.

    - 5 Paramètres de réplication : Configurer les paramètres de réplication

        Vérifiez que la stratégie de réplication sélectionnée dans la liste déroulante est **myReplicationPolicy**, puis cliquez sur **OK**.

3. Quand l’Assistant est terminé, cliquez sur **Activer la réplication**.


Vous pouvez suivre la progression du travail **Activer la protection** dans **Surveillance et rapports** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.        

Quand vous activez la réplication pour une machine virtuelle, vous devrez peut-être attendre 15 minutes ou plus avant que les modifications prennent effet et qu’elles apparaissent dans le portail.

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

Quand vous effectuez un test de basculement, voici ce qui se produit :

1. Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour le basculement sont en place.
2. Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
3. Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

Dans le portail, effectuez le test de basculement comme suit :

1. Dans la page correspondant à votre coffre, accédez à **Éléments protégés** > **Éléments répliqués** > cliquez sur la machine virtuelle > **+ Tester le basculement**.

2. Sélectionnez un point de récupération à utiliser pour le basculement :
    - **Dernier point traité** : bascule la machine virtuelle vers le dernier point de récupération qui a été traité par Site Recovery. L’horodatage est affiché. Cette option, avec laquelle aucun temps n’est passé à traiter les données, offre un objectif de délai de récupération faible.
    - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
    - **Personnalisé** : sélectionnez n’importe quel point de récupération.
3. Dans **Tester le basculement**, sélectionnez le réseau Azure cible auquel les machines virtuelles Azure seront connectées après le basculement. Il doit s’agir du réseau que vous avez créé dans la section [Préparer les ressources Azure](#prepare-azure-resources).
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression en cliquant sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez aussi cliquer sur le travail **Tester le basculement** dans la page correspondant à votre coffre dans **Surveillance et rapports** > **Travaux** >
   **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution.
6. Vous devriez à présent pouvoir vous connecter à la machine virtuelle répliquée dans Azure.
7. Pour supprimer les machines virtuelles créées lors du test de basculement, cliquez sur **Nettoyer le test de basculement** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes.


## <a name="migrate-to-azure"></a>Migrer vers Azure

Effectuez un basculement réel pour les instances EC2 pour les migrer vers des machines virtuelles Azure.

1. Dans **Éléments protégés** > **Éléments répliqués**, cliquez sur les instances AWS > **Basculer**.
2. Dans **Basculer**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le dernier point de récupération.
3. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
4. Vérifiez que la machine virtuelle apparaît dans **Éléments répliqués**.
5. Cliquez avec le bouton droit sur chaque machine virtuelle > **Effectuer la migration**. Ceci termine le processus de migration, arrête la réplication pour la machine virtuelle AWS et arrête la facturation Site Recovery pour la machine virtuelle.

    ![Terminer la migration](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.  




## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez découvert comment migrer des instances AWS EC2 vers des machines virtuelles Azure. Pour plus d’informations sur les machines virtuelles Azure, consultez les didacticiels sur les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](../virtual-machines/windows/tutorial-manage-vm.md)
