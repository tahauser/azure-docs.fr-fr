---
title: Configurer et consulter les journaux du serveur pour PostgreSQL dans le portail Azure | Microsoft Docs
description: "Cet article décrit comment configurer et consulter les journaux du serveur dans Azure Database pour PostgreSQL à partir du portail Azure."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: a2f67b21293a1a0456b27cad9043be01fdd5274a
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="configure-and-access-server-logs-in-the-azure-portal"></a>Configurer et consulter les journaux du serveur dans le portail Azure

Vous pouvez configurer, répertorier et télécharger les [journaux du serveur Azure Database pour PostgreSQL](concepts-server-logs.md) à partir du portail Azure.

## <a name="prerequisites"></a>Conditions préalables
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Un serveur Azure Database pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Configuration de la journalisation
Configurer l’accès aux journaux des requêtes et journaux des erreurs. 

1. Connectez-vous au [Portail Azure](http://portal.azure.com/).

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Dans la section **Surveillance** dans la barre latérale, sélectionnez **Journaux du serveur**. 

   ![Sélectionnez les journaux du serveur, puis « Cliquez ici pour activer... ».](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Sélectionnez le titre **Cliquez ici pour activer les journaux et configurer les paramètres associés** pour afficher les paramètres du serveur.

5. Sélectionnez le développeur de vue **Afficher plus** pour voir la liste étendue des paramètres disponibles. 

   Pour plus d’informations sur les définitions de paramètres, consultez la documentation de PostgreSQL sur le [signalement et la journalisation des erreurs](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

   ![Liste courte des paramètres du journal. Cliquez sur Afficher plus pour voir la liste longue](./media/howto-configure-server-logs-in-portal/2-show-more.png)

6. Modifiez les paramètres que vous devez ajuster. Toutes les modifications que vous apportez dans cette session sont surlignées en violet.

   Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications. 

   ![Liste longue des paramètres avec les modifications à enregistrer ou à abandonner](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

7. Revenez à la liste des journaux en cliquant sur le **bouton Fermer** (icône de X) dans la page **Paramètres serveur**.

## <a name="view-list-and-download-logs"></a>Afficher la liste et télécharger les journaux
Une fois que la journalisation commence, vous pouvez afficher la liste des journaux disponibles et télécharger des fichiers journaux dans le volet Journaux du serveur. 

1. Ouvrez le portail Azure.

2. Sélectionnez votre serveur Azure Database pour PostgreSQL.

3. Dans la section **Surveillance** dans la barre latérale, sélectionnez **Journaux du serveur**. La page affiche la liste de vos fichiers journaux, comme illustré :

   ![Liste des journaux du serveur](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convention d’affectation des noms de journaux est **postgresql-aaaa-mm-dd_hh0000.log**. La date et l’heure utilisées dans le nom de fichier correspondent au moment d’émission du journal. Les fichiers journaux font l’objet d’une rotation toutes les heures ou par tranche de 100 Mo, selon la limite atteinte en premier.

4. Si nécessaire, utilisez la **zone de recherche** pour circonscrire rapidement un journal spécifique en fonction d’une date et d’une heure. La recherche est effectuée sur le nom du journal.

   ![Exemple de recherche sur les noms de journaux](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Téléchargez des fichiers journaux à l’aide du bouton **Télécharger** (icône de flèche bas) en regard de chaque fichier journal figurant dans la ligne de table, comme illustré :

   ![Cliquez sur l’icône Télécharger](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>étapes suivantes
- Pour découvrir comment télécharger des journaux par programme, voir [Accéder aux journaux du serveur à l’aide de l’interface de ligne de commande](howto-configure-server-logs-using-cli.md).
- Apprenez-en davantage sur les [Journaux du serveur](concepts-server-logs.md) dans Azure Database pour PostgreSQL. 
- Pour plus d’informations sur les définitions de paramètres et la journalisation de PostgreSQL, voir la documentation de PostgreSQL concernant [le signalement et la journalisation des erreurs](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

