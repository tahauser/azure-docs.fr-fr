---
title: "Démarrage rapide : création d’un serveur Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Cet article vous guide dans le portail Azure pour créer rapidement un exemple de serveur Azure Database pour MySQL en environ cinq minutes."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/01/2017
ms.openlocfilehash: 88fd06d45d0cd966199ce198578eedd349adc2c8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure
Azure Database pour MySQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer en quelques minutes un serveur Azure Database pour MySQL à l’aide du portail Azure.  

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Ouvrez votre navigateur Web, puis accédez au [portail Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Vous créez un serveur Azure Database pour MySQL avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-unit-and-storage.md). Vous créez ce serveur dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Pour créer un serveur de base de données Azure pour MySQL, suivez les étapes ci-après :

1. Cliquez sur le bouton **Créer une ressource** (+) dans le coin supérieur gauche du portail.

2. Sélectionnez **Bases de données** > **Azure Database pour MySQL**. Vous pouvez également taper **MySQL** dans la zone de recherche pour localiser le service.

    ![Options Azure Database pour MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

4. Renseignez le formulaire du nouveau serveur avec les informations suivantes, comme indiqué dans l’illustration précédente :

    **Paramètre** | **Valeur suggérée** | **Description du champ** 
    ---|---|---
    Nom du serveur | Nom de serveur unique | Choisissez un nom unique qui identifie votre serveur de base de données Azure pour MySQL. Par exemple, myserver4demo. Le nom de domaine *mysql.database.azure.com* est ajouté au nom de serveur que vous fournissez. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
    Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, sélectionnez l’abonnement dans lequel la ressource est facturée.
    Groupe de ressources | myResourceGroup | Spécifiez un nom de groupe de ressources nouveau ou existant.
    Connexion d’administrateur serveur | myadmin | Un compte de connexion à utiliser lors de la connexion au serveur. Le nom de connexion d’administrateur ne doit pas être **azure_superuser**, **admin**, **administrator**, **root**, **guest** ou **public**.
    Mot de passe | *Votre choix* | Spécifiez un mot de passe pour le compte Administrateur du serveur. Il doit inclure entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères appartenant à trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
    Confirmer le mot de passe | *Votre choix*| Confirmez le mot de passe du compte d’administrateur.
    Lieu | *La région la plus proche de vos utilisateurs*| Choisissez l’emplacement le plus proche de vos utilisateurs ou d’autres applications Azure.
    Version | *La version la plus récente*| La version la plus récente (sauf si vous avez des exigences spécifiques).
    Niveau tarifaire | **De base**, **50 unités de calcul**, **50 Go** | Choisissez le niveau de service et celui des performances de votre nouvelle base de données. Sélectionnez le **niveau De base** dans l’onglet figurant dans la partie supérieure. Sélectionnez l’extrémité gauche du curseur **Unités de calcul** pour reconfigurer la valeur sur le plus petit nombre d’unités disponible pour ce guide de démarrage rapide. Sélectionnez **OK** pour enregistrer la sélection du niveau tarifaire. Pour plus d’informations, consultez la capture d’écran suivante.
    Épingler au tableau de bord | Vérification | Facilite le suivi de votre serveur sur la première page du tableau de bord de votre portail Azure.

    > [!IMPORTANT]
    > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.
    > 

    ![Portail Azure - Créer MySQL en fournissant l’entrée de formulaire requise](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Sélectionnez **Créer** pour approvisionner le serveur. L’approvisionnement peut durer jusqu’à 20 minutes.
   
5.  Dans la barre d’outils, sélectionnez **Notifications** (icône de cloche) pour surveiller le processus de déploiement.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Base de données Azure pour MySQL crée un pare-feu au niveau du serveur. Il empêche les applications et les outils externes de se connecter au serveur et à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. 

1.   Après le déploiement, localisez votre serveur. Si nécessaire, vous pouvez le rechercher. Par exemple, sélectionnez **Toutes les ressources** dans le menu de gauche. Tapez ensuite le nom du serveur, tel que **myserver4demo**, pour rechercher le serveur que vous venez de créer. Sélectionnez le nom du serveur dans la liste des résultats. La page **Présentation** correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

2. Sur la page du serveur, sélectionnez **Sécurité de la connexion**.

3.  Sous le titre **Règles de pare-feu**, sélectionnez la zone de texte vide de la colonne **Nom de la règle** pour commencer à créer la règle de pare-feu. 

   ![Sécurité de connexion : règles de pare-feu](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

   Pour ce guide de démarrage rapide, autorisons toutes les adresses IP sur le serveur en remplissant les zones de chaque colonne avec les valeurs suivantes :

   Nom de la règle | Adresse IP de début | Adresse IP de fin 
   ---|---|---
   AllowAllIps |  0.0.0.0 | 255.255.255.255
   
   Autoriser toutes les adresses IP n’est pas une méthode sécurisée. Cet exemple est fourni par souci de simplicité, mais dans un scénario réel, vous devez connaître les plages d’adresses IP précis à ajouter pour vos applications et utilisateurs. 

4. Dans la barre d’outils supérieure de la page **Sécurité de la connexion**, sélectionnez **Enregistrer**. Avant de continuer, attendez la notification indiquant que la mise à jour a été faite avec succès. 

   > [!NOTE]
   > Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
   > 

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion
Pour vous connecter à votre serveur de base de données, il vous faut le nom de serveur complet et les informations d’identification de connexion d’administrateur. Il est possible que vous ayez noté ces valeurs précédemment dans l’article relatif au guide de démarrage rapide. Si vous ne l’avez pas fait, vous pouvez facilement localiser le nom du serveur et les informations de connexion sur la page **Vue d’ensemble** ou sur la page **Propriétés** du serveur sur le portail Azure.

Pour ce faire, effectuez les étapes suivantes : 

1. Ouvrez la page **Vue d’ensemble** de votre serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**. 

2. Placez le curseur sur chaque champ afin de faire apparaître l’icône de copie à droite du texte. Sélectionnez l’icône de copie appropriée pour copier les valeurs qui vous intéressent.

Dans cet exemple, le nom du serveur est **myserver4demo.mysql.database.azure.com**, et la connexion d’administrateur du serveur est **myadmin@myserver4demo**.

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Se connecter à MySQL avec l’outil de ligne de commande mysql
Vous pouvez utiliser différentes applications pour vous connecter à votre serveur de base de données Azure pour MySQL. 

Commençons par utiliser l’outil de ligne de commande [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) pour illustrer la procédure de connexion au serveur. Vous pouvez aussi utiliser un navigateur web et Azure Cloud Shell comme décrit dans cet article sans avoir besoin d’installer d’autres logiciels. Si vous avez installé l’utilitaire mysql en local, vous pouvez également vous y connecter depuis cet emplacement.

1. Lancez Azure Cloud Shell via l’icône de la console (**>_**) située dans le coin supérieur droit du portail Azure.

2.  Azure Cloud Shell s’ouvre dans votre navigateur pour vous permettre de taper des commandes de l’interpréteur de commandes bash.

    ![Invite de commandes - Exemple de ligne de commande mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur Azure Database pour MySQL en tapant la ligne de commande mysql.

    Pour vous connecter à un serveur Azure Database pour MySQL avec l’utilitaire mysql, utilisez le format suivant :

    ```bash
    mysql --host <fully qualified servername> --user <serveradminlogin@servername> -p
    ```

    Par exemple, la commande ci-après permet de se connecter au serveur de notre exemple :

    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo -p
    ```

    Paramètre mysql |Valeur suggérée|DESCRIPTION
    ---|---|---
    --host | *Nom du serveur* | Spécifiez la valeur de nom de serveur utilisée lorsque vous avez créé la base de données Azure pour MySQL. Le serveur que nous utilisons dans notre exemple est **myserver4demo.mysql.database.azure.com**. Utilisez le nom de domaine complet (**\*.mysql.database.azure.com**), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion. 
    --user | *Nom de connexion d’administrateur du serveur* |Le nom d’utilisateur de connexion d’administrateur du serveur fourni lorsque vous avez créé le serveur Azure Database pour MySQL. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    -p | *Patienter jusqu’à être invité* |Lorsque vous y êtes invité, spécifiez le mot de passe que vous avez fourni lorsque vous avez créé le serveur. Notez que les caractères du mot de passe que vous tapez ne sont pas visibles au niveau de l’invite bash. Après avoir entré le mot de passe, sélectionnez **Entrer**.

   Une fois connecté, l’utilitaire mysql affiche une invite `mysql>` dans laquelle vous pouvez taper des commandes. 

   Voici un exemple de sortie mysql :

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Si le pare-feu n’est pas configuré pour autoriser l’adresse IP d’Azure Cloud Shell, l’erreur suivante se produit :
    >
    > ERROR 2003 (28000) : Le client avec l’adresse IP 123.456.789.0 n’est pas autorisé à accéder au serveur.
    >
    > Pour résoudre l’erreur, veillez à ce que la configuration du serveur corresponde à celle détaillée dans la section « Configurer une règle de pare-feu au niveau du serveur » de l’article.

4. Pour s’assurer que la connexion est opérationnelle, affichez le statut du serveur en tapant `status` dans l’invite mysql.

    ```sql
    status
    ```

   > [!TIP]
   > Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Créez une base de données vide à l’invite **mysql** en tapant la commande suivante :
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    La commande peut prendre quelques instants. 

    Dans un serveur Azure Database pour MySQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Il n’existe aucune limite au nombre de bases de données que vous pouvez créer, mais plusieurs bases de données partagent les mêmes ressources de serveur. 

6. Répertoriez les bases de données à l’invite **mysql** en tapant la commande suivante :

    ```sql
    SHOW DATABASES;
    ```

7.  Tapez `\q`, puis sélectionnez la clé **Entrer** pour quitter l’outil mysql. Une fois terminé, vous pouvez fermer Azure Cloud Shell.

Vous venez de vous connecter au serveur Azure Database pour MySQL et de créer une base de données utilisateur vide. Passez à la section suivante pour effectuer un exercice similaire. L’exercice suivant montre comment se connecter au même serveur à l’aide d’un autre outil commun, MySQL Workbench.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
Pour vous connecter au serveur à l’aide de l’outil MySQL Workbench GUI, procédez comme suit :

1.  Ouvrez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir de la page [Télécharger MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

2. Créez une nouvelle connexion. Cliquez sur le signe plus (+) à côté du titre **Connexions MySQL**.

3. Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations de votre connexion au serveur dans l’onglet **Paramètres**. Les valeurs d’espace réservé sont affichées à titre d’exemple. Remplacez le nom d’hôte, le nom d’utilisateur et le mot de passe avec vos propres valeurs.

    ![Configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Paramètre |Valeur suggérée|Description du champ|
    |---|---|---|
     Nom de connexion | Connexion démo | Une étiquette pour cette connexion. |
    Méthode de connexion | Standard (TCP/IP) | Standard (TCP/IP) est suffisant. |
    Nom d’hôte | *Nom du serveur* | La valeur de nom de serveur utilisée lorsque vous avez créé le serveur Azure Database pour MySQL. Le serveur que nous utilisons dans notre exemple est **myserver4demo.mysql.database.azure.com**. Utilisez le nom de domaine complet (**\*.mysql.database.azure.com**), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion.|
     Port | 3306 | Le port à utiliser lors de la connexion au serveur Azure Database for MySQL. |
    Nom d’utilisateur |  *Nom de connexion d’administrateur du serveur* | Les informations de connexion d’administrateur du serveur fourni lorsque vous avez créé le serveur Azure Database pour MySQL. Le nom d’utilisateur dans notre exemple est **myadmin@myserver4demo**. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    Mot de passe | *Votre mot de passe* | Sélectionnez le bouton **Stocker dans le coffre-fort…** pour enregistrer le mot de passe. |

4. Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés. Sélectionnez ensuite **OK** pour enregistrer la connexion. 

    > [!NOTE]
    > Le protocole SSL est appliqué par défaut sur votre serveur et requiert une configuration supplémentaire afin de vous connecter avec succès. Pour plus d’informations, consultez l’article [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md). Pour désactiver le protocole SSL dans ce guide de démarrage rapide, rendez-vous sur le portail Azure. Sélectionnez ensuite la page Sécurité de la connexion pour désactiver le bouton **Appliquer le protocole SSL**.

## <a name="clean-up-resources"></a>Supprimer des ressources
Vous disposez de deux moyens de supprimer les ressources que vous avez créées dans le guide de démarrage rapide. Vous pouvez supprimer le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md), qui inclut toutes les ressources du groupe de ressources. Si vous souhaitez conserver les autres ressources, supprimez uniquement la ressource d’un serveur.

> [!TIP]
> Les autres guides de démarrage rapide de cette collection reposent sur ce guide. Si vous souhaitez continuer à utiliser d’autres guides de démarrages rapides, ne nettoyez pas les ressources créées au cours de ce guide. Sinon, procédez comme suit pour supprimer toutes les ressources que vous avez créées avec ce guide de démarrage rapide.
>

Pour supprimer l’intégralité du groupe de ressources, y compris le serveur que vous venez de créer, procédez comme suit :

1.  Localisez votre groupe de ressources dans le portail Azure. Dans le menu de gauche, sélectionnez **Groupes de ressources**, puis le nom du groupe de ressources, **myresourcegroup** dans notre exemple.

2.  Sur la page de votre groupe de ressources, sélectionnez **Supprimer**. Tapez ensuite le nom de votre groupe de ressources, **myresourcegroup** dans notre exemple, dans la zone pour confirmer la suppression, et sélectionnez **Supprimer**.

Pour supprimer uniquement le serveur que vous venez de créer, procédez comme suit :

1.  Localisez votre serveur dans le portail Azure, s’il n’est pas déjà ouvert. Cliquez sur **Toutes les ressources** dans le menu de gauche du portail Azure. Recherchez ensuite le serveur que vous avez créé.

2.  Sur la page **Vue d’ensemble**, sélectionnez **Supprimer**. 

    ![Azure Database pour MySQL : Supprimer le serveur](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3.  Vérifiez le nom du serveur à supprimer et affichez les bases de données affectées situées sous celui-ci. Tapez votre nom de serveur dans la zone, **myserver4demo** dans notre exemple. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [Concevoir votre première base de données Azure pour MySQL](./tutorial-design-database-using-portal.md)

