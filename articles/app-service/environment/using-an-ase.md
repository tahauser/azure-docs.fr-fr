---
title: "Utilisation d’un environnement Azure App Service"
description: "Comment créer, publier et mettre à l’échelle des applications dans un environnement Azure App Service"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 64e1652ac4067a3f1639bf81cfcd0f79637ade9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-an-app-service-environment"></a>Utilisation d’un environnement App Service #

## <a name="overview"></a>Vue d'ensemble ##

Azure App Service Environment est un déploiement d’Azure App Service dans un sous-réseau dans le réseau virtuel Azure d’un client. Elle comprend :

- **Serveurs frontaux** : c’est sur les serveurs frontaux que HTTP/HTTPS se termine dans un environnement App Service (ASE).
- **Workers** : il s’agit des ressources qui hébergent vos applications.
- **Base de données** : celle-ci contient des informations qui définissent l’environnement.
- **Stockage** : le stockage est utilisé pour héberger les applications publiées client.

> [!NOTE]
> Il existe deux versions de l’environnement App Service : ASEv1 et ASEv2. Dans un environnement ASEv1, vous devez gérer les ressources avant de pouvoir les utiliser. Pour savoir comment configurer et gérer un environnement ASEv1, consultez [Configuration d’un environnement App Service v1][ConfigureASEv1]. Le reste de cet article se concentre sur ASEv2.
>
>

Vous pouvez déployer un environnement ASE (ASEv1 et ASEv2) avec une adresse IP virtuelle externe ou interne pour accéder à l’application. Le déploiement avec une adresse IP virtuelle externe est communément appelé ASE externe. La version interne est appelée ASE d’équilibrage de charge interne, car elle utilise un équilibreur de charge interne (ILB). Pour plus d’informations sur l’environnement ASE d’équilibrage de charge interne, consultez [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Créer et utiliser un équilibreur de charge interne avec un environnement App Service).

## <a name="create-a-web-app-in-an-ase"></a>Créer une application web dans un environnement ASE ##

Pour créer une application web dans un environnement ASE, vous utilisez le même processus que lorsque vous la créez normalement, mais avec quelques petites différences. Lorsque vous créez un plan App Service :

- Au lieu de choisir un emplacement géographique dans lequel déployer votre application, vous choisissez un environnement ASE comme emplacement.
- Tous les plans App Service créés dans un environnement ASE doivent être dans un niveau tarifaire Isolé.

Si vous ne disposez pas d’un environnement ASE, vous pouvez en créer un en suivant les instructions de l’article [Create an App Service Environment][MakeExternalASE] (Créer un environnement App Service).

Pour créer une application web dans un environnement ASE :

1. Sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

2. Entrez le nom de l’application web. Si vous avez déjà sélectionné un plan App Service dans un environnement ASE, le nom de domaine pour l’application reflète le nom de domaine de l’environnement ASE.

    ![Sélection du nom de l’application web][1]

3. Sélectionnez un abonnement.

4. Entrez un nom pour un nouveau groupe de ressources ou sélectionnez **Use existing** (Utiliser existant) et choisissez-en un dans la liste déroulante.

5. Sélectionnez un plan App Service existant dans votre environnement ASE ou créez-en un en procédant comme suit :

    a. Sélectionnez **Créer**.

    b. Entrez le nom de votre plan App Service.

    c. Sélectionnez votre environnement ASE dans la liste déroulante **Emplacement**.

    d. Sélectionnez un niveau tarifaire **Isolé**. Sélectionnez **Sélectionner**.

    e. Sélectionnez **OK**.
    
    ![Niveaux tarifaires isolés][2]

6. Sélectionnez **Créer**.

## <a name="how-scale-works"></a>Fonctionnement de la mise à l’échelle ##

Chaque application App Service s’exécute dans un plan App Service. Le modèle de conteneur est le suivant : les environnements contiennent les plans App Service et les plans App Service contiennent les applications. Lorsque vous mettez à l’échelle une application, vous mettez à l’échelle le plan App Service, et donc toutes les applications contenues dans le même plan.

Dans un environnement ASEv2, lorsque vous mettez à l’échelle un plan App Service, l’infrastructure nécessaire est automatiquement ajoutée. Il existe un délai pour les opérations de mise à l’échelle lorsque l’infrastructure est ajoutée. Dans l’environnement ASEv1, l’infrastructure nécessaire doit être ajoutée avant que vous puissiez créer ou augmenter la taille des instances de votre plan App Service. 

Dans l’App Service multilocataire, la mise à l’échelle est généralement immédiate, car il existe un pool de ressources instantanément disponible pour la prendre en charge. Dans un environnement ASE, il n’existe pas de mémoire tampon et les ressources peuvent être allouées en fonction des besoins.

