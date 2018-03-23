---
title: Didacticiel SaaS multi-locataire - Azure SQL Database | Microsoft Docs
description: Déployer et explorer une application SaaS à client unique autonome qui utilise Azure SQL Database.
keywords: didacticiel sur les bases de données SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: e30f096a9f02214839550c2dc143ab665e1cd85c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Déployer et explorer une application à client unique autonome qui utilise Azure SQL Database

Ce didacticiel décrit comment déployer et explorer l’exemple d’application SaaS Wingtip Tickets développé à l’aide du modèle application autonome ou d’application par client.  L’application est conçue pour présenter des fonctionnalités d’Azure SQL Database qui simplifient l’activation de scénarios SaaS mutualisés.

Le modèle application autonome ou d’application par client déploie une instance d’application pour chaque client.  Chaque application est configurée pour un client spécifique, et déployée dans un groupe de ressources Azure distinct. Plusieurs instances de l’application sont approvisionnées pour fournir une solution mutualisée. Ce modèle convient parfaitement pour des nombres restreints de clients où l’isolation des clients est une priorité de premier plan. Azure disposent de programmes partenaires qui permettent le déploiement de ressources dans un abonnement du client et leur gestion par un fournisseur de services pour le compte du client. 

Dans le cadre de ce didacticiel, vous allez déployer les trois applications autonomes pour trois clients dans votre abonnement Azure.  Vous avez un accès complet pour explorer et utiliser les composants d’application individuels.

Le code source de l’application et les scripts de gestion sont disponibles dans le référentiel GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp).


Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer l’application autonome SaaS Wingtip Tickets.
> * Obtenir le code source de l’application et les scripts de gestion.
> * Explorer les serveurs et les bases de données qui composent l’application.

D’autres didacticiels seront publiés. Ils vous permettront d’explorer un éventail de scénarios de gestion basés sur ce modèle d’application.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Déployer l’exemple d’application autonome SaaS Wingtip Tickets

Déployez l’application pour les trois clients fournis :

1. Cliquez sur chaque bouton bleu **Déployer sur Azure** pour ouvrir le modèle de déploiement dans le [portail Azure](https://portal.azure.com). Chaque modèle nécessite deux valeurs de paramètre ; le nom d’un nouveau groupe de ressources et un nom d’utilisateur qui distingue ce déploiement des autres déploiements de l’application. L’étape suivante fournit des détails sur la définition de ces valeurs.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Salle de concert Contoso**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Dojo Dogwood**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp; **Club de jazz Fabrikam**

2. Entrez les valeurs de paramètre requises pour chaque déploiement.

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. **Créez un groupe de ressources** pour chaque déploiement d’application.  N’utilisez pas un groupe de ressources existant. N’utilisez pas cette application, ni les ressources qu’elle crée, pour la production. Supprimez tous les groupes de ressources lorsque vous en avez terminé avec les applications pour interrompre la facturation associée.

    Il est préférable d’utiliser uniquement des lettres minuscules, des chiffres et des traits d’union dans les noms de ressource.
    * Pour **Groupe de ressources**, sélectionnez Création et indiquez un nom en minuscules pour le groupe de ressources. **wingtip-sa-\<venueName\>-\<user\>** est le modèle recommandé.  Pour \<venueName\>, entrez le nom du lieu sans espaces. Pour \<user\>, entrez la valeur de l’utilisateur ci-dessous.  Avec ce modèle, des noms de groupes de ressources pourraient être *wingtip-sa-contosoconcerthall-af1*, *wingtip-sa-dogwooddojo-af1*, *wingtip-sa-fabrikamjazzclub-af1*.
    * Sélectionnez un **Emplacement** dans la liste déroulante.

    * Pour **Utilisateur**, nous vous recommandons de choisir une valeur d’utilisateur courte, comme vos initiales plus un chiffre : par exemple, *af1*.


3. **Déployez l’application**.

    * Cliquez pour accepter les conditions générales.
    * Cliquez sur **Achat**.

4. Surveillez l’état des trois déploiements en cliquant sur **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement des applications prend environ cinq minutes.


## <a name="run-the-applications"></a>Exécution des applications

L’application présente les lieux qui hébergent des événements.  Les lieux sont les clients de l’application. Chaque lieu obtient un site web personnalisé pour répertorier ses événements et vendre des billets. Les types de lieu incluent des salles de concert, des clubs de jazz et des salles de sport. Dans l’exemple, le type de lieu détermine la photographie d’arrière-plan affichée sur le site web du lieu.   Dans le modèle d’application autonome, chaque lieu dispose d’une instance d’application distincte avec sa propre base de données SQL autonome.

1. Ouvrez la page d’événements pour chacun des trois clients dans des onglets de navigateur distincts :

    - http://events.contosoconcerthall.&lt;user&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;Utilisateur&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;Utilisateur&gt;.trafficmanager.net

    (Dans chaque URL, remplacez &lt;user&gt; par la valeur d’utilisateur de votre déploiement.)

   ![Événements](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Pour contrôler la distribution des demandes entrantes, l’application utilise [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Chaque instance d’application propre au locataire comprend le nom du locataire en tant que partie du nom de domaine dans l’URL. Toutes les URL de locataire comprennent votre valeur **Utilisateur** spécifique. Les URL respectent le format suivant :
- http://events.&lt;venuename&gt;.&lt;user&gt;.trafficmanager.net

**L’emplacement** de base de données de chaque locataire est inclus dans les paramètres d’application de l’application déployée correspondante.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](../traffic-manager/traffic-manager-point-internet-domain.md) vers l’URL du profil Traffic Manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorer les serveurs et les bases de données de locataires

Examinons quelques-unes des ressources qui ont été déployées :

1. Dans le [portail Azure](http://portal.azure.com), accédez à la liste des groupes de ressources.
2. Vous devrez voir les trois groupes de ressources client.
3. Ouvrez le groupe de ressources **wingtip-sa-fabrikam-&lt;user&gt;** qui contient les ressources pour le déploiement du Fabrikam Jazz Club.  Le serveur **fabrikamjazzclub-&lt;user&gt;** contient la base de données **fabrikamjazzclub**.

Chaque base de données client est une base de données *autonome* de 50 DTU.

## <a name="additional-resources"></a>Ressources supplémentaires

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Pour de plus amples informations sur les applications SaaS mutualisées, consultez [Modèles de location de base de données SaaS multi-locataire](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Supprimer des groupes de ressources pour arrêter la facturation ##

Lorsque vous avez fini d’utiliser l’exemple, supprimez tous les groupes de ressources que vous avez créés pour arrêter la facturation associée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer l’application autonome SaaS Wingtip Tickets.
> * Explorer les serveurs et les bases de données qui composent l’application.
> * Comment supprimer les exemples de ressources pour arrêter la facturation associée.

Essayez ensuite le didacticiel [fourniture et catalogue](saas-standaloneapp-provision-and-catalog.md) dans lequel vous allez explorer l’utilisation d’un catalogue de clients qui active un éventail de scénarios inter-clients tels que la gestion des schémas et l’analyse des clients.
 

