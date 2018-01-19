---
title: "Procédure de configuration des paramètres de serveur dans Azure Database pour MySQL | Microsoft Docs"
description: "Cet article décrit comment configurer les paramètres de serveur MySQL dans Azure Database pour MySQL à l’aide du portail Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/10/2017
ms.openlocfilehash: f3b32c1f6b33bc60b50f1496414a300db468dc92
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Guide pratique pour configurer des paramètres de serveur dans Azure Database pour MySQL à l’aide du portail Azure

Azure Database pour MySQL prend en charge la configuration de certains paramètres de serveur. Cette rubrique décrit comment configurer ces paramètres à l’aide du portail Azure. Les paramètres du serveur ne sont pas tous modifiables. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Accéder à Paramètres du serveur sur le portail Azure
1. Connectez-vous au portail Azure, puis recherchez votre serveur Azure Database pour MySQL.
2. Sous la section **Paramètres**, cliquez sur **Paramètres du serveur** pour ouvrir la page Paramètres du serveur correspondant à Azure Database pour MySQL.
3. Recherchez les paramètres que vous devez ajuster. Examinez la colonne **Description** pour comprendre la fonction et les valeurs autorisées. 
4. Cliquez sur **Enregistrer** pour enregistrer vos modifications.

![Panneau Paramètres du serveur du portail Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Liste des paramètres de serveur configurables

La liste des paramètres de serveur pris en charge s’allonge en permanence. Utilisez l’onglet Paramètres du serveur dans le portail Azure pour obtenir la définition et configurer les paramètres du serveur en fonction des besoins de votre application. 

## <a name="nonconfigurable-server-parameters"></a>Paramètres de serveur non configurables
Le pool de mémoires tampons InnoDB et le nombre maximal de connexions ne sont pas configurables et dépendent de votre [niveau tarifaire](concepts-service-tiers.md). 

| **Niveau tarifaire** | **Pool de mémoires tampons InnoDB (Mo)** | **Nombre maximal de connexions** |
| :------------------------ | :-------- | :----------- |
| De base 50 | 1 024 | 50 | 
| De base 100  | 2560 | 100 | 
| Standard 100 | 2560 | 200 | 
| Standard 200 | 5120 | 400 | 
| Standard 400 | 10240 | 800 | 
| Standard 800 | 20480 | 1 600 |

Ces paramètres de serveur ne sont pas configurables dans le système <br>
 innodb_file_per_table in Basic tier: OFF<br>
 innodb_flush_log_at_trx_commit=1<br>
 sync_binlog=1<br>
 innodb_log_file_size=512MB<br>
 
Tous les autres paramètres de serveur sont définis sur leurs valeurs MySQL par défaut pour les versions [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) et [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>étapes suivantes
- [Bibliothèques de connexions pour Azure Database pour MySQL](concepts-connection-libraries.md).
