---
title: Configurer et consulter les journaux du serveur pour Azure Database pour MySQL dans le portail Azure
description: "Cet article décrit comment configurer et consulter les journaux du serveur dans Azure Database pour MySQL à partir du portail Azure."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b68d92688b18e69f74c0e0a9b2e572795a288579
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurer et consulter les journaux du serveur dans le portail Azure

Vous pouvez configurer, répertorier et télécharger les [journaux du serveur Azure Database pour MySQL](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès au journal des requêtes lentes de MySQL. 

1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database pour MySQL.

3. Dans la section **Surveillance** dans la barre latérale, sélectionnez **Journaux du serveur**. 
   ![Sélectionnez Journaux du serveur, cliquez pour configurer](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Sélectionnez le titre **Cliquez ici pour activer les journaux et configurer les paramètres associés** pour afficher les paramètres du serveur.

5. Modifiez les paramètres que vous devez ajuster. Toutes les modifications que vous apportez dans cette session sont surlignées en violet. 

   Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.

   ![Cliquez sur Enregistrer ou Abandonner](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Revenez à la liste des journaux en cliquant sur le **bouton Fermer** (icône de X) dans la page **Paramètres serveur**.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux
Une fois que la journalisation commence, vous pouvez afficher la liste des journaux disponibles et télécharger des fichiers journaux dans le volet Journaux du serveur. 

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database pour MySQL.

3. Dans la section **Surveillance** dans la barre latérale, sélectionnez **Journaux du serveur**. La page affiche la liste de vos fichiers journaux, comme illustré :

   ![Liste des journaux](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convention d’affectation des noms de journaux est **mysql-slow-<nom de votre serveur>-aaaammjjhh.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment d’émission du journal. Les fichiers journaux font l’objet d’une rotation toutes les 24 heures ou une fois atteint le volume de 7,5 Go, selon ce qui se produit en premier.

4. Si nécessaire, utilisez la **zone de recherche** pour circonscrire rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

5. Téléchargez des fichiers journaux à l’aide du bouton **Télécharger** (icône de flèche bas) en regard de chaque fichier journal figurant dans la ligne de table, comme illustré :

   ![Cliquez sur l’icône Télécharger](./media/howto-configure-server-logs-in-portal/5-download.png)


## <a name="next-steps"></a>Étapes suivantes
- Pour découvrir comment télécharger des journaux par programme, voir [Accéder aux journaux du serveur à l’aide de l’interface de ligne de commande](howto-configure-server-logs-in-cli.md).
- Apprenez-en davantage sur les [Journaux du serveur](concepts-server-logs.md) dans Azure Database pour MySQL. 
- Pour plus d’informations sur les définitions de paramètres et la journalisation de MySQL, voir la documentation MySQL sur les [Journaux](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

