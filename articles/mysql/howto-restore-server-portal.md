---
title: Comment restaurer un serveur dans Azure Database pour MySQL
description: "Cet article décrit comment restaurer un serveur Azure Database pour MySQL à l’aide du portail."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 5bef3f11d0b546fbd6b1161b20d7dfb81e975f99
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Guide pratique pour sauvegarder et restaurer un serveur Azure Database pour MySQL à l’aide du portail Azure

## <a name="backup-happens-automatically"></a>La sauvegarde s’effectue automatiquement
Les serveurs Azure Database pour MySQL sont sauvegardés régulièrement pour activer les fonctionnalités de restauration. À l’aide de cette fonctionnalité, vous pouvez restaurer le serveur et toutes ses bases de données à un point dans le temps antérieur, sur un nouveau serveur.

## <a name="prerequisites"></a>Prérequis
Pour utiliser ce guide pratique, il vous faut :
- Un [serveur et une base de données Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Définir la configuration de sauvegarde

Vous choisissez entre la configuration de votre serveur pour des sauvegardes redondantes localement ou géographiquement redondantes lors de la création du serveur, dans la fenêtre **Niveau tarifaire**.

> [!NOTE]
> Après la création d’un serveur, son type de redondance (géographique ou locale) ne peut pas être modifié.
>

Lors de la création d’un serveur via le portail Azure, la fenêtre **Niveau tarifaire** vous permet de sélectionner **Localement redondant** ou **Géographiquement redondant** pour les sauvegardes de votre serveur. Cette fenêtre vous permet également de sélectionner la **période de rétention de la sauvegarde**, c’est-à-dire la durée (en nombre de jours) pendant laquelle vous souhaitez conserver les sauvegardes de serveur.

   ![Niveau tarifaire - Choisir la redondance de sauvegarde](./media/howto-restore-server-portal/pricing-tier.png)

Pour plus d’informations sur la définition de ces valeurs lors de la création, consultez [Démarrage rapide du serveur Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

La période de rétention de sauvegarde peut être modifiée sur un serveur en suivant les étapes ci-après :
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez votre serveur Azure Database pour MySQL. Cette action ouvre la page **Vue d’ensemble**.
3. Sélectionnez **Niveau tarifaire** dans le menu, sous **PARAMÈTRES**. Le curseur vous permet de modifier la **période de rétention de sauvegarde** selon vos préférences entre 7 et 35 jours.
Dans la capture d’écran ci-dessous, elle a été augmentée à 34 jours.
![Période de rétention de sauvegarde augmentée](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. Cliquez sur **OK** pour confirmer la modification.

La période de rétention de sauvegarde détermine jusqu’à quelle date une restauration à un point dans le temps peut être récupérée, dans la mesure où elle est basée sur les sauvegardes disponibles. La restauration à un point dans le temps est décrite plus loin dans la section suivante. 

## <a name="point-in-time-restore-in-the-azure-portal"></a>Restauration à un point dans le temps dans le portail Azure
Azure Database pour MySQL vous permet de restaurer le serveur à un moment donné et dans une nouvelle copie du serveur. Vous pouvez utiliser ce nouveau serveur pour récupérer vos données ou faire en sorte que vos applications client pointent vers ce nouveau serveur.

Par exemple, si une table a été accidentellement supprimée à midi aujourd'hui, vous pourrez restaurer à l’heure juste avant midi et retrouver la table et les données manquantes à partir de cette nouvelle copie du serveur. La restauration à un point dans le temps est effectuée au niveau du serveur, pas au niveau de la base de données.

Les étapes suivantes restaurent l’exemple de serveur à un point dans le temps :
1. Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL. 

2. Dans la barre d’outils de la page **Vue d’ensemble** du serveur, sélectionnez **Restaurer**.

   ![Azure Database pour MySQL - Vue d’ensemble - Bouton Restaurer](./media/howto-restore-server-portal/2-server.png)

3. Remplissez le formulaire Restaurer avec les informations requises :

   ![Azure Database pour MySQL - Informations de restauration ](./media/howto-restore-server-portal/3-restore.png)
  - **Point de restauration** : sélectionnez le point dans le temps vers lequel vous souhaitez restaurer.
  - **Serveur cible** : fournissez un nom pour le nouveau serveur.
  - **Emplacement :** vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source.
  - **Niveau tarifaire** : vous ne pouvez pas modifier ces paramètres lorsque vous effectuez une restauration à un point dans le temps. Elle est identique à celle du serveur source. 

4. Cliquez sur **OK** pour restaurer le serveur à un point dans le temps. 

5. Une fois la restauration terminée, recherchez le nouveau serveur créé pour vérifier que les données ont été restaurées correctement.

>[!Note]
>Notez que le serveur créé par la restauration à un point dans le temps a les mêmes nom de connexion administrateur de serveur et mot de passe qui étaient valides pour le serveur existant au point dans le temps choisi. Vous pouvez modifier le mot de passe sur la page **Vue d’ensemble** du nouveau serveur.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [sauvegardes](concepts-backup.md) du service.
- En savoir plus sur les options de [continuité d’activité](concepts-business-continuity.md).
