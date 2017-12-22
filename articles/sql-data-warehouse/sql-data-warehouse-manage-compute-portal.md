---
title: "Gestion de la puissance de calcul dans Azure SQL Data Warehouse (portail Azure) | Microsoft Docs"
description: "Tâches du portail Azure permettant de gérer la puissance de calcul. Mettez à l’échelle les ressources de calcul en ajustant les unités DWU. Ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 63888d5dd103b585cf18e4787d3e779810163e3d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gestion de la puissance de calcul dans Azure SQL Data Warehouse (portail Azure)
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
> * [Portail](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>Mise à l’échelle de la puissance de calcul
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les ressources de calcul :

1. Ouvrez le [portail Azure][Azure portal], ouvrez votre base de données, puis cliquez sur **Mettre à l’échelle**.

    ![Cliquez sur Mettre à l’échelle.][1]
2. Dans le panneau de mise à l’échelle, déplacez le curseur vers la gauche ou vers la droite pour modifier le paramètre DWU.

    ![Déplacez le curseur][2]
3. Cliquez sur **Save**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Cliquez sur Enregistrer.][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Suspension du calcul
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données :

1. Ouvrez le [portail Azure][Azure portal], puis votre base de données. Notez que l’état est **En ligne**.

    ![État En ligne][6]
2. Pour interrompre les ressources de calcul et de mémoire, cliquez sur **Pause**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Confirmer la pause][7]
3. Lorsque SQL Data Warehouse démarre la base de données, l’état est **Interruption**.
4. Lorsque l’état est **Interrompu**, l’opération d’interruption est effectuée et aucune DWU ne vous est plus facturée.

    ![État Pause][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Reprise du calcul
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour reprendre une base de données :

1. Ouvrez le [portail Azure][Azure portal], puis votre base de données. Notez que l’état est **Suspendu**.

    ![Suspendre la base de données][4]
2. Pour relancer la base de données, cliquez sur **Démarrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Confirmer la reprise][5]
3. Lorsque SQL Data Warehouse démarre la base de données, l’état est « Reprise ».
4. Lorsque l’état est **En ligne**, la base de données est prête.

    ![État En ligne][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez la [vue d’ensemble de la gestion][Management overview].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
