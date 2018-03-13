---
title: "Intégrer votre environnement App Service ILB à l’aide d’une passerelle d’application"
description: "Procédure pas à pas d’intégration d’une application à votre environnement App Service ILB à l’aide d’une passerelle d’application"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: d56eab79c3b3f6b37dc39d8e4bea0d5b7759631a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="integrate-your-ilb-app-service-environment-with-an-application-gateway"></a>Intégrer votre environnement App Service ILB à l’aide d’une passerelle d’application #

L’[environnement Azure App Service pour PowerApps](./intro.md) est un déploiement d’Azure App Service dans le sous-réseau du réseau virtuel Azure d’un client. Il peut être déployé avec un point de terminaison public ou privé pour accéder aux applications. Le déploiement de l’environnement App Service avec un point de terminaison privé (autrement dit, un équilibreur de charge interne) est appelé un environnement App Service ILB.  

Azure Application Gateway est une appliance virtuelle qui fournit l’équilibrage de charge de couche 7, le déchargement SSL et la protection du pare-feu d’applications web (WAF). Elle peut écouter sur une adresse IP publique et acheminer le trafic jusqu’au point de terminaison de votre application. 

Les informations suivantes expliquent comment intégrer une passerelle d’application configurée avec le WAF à une application dans un environnement App Service ILB.  

L’intégration de la passerelle d’application à l’environnement App Service ILB se fait au niveau de l’application. Quand vous configurez la passerelle d’application avec votre environnement App Service ILB, vous le faites pour des applications spécifiques dans cet environnement. Cette technique permet d’héberger des applications mutualisées sécurisées dans un environnement App Service ILB unique.  

![Passerelle d’application pointant vers une application dans un environnement App Service ILB][1]

Lors de cette procédure pas à pas, vous allez :

* Créer une passerelle d’application
* Configurer la passerelle d’application pour pointer vers une application dans votre environnement App Service ILB
* Configurer votre application pour honorer le nom de domaine personnalisé
* Modifier le nom d’hôte DNS public qui pointe vers votre passerelle d’application

## <a name="prerequisites"></a>Prérequis

Pour intégrer votre passerelle d’application à votre environnement App Service ILB, les éléments suivants sont nécessaires :

* Un environnement App Service ILB.
* Une application exécutée dans l’environnement App Service ILB.
* Un nom de domaine routable sur Internet à utiliser avec votre application dans l’environnement App Service ILB.
* L’adresse ILB utilisée par votre environnement App Service ILB. Cette information est disponible dans le portail de l’environnement App Service, sous **Paramètres** > **Adresses IP** :

    ![Exemple de liste d’adresses IP utilisées par l’environnement App Service ILB][9]
    
* Un nom DNS public à utiliser ultérieurement pour pointer vers votre passerelle d’application. 

Pour plus d’informations sur la création d’un environnement App Service ILB, consultez [Création et utilisation d’un environnement App Service ILB][ilbase].

Cet article part du principe que vous souhaitez une passerelle d’application dans le même réseau virtuel Azure que celui où est déployé l’environnement App Service. Avant de commencer à créer la passerelle d’application, choisissez ou créez un sous-réseau qui hébergera la passerelle. 

Vous devez utiliser un sous-réseau autre que GatewaySubnet. Si vous placez la passerelle d’application sur GatewaySubnet, il vous sera impossible par la suite de créer une passerelle de réseau virtuel. 

Vous ne pouvez pas non plus placer la passerelle dans le sous-réseau que votre environnement App Service ILB utilise. L’environnement App Service est le seul élément qui peut être inclus dans ce sous-réseau.

## <a name="configuration-steps"></a>Configuration ##

1. Dans le portail Azure, accédez à **Nouveau** > **Réseau** > **Passerelle d’application**.

