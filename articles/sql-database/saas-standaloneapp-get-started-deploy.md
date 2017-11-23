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
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: ce0268f800dcf1900730d6ad9c476fb06320a79e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Déployer et explorer une application à client unique autonome qui utilise Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application autonome SaaS Wingtip Tickets. L’application est conçue pour tirer parti des fonctionnalités SQL Azure Database qui simplifient l’activation des scénarios SaaS.

Le modèle d’application autonome déploie un groupe de ressources Azure contenant une application à locataire unique et une base de données locataire unique pour chaque client.  Plusieurs instances de l’application peuvent être configurées pour fournir une solution mutualisée.

Alors que, dans ce didacticiel, vous allez déployer des groupes de ressources pour plusieurs clients dans votre abonnement Azure, ce modèle permet aux groupes de ressources d’être déployés dans un abonnement de client Azure.  Azure propose des programmes partenaires qui permettent à ces groupes de ressources d’être gérés par un fournisseur au nom du client en tant qu’administrateur dans le l’abonnement du client.

Dans la section de déploiement ci-dessous, vous trouverez trois boutons Déployer sur Azure, chacun d’eux déployant une autre instance de l’application personnalisée pour un client spécifique. Lorsque vous appuyez sur chacun de ces boutons, l’application correspondante est entièrement déployée cinq minutes plus tard.  Les applications sont déployées dans votre abonnement Azure.  Vous avez un accès complet pour explorer et utiliser les composants d’application individuels.

Le code source de l’application et les scripts de gestion sont disponibles dans le référentiel GitHub [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp).


Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Guide de déploiement de l’Application Wingtip Tickets SaaS autonome
> * Obtenir le code source de l’application et les scripts de gestion
> * Explorer les serveurs et les bases de données qui composent l’application

D’autres didacticiels seront publiés en temps voulu pour vous permettre d’explorer un éventail de scénarios de gestion basés sur ce modèle d’application.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Déployer l’exemple d’application autonome SaaS Wingtip Tickets

Déployez l’application pour les trois clients fournis :

1. Cliquez sur chaque bouton **Déployer sur Azure** pour ouvrir le modèle de déploiement dans le portail Azure. Chaque modèle nécessite deux valeurs de paramètre ; le nom d’un nouveau groupe de ressources et un nom d’utilisateur qui distingue ce déploiement des autres déploiements de l’application. L’étape suivante fournit des détails sur la définition de ces valeurs.<br><br>
    <a href="http://aka.ms/deploywingtipsa-contoso" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>     &nbsp;&nbsp;**Salle de concert Contoso**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-dogwood" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a> &nbsp;&nbsp;**Dogwood Dojo**
<br><br>
    <a href="http://aka.ms/deploywingtipsa-fabrikam" target="_blank"><img style="vertical-align:middle" src="media/saas-standaloneapp-get-started-deploy/deploy.png"/></a>    &nbsp;&nbsp;**Fabrikam Jazz Club**

2. Entrez les valeurs de paramètre requises pour chaque déploiement.

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. **Créez un groupe de ressources** pour chaque déploiement d’application.  N’utilisez pas un groupe de ressources existant. N’utilisez pas cette application, ni les ressources qu’elle crée, pour la production. Supprimez tous les groupes de ressources lorsque vous en avez terminé avec les applications pour interrompre la facturation associée.

    Il est préférable d’utiliser uniquement des lettres minuscules, des chiffres et des traits d’union dans les noms de ressource.
    * Pour **Groupe de ressources** : sélectionnez Création et indiquez un Nom pour le groupe de ressources (sensible à la casse).
        * Nous recommandons que toutes les lettres du nom de votre groupe de ressources soient en minuscules.
        * Nous vous recommandons d’inclure un tiret, suivi de vos initiales, puis d’un chiffre : par exemple, _wingtip-sa-af1_.
        * Sélectionnez un Emplacement dans la liste déroulante.

    * Pour **Utilisateur**, nous vous recommandons de choisir une valeur courte, comme vos initiales plus un chiffre : par exemple, _af1_.


1. **Déployez l’application**.

    * Cliquez pour accepter les conditions générales.
    * Cliquez sur **Achat**.

1. Surveiller l’état du déploiement des trois déploiements en cliquant sur **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement de l’application dure environ cinq minutes.


## <a name="run-the-application"></a>Exécution de l'application

L’application présente des lieux, comme des salles de concert, des clubs de jazz ou des salles de sport qui accueillent des événements. Les lieux sont inscrits en tant que clients de Wingtip Tickets, offrant ainsi un moyen simple de répertorier les événements et de vendre des tickets. Chaque lieu obtient un site web personnalisé pour gérer et répertorier ses événements, ainsi que pour vendre des billets indépendamment des autres locataires. Dans le système, chaque client obtient une instance d’application distincte et une base de données SQL autonome.

1. Ouvrez la page d’événements pour chacun des trois clients dans des onglets de navigateur distincts :

    http://events.contosoconcerthall.&lt;Utilisateur&gt;.trafficmanager.net <br>
    http://events.dogwooddojo.&lt;Utilisateur&gt;.trafficmanager.net<br>
    http://events.fabrikamjazzclub.&lt;Utilisateur&gt;.trafficmanager.net

    (remplacez &lt;Utilisateur&gt; avec la valeur Utilisateur de votre déploiement).

   ![Événements](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)


Pour contrôler la distribution des demandes entrantes, l’application utilise [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Chaque application propre au client comprend le nom du client en tant que partie du nom de domaine dans l’URL. Toutes les URL de locataires incluent votre propre valeur *Utilisateur* et respectent le format suivant : http://events.&lt;emplacement&gt;.&lt;UTILISATEUR&gt;.trafficmanager.net/. L’emplacement de base de données de chaque client est inclus dans les paramètres d’application de l’application déployée correspondante.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) vers l’URL du profil Traffic Manager.


## <a name="explore-the-servers-and-tenant-databases"></a>Explorer les serveurs et les bases de données de locataires

Examinons quelques-unes des ressources qui ont été déployées :

1. Dans le [portail Azure](http://portal.azure.com), accédez à la liste des groupes de ressources.  Vous devez voir le groupe de ressources **wingtip-sa-catalog-&lt;Utilisateur&gt;** dans lequel le serveur **catalog-sa-&lt;Utilisateur&gt;** est déployé avec la base de données **tenantcatalog**. Vous devriez également voir trois groupes de ressources client.

1. Ouvrez le groupe de ressources **wingtip-sa-fabrikam-&lt;Utilisateur&gt;** qui contient les ressources pour le déploiement du Fabrikam Jazz Club.  Le serveur **fabrikamjazzclub-&lt;Utilisateur&gt;** contient la base de données **fabrikamjazzclub**.


Chaque base de données est une base de données 50 DTU _autonome_.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Guide de déploiement de l’Application Wingtip Tickets SaaS autonome
> * Explorer les serveurs et les bases de données qui composent l’application
> * Supprimer les exemples de ressources pour arrêter la facturation associée


## <a name="additional-resources"></a>Ressources supplémentaires

<!--* Additional [tutorials that build on the Wingtip SaaS application](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview) -->
* Pour de plus amples informations sur les applications SaaS mutualisées, consultez [*Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure*](saas-tenancy-app-design-patterns.md).
