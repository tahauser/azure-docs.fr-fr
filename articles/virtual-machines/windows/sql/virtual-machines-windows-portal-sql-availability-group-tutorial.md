---
title: "Groupes de disponibilité SQL Server - Machines virtuelles Azure - Didacticiel | Microsoft Docs"
description: "Ce didacticiel montre comment créer un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 70e483f8b64648200bd6f0898a2877c2bf95e590
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Configurer manuellement des groupes de disponibilité AlwaysOn dans une machine virtuelle Azure

Ce didacticiel montre comment créer un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles Azure. Il crée un groupe de disponibilité avec un réplica de base de données sur deux serveurs SQL Server.

**Durée estimée** : environ 30 minutes une fois les conditions préalables remplies.

Ce schéma illustre ce que vous allez créer dans ce didacticiel.

![Groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>configuration requise

Ce didacticiel suppose que vous avez des notions de base sur les groupes de disponibilité AlwaysOn SQL Server. Pour plus d’informations, consultez [Vue d’ensemble des groupes de disponibilité AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

Le tableau suivant répertorie les conditions requises que vous devez remplir avant de commencer ce didacticiel :

|  |Prérequis |Description |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Deux serveurs SQL Server | - Dans un groupe à haute disponibilité Azure <br/> - Dans un domaine <br/> - Avec la fonctionnalité de Clustering de basculement installée |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Partage de fichiers pour le témoin de cluster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Compte du service SQL Server | Compte du domaine |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Compte du service SQL Server Agent | Compte du domaine |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Ports du pare-feu ouverts | - SQL Server : **1433** pour l’instance par défaut <br/> - Point de terminaison de mise en miroir de bases de données : **5022** ou un port disponible <br/> - Sonde d’équilibrage de charge Azure : **59999** ou un port disponible |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Ajout de la fonctionnalité de Clustering de basculement | Fonctionnalité requise sur les deux serveurs SQL Server |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Compte du domaine d’installation | - Administrateur local sur chaque serveur SQL Server <br/> - Membres du rôle de serveur fixe sysadmin pour chaque instance de SQL Server  |


Avant de commencer ce didacticiel, vous devez [remplir les conditions préalables pour la création de groupes de disponibilité AlwaysOn sur des machines virtuelles Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Si ces conditions préalables sont déjà remplies, vous pouvez passer à l’étape [Création d’un cluster](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## Création du cluster

Une fois les conditions préalables remplies, la première étape consiste à créer un cluster de basculement Windows Server comprenant deux serveurs SQL Server et un serveur témoin.  

1. RDP vers le premier serveur SQL Server à l’aide d’un compte de domaine qui est administrateur sur les deux serveurs SQL Server et le serveur témoin.

   >[!TIP]
   >Si vous avez suivi le [document sur les conditions requises](virtual-machines-windows-portal-sql-availability-group-prereq.md), vous avez créé un compte appelé **CORP\Install**. Utilisez ce compte.

2. Dans le tableau de bord **Gestionnaire de serveur**, sélectionnez **Outils**, puis cliquez sur **Gestionnaire du cluster de basculement**.
3. Dans le volet gauche, cliquez avec le bouton droit sur **Gestionnaire du cluster de basculement**, puis cliquez sur **Créer un cluster**.
   ![Création d’un cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Dans l’Assistant Création d’un cluster, créez un cluster à un nœud en complétant les pages avec les paramètres figurant dans le tableau ci-dessous :

   | Page | Paramètres |
   | --- | --- |
   | Avant de commencer |Utilisation des valeurs par défaut |
   | Sélection des serveurs |Tapez le nom du premier SQL Server dans **Entrer le nom du serveur**, puis cliquez sur **Ajouter**. |
   | Avertissement de validation |Cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster**. |
   | Point d'accès pour l'administration du cluster |Tapez un nom de cluster, par exemple **SQLAGCluster1** dans **Nom du cluster**.|
   | Confirmation |Utilisez les valeurs par défaut, sauf si vous utilisez des espaces de stockage. Consultez la remarque qui suit ce tableau. |

### <a name="set-the-cluster-ip-address"></a>Définir l’adresse IP du cluster

1. Dans **Gestionnaire du cluster de basculement**, accédez à **Principales ressources du cluster** et développez les détails du cluster. Les ressources **Nom** et **Adresse IP** doivent toutes deux être à l’état **Échec**. La ressource à adresse IP ne peut pas être mise en ligne, car le cluster a la même adresse IP que la machine elle-même. Cette adresse est donc dupliquée.

2. Cliquez avec le bouton droit sur la ressource **Adresse IP** en échec, puis cliquez sur **Propriétés**.

   ![Propriétés du cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Sélectionnez **Adresse IP statique** et spécifiez une adresse disponible dans le sous-réseau où se trouve le serveur SQL Server dans la zone de texte Adresse. Cliquez ensuite sur **OK**.
4. Dans la section **Principales ressources du cluster**, cliquez avec le bouton droit sur le nom du cluster et cliquez sur **Mettre en ligne**. Attendez que les deux ressources soient en ligne. Lorsque la ressource du cluster apparaît en ligne, elle met à jour le serveur de contrôleur de domaine avec un nouveau compte d’ordinateur Active Directory (AD). Utilisez ce compte AD pour exécuter le service en cluster du groupe de disponibilité ultérieurement.

### <a name="addNode"></a>Ajouter l’autre serveur SQL Server au cluster

Ajoutez l’autre serveur SQL Server au cluster.

1. Dans l’arborescence du navigateur, cliquez avec le bouton droit sur le cluster, puis cliquez sur **Ajouter un nœud**.

    ![Ajout d’un nœud au cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Dans **l’Assistant d’ajout de nœud**, cliquez sur **Suivant**. Dans la page **Sélectionner les serveurs**, ajouter le second SQL Server. Tapez le nom du serveur dans **Entrer le nom du serveur**, puis cliquez sur **Ajouter**. Une fois ces opérations effectuées, cliquez sur **Suivant**.

1. Sur la page **Avertissement de validation**, cliquez sur **Non** (dans un scénario de production, vous devez effectuer les essais de validation). Cliquez ensuite sur **Suivant**.

8. Dans la page **Confirmation**, si vous utilisez des espaces de stockage, vous devez désactiver la case à cocher **Ajouter la totalité du stockage disponible au cluster**.

   ![Confirmation de l’ajout du nœud](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Si vous utilisez des espaces de stockage et que vous ne désactivez pas la case à cocher **Ajouter la totalité du stockage disponible au cluster**, Windows détache les disques virtuels pendant le processus de mise en cluster. Par conséquent, ils n’apparaissent pas dans le Gestionnaire de disque ou dans l’Explorateur, jusqu’à ce que les espaces de stockage soient supprimés du cluster et rattachés à l’aide de PowerShell. Les espaces de stockage regroupent plusieurs disques dans des pools de stockage. Pour plus d’informations, consultez la page [Espaces de stockage](https://technet.microsoft.com/library/hh831739).

1. Cliquez sur **Suivant**.

1. Cliquez sur **Terminer**.

   Le Gestionnaire du cluster de basculement indique que votre cluster a un nouveau nœud et l’affiche dans le conteneur **Nœuds**.

10. Déconnectez-vous de la session Bureau à distance.

### <a name="add-a-cluster-quorum-file-share"></a>Ajouter un partage de fichiers de quorum de cluster

Dans cet exemple, le cluster Windows utilise un partage de fichiers pour créer un quorum de cluster. Ce didacticiel utilise un quorum Nœud et partage de fichiers majoritaires. Pour plus d’informations, consultez [Présentation des configurations de quorum dans un cluster de basculement](http://technet.microsoft.com/library/cc731739.aspx).

1. Connectez-vous au serveur de membre de témoin de partage de fichiers avec une session Bureau à distance.

1. Dans **Gestionnaire de serveur**, cliquez sur **Outils**. Ouvrez **Gestion de l’ordinateur**.

1. Cliquez sur **Dossiers partagés**.

1. Cliquez avec le bouton droit sur **Partages**, puis cliquez sur **Nouveau partage...**.

   ![Nouveau partage](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilisez **Assistant Création d’un dossier partagé** pour créer un partage.

1. Dans **chemin d’accès du dossier**, cliquez sur **Parcourir** et recherchez ou créez le chemin d’accès du dossier partagé. Cliquez sur **Suivant**.

1. Dans **Nom, Description et Paramètres**, vérifiez le nom et le chemin d’accès du partage. Cliquez sur **Suivant**.

1. Dans **Autorisations du dossier partagé**, sélectionnez **Personnaliser les autorisations**. Cliquez sur **Personnalisé...**.

1. Dans **Personnaliser les autorisations**, cliquez sur **Ajouter...**.

1. Vérifiez que le compte utilisé pour créer le cluster possède le contrôle total.

   ![Nouveau partage](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Cliquez sur **OK**.

1. Dans **Autorisations du dossier partagé**, cliquez sur **Terminer**. Cliquez à nouveau sur **Terminer**.  

1. Déconnectez-vous du serveur.

### <a name="configure-cluster-quorum"></a>Configurer le quorum du cluster

Ensuite, configurez le quorum du cluster.

1. Connectez-vous au premier nœud du cluster avec le Bureau à distance.

1. Dans **Gestionnaire du cluster de basculement**, cliquez avec le bouton droit sur le cluster, pointez sur **Autres Actions** et cliquez sur **Configurer les paramètres du quorum du cluster...**.

   ![Nouveau partage](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Dans l’**Assistant Configuration de quorum du cluster**, cliquez sur **Suivant**.

1. Dans **Sélectionner l’option de configuration du quorum**, choisissez **Sélectionner le témoin de quorum**, puis cliquez sur **Suivant**.

1. Dans **Sélectionner le témoin de quorum**, cliquez sur **Configurer un témoin de partage de fichiers**.

   >[!TIP]
   >Windows Server 2016 prend en charge un témoin cloud. Si vous choisissez ce type de témoin, un témoin de partage de fichiers est inutile. Pour plus d’informations, consultez [Déployer un témoin cloud pour un cluster de basculement](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Ce didacticiel utilise un témoin de partage de fichiers, que les systèmes d’exploitation antérieurs prennent en charge.

1. Dans **Configurer le témoin de partage de fichiers**, tapez le chemin d’accès du partage que vous avez créé. Cliquez sur **Suivant**.

1. Vérifiez les paramètres dans **Confirmation**. Cliquez sur **Suivant**.

1. Cliquez sur **Terminer**.

Les principales ressources de cluster sont configurées avec un témoin de partage de fichiers.

## <a name="enable-availability-groups"></a>Activer les groupes de disponibilité

Ensuite, activez la fonctionnalité **Groupes de disponibilité AlwaysOn**. Effectuez ces étapes pour les deux serveurs SQL Server.

1. À partir de l’écran **d’accueil**, lancez le **Gestionnaire de configuration SQL Server**.
2. Dans l’arborescence du navigateur, cliquez sur **Services SQL Server**, cliquez avec le bouton droit sur **SQL Server (MSSQLSERVER)**, puis cliquez sur **Propriétés**.
3. Cliquez sur l’onglet **Haute disponibilité AlwaysOn**, puis sélectionnez **Activer les groupes de disponibilité AlwaysOn**, comme indiqué ci-dessous :

    ![Activation des groupes à haute disponibilité AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Cliquez sur **Appliquer**. Cliquez sur **OK** dans la boîte de dialogue contextuelle.

5. Redémarrez le service SQL Server.

Répétez ces étapes sur l’autre serveur SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Créer une base de données sur le premier serveur SQL Serveur

1. Lancez le fichier RDP pour le premier serveur SQL Server avec un compte de domaine qui est membre du rôle serveur fixe sysadmin.
1. Ouvrez SQL Server Management Studio et connectez-vous au serveur SQL Server.
7. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur **Bases de données**, puis cliquez sur **Nouvelle base de données**.
8. Dans **Nom de base de données**, saisissez **MyDB1**, puis cliquez sur **OK**.

### <a name="backupshare"></a> Créer un partage de sauvegarde

1. Sur le premier serveur SQL Server dans **Gestionnaire de serveur**, cliquez sur **outils**. Ouvrez **Gestion de l’ordinateur**.

1. Cliquez sur **Dossiers partagés**.

1. Cliquez avec le bouton droit sur **Partages**, puis cliquez sur **Nouveau partage...**.

   ![Nouveau partage](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilisez **Assistant Création d’un dossier partagé** pour créer un partage.

1. Dans **Chemin d’accès du dossier**, cliquez sur **Parcourir** et recherchez ou créez le chemin d’accès du dossier partagé de la sauvegarde de la base de données. Cliquez sur **Suivant**.

1. Dans **Nom, Description et Paramètres**, vérifiez le nom et le chemin d’accès du partage. Cliquez sur **Suivant**.

1. Dans **Autorisations du dossier partagé**, sélectionnez **Personnaliser les autorisations**. Cliquez sur **Personnalisé...**.

1. Dans **Personnaliser les autorisations**, cliquez sur **Ajouter...**.

1. Vérifiez que les comptes de service SQL Server et SQL Server Agent pour les deux serveurs ont un contrôle total.

   ![Nouveau partage](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Cliquez sur **OK**.

1. Dans **Autorisations du dossier partagé**, cliquez sur **Terminer**. Cliquez à nouveau sur **Terminer**.  

### <a name="take-a-full-backup-of-the-database"></a>Créer une sauvegarde complète de la base de données

Vous devez sauvegarder la nouvelle base de données pour initialiser la chaîne du journal. Si vous ne sauvegardez pas la nouvelle base de données, elle ne peut pas être incluse dans un groupe de disponibilité.

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la base de données, pointez sur **Tâches...** et cliquez sur **Sauvegarder**.

1. Cliquez sur **OK** pour effectuer une sauvegarde complète à l’emplacement de sauvegarde par défaut.

## <a name="create-the-availability-group"></a>Création du groupe de disponibilité
Vous pouvez maintenant configurer le groupe de disponibilité en procédant comme suit :

* Création d’une base de données sur le premier serveur SQL Server
* Sauvegarde complète et sauvegarde du journal des transactions de la base de données
* Restauration des sauvegardes complète et journalisée sur le deuxième serveur SQL Server avec l’option **NORECOVERY**
* Création du groupe de disponibilité (**AG1**) avec validation synchrone, basculement automatique et réplicas secondaires lisibles

### <a name="create-the-availability-group"></a>Créer le groupe de disponibilité :

1. Dans une session Bureau à distance pour le premier serveur SQL Server. Dans l’**Explorateur d’objets** dans SSMS, cliquez avec le bouton droit sur **Haute disponibilité AlwaysOn**, puis cliquez sur **Assistant Nouveau groupe de disponibilité**.

    ![Lancer l'Assistant Nouveau groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Sur la page **Introduction**, cliquez sur **Suivant**. Dans la page **Spécifier le nom du groupe de disponibilité**, tapez le nom du groupe de disponibilité, par exemple **AG1** dans **Nom du groupe de disponibilité**. Cliquez sur **Suivant**.

    ![Assistant Nouveau groupe de disponibilité, spécifier le nom du groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Dans la page **Sélectionner des bases de données**, sélectionnez votre base de données et cliquez sur **Suivant**.

   >[!NOTE]
   >Cette base de données présente la configuration requise pour un groupe de disponibilité, car vous avez effectué au moins une sauvegarde complète sur le réplica principal prévu.

   ![Assistant Nouveau groupe de disponibilité, sélectionner les bases de données](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Sur la page **Spécifier les réplicas**, cliquez sur **Ajouter un réplica**.

   ![Assistant Nouveau groupe de disponibilité, spécifier les réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. La boîte de dialogue **Se connecter au serveur** apparaît. Tapez le nom du deuxième serveur dans **Nom du serveur**. Cliquez sur **Connecter**.

   Dans la page **Spécifier les réplicas**, vous devez maintenant voir le deuxième serveur dans **Réplicas de disponibilité**. Configurez les réplicas comme indiqué ci-dessous.

   ![Assistant Nouveau groupe de disponibilité, spécifier les réplicas (Terminé)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Cliquez sur **Points de terminaison** pour afficher le point de terminaison de mise en miroir de la base de données pour ce groupe de disponibilité. Utilisez le même port que vous avez utilisé lorsque vous avez défini la [règle de pare-feu pour les points de terminaison de mise en miroir de base de données](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Assistant Nouveau groupe de disponibilité, sélectionner la synchronisation initiale des données](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Dans la page **Sélectionner la synchronisation de données initiale**, sélectionnez **Complète** et spécifiez un emplacement réseau partagé. Pour l’emplacement, utilisez le [partage de sauvegarde que vous avez créé](#backupshare). Dans l’exemple, il s’agissait de *\\\\\<Premier serveur SQL Server\>\Backup\**. Cliquez sur **Suivant**.

   >[!NOTE]
   >La synchronisation complète effectue une sauvegarde complète de la base de données sur la première instance de SQL Server et la restaure sur la deuxième instance. Pour les bases de données volumineuses, une synchronisation complète n’est pas recommandée, car elle peut prendre longtemps. Vous pouvez réduire ce temps en effectuant manuellement une sauvegarde de la base de données et en la restaurant avec `NO RECOVERY`. Si la base de données est déjà restaurée avec `NO RECOVERY` sur le second serveur SQL Server avant de configurer le groupe de disponibilité, choisissez **Join only (Joindre uniquement)**. Si vous souhaitez effectuer la sauvegarde après avoir configuré le groupe de disponibilité, choisissez **Ignorer la synchronisation de données initiale**.

    ![Assistant Nouveau groupe de disponibilité, sélectionner la synchronisation initiale des données](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Dans la page **Validation**, cliquez sur **Suivant**. Cette page doit ressembler à l’image suivante :

    ![Assistant Nouveau groupe de disponibilité, validation](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Un avertissement concernant la configuration de l’écouteur s’affiche, car aucun écouteur de groupe de disponibilité n’est configuré. Vous pouvez ignorer cet avertissement car, sur les machines virtuelles, vous créez l’écouteur après Azure Load Balancer.

10. Dans la page **Résumé**, cliquez sur **Terminer**, puis patientez le temps que l’assistant configure le nouveau groupe de disponibilité. Dans la page **Progression**, vous pouvez cliquer sur **Plus de détails** pour afficher la progression détaillée. Une fois l’Assistant terminé, examinez la page **Résultats** et vérifiez que le groupe de disponibilité est créé.

     ![Assistant Nouveau groupe de disponibilité, résultats](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Cliquez sur **Fermer** pour quitter l’Assistant.

### <a name="check-the-availability-group"></a>Vérifier le groupe de disponibilité

1. Dans l’**Explorateur d’objets**, développez **Haute disponibilité AlwaysOn**, puis **Groupes de disponibilité**. Vous devez maintenant voir le nouveau groupe de disponibilité dans ce conteneur. Cliquez avec le bouton droit sur le groupe de disponibilité, puis cliquez sur **Afficher le tableau de bord**.

   ![Afficher le tableau de bord de groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Votre **tableau de bord AlwaysOn** doit ressembler à celui-ci.

   ![Tableau de bord de groupe de disponibilité](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Vous pouvez voir les réplicas, le mode de basculement de chaque réplica et l'état de synchronisation.

2. Dans le **Gestionnaire du cluster de basculement**, cliquez sur votre cluster. Sélectionnez **Rôles**. Le nom de groupe de disponibilité est un rôle sur le cluster. Ce groupe de disponibilité n’a pas une adresse IP pour les connexions clientes, car vous n’avez pas configuré d’écouteur. Vous allez configurer l’écouteur après avoir créé un équilibrage de charge Azure.

   ![Groupe de disponibilité dans le Gestionnaire du cluster de basculement](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Ne tentez pas de faire basculer le groupe de disponibilité à partir du Gestionnaire du cluster de basculement. Vous devez effectuer toutes les opérations de basculement à partir du **tableau de bord AlwaysOn** dans SSMS. Pour plus d’informations, consultez [Restrictions d’utilisation du Gestionnaire du cluster de basculement avec des groupes de disponibilité](https://msdn.microsoft.com/library/ff929171.aspx).
    >

À ce stade, vous avez un groupe de disponibilité avec des réplicas sur les deux instances de SQL Server. Vous pouvez déplacer le groupe de disponibilité entre des instances. Vous ne pouvez pas encore vous connecter au groupe de disponibilité, car vous n’avez pas d’écouteur. Dans les machines virtuelles Azure, l’écouteur requiert un équilibrage de charge. L’étape suivante consiste à créer l’équilibrage de charge dans Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Crée un équilibrage de charge Azure

Sur les machines virtuelles Azure, un groupe de disponibilité SQL Serveur requiert un équilibrage de charge. Cet équilibrage de charge stocke l’adresse IP de l’écouteur du groupe de disponibilité. Cette section indique comment créer l’équilibrage de charge dans le portail Azure.

1. Dans le portail Azure, accédez au groupe de ressources où se trouvent vos serveurs SQL Server et cliquez sur **+ Ajouter**.
2. Recherchez **Équilibrage de charge**. Choisissez l’équilibrage de charge publié par Microsoft.

   ![Groupe de disponibilité dans le Gestionnaire du cluster de basculement](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Cliquez sur **Créer**.
3. Configurez les paramètres suivants pour l’équilibreur de charge.

   | Paramètre | Champ |
   | --- | --- |
   | **Name** |Utilisez un nom pour l’équilibrage de charge, par exemple **sqlLB**. |
   | **Type** |Interne |
   | **Réseau virtuel** |Utilisez le nom de votre réseau virtuel Azure. |
   | **Sous-réseau** |Utilisez le nom du sous-réseau auquel appartient la machine virtuelle.  |
   | **Affectation d’adresses IP** |statique |
   | **Adresse IP** |Utilisez une adresse disponible du sous-réseau. |
   | **Abonnement** |Utilisez le même abonnement que la machine virtuelle. |
   | **Lieu** |Utilisez le même emplacement que la machine virtuelle. |

   Le panneau du portail Azure doit ressembler à ceci :

   ![Créer un équilibreur de charge](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Cliquez sur **Créer** pour générer l’équilibrage de charge.

Pour configurer l’équilibrage de charge, vous devez créer un pool principal ainsi qu’une sonde et définir les règles d’équilibrage de charge. Effectuez ces opérations dans le portail Azure.

### <a name="add-backend-pool"></a>Ajouter le pool principal

1. Dans le portail Azure, accédez à votre groupe de disponibilité. Vous devrez peut-être actualiser l’affichage pour voir l’équilibrage de charge tout juste créé.

   ![Rechercher l’équilibrage de charge dans le groupe de ressources](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Cliquez sur l’équilibrage de charge, sur **Pools principaux**, puis sur **+Ajouter**. Configurez le pool principal comme suit :

   | Paramètre | Description | exemples
   | --- | --- |---
   | **Name** | Tapez un nom. | SQLLBBE
   | **Associé à** | Choisir dans la liste | Groupe à haute disponibilité
   | **Groupe à haute disponibilité** | Utilisez le nom du groupe à haute disponibilité auquel appartiennent vos machines virtuelles SQL Server. | sqlAvailabilitySet |
   | **Machines virtuelles** |Les deux noms des machines virtuelles SQL Server Azure | sqlserver-0, sqlserver-1

1. Tapez le nom du pool principal.

1. Cliquez sur **+ Ajouter une machine virtuelle**.

1. Pour le groupe à haute disponibilité, choisissez le groupe de disponibilité qui contient les serveurs SQL Server.

1. Pour les machines virtuelles, incluez les deux serveurs SQL Server. N’incluez pas le serveur témoin de partage de fichiers.

1. Cliquez sur **OK** pour créer le pool principal.

### <a name="set-the-probe"></a>Configurer la sonde

1. Cliquez sur l’équilibrage de charge, sur **Health probes (Sondes d’intégrité)**, puis sur **+Ajouter**.

1. Configurez la sonde d’intégrité comme suit :

   | Paramètre | Description | exemples
   | --- | --- |---
   | **Name** | Texte | SQLAlwaysOnEndPointProbe |
   | **Protocole** | Choisissez TCP. | TCP |
   | **Port** | Tout port inutilisé. | 59999 |
   | **Intervalle**  | Durée entre les tentatives de la sonde, en secondes. |5. |
   | **Seuil de défaillance sur le plan de l’intégrité** | Nombre d’échecs de sonde consécutifs qui doivent se produire pour qu’une machine virtuelle soit considérée comme défectueuse.  | 2 |

1. Cliquez sur **OK** pour configurer la sonde d’intégrité.

### <a name="set-the-load-balancing-rules"></a>Configurer les règles d’équilibrage de charge

1. Cliquez sur l’équilibrage de charge, sur **Règles d’équilibrage de charge** et sur **+Ajouter**.

1. Configurez les règles d’équilibrage de charge comme suit :
   | Paramètre | Description | exemples
   | --- | --- |---
   | **Name** | Texte | SQLAlwaysOnEndPointListener |
   | **Frontend IP address (Adresse IP frontale)** | Choisissez une adresse. |Utilisez l’adresse que vous avez créée lorsque vous avez créé l’équilibrage de charge. |
   | **Protocole** | Choisissez TCP. |TCP |
   | **Port** | Utilisez le port de l’instance de SQL Server. | 1433 |
   | **Port principal** | Ce champ est inutilisé lorsque l’option IP flottante est définie pour Retour au serveur direct. | 1433 |
   | **Sonde** |Nom que vous avez spécifié pour la sonde. | SQLAlwaysOnEndPointProbe |
   | **Persistance de session** | Liste déroulante | **Aucun** |
   | **Délai d’inactivité** | Délai en minutes de maintien d’une connexion TCP ouverte | 4 |
   | **Adresse IP flottante (retour serveur direct)** | |activé |

   > [!WARNING]
   > Le retour au serveur direct est configuré lors de la création. Cette valeur n’est pas modifiable.

1. Cliquez sur **OK** pour définir les règles d’équilibrage de charge.

## <a name="configure-listener"></a> Configurer l’écouteur

La prochaine étape consiste à configurer un écouteur de groupe de disponibilité sur le cluster de basculement.

> [!NOTE]
> Ce didacticiel montre comment créer un seul écouteur avec une adresse IP d’ILB. Pour créer un ou plusieurs écouteurs en utilisant une ou plusieurs adresses IP, consultez [Configure one or more AlwaysOn Availability Group Listeners - Resource Manager (Configurer un ou plusieurs écouteurs de groupe de disponibilité AlwaysOn - Resource Manager)](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Configurer le port d’écoute

Dans SQL Server Management Studio, configurez le port d’écoute.

1. Lancez SQL Server Management Studio et connectez-vous au réplica principal.

1. Accédez à **Haute disponibilité AlwaysOn** | **Groupes de disponibilité** | **Écouteurs de groupe de disponibilité**.

1. Vous devez maintenant voir le nom de l'écouteur que vous avez créé dans le Gestionnaire du cluster de basculement. Cliquez avec le bouton droit sur l’écouteur, puis cliquez sur **Propriétés**.

1. Dans le champ **Port**, indiquez le numéro de port de l’écouteur du groupe de disponibilité à l’aide du paramètre $EndpointPort utilisé précédemment (valeur par défaut : 1433), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité SQL Server dans des machines virtuelles Azure exécutées en mode Resource Manager.

## <a name="test-connection-to-listener"></a>Tester la connexion à l’écouteur

Pour tester la connexion :

1. Envoyez une requête RDP à un serveur SQL qui se trouve dans le même réseau virtuel, mais qui ne possède pas le réplica. Vous pouvez utiliser l’autre serveur SQL Server du cluster.

1. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** avec le réplica principal au moyen de l’écouteur avec une authentification Windows :

    ```
    sqlcmd -S <listenerName> -E
    ```

    Si l’écouteur utilise un port autre que le port par défaut (1433), spécifiez le port dans la chaîne de connexion. Par exemple, la commande sqlcmd suivante se connecte à un écouteur sur le port 1435 :

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La connexion SQLCMD se connecte automatiquement à l’instance SQL Server hébergeant le réplica principal.

> [!TIP]
> Vérifiez que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant sur le port TCP utilisé. Pour plus d’informations, consultez [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Étapes suivantes

- [Add an IP address to an existing load balancer with PowerShell (Ajouter une adresse IP à un équilibrage de charge pour un deuxième groupe de disponibilité)](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
