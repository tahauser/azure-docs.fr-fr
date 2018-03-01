---
title: "Configurer une haute disponibilité pour les machines virtuelles Azure Resource Manager | Microsoft Docs"
description: "Ce didacticiel vous explique comment créer un groupe de disponibilité Always On avec des machines virtuelles Azure en mode Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a612ffd5a68e34cb0a367a6a883495ef26aeb4bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configurer des groupes de disponibilité Always On dans des machines virtuelles Azure automatiquement : Resource Manager

Ce didacticiel vous montre comment créer un groupe de disponibilité SQL Server qui utilise des machines virtuelles Azure Resource Manager. Il utilise des panneaux Azure pour configurer un modèle. Au cours de ce didacticiel, vous pourrez passer en revue les paramètres par défaut, entrer les paramètres exigés et mettre à jour les panneaux du portail.

Le didacticiel complet couvre la création d’un groupe de disponibilité SQL Server sur des machines virtuelles Azure incluant les éléments suivants :

* un réseau virtuel avec plusieurs sous-réseaux, notamment un sous-réseau frontal et un sous-réseau principal ;
* deux contrôleurs de domaine avec un domaine Active Directory ;
* deux machines virtuelles qui exécutent SQL Server et qui sont déployées sur le sous-réseau principal et jointes au domaine Active Directory ;
* un cluster de basculement à trois nœuds avec le modèle de quorum Nœud majoritaire ;
* un groupe de disponibilité avec deux réplicas avec validation synchrone d’une base de données de disponibilité.

L’illustration suivante représente la solution complète.

