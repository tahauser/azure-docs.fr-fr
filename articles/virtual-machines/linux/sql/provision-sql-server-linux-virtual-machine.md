---
title: "Créer une machine virtuelle Linux SQL Server 2017 dans Azure | Microsoft Docs"
description: "Ce didacticiel montre comment créer une machine virtuelle Linux SQL Server 2017 dans le portail Azure."
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/25/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 4105e0b4038f5dc09c503ac90ba7ad67c2fd93b8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Approvisionnement d’une machine virtuelle Linux SQL Server dans le portail Azure

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Dans ce guide de démarrage rapide, vous utilisez le portail Azure pour créer une machine virtuelle Linux dans laquelle est installée SQL Server 2017.

Ce didacticiel présente les procédures suivantes :

* [Créer une machine virtuelle Linux SQL à partir de la galerie](#create)
* [Se connecter à la nouvelle machine virtuelle avec SSH](#connect)
* [Modifier le mot de passe d’administrateur](#password)
* [Configurer des connexions à distance](#remote)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a id="create"></a> Créer une machine virtuelle Linux dans laquelle est installée SQL Server

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Dans le volet de gauche, cliquez sur **Créer une ressource**.

1. Dans le panneau **Créer une ressource**, cliquez sur **Calcul**.

1. Cliquez sur **See all** (Afficher tout) à côté du titre **Recommandés**.

   ![Afficher toutes les images de machines virtuelles](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Dans la zone de recherche, tapez **SQL Server 2017**, puis appuyez sur **Entrée** pour démarrer la recherche.

1. Cliquez sur l’icône **Filtre**, limitez la recherche aux images **Selon Linux** et **Microsoft**, puis cliquez sur **Terminé**.

    ![En filtrant, chercher les images de machines virtuelles SQL Server 2017](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Sélectionnez une image Linux SQL Server 2017 dans les résultats de la recherche. Ce didacticiel utilise **Free SQL Server License: SQL Server 2017 Developer on Red Hat Enterprise Linux 7.4**(Licence gratuite SQL Server : SQL Server 2017 Developer sur Linux Red Hat Enterprise 7.4).

   > [!TIP]
   > L’édition Developer (Développeur) vous permet de tester ou développer avec les fonctionnalités de l’édition Enterprise (Entreprise) sans coûts liés à une licence SQL Server. Vous payez uniquement pour le coût d’exécution de la machine virtuelle Linux.

1. Cliquez sur **Créer**.

1. Dans la fenêtre **Bases**, renseignez les détails de votre machine virtuelle Linux. 

    ![Fenêtre Bases](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Vous pouvez choisir d’utiliser une clé publique SSH ou un mot de passe pour l’authentification. L’utilisation d’une clé SSH est plus sécurisée. Pour savoir comment générer une clé SSH, consultez [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Cliquez sur **OK**.

1. Dans la fenêtre **Taille**, choisissez une taille pour votre machine. Pour voir d’autres tailles, sélectionnez **Afficher tout**. Pour en savoir plus sur les tailles de machines virtuelles, consultez [Tailles de machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

    ![Choisir une taille de machine virtuelle](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Pour des tests fonctionnels et de développement, nous vous recommandons une taille de machine virtuelle **DS2** ou supérieure. Pour des tests de performances, utilisez **DS13** ou supérieure.

1. Cliquez sur **Sélectionner**.

1. Dans la fenêtre **Paramètres**, vous pouvez effectuer des modifications des paramètres ou conserver ceux par défaut.

1. Cliquez sur **OK**.

1. Sur la page **Récapitulatif**, cliquez sur **Acheter** pour créer la machine virtuelle.

## <a id="connect"></a> Se connecter à la machine virtuelle Linux

Si vous utilisez déjà un interpréteur de commandes BASH, connectez-vous à la machine virtuelle Azure à l’aide de la commande **SSH**. Dans la commande suivante, remplacez le nom d’utilisateur et l’adresse IP de la machine virtuelle pour vous connecter à votre machine virtuelle Linux.

```bash
ssh azureadmin@40.55.55.555
```

Vous pouvez trouver l’adresse IP de votre machine virtuelle dans le portail Azure.

![Adresse IP dans le portail Azure](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Si vous êtes sous Windows et que vous n’avez pas d’interpréteur de commandes BASH, vous pouvez installer un client SSH , tel que PuTTY.

1. [Téléchargez et installez PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Exécutez PuTTY.

1. À l’écran de configuration de PuTTY, entrez l’adresse IP publique de votre machine virtuelle.

1. Cliquez sur Ouvrir et entrez le nom d’utilisateur et le mot de passe lorsque vous y êtes invité.

Pour en savoir plus la connexion aux machines virtuelles Linux, consultez [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm).

## <a id="password"></a> Modifier le mot de passe d’administrateur

La nouvelle machine virtuelle installe SQL Server avec un mot de passe d’administrateur aléatoire. Vous devez réinitialiser ce mot de passe pour pouvoir vous connecter à SQL Server avec les informations d’administrateur.

1. Une fois connecté à votre machine virtuelle Linux, ouvrez un nouveau terminal de commande.

1. Modifiez le mot de passe d’administrateur avec les commandes suivantes :

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Entrez un nouveau mot de passe d’administrateur et confirmez-le lorsque vous y êtes invité.

1. Redémarrez le service SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Ajoutez les outils à votre chemin (facultatif)

Plusieurs [packages](sql-server-linux-virtual-machines-overview.md#packages) SQL Server sont installés par défaut, y compris le package d’outils de lignes de commandes SQL Server. Le package contient les outils **sqlcmd** et **bcp**. Pour des questions pratiques, vous pouvez ajouter le chemin des outils, `/opt/mssql-tools/bin/`, à la variable d’environnement **CHEMIN** (facultatif).

1. Exécutez les commandes suivantes pour modifier le **CHEMIN** des sessions de connexion et des sessions interactives :

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Configurer des connexions à distance

Si vous avez besoin de vous connecter à distance à SQL Server sur la machine virtuelle Azure, vous devez configurer une règle entrante sur le groupe de sécurité réseau. La règle autorise le trafic sur le port qu’écoute SQL Server (port 1433 par défaut). Les étapes suivantes montrent comment utiliser le portail Azure. 

1. Dans le portail, sélectionnez **Machines virtuelles**, puis sélectionnez votre machine virtuelle SQL Server.

1. Dans la liste des propriétés, sélectionnez **Mise en réseau**.

1. Dans la fenêtre **Mise en réseau**, cliquez sur le bouton **Ajouter** sous **Règles des ports d’entrée**.

   ![Règles des ports d’entrée](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. Dans la liste **Service**, sélectionnez **MS SQL**.

    ![Règle de groupe de sécurité réseau MS SQL](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Cliquez sur **OK** pour enregistrer la règle pour votre machine virtuelle.

### <a name="open-the-firewall-on-rhel"></a>Ouvrez les ports du pare-feu sur RHEL

Ce didacticiel vous a montré comment créer une machine virtuelle Linux Red Hat Enterprise (RHEL). Si vous voulez vous connecter à distance aux machines virtuelles RHEL, vous devez aussi ouvrir le port 1433 du pare-feu Linux.

1. [Connectez-vous](#connect) à votre machine virtuelle RHEL.

1. Dans l’interpréteur de commandes BASH, exécutez les commandes suivantes :

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une machine virtuelle SQL Server 2017  dans Azure, vous pouvez vous connecter en local avec **sqlcmd** pour exécuter des requêtes Transact-SQL.

Si vous avez configuré la connexion à distance de SQL Server à la machine virtuelle Azure, vous devriez pourquoi vous connecter à distance. Si vous voulez un exemple de connexion à distance à SQL Server sur Linux ou Windows, consultez [Utiliser SSMS sur Windows pour se connecter à SQL Server sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Pour vous connecter avec Visual Studio Code, consultez [Utilisez Visual Studio Code pour créer et exécuter des scripts Transact-SQL pour SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Pour en savoir plus sur SQL Server sur Linux, consultez la [Vue d’ensemble de SQL Server 2017  sur Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Pour en savoir plus sur l’utilisation de machines virtuelles Azure Linux SQL Server 2017 , consultez [Vue d’ensemble des machines virtuelles SQL Server 2017 sur Azure](sql-server-linux-virtual-machines-overview.md).
