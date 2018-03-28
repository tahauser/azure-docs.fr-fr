---
title: 'Portail Azure : masquage de données dynamiques de base de données SQL | Microsoft Docs'
description: Guide de prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 11/22/2016
ms.author: ronitr
ms.openlocfilehash: 2db546d8165956dc6f9fad21f66af315ba301c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure

Cet article vous montre comment implémenter [le masquage des données dynamiques](sql-database-dynamic-data-masking-get-started.md) avec le portail Azure. Vous pouvez également implémenter le masquage de données dynamique avec les [applets de commande de base de données Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) ou [l’API REST](https://msdn.microsoft.com/library/dn505719.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configuration du masquage des données dynamiques pour votre base de données à l’aide du portail Azure
1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page des paramètres de la base de données comprenant les données sensibles que vous souhaitez masquer.
3. Cliquez sur la vignette **Dynamic Data Masking** qui lance la page de configuration **Dynamic Data Masking**.
   
   * Vous pouvez également faire défiler vers le bas vers la section **Opérations** et cliquer sur **Dynamic Data Masking**.
     
     ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. Dans la page de configuration **Masquage des données dynamiques**, certaines colonnes de base de données ont été indiquées par le moteur de recommandations pour le masquage. Pour accepter les recommandations, cliquez simplement sur **Ajouter un masque** pour une ou plusieurs colonnes et un masque est créé en fonction du type par défaut pour cette colonne. Vous pouvez modifier la fonction de masquage en cliquant sur la règle de masquage et en modifiant le format du champ de masquage pour un format différent de votre choix. N'oubliez pas de cliquer sur **Enregistrer** pour enregistrer vos paramètres.
   
    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. Pour ajouter un masque pour une colonne de votre base de données, au sommet de la page de configuration **Dynamic Data Masking**, cliquez sur **Ajouter un masque** pour ouvrir la page de configuration **Ajouter une règle de masquage**.
   
    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. Sélectionnez le **Schéma**, la **Table** et la **Colonne** pour définir les champs désignés qui seront masqués.
7. Choisissez un **Format de champ de masquage** dans la liste des catégories de masquage des données sensibles.
   
    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. Cliquez sur **Enregistrer** dans la page des règles de masquage pour mettre à jour l'ensemble des règles de la stratégie de masquage des données dynamiques.
9. Tapez les utilisateurs SQL ou les identités AAD à exclure du masquage et qui doivent avoir accès aux données sensibles non masquées. La liste d'utilisateurs doit être délimitée par des points-virgules. Les utilisateurs disposant de privilèges administrateur ont toujours accès aux données d'origine non masquées.
   
    ![Volet de navigation](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > Pour que la couche d'application puisse afficher des données sensibles aux utilisateurs d'application privilégiés, ajoutez l'utilisateur SQL ou l'identité AAD qu'utilise l'application pour interroger la base de données. Il est vivement recommandé de limiter le nombre d'utilisateurs privilégiés dans cette liste afin de limiter l'exposition des données sensibles.
   > 
   > 
10. Cliquez sur **Enregistrer** dans la page de configuration du masquage des données afin d'enregistrer la stratégie de masquage, nouvelle ou mise à jour.


## <a name="next-steps"></a>Étapes suivantes

* Pour une vue d’ensemble du masquage des données dynamiques, consultez [Masquage des données dynamiques](sql-database-dynamic-data-masking-get-started.md).
* Vous pouvez également implémenter le masquage de données dynamique avec les [applets de commande de base de données Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) ou [l’API REST](https://msdn.microsoft.com/library/dn505719.aspx).
