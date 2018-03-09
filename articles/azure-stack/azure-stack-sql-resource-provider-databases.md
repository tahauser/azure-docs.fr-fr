---
title: "Utilisation des bases de données fournies par le fournisseur de ressources SQL Adapter sur Azure Stack | Microsoft Docs"
description: "Comment créer et gérer des bases de données SQL approvisionnées avec le fournisseur de ressources de l’adaptateur SQL"
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 39f6cc30191f07a7c891446a9132222a6d264dc4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="create-sql-databases"></a>Créer des bases de données SQL

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les bases de données libre-service sont fournies par le biais de l’expérience du portail de l’utilisateur. Un utilisateur a besoin d’un abonnement disposant d’une offre contenant le service de base de données.

1. Connectez-vous au portail [Azure Stack](azure-stack-poc.md) (les administrateurs de service peuvent également utiliser le portail d’administration).

2. Cliquez sur **+ Nouveau** &gt; **Données + stockage** &gt; **Base de données SQL Server (préversion)** &gt; **Ajouter**.

3. Remplissez le formulaire avec les détails sur la base de données, notamment le **Nom de la base de données** et une **Taille maximale**, puis changez les autres paramètres si nécessaire. Vous êtes invité à choisir une référence (SKU) pour votre base de données. Lors de leur ajout, les serveurs d’hébergement se voient affectés une référence SKU. Les bases de données sont créées dans ce pool de serveurs d’hébergement qui constituent la référence SKU.

  ![Nouvelle base de données](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > La taille de la base de données doit être d’au moins 64 Mo. Il est possible de l’augmenter à l’aide de paramètres.

4. Renseignez les paramètres de connexion : **Connexion à la base de données** et **Mot de passe**. Ces paramètres sont des informations d’identification d’authentification SQL qui sont créées pour votre accès à cette base de données uniquement. Le nom d’utilisateur de connexion doit être globalement unique. Créez un paramètre de connexion ou sélectionnez-en un qui existe déjà. Vous pouvez réutiliser les paramètres de connexion d’autres bases de données utilisant la même référence (SKU).

    ![Créer une connexion de base de données](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Envoyez le formulaire, puis attendez que le déploiement se termine.

    Dans le panneau qui s’affiche, notez le champ « Chaîne de connexion ». Vous pouvez utiliser cette chaîne dans toute application qui nécessite un accès à SQL Server (par exemple, une application web) dans Azure Stack.

    ![Récupérer la chaîne de connexion](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-databases"></a>Supprimer les bases de données SQL
À partir du portail,

>[!NOTE]
>
>Lorsqu’une base de données SQL AlwaysOn est supprimée du fournisseur de ressources, celle-ci est supprimée avec succès du groupe de disponibilité principal et AlwaysOn. Toutefois, par définition, le groupe de disponibilité SQL place la base de données en état de restauration dans chaque réplica et ne supprime pas la base de données, sauf si elle a été déclenchée. Si une base de données n’est pas supprimée, les réplicas secondaires passent à l’état Sans synchronisation. La fonction de réajout d’une nouvelle base de données au groupe de disponibilité avec le même via le fournisseur de ressources fonctionne toujours. Voir ![Suppression d’une base de données secondaire](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/remove-a-secondary-database-from-an-availability-group-sql-server)

## <a name="manage-database-credentials"></a>Gérer les informations d’identification de la base de données
Vous pouvez mettre à jour les informations d’identification de la base de données (paramètres de connexion).

## <a name="verify-sql-alwayson-databases"></a>Vérifier les bases de données SQL AlwaysOn
Les bases de données AlwaysOn doivent s’afficher comme étant synchronisées et disponibles sur toutes les instances et dans le groupe de disponibilité. Après le basculement, la base de données doit se connecter en toute transparence. Vous pouvez utiliser SQL Server Management Studio pour vérifier qu’une base de données est en cours de synchronisation :

![Vérifier AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)
