---
title: Guide pratique pour configurer les paramètres de serveur dans Azure Database pour MySQL
description: Cet article décrit comment configurer les paramètres de serveur MySQL dans Azure Database pour MySQL à l’aide du portail Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6865663bebc84df288f4c7e2564ddb4870667c6f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Guide pratique pour configurer des paramètres de serveur dans Azure Database pour MySQL à l’aide du portail Azure

Azure Database pour MySQL prend en charge la configuration de certains paramètres de serveur. Cet article décrit comment configurer ces paramètres à l’aide du portail Azure. Les paramètres du serveur ne sont pas tous modifiables. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Accéder à Paramètres du serveur sur le portail Azure
1. Connectez-vous au portail Azure, puis recherchez votre serveur Azure Database pour MySQL.
2. Sous la section **Paramètres**, cliquez sur **Paramètres du serveur** pour ouvrir la page Paramètres du serveur correspondant à Azure Database pour MySQL.
![Page Paramètres du serveur du portail Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Recherchez les paramètres que vous devez ajuster. Examinez la colonne **Description** pour comprendre la fonction et les valeurs autorisées. 
![Bouton déroulant Énumérer](./media/howto-server-parameters/3-toggle_parameter.png)
4. Cliquez sur **Enregistrer** pour enregistrer vos modifications.
![Enregistrer ou annuler les modifications](./media/howto-server-parameters/4-save_parameters.png)
5. Si vous avez enregistré de nouvelles valeurs pour les paramètres, vous pouvez toujours rétablir toutes les valeurs par défaut en sélectionnant **Rétablir toutes les valeurs par défaut**.
![Rétablir toutes les valeurs par défaut](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Liste des paramètres de serveur configurables

La liste des paramètres de serveur pris en charge s’allonge en permanence. Utilisez l’onglet Paramètres du serveur dans le portail Azure pour obtenir la définition et configurer les paramètres du serveur en fonction des besoins de votre application. 

## <a name="nonconfigurable-server-parameters"></a>Paramètres de serveur non configurables
Le pool de mémoires tampons InnoDB et le nombre maximal de connexions ne sont pas configurables et dépendent de votre [niveau tarifaire](concepts-service-tiers.md). 

|**Niveau tarifaire**| **Génération de calcul**|**vCore(s)**|**Pool de mémoires tampons InnoDB (Mo)**| **Nombre maximal de connexions**|
|---|---|---|---|--|
|De base| Gen 4| 1| 1 024| 50|
|De base| Gen 4| 2| 2560| 100|
|De base| Gen 5| 1| 1 024| 50|
|De base| Gen 5| 2| 2560| 100|
|Usage général| Gen 4| 2| 3584| 300|
|Usage général| Gen 4| 4| 7680| 625|
|Usage général| Gen 4| 8| 15360| 1250|
|Usage général| Gen 4| 16| 31232| 2 500|
|Usage général| Gen 4| 32| 62976| 5 000|
|Usage général| Gen 5| 2| 3584| 300|
|Usage général| Gen 5| 4| 7680| 625|
|Usage général| Gen 5| 8| 15360| 1250|
|Usage général| Gen 5| 16| 31232| 2 500|
|Usage général| Gen 5| 32| 62976| 5 000|
|Mémoire optimisée| Gen 5| 2| 7168| 600|
|Mémoire optimisée| Gen 5| 4| 15360| 1250|
|Mémoire optimisée| Gen 5| 8| 30720| 2 500|
|Mémoire optimisée| Gen 5| 16| 62464| 5 000|

Ces paramètres de serveur ne sont pas configurables dans le système :

|**Paramètre**|**Valeur fixe**|
| :------------------------ | :-------- |
|innodb_file_per_table dans le niveau de base|ÉTEINT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512 Mo|

Tous les autres paramètres de serveur sont définis sur leurs valeurs MySQL par défaut pour les versions [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) et [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Étapes suivantes
- [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md).