![Architecture de laboratoire de test pour les groupes de disponibilité dans Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Toutes les ressources de cette solution appartiennent à un groupe de ressources unique.

Avant de commencer ce didacticiel, vérifiez les éléments suivants :

* Vous disposez déjà d’un compte Azure. Si vous n’en avez pas, [inscrivez-vous pour obtenir un compte d’évaluation](http://azure.microsoft.com/pricing/free-trial/).
* Vous savez déjà comment utiliser l’interface utilisateur graphique pour approvisionner une machine virtuelle SQL Server à partir de la galerie de machines virtuelles. Pour plus d'informations, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Vous disposez déjà d’une connaissance approfondie des groupes de disponibilité. Pour plus d’informations, voir [Groupes de disponibilité AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Si l’utilisation des groupes de disponibilité avec SharePoint vous intéresse, consultez également [Configurer des groupes de disponibilité AlwaysOn SQL Server 2012 pour SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).
>
>

Dans ce didacticiel, vous allez utiliser le Portail Azure pour :

* choisir le modèle AlwaysOn à partir du portail ;
* passer en revue les paramètres du modèle et mettre à jour quelques paramètres de configuration pour votre environnement ;
* surveiller Azure pendant la création de tout l’environnement ;
* vous connecter à un contrôleur de domaine, puis à un serveur qui exécute SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Approvisionnement du cluster à partir de la galerie
Azure fournit une image de galerie pour l’ensemble de la solution. Pour localiser le modèle :

1. Connectez-vous au portail Azure à l’aide de votre compte.
2. Dans le portail Azure, cliquez sur **Créer une ressource** pour ouvrir le panneau **Nouveau**.
3. Dans le panneau **Nouveau**, recherchez **AlwaysOn**.
   ![Rechercher le modèle AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Dans les résultats de la recherche, recherchez **Cluster AlwaysOn SQL Server**.
   ![Modèle AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Dans **Sélectionner un modèle de déploiement**, choisissez **Resource Manager**.

### <a name="basics"></a>Concepts de base
Cliquez sur les **concepts de base** et configurez les paramètres suivants :

* **Nom d’utilisateur de l’administrateur** correspond à un compte d’utilisateur doté d’autorisations d’administrateur de domaine et est membre du rôle serveur fixe sysadmin SQL Server sur les deux instances de SQL Server. Pour ce didacticiel, utilisez **DomainAdmin**.
* **Mot de passe** correspond au mot de passe du compte d’administrateur de domaine. Utilisez un mot de passe complexe. Confirmez le mot de passe.
* **Abonnement** correspond à l’abonnement facturé par Azure pour exécuter toutes les ressources déployées pour le groupe de disponibilité. Vous pouvez spécifier un autre abonnement si votre compte en dispose de plusieurs.
* **Groupe de ressources** correspond au nom du groupe auquel appartiennent toutes les ressources Azure créées par ce modèle. Pour ce didacticiel, utilisez **SQL-HA-RG**. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Emplacement** correspond à la région Azure dans laquelle les ressources sont créées par ce didacticiel. Sélectionnez une région Azure.

La capture d’écran suivante représente le panneau **Concepts de base** renseigné :

![Concepts de base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Cliquez sur **OK**.

### <a name="domain-and-network-settings"></a>Paramètres de domaine et réseau
Ce modèle de galerie Azure crée un domaine et des contrôleurs de domaine. Il crée également un réseau et deux sous-réseaux. Le modèle ne peut pas créer des serveurs dans un domaine ou un réseau virtuel existant. L’étape suivante configure les paramètres de domaine et de réseau.

Dans le panneau des **paramètres de domaine et réseau**, examinez les valeurs prédéfinies des paramètres de domaine et de réseau :

* **Nom de domaine racine de la forêt** correspond au nom de domaine utilisé pour le domaine Active Directory qui héberge le cluster. Pour ce didacticiel, utilisez **contoso.com**.
* **Nom du réseau virtuel** correspond au nom du réseau virtuel Azure. Pour le didacticiel, utilisez **autohaVNET**.
* **Nom du sous-réseau de contrôleur de domaine** correspond au nom d’une partie du réseau virtuel qui héberge le contrôleur de domaine. Utilisez **subnet-1**. Ce sous-réseau utilise le préfixe d’adresse **10.0.0.0/24**.
* **Nom du sous-réseau SQL Server** correspond au nom d’une partie du réseau virtuel qui héberge les serveurs qui exécutent SQL Server et le témoin de partage de fichiers. Utilisez **subnet-2**. Ce sous-réseau utilise le préfixe d’adresse **10.0.1.0/26**.

Pour plus d’informations sur les réseaux virtuels dans Azure, voir [Présentation du réseau virtuel](../../../virtual-network/virtual-networks-overview.md).  

Les **paramètres de domaine et de réseau** doivent ressembler à la capture d’écran suivante :

![Paramètres de domaine et réseau](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, utilisez les valeurs prédéfinies.

Vérifiez les paramètres, puis cliquez sur **OK**.

### <a name="availability-group-settings"></a>Paramètres de groupe de disponibilité
Dans **Paramètres de groupe de disponibilité**, passez en revue les valeurs prédéfinies du groupe de disponibilité et de l’écouteur.

* **Nom du groupe de disponibilité** correspond au nom de ressource en cluster du groupe de disponibilité. Pour ce didacticiel, utilisez **Contoso-ag**.
* **Nom de l’écouteur de groupe de disponibilité** est utilisé par le cluster et l’équilibreur de charge interne. Les clients qui se connectent à SQL Server peuvent utiliser ce nom pour se connecter au réplica approprié de la base de données. Pour ce didacticiel, utilisez **Contoso-listener**.
* **Port de l’écouteur de groupe de disponibilité** spécifie le port TCP pour l’écouteur SQL Server. Pour ce didacticiel, utilisez le port par défaut, **1433**.

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, utilisez les valeurs prédéfinies.  

![paramètres de groupe de disponibilité](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Cliquez sur **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Taille de la machine virtuelle, paramètres de stockage
Sous **Taille de la machine virtuelle, paramètres de stockage**, choisissez une taille de machine virtuelle SQL Server et vérifiez les autres paramètres.

* **Taille de la machine virtuelle SQL Server** correspond à la taille des deux machines virtuelles exécutant SQL Server. Choisissez une taille de machine virtuelle appropriée à votre charge de travail. Si vous générez cet environnement pour le didacticiel, utilisez **DS2**. Pour des charges de travail de production, choisissez une taille de machine virtuelle pouvant gérer la charge de travail. Dans le cas d’importants volumes de charges de travail de production, **DS4** ou plus est nécessaire. Le modèle génère deux machines virtuelles de cette taille et installe SQL Server sur chacune d’elles. Pour plus d’informations, consultez [Tailles des machines virtuelles](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Azure installe SQL Server Enterprise Edition. Le coût dépend de l’édition et de la taille de la machine virtuelle. Pour plus d’informations sur les coûts actuels, consultez la [tarification des machines virtuelles](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Taille de la machine virtuelle de contrôleur de domaine** correspond à la taille de la machine virtuelle des contrôleurs de domaine. Pour ce didacticiel, utilisez **D2**.
* **Taille de la machine virtuelle du témoin de partage de fichiers** correspond à la taille de la machine virtuelle du témoin de partage de fichiers. Pour ce didacticiel, utilisez **A1**.
* **Compte de stockage SQL** correspond au nom du compte de stockage qui contient les données SQL Server et les disques du système d’exploitation. Pour ce didacticiel, utilisez **alwaysonsql01**.
* **Compte de stockage des contrôleurs de domaine** correspond au nom du compte de stockage des contrôleurs de domaine. Pour ce didacticiel, utilisez **alwaysondc01**.
* **Taille du disque de données SQL Server** correspond à la taille du disque de données SQL Server en To. Spécifiez un nombre compris entre 1 et 4 Pour ce didacticiel, utilisez **1**.
* **Optimisation du stockage** définit les paramètres de configuration de stockage spécifiques des machines virtuelles SQL Server en fonction du type de charge de travail. Toutes les machines virtuelles SQL Server de ce scénario utilisent le stockage Premium avec un cache hôte de disque Azure défini en lecture seule. De plus, vous pouvez optimiser les paramètres SQL Server pour la charge de travail en choisissant l’un de ces trois paramètres :

  * **Charge de travail générale** ne définit aucun paramètre de configuration spécifique.
  * **Traitement transactionnel** définit les indicateurs de suivi 1117 et 1118.
  * **Data warehousing** définit les indicateurs de suivi 1117 et 610.

Pour ce didacticiel, utilisez le paramètre **Charge de travail générale**.

![Taille de la machine virtuelle, paramètres de stockage](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Vérifiez les paramètres, puis cliquez sur **OK**.

#### <a name="a-note-about-storage"></a>Remarque sur le stockage
D’autres optimisations dépendent de la taille des disques de données SQL Server. Pour chaque téraoctet de disque de données, Azure ajoute un stockage Premium de 1 To supplémentaire. Si un serveur exige 2 To ou plus, le modèle crée un pool de stockage sur chaque machine virtuelle SQL Server. Un pool de stockage est une forme de virtualisation du stockage où plusieurs disques sont configurés pour fournir une capacité, une résilience et des performances plus élevées.  Le modèle crée alors un espace de stockage dans le pool de stockage et présente un disque de données unique au système d’exploitation. Le modèle désigne ce disque en tant que disque de données pour SQL Server. Il règle le pool de stockage pour SQL Server en utilisant les paramètres suivants :

* La taille d’entrelacement est le paramètre d’entrelacement du disque virtuel. Les charges de travail transactionnelles utilisent 64 Ko. Les charges de travail d’entreposage de données utilisent 256 Ko.
* La résilience est simple (aucune résilience).

> [!NOTE]
> Azure Premium Storage est localement redondant et conserve trois copies des données au sein d’une seule région, si bien qu’aucune résilience supplémentaire n’est exigée dans le pool de stockage.
>
>

* Le nombre de colonnes est égal au nombre de disques dans le pool de stockage.

Pour plus d’informations sur l’espace de stockage et les pools de stockage, consultez :

* [Vue d’ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx)
* [Sauvegarde et pools de stockage Windows Server](http://technet.microsoft.com/library/dn390929.aspx)

Pour plus d’informations sur les meilleures pratiques de configuration SQL Server, consultez [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Paramètres de SQL Server
Dans **Paramètres SQL Server**, passez en revue et modifiez le préfixe du nom de la machine virtuelle SQL Server, la version de SQL Server, le compte de service et le mot de passe SQL Server, ainsi que la planification de maintenance de la mise à jour corrective automatique SQL.

* **Préfixe du nom SQL Server** est utilisé pour créer un nom pour chaque machine virtuelle SQL Server. Pour ce didacticiel, utilisez **sqlserver**. Le modèle nomme les machines virtuelles SQL Server *sqlserver-0* et *sqlserver-1*.
* **Version de SQL Server** correspond à la version de SQL Server. Pour ce didacticiel, utilisez **SQL Server 2014**. Vous pouvez également choisir **SQL Server 2012** ou **SQL Server 2016**.
* **Nom d’utilisateur du compte de service SQL Server** correspond au nom du compte de domaine pour le service SQL Server. Pour ce didacticiel, utilisez **sqlservice**.
* **Mot de passe** correspond au mot de passe du compte de service SQL Server.  Utilisez un mot de passe complexe. Confirmez le mot de passe.
* **Planification de maintenance de la mise à jour corrective automatique SQL** identifie le jour de la semaine pendant lequel Azure effectue automatiquement les mises à jour correctives des serveurs SQL Server. Pour ce didacticiel, tapez **Sunday** (Dimanche).
* **Heure de début de maintenance de la mise à jour corrective automatique SQL** correspond à l’heure à laquelle la mise à jour corrective automatique commence pour la région Azure.

> [!NOTE]
> La fenêtre de mise à jour corrective de chaque machine virtuelle est décalée d’une heure. Une seule machine virtuelle est corrigée à la fois pour éviter toute interruption des services.
>
>

![Paramètres de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Vérifiez les paramètres, puis cliquez sur **OK**.

### <a name="summary"></a>Résumé
Dans la page Résumé, Azure valide les paramètres. Vous pouvez également télécharger le modèle. Passez en revue le résumé. Cliquez sur **OK**.

### <a name="buy"></a>Acheter
Ce panneau final contient les **conditions d’utilisation** et la **politique de confidentialité**. Prenez connaissance de ces informations. Quand vous êtes prêt pour qu’Azure commence à créer les machines virtuelles et toutes les autres ressources nécessaires pour le groupe de disponibilité, cliquez sur **Créer**.

Le Portail Azure crée le groupe de ressources et toutes les ressources.

## <a name="monitor-deployment"></a>Surveiller le déploiement
Surveillez la progression du déploiement depuis le portail Azure. Une icône représentant le déploiement est automatiquement épinglée au tableau de bord du portail Azure.

![Tableau de bord Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Se connecter à SQL Server
Les nouvelles instances de SQL Server sont en cours d’exécution sur les machines virtuelles possédant des adresses IP connectées à Internet. Vous pouvez vous connecter directement à chaque machine virtuelle SQL Server à l’aide du bureau à distance (RDP).

Pour établir une connexion RDP à un serveur SQL Server, procédez comme suit :

1. À partir du tableau de bord du portail Azure, vérifiez que le déploiement a réussi.
2. Cliquez sur **Ressources**.
3. Dans le panneau **Ressources**, cliquez sur **sqlserver-0**, qui correspond au nom de l’une des machines virtuelles exécutant SQL Server.
4. Dans le panneau de **sqlserver-0**, cliquez sur **Connecter**. Votre navigateur vous demande si vous voulez ouvrir ou enregistrer l’objet de connexion à distance. Cliquez sur **Ouvrir**.
5. La **connexion Bureau à distance** peut vous informer que le serveur de publication de cette connexion à distance n’est pas identifiable. Cliquez sur **Connecter**.
6. La sécurité de Windows vous invite à entrer vos informations d’identification pour vous connecter à l’adresse IP du contrôleur de domaine principal. Cliquez sur **Utiliser un autre compte**. Pour **Nom d’utilisateur**, tapez **contoso\DomainAdmin**. Vous avez configuré ce compte lorsque vous avez défini le nom d’utilisateur de l’administrateur dans le modèle. Utilisez le mot de passe complexe que vous avez choisi quand vous avez configuré le modèle.
7. **Bureau à distance** peut vous avertir que l’ordinateur distant n’a pas pu être authentifié en raison de problèmes liés à son certificat de sécurité. Le nom du certificat de sécurité vous est indiqué. Si vous avez suivi le didacticiel, ce nom est **sqlserver-0.contoso.com**. Cliquez sur **Oui**.

Vous êtes maintenant connecté avec RDP à la machine virtuelle SQL Server. Vous pouvez ouvrir SQL Server Management Studio, vous connecter à l’instance par défaut de SQL Server et vérifier que le groupe de disponibilité est configuré.