Dans un environnement ASE, vous pouvez monter en puissance jusqu’à 100 instances maximum. Ces 100 instances peuvent toutes se trouver dans un seul plan App Service ou être distribuées dans plusieurs plans App Service.

## <a name="ip-addresses"></a>Adresses IP ##

App Service a la possibilité d’allouer une adresse IP dédiée à une application. Cette fonctionnalité est disponible après la configuration d’un certificat SSL basé sur IP, comme indiqué ici : [Lier un certificat SSL existant à des applications web Azure][ConfigureSSL]. Toutefois, dans un environnement ASE, il existe une exception notable. Vous ne pouvez pas ajouter des adresses IP supplémentaires à utiliser pour un SSL basé sur IP dans un environnement ASE d’équilibrage de charge interne (ILB).

Dans un environnement ASEv1, vous devez allouer les adresses IP en tant que ressources avant de pouvoir les utiliser. Dans un environnement ASEv2, utilisez-les à partir de votre application comme vous le feriez dans l’App Service multilocataire. Il existe toujours une adresse de secours dans un environnement ASEv2, dans la limite de 30 adresses IP. Chaque fois que vous en utilisez une, une autre est ajoutée afin qu’une adresse soit toujours disponible si besoin. Un délai est nécessaire pour allouer une autre adresse IP, ce qui empêche d’ajouter des adresses IP de façon rapprochée.

## <a name="front-end-scaling"></a>Mise à l’échelle du serveur frontal ##

Dans un environnement ASEv2, lorsque vous augmentez la taille des instances de vos plans App Service, les workers sont automatiquement ajoutés pour les prendre en charge. Chaque environnement ASE est créé avec deux serveurs frontaux. De plus, les serveurs frontaux se mettent automatiquement à l’échelle sur le rythme d’un serveur frontal toutes les 15 instances dans vos plans App Service. Par exemple, si vous avez 15 instances, vous avez trois serveurs frontaux. Si vous effectuez une mise à l’échelle pour 30 instances, vous avez quatre serveurs frontaux, et ainsi de suite.

Ce nombre de serveurs frontaux doit être plus que suffisant pour la plupart des scénarios. Toutefois, vous pouvez le faire évoluer à un rythme plus rapide. Vous pouvez modifier le ratio sur la valeur d’un serveur frontal toutes les cinq instances. La modification du ratio est facturée. Pour en savoir plus, consultez [Tarification d’App Service][Pricing].

Les ressources du serveur frontal sont le point de terminaison HTTP/HTTPS pour l’environnement ASE. Avec la configuration de serveur frontal par défaut, l’utilisation de mémoire par serveur frontal se situe en permanence autour de 60 %. Les charges de travail client ne s’exécutent pas sur un serveur frontal. Le facteur clé pour un serveur frontal par rapport à la mise à l’échelle est l’UC, qui dépend principalement du trafic HTTPS.

## <a name="app-access"></a>Accès de l’application ##

Dans un environnement ASE externe, le domaine qui est utilisé lorsque vous créez des applications est différent de l’App Service multilocataire. Il inclut le nom de l’environnement App Service. Pour plus d’informations sur la création d’un environnement ASE externe, consultez [Create an App Service Environment][MakeExternalASE] (Créer un environnement App Service). Le nom de domaine dans un environnement ASE externe ressemble à *.&lt;asename&gt;.p.azurewebsites.net*. Par exemple, si votre ASE s’appelle _external-ase_, et si vous hébergez une application appelée _contoso_ dans cet environnement ASE, vous l’atteignez aux URL suivantes :

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

L’URL contoso.scm.external-ase.p.azurewebsites.net est utilisée pour accéder à la console Kudu ou pour publier votre application à l’aide de web deploy. Pour obtenir des informations sur la console Kudu, visionnez [Exploring the Super Secret Kudu Debug Console - with David Ebbo][Kudu] (Découverte de la console de débogage Kudu super secrète avec David Ebbo). La console Kudu offre une interface utilisateur web pour le débogage, le chargement et la modification des fichiers, etc.

Dans un environnement ASE d’équilibrage de charge interne, vous déterminez le domaine au moment du déploiement. Pour plus d’informations sur la création d’un environnement ASE d’équilibrage de charge interne, consultez [Create and use an ILB ASE][MakeILBASE] (Créer et utiliser un environnement ASE d’équilibrage de charge interne). Si vous spécifiez le nom de domaine _ilb-ase.info_, les applications se trouvant dans cet environnement ASE utilisent ce domaine lors de la création d’applications. Pour l’application nommée _contoso_, les URL sont :

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Publication ##

