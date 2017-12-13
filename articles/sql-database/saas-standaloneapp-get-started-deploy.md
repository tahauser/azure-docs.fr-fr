---
title: Didacticiel SaaS multi-locataire - Azure SQL Database | Microsoft Docs
description: "Déployer et explorer une application SaaS à client unique autonome qui utilise Azure SQL Database."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: genemi
ms.openlocfilehash: d38cd108821bce05824732bbdbdd322ae8563bde
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Déployer et explorer une application à client unique autonome qui utilise Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application autonome SaaS Wingtip Tickets. L’application est conçue pour tirer parti des fonctionnalités SQL Azure Database qui simplifient l’activation des scénarios SaaS.

Le modèle d’application autonome déploie un groupe de ressources Azure contenant une application à locataire unique et une base de données locataire unique pour chaque client.  Plusieurs instances de l’application peuvent être configurées pour fournir une solution mutualisée.

Dans ce didacticiel, vous déployez des groupes de ressources pour plusieurs clients dans votre abonnement Azure.  Ce modèle permet aux groupes de ressources d’être déployés dans l’abonnement Azure d’un locataire. Azure propose des programmes partenaires qui permettent à ces groupes de ressources d’être gérés par un fournisseur de services pour le compte du locataire. Le fournisseur de services est un administrateur dans l’abonnement du locataire.

Dans la section suivante sur le déploiement, il existe trois boutons bleus **Déployer sur Azure**. Chaque bouton déploie une instance différente de l’application. Chaque instance est personnalisée pour un locataire spécifique. Lorsque vous appuyez sur chacun de ces boutons, l’application correspondante est entièrement déployée en cinq minutes.  Les applications sont déployées dans votre abonnement Azure.  Vous avez un accès complet pour explorer et utiliser les composants d’application individuels.

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
    * Pour **Groupe de ressources**, sélectionnez **Création** et indiquez un **nom** en minuscules pour le groupe de ressources.
        * Nous vous recommandons d’inclure un tiret, suivi de vos initiales, puis d’un chiffre : par exemple, *wingtip-sa-af1*.
        * Sélectionnez un **Emplacement** dans la liste déroulante.

    * Pour **Utilisateur**, nous vous recommandons de choisir une valeur d’utilisateur courte, comme vos initiales plus un chiffre : par exemple, *af1*.


3. **Déployez l’application**.

    * Cliquez pour accepter les conditions générales.
    * Cliquez sur **Achat**.

4. Surveillez l’état du déploiement des trois déploiements en cliquant sur **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement de l’application dure cinq minutes.


## <a name="run-the-application"></a>Exécution de l'application

L’application présente les lieux qui hébergent des événements. Les types de lieu incluent des salles de concert, des clubs de jazz et des salles de sport. Les lieux sont les clients de l’application Wingtip Tickets. Dans Wingtip Tickets, les lieux sont enregistrés en tant que *locataires*. En étant locataire, un lieu obtient un moyen simple de répertorier des événements et de vendre des billets à ses clients. Chaque lieu obtient un site web personnalisé pour répertorier ses événements et vendre des billets. Chaque locataire est isolé des autres locataires et est indépendant. Dans le système, chaque locataire obtient une instance d’application distincte et sa propre base de données SQL autonome.

1. Ouvrez la page d’événements pour chacun des trois clients dans des onglets de navigateur distincts :

    - http://events.contosoconcerthall.&lt;Utilisateur&gt;.trafficmanager.net
    - http://events.dogwooddojo.&lt;Utilisateur&gt;.trafficmanager.net
    - http://events.fabrikamjazzclub.&lt;Utilisateur&gt;.trafficmanager.net

    (Dans chaque URL, remplacez &lt;Utilisateur&gt; avec la valeur d’utilisateur de votre déploiement.)

   ![Événements](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Pour contrôler la distribution des demandes entrantes, l’application utilise [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). Chaque instance d’application propre au locataire comprend le nom du locataire en tant que partie du nom de domaine dans l’URL. Toutes les URL de locataire comprennent votre valeur **Utilisateur** spécifique. Les URL respectent le format suivant :
- http://events.&lt;venuename&gt;.&lt;USER&gt;.trafficmanager.net

**L’emplacement** de base de données de chaque locataire est inclus dans les paramètres d’application de l’application déployée correspondante.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](../traffic-manager/traffic-manager-point-internet-domain.md) vers l’URL du profil Traffic Manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorer les serveurs et les bases de données de locataires

Examinons quelques-unes des ressources qui ont été déployées :

1. Dans le [portail Azure](http://portal.azure.com), accédez à la liste des groupes de ressources.
2. Consultez le groupe de ressources **wingtip-sa-catalog-&lt;USER&gt;**.
    - Dans ce groupe de ressources, le serveur **catalog-sa-&lt;USER&gt;** est déployé. Le serveur contient la base de données **tenantcatalog**.
    - Vous devriez également voir trois groupes de ressources client.
3. Ouvrez le groupe de ressources **wingtip-sa-fabrikam-&lt;Utilisateur&gt;** qui contient les ressources pour le déploiement du Fabrikam Jazz Club.  Le serveur **fabrikamjazzclub-&lt;Utilisateur&gt;** contient la base de données **fabrikamjazzclub**.

Chaque base de données est une base de données 50 DTU *autonome*.

## <a name="additional-resources"></a>Ressources supplémentaires

<!--
* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
-->

- Pour de plus amples informations sur les applications SaaS mutualisées, consultez [Modèles de location de base de données SaaS multi-locataire](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer l’application autonome SaaS Wingtip Tickets.
> * Explorer les serveurs et les bases de données qui composent l’application.
> * Comment supprimer les exemples de ressources pour arrêter la facturation associée.

