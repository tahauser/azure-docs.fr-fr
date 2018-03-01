---
title: "Création et gestion d’un serveur Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Cet article explique comment créer rapidement un nouveau serveur Azure Database pour MySQL et gérer le serveur à l’aide du portail Azure."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: b73fe2214a165d7c02c0a58551d8b84bee39f919
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Création et gestion d’un serveur Azure Database pour MySQL à l’aide du portail Azure
Cette rubrique explique comment créer rapidement un nouveau serveur Azure Database pour MySQL. Il comprend également des informations sur la gestion du serveur à l’aide du Portail Azure, notamment l’affichage des détails du serveur et des bases de données, la réinitialisation de mot de passe et la suppression du serveur.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Suivez ces étapes pour créer un serveur Azure Database pour MySQL nommé « mysqlserver4demo ».

1. Cliquez sur le bouton **Créer une ressource** dans le coin supérieur gauche du portail Azure.

2. Sur la page Nouveau, sélectionnez **Bases de données**, puis, sur la page Bases de données, sélectionnez **Azure Database pour MySQL**.

    > Un serveur Azure Database pour MySQL est créé avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-unit-and-storage.md). La base de données est créée dans un groupe de ressources Azure et dans un serveur Azure Database pour MySQL.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Remplissez le formulaire Azure Database pour MySQL avec les informations suivantes :

    | **Champ de formulaire** | **Description du champ** |
    |----------------|-----------------------|
    | *Nom du serveur* | azure-mysql (le nom du serveur est globalement unique) |
    | *Abonnement* | MySQLaaS (sélectionnez dans le menu déroulant) |
    | *Groupe de ressources* | myresource (créez un nouveau groupe de ressources ou utilisez un groupe existant) |
    | *Connexion d’administrateur serveur* | myadmin (nom du compte administrateur de l’installation) |
    | *Mot de passe* | mot de passe du compte administrateur de l’installation |
    | *Confirmer le mot de passe* | confirmez le mot de passe du compte administrateur |
    | *Emplacement* | Europe du Nord (sélection Europe du Nord ou États-Unis de l'Ouest) |
    | *Version* | 5.6 (choisissez la version du serveur Azure Database pour MySQL) |

4. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances de votre nouveau serveur. L’unité de calcul peut être configurée entre 50 et 100 dans le niveau De base et entre 100 et 200 dans le niveau Standard ; vous pouvez augmenter le stockage en fonction de la quantité incluse. Dans le cadre de ce guide pratique, choisissons 50 unités de calcul et 50 Go. Cliquez sur **OK** pour enregistrer votre sélection.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Cliquez sur **Créer** pour approvisionner le serveur. L’approvisionnement prend quelques minutes.

    > Sélectionnez l’option **Épingler au tableau de bord** pour faciliter le suivi de vos déploiements.
    > [!NOTE]
    > Bien que les niveaux De base et Standard prennent en charge jusqu’à 1 000 Go et 10 000 Go de stockage respectivement, le stockage maximal de la Préversion publique reste temporairement limité à 1 000 Go.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Mise à jour d’un serveur Azure Database pour MySQL
Une fois le nouveau serveur configuré, l’utilisateur a deux possibilités pour modifier un serveur existant : réinitialiser un mot de passe administrateur ou effectuer une montée/descente en puissance du serveur en modifiant les unités de calcul.

### <a name="change-the-administrator-user-password"></a>Modification du mot de passe administrateur
1. Dans le panneau **Vue d’ensemble** du serveur, cliquez sur **Réinitialiser le mot de passe** pour afficher une fenêtre de saisie puis de confirmation du mot de passe.

2. Entrez puis confirmez le nouveau mot de passe dans la fenêtre, de la façon suivante :

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Cliquez sur **OK** pour enregistrer le nouveau mot de passe.

### <a name="scale-updown-by-changing-compute-units"></a>Montée/descente en puissance en modifiant les unités de calcul

1. Dans le panneau du serveur, sous **Paramètres**, cliquez sur **Niveau tarifaire** pour ouvrir le panneau Niveau tarifaire du serveur Azure Database pour MySQL.

2. Suivez l’étape 4 de la rubrique **Créer un serveur Azure Database pour MySQL** pour modifier les unités de calcul dans le même niveau tarifaire.

## <a name="delete-an-azure-database-for-mysql-server"></a>Suppression d’un serveur Azure Database pour MySQL

1. Dans le panneau **Vue d’ensemble** du serveur, cliquez sur le bouton de commande **Supprimer** pour ouvrir le panneau Confirmation de suppression.

2. Tapez le nom du serveur dans la zone de saisie du panneau afin de redoubler la confirmation.

3. Cliquez de nouveau sur le bouton **Supprimer** pour confirmer l’action de suppression, puis attendez l’affichage du message « Suppression réussie » dans la barre de notification.

## <a name="list-the-azure-database-for-mysql-databases"></a>Liste des bases de données Azure pour MySQL
Dans le panneau **Vue d’ensemble** du serveur, descendez jusqu’à la vignette des bases de données. Toutes les bases de données apparaissent dans le tableau. Cliquez sur le bouton de commande **Supprimer** pour ouvrir le panneau Confirmation de suppression.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Affichage des détails d’un serveur Azure Database pour MySQL
Dans le panneau du serveur, sous **Paramètres**, cliquez sur **Propriétés** pour ouvrir le panneau **Propriétés**, puis affichez toutes les informations détaillées sur le serveur.

## <a name="next-steps"></a>étapes suivantes

[Démarrage rapide : création d’un serveur Azure Database pour MySQL à l’aide du portail Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)