---
title: "Accéder à des sources de données locales pour Azure Logic Apps | Documents Microsoft"
description: "Configurer la passerelle de données locale pour pouvoir accéder à des sources de données locales à partir d’applications logiques"
keywords: "accéder aux données, localement, transfert de données, chiffrement, sources de données"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 5160913b2f08a04f0a985d8ddadd6641a664b258
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>Connexion à des sources de données locales à partir d’applications logiques avec la passerelle de données locale

Pour accéder à des sources de données locales à partir de vos applications logiques, configurez une passerelle de données locale que les applications logiques peuvent utiliser avec des connecteurs pris en charge. La passerelle agit comme un pont permettant un transfert et un chiffrement de données rapides entre les sources de données locales et vos applications logiques. La passerelle relaie les données des sources locales sur des canaux chiffrés via Azure Service Bus. Tout le trafic est initialisé en tant que trafic sortant de l’agent de passerelle sécurisé. Pour en savoir plus, voir [Fonctionnement de la passerelle de données](logic-apps-gateway-install.md#gateway-cloud-service). 

La passerelle prend en charge les connexions aux sources de données locales suivantes :

*   BizTalk Server 2016
*   DB2  
*   Système de fichiers
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*   SharePoint
*   SQL Server
*   Teradata

Ces étapes décrivent comment configurer la passerelle de données locale pour travailler avec vos applications logiques. Pour plus d’informations sur les connecteurs pris en charge, voir [Connecteurs pour Azure Logic Apps](../connectors/apis-list.md). 

Pour plus d’informations sur l’utilisation de la passerelle avec d’autres services, consultez les articles suivants :

*   [Passerelle de données locale Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Passerelle de données locale Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Passerelle de données locale Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Passerelle de données locale Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Configuration requise

* Vous devez avoir déjà [installé la passerelle de données sur un ordinateur local](logic-apps-gateway-install.md).

* Lorsque vous vous connectez au portail Azure, vous devez utiliser le même compte professionnel ou scolaire qui a été utilisé pour [installer la passerelle de données locale](logic-apps-gateway-install.md#requirements). Votre compte de connexion doit aussi avoir un abonnement Azure à utiliser lorsque vous créez une ressource de passerelle dans le portail Azure pour l’installation de votre passerelle.

* L’installation de votre passerelle ne peut pas être revendiquée par une autre ressource de passerelle Azure. Vous ne pouvez associer l’installation de votre passerelle qu’à une seule ressource de passerelle. Une revendication se produit lorsque vous créez la ressource de passerelle de façon à ce que l’installation soit indisponible pour d’autres ressources.

* La passerelle de données locale fonctionne comme un service Windows ; elle est configurée pour utiliser `NT SERVICE\PBIEgwService` comme informations d’identification pour la connexion au service Windows. Pour créer et gérer les ressources de la passerelle dans le portail Azure, le [compte de service Windows](../logic-apps/logic-apps-gateway-install.md) doit avoir au moins les autorisations **Collaborateur**. 

  > [!NOTE]
  > Le compte de service Windows diffère du compte utilisé pour la connexion à des sources de données locales, et du compte professionnel ou scolaire Azure utilisé pour se connecter aux services cloud.

## <a name="install-the-on-premises-data-gateway"></a>Installer la passerelle de données locale

Si ce n’est déjà fait, suivez les [étapes d’installation de la passerelle de données locale](logic-apps-gateway-install.md). Avant de passer aux autres suivantes, vérifiez que vous avez installé la passerelle de données sur un ordinateur local.

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>Créer une ressource Azure pour la passerelle de données locale

Après avoir installé la passerelle sur un ordinateur local, vous devez créer votre passerelle de données en tant que ressource dans Azure. Cette étape associe également votre ressource de passerelle à votre abonnement Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com "portail Azure"). Veillez à utiliser l’adresse e-mail professionnelle ou scolaire Azure que vous avez utilisée pour installer la passerelle.

2. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration Entreprise** > **Passerelle de données locale**.

   ![Cherchez « Passerelle de données locale »](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Dans la page **Créer une passerelle connexion**, fournissez ces détails pour créer votre ressource de passerelle de données :

    * **Nom** : entrez un nom pour votre ressource de passerelle. 

    * **Abonnement** : sélectionnez l’abonnement Azure à associer à votre ressource de passerelle. 
    Cet abonnement doit être le même que celui de votre application logique.
   
      L’abonnement par défaut est basé sur le compte Azure que vous avez utilisé pour vous connecter.

    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez un groupe de ressources existant pour le déploiement de votre ressource de passerelle. 
    Les groupes de ressources vous aident à gérer des ressources Azure associées en tant que collection.

    * **Emplacement** : Azure limite cet emplacement à la région sélectionnée pour le service de cloud de passerelle pendant l’[installation de la passerelle](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Assurez-vous que l’emplacement de la ressource de passerelle correspond à l’emplacement du service de cloud de passerelle. Autrement, votre installation de passerelle pourrait ne pas figurer dans la liste des passerelles installées dans laquelle vous allez opérer une sélection à l’étape suivante.
      > 
      > Vous pouvez utiliser des régions différentes pour votre ressource de passerelle et votre application logique.

    * **Nom de l’installation** : si votre installation de passerelle n’est pas encore sélectionnée, sélectionnez la passerelle que vous avez installée précédemment. 

    Pour ajouter la ressource de passerelle à votre tableau de bord Azure, choisissez **Épingler au tableau de bord**. 
    Lorsque vous êtes prêt, choisissez **Créer**.

    Par exemple : 

    ![Fournir des détails pour créer votre passerelle de données locale](./media/logic-apps-gateway-connection/createblade.png)

    Pour rechercher ou consulter votre passerelle de données à tout moment, dans le menu principal Azure, choisissez **Tous les services**. 
    Dans la zone de recherche, entrez « passerelles de données locales », puis choisissez **Passerelles de données locales**.

    ![Recherche sur « Passerelle de données locale »](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>Connectez votre application logique à la passerelle de données locale

À présent que vous avez créé votre ressource de passerelle de données et associé votre abonnement Azure à cette ressource, créez une connexion entre votre application logique et la passerelle de données.

> [!NOTE]
> Votre emplacement de connexion de passerelle doit exister dans la même région que votre application logique, mais vous pouvez utiliser une passerelle de données existant dans une autre région.

1. Dans le portail Azure, créez ou ouvrez votre application logique dans le Concepteur d’application logique.

2. Ajoutez un connecteur prenant en charge des connexions locales, tel que SQL Server.

3. En suivant l’ordre indiqué, sélectionnez **Se connecter via la passerelle de données locale**, entrez un nom de connexion unique et les informations requises, puis sélectionnez la ressource de passerelle de données que vous souhaitez utiliser. Lorsque vous êtes prêt, choisissez **Créer**.

   > [!TIP]
   > Un nom de connexion unique vous aidera à identifier facilement cette connexion ultérieurement, en particulier si vous créez plusieurs connexions. Le cas échéant, incluez également le nom de domaine complet dans votre nom d’utilisateur. 

   ![Créer une connexion entre une application logique et une passerelle de données](./media/logic-apps-gateway-connection/blankconnection.png)

Félicitations, votre connexion de passerelle est maintenant prête et votre application logique peut l’utiliser.

## <a name="edit-your-gateway-connection-settings"></a>Modifier vos paramètres de connexion de passerelle

Après avoir créé une connexion de passerelle pour votre application logique, vous pouvez mettre à jour les paramètres pour cette connexion spécifique.

1. Pour trouver la connexion de passerelle :

   * Dans le menu de l’application logique, sous **Outils de développement**, sélectionnez **Connexions d’API**. 
   
     Le volet **Connexions d’API** affiche toutes les connexions d’API associées à votre application logique, y compris les connexions de passerelle.

     ![Accédez à votre application logique, puis sélectionnez « Connexions d’API ».](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Ou bien, dans le menu Azure principal, accédez à **Autres services** > **Web + mobile** > **Connexions d’API** pour toutes les connexions d’API, dont les connexions de passerelle, associées à votre abonnement Azure. 

   * Ou encore, dans le menu Azure principal, accédez à **Toutes les ressources** pour toutes les connexions d’API, dont les connexions de passerelle, associées à votre abonnement Azure.

2. Sélectionnez la connexion de passerelle que vous souhaitez afficher ou modifier, puis choisissez **Modifier une connexion d’API**.

   > [!TIP]
   > Si vos mises à jour ne prennent pas effet, essayez d’[arrêter et redémarrer la service Windows de passerelle](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Basculer ou supprimer votre ressource de passerelle de données locale

Pour créer une autre ressource de passerelle, associer votre passerelle à une autre ressource ou supprimer la ressource de passerelle, vous pouvez effectivement supprimer celle-ci sans que cela affecte l’installation de la passerelle. 

1. Dans le menu Azure principal, accédez à **Toutes les ressources**. 
2. Cherchez et sélectionnez votre ressource de passerelle de données.
3. Choisissez **Passerelle de données locale**, puis, dans la barre d’outils des ressources, choisissez **Supprimer**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser vos applications logiques](./logic-apps-securing-a-logic-app.md)
* [Exemples et scénarios courants pour les applications logiques](./logic-apps-examples-and-scenarios.md)
