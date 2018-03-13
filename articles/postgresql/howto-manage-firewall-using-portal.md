---
title: "Créer et gérer des règles de pare-feu dans Azure Database pour PostgreSQL"
description: "Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide du portail Azure"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: bef927cff49d957728a2a12362786d48d60e61b7
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide du portail Azure
Les règles de pare-feu au niveau du serveur permettent aux administrateurs d’accéder à un serveur Azure Database pour PostgreSQL à partir d’une adresse IP spécifiée ou d’une plage d’adresses IP. 

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un serveur [Création d’une base de données Azure pour PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure
1. Dans la page du serveur PostgreSQL, sous le titre Paramètres, cliquez sur **Sécurité des connexions** afin d’ouvrir la page correspondante pour Azure Database pour PostgreSQL.

  ![Portail Azure - cliquez sur Sécurité des connexions](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. Cliquez sur **Ajouter mon adresse IP** dans la barre d’outils. Cette opération crée automatiquement une règle de pare-feu avec l’adresse IP publique de votre ordinateur, telle que détectée par le système Azure.

  ![Portail Azure - cliquez sur Ajouter mon adresse IP](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. Vérifiez votre adresse IP avant d’enregistrer la configuration. Dans certains cas, l’adresse IP identifiée par le portail Azure diffère de l’adresse IP utilisée lors de l’accès à Internet et aux serveurs Azure. Par conséquent, vous devrez peut-être modifier l’adresse IP de début et l’adresse IP de fin pour que la règle fonctionne comme prévu.
Utilisez un moteur de recherche ou tout autre outil en ligne pour vérifier votre adresse IP (par exemple, lancez une recherche sur « quelle est mon adresse IP »).

  ![Recherche Bing « quelle est mon adresse IP »](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Ajoutez des plages d’adresses supplémentaires. Dans les règles de pare-feu d’Azure Database pour PostgreSQL, vous pouvez spécifier une seule adresse IP ou une plage d’adresses. Si vous souhaitez limiter la règle à une seule adresse IP, saisissez la même adresse dans les champs d’adresse IP de début et d’adresse IP de fin. Ouvrir le pare-feu permet aux administrateurs, utilisateurs et applications de se connecter à toute base de données sur le serveur PostgreSQL pour laquelle ils disposent d’informations d’identification valides.

  ![Portail Azure - règles de pare-feu ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur. Attendez la confirmation que la mise à jour des règles de pare-feu a réussi.

  ![Portail Azure - cliquez sur Enregistrer](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Connexion à partir d’Azure
Pour autoriser des applications d’Azure à se connecter à votre serveur Azure Database pour PostgreSQL, les connexions Azure doivent être activées. Par exemple, pour héberger une application Azure Web Apps ou une application qui s’exécute sur une machine virtuelle Azure, ou pour vous connecter à partir d’une passerelle de gestion des données Azure Data Factory. Les ressources ne doivent pas obligatoirement se trouver sur le même réseau virtuel (VNET) ou dans le même groupe de ressources pour que la règle de pare-feu autorise ces connexions. Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Il existe deux méthodes pour activer ces types de connexions. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que ces connexions sont autorisées. Vous pouvez également affecter la valeur **ON** à l’option **Autoriser l’accès aux services Azure** dans le portail à partir du volet **Sécurité de la connexion** et cliquer sur **Enregistrer**. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure Database pour PostgreSQL.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure
Répétez les étapes pour gérer les règles de pare-feu.
* Pour ajouter l’ordinateur actuel, cliquez sur le bouton **Ajouter mon adresse IP**. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les. Cliquez sur **Enregistrer** pour enregistrer les modifications.
* Pour supprimer une règle existante, cliquez sur les points de suspension [...], puis sur **Supprimer**. Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes
- De la même manière, vous pouvez utiliser un script pour [créer et gérer des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure](howto-manage-firewall-using-cli.md).
- Pour vous aider à vous connecter à un serveur Azure Database pour PostgreSQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md).
