---
title: "Création et gestion des règles de pare-feu MySQL dans Azure Database pour MySQL | Microsoft Docs"
description: "Création et gestion des règles de pare-feu Azure Database pour MySQL à l’aide du portail Azure"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/27/2017
ms.openlocfilehash: 63ea6337b35193420924096690ed15cc1d5ede25
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide du Portail Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs d’accéder à un serveur Azure Database pour MySQL à partir d’une adresse IP ou d’une plage d’adresses IP spécifiée. 

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

1. Dans la page du serveur MySQL, sous le titre Paramètres, cliquez sur **Sécurité des connexions** pour ouvrir la page Sécurité des connexions pour Azure Database pour MySQL.

   ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Cliquez sur **Ajouter mon adresse IP** dans la barre d’outils pour créer une règle avec l’adresse IP de votre ordinateur, telle que détectée par le système Azure.

   ![Portail Azure - cliquez sur Ajouter mon adresse IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Vérifiez votre adresse IP avant d’enregistrer la configuration. Dans certains cas, l’adresse IP identifiée par le portail Azure diffère de l’adresse IP utilisée lors de l’accès à Internet et aux serveurs Azure. Par conséquent, vous devrez peut-être modifier l’adresse IP de début et l’adresse IP de fin pour que la règle fonctionne comme prévu.

   Utilisez un moteur de recherche ou tout autre outil en ligne pour vérifier votre adresse IP (par exemple, lancez une recherche « quelle est mon adresse IP »).

   ![Recherche Bing « quelle est mon adresse IP »](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Ajoutez des plages d’adresses supplémentaires. Dans les règles de pare-feu Azure Database pour MySQL, vous pouvez spécifier une seule adresse IP ou une plage d’adresses. Si vous souhaitez limiter la règle à une seule adresse IP, tapez la même adresse dans les champs d’adresse IP de début et d’adresse IP de fin. Ouvrir le pare-feu permet aux administrateurs et utilisateurs d’accéder à toute base de données sur le serveur MySQL pour laquelle ils disposent d’informations d’identification valides.

   ![Portail Azure - règles de pare-feu ](./media/howto-manage-firewall-using-portal/5-specify-addresses.png)


5. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur. Attendez la confirmation de la mise à jour des règles de pare-feu.

   ![Portail Azure - cliquez sur Enregistrer](./media/howto-manage-firewall-using-portal/4-save-firewall-rule.png)

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Gérer des règles de pare-feu existantes au niveau du serveur à l’aide du Portail Azure
Répétez les étapes pour gérer les règles de pare-feu.
* Pour ajouter l’ordinateur actuel, cliquez sur **Ajouter mon adresse IP**.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs **Nom de la règle**, **Adresse IP de début** et **Adresse IP de fin**.
* Pour modifier une règle existante, cliquez sur l’un de ses champs, puis modifiez-le.
* Pour supprimer une règle existante, cliquez sur les points de suspension [...], puis sur **Supprimer**.
* Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes
Pour vous aider à vous connecter à un serveur Azure Database pour MySQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour MySQL](./concepts-connection-libraries.md)