2. Dans la zone **Bases** :

   a. Sous **Nom**, entrez le nom de la passerelle d’application.

   b. Sous **Niveau**, sélectionnez **WAF**.

   c. Sous **Abonnement**, sélectionnez le même abonnement que celui qu’utilise le réseau virtuel de l’environnement App Service.

   d. Sous **Groupe de ressources**, créez ou sélectionnez le groupe de ressources.

   e. Sous **Emplacement**, sélectionnez l’emplacement du réseau virtuel de l’environnement App Service.

   ![Informations de base indispensables à la création d’une passerelle d’application][2]

3. Dans la zone **Paramètres** :

   a. Sous **Réseau virtuel**, sélectionnez le réseau virtuel de l’environnement App Service.

   b. Sous **Sous-réseau**, sélectionnez le sous-réseau dans lequel la passerelle d’application doit être déployée. N’utilisez pas GatewaySubnet, car il empêche la création de passerelles VPN.

   c. Sous **Type d’adresse IP**, sélectionnez **Public**.

   d. Sous **Adresse IP publique**, sélectionnez une adresse IP publique. Si vous n'en avez pas, créez-en une.

   e. Sous **Protocole**, sélectionnez **HTTP** ou **HTTPS**. Si vous choisissez le protocole HTTPS, vous devez fournir un certificat PFX.

   f. Sous **Pare-feu d’applications web**, vous pouvez activer le pare-feu et le définir également pour la **détection** ou la **prévention**, en fonction de vos besoins.

   ![Paramètres de création d’une passerelle d’application][3]
    
4. Dans la section **Résumé**, vérifiez les paramètres, puis cliquez sur **OK**. L’installation de votre passerelle d’application peut prendre une trentaine de minutes.  

5. Lorsque l’installation est terminée, accédez au portail de votre passerelle d’application. Sélectionnez **Pool principal**. Ajoutez l’adresse ILB de votre environnement App Service ILB.

   ![Configuration du pool principal][4]

6. Une fois l’opération de configuration de votre pool principal terminée, sélectionnez **Sondes d’intégrité**. Créez une sonde d’intégrité pour le nom de domaine que vous souhaitez destiner à votre application. 

   ![Configurer les sondes d’intégrité][5]
    
7. Une fois l’opération de configuration de vos sondes d’intégrité terminée, sélectionnez **Paramètres HTTP**. Modifiez les paramètres existants, sélectionnez **Utiliser la sonde personnalisée** et choisissez la sonde que vous avez configurée.

   ![Configuration des paramètres HTTP][6]
    
8. Accédez à la section **Vue d’ensemble** de la passerelle d’application et copiez l’adresse IP publique utilisée par la passerelle d’application. Définissez cette adresse IP en tant qu’enregistrement A pour le nom de domaine de votre application, ou utilisez le nom DNS pour cette adresse dans un enregistrement CNAME. Il est plus facile de sélectionner l’adresse IP publique et de la copier à partir de l’interface utilisateur de l’adresse IP publique que de la copier à partir du lien présent dans la section **Vue d’ensemble** de la passerelle d’application. 

   ![Portail de la passerelle d’application][7]

9. Définissez le nom de domaine personnalisé pour votre application dans l’environnement App Service ILB. Accédez à votre application dans le portail et, sous **Paramètres**, sélectionnez **Domaines personnalisés**.

   ![Définition d’un nom de domaine personnalisé sur l’application][8]

Des informations sur la définition des noms de domaine personnalisés pour vos applications web sont disponibles dans l’article [Définition de noms de domaine personnalisés pour votre application web][custom-domain]. Notez toutefois que pour une application dans un environnement App Service ILB, il n’y a aucune validation du nom de domaine. Étant donné que vous possédez le DNS qui gère les points de terminaison de l’application, vous pouvez y placer tout ce que vous voulez. Le nom de domaine personnalisé que vous ajoutez dans ce cas ne doit pas nécessairement être dans votre système DNS, mais il a toujours besoin d’être configuré avec votre application. 

Dès lors que l’opération de configuration est terminée et que vous avez autorisé un court laps de temps pour que les modifications DNS se propagent, vous pouvez accéder à votre application à l’aide du nom de domaine personnalisé que vous avez créé. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