Tout comme vous le feriez avec l’App Service multilocataire, dans un environnement ASE, vous pouvez publier avec :

- Déploiement web.
- FTP.
- Intégration continue.
- Opération de glisser-déplacer dans la console Kudu.
- Un IDE tel que Visual Studio, Eclipse ou Intellij IDEA.

Avec un environnement ASE externe, toutes ces options de publication ont le même comportement. Pour plus d’informations, consultez [Déploiement dans Azure App Service][AppDeploy]. 

La principale différence avec la publication concerne un environnement ASE d’équilibrage de charge interne. Avec un environnement ASE d’équilibrage de charge interne, les points de terminaison de publication sont tous disponibles uniquement via l’équilibrage de charge interne. L’équilibrage de charge interne se trouve sur une adresse IP privée dans le sous-réseau de l’environnement ASE du réseau virtuel. Si vous n’avez pas d’accès réseau à l’équilibrage de charge interne, vous ne pouvez pas publier d’applications dans cet environnement ASE. Comme indiqué dans [Create and Use an Internal Load Balancer with an App Service Environment][MakeILBASE] (Créer et utiliser un équilibreur de charge interne avec un environnement App Service), vous devez configurer le DNS pour les applications du système. Cela inclut le point de terminaison GCL. S’ils ne sont pas définis correctement, vous ne pouvez pas publier. Vos IDE doivent également avoir un accès réseau à l’équilibreur de charge interne pour publier directement dans celui-ci.

Les systèmes d’intégration continue basés sur Internet, comme GitHub et Visual Studio Team Services ne fonctionnent pas avec un environnement ASE d’équilibrage de charge interne, car le point de terminaison de publication n’est pas accessible via Internet. À la place, vous devez utiliser un système d’intégration continue utilisant un modèle d’extraction, par exemple Dropbox.

Les points de terminaison de publication pour les applications d’un environnement ASE d’équilibreur de charge interne utilisent le domaine avec lequel l’environnement ASE d’équilibreur de charge interne a été créé. Vous pouvez le voir dans le profil de publication de l’application et dans le panneau du portail de l’application (dans **Vue d’ensemble** > **Bases** et également dans **Propriétés**). 

## <a name="pricing"></a>Tarifs ##

La référence (SKU) de tarification **Isolé** a été créée pour une utilisation avec ASEv2 uniquement. Tous les plans App Service qui sont hébergés dans un environnement ASEv2 se trouvent dans la référence SKU de tarification Isolé. Les tarifs du plan App Service Isolé peuvent varier selon la région. 

En plus de vos plans App Service, il existe un tarif fixe pour l’environnement ASE en lui-même. Le forfait ne change pas avec la taille de votre ASE et paie pour l’infrastructure ASE au taux de mise à l’échelle par défaut de 1 frontal pour 15 instances de plan App Service.  

Si le taux de mise à l’échelle par défaut de 1 frontal pour 15 instances de plan App Service n’est pas assez rapide, vous pouvez ajuster le rapport auquel les frontaux sont ajoutés ou la taille des frontaux.  Lorsque vous ajustez le rapport ou la taille, vous payez pour les cœurs frontaux qui ne seraient pas ajoutés par défaut.  

Par exemple, si vous ajustez le ratio de mise à l’échelle sur 10, un serveur frontal est ajouté toutes les 10 instances dans vos plans App Service. Le prix fixe couvre un taux de mise à l’échelle d’un serveur frontal toutes les 15 instances. Avec un ratio de mise à l’échelle de 10, vous payez pour le troisième serveur frontal qui est ajouté pour les 10 instances du plan App Service. Vous n’avez pas besoin de payer lorsque vous atteignez 15 instances, car il a été ajouté automatiquement.

Si vous avez ajusté la taille des frontaux à 2 cœurs, mais n’avez pas modifié le rapport, vous payez pour les cœurs supplémentaires.  Un ASE est créé avec 2 serveurs frontaux, ainsi vous payeriez pour 2 cœurs supplémentaires même en dessous du seuil de mise à l’échelle automatique si vous augmentez la taille à des serveurs frontaux sur 2 cœurs.

Pour en savoir plus, consultez [Tarification d’App Service][Pricing].

## <a name="delete-an-ase"></a>Supprimer un environnement ASE ##

Pour supprimer un environnement ASE : 

1. Utilisez **Supprimer** en haut du panneau **Environnement App Service**. 

2. Entrez le nom de votre environnement ASE pour confirmer que vous souhaitez le supprimer. Lorsque vous supprimez un environnement ASE, vous supprimez également l’ensemble de son contenu. 

    ![Suppression de ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
