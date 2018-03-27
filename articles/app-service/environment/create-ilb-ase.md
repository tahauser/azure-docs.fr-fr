---
title: Créer et utiliser un équilibreur de charge interne avec un environnement Azure App Service
description: Informations détaillées sur la création et l’utilisation d’un environnement Azure App Service isolé d’Internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 0d08d140ab338d8c742277835fdfb4316862f07b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Créer et utiliser un équilibreur de charge interne avec un environnement App Service #

 Un environnement Azure App Service est un déploiement d’Azure App Service dans un sous-réseau de réseau virtuel Azure. Il existe deux façons de déployer un environnement App Service (ASE, App Service Environment) : 

- avec une adresse IP virtuelle sur une adresse IP externe, solution souvent appelée ASE externe ;
- avec une adresse IP virtuelle sur une adresse IP interne, solution souvent appelée ASE ILB, car le point de terminaison interne est un équilibreur de charge interne (ILB, Internal Load Balancer). 

Cet article explique comment créer un ASE ILB. Pour une présentation de l’ASE, voir [Présentation des environnements App Service][Intro]. Pour savoir comment créer un ASE externe, voir [Créer un environnement App Service externe][MakeExternalASE].

## <a name="overview"></a>Vue d'ensemble ##

Vous pouvez déployer un ASE avec un point de terminaison accessible via Internet ou avec une adresse IP de votre réseau virtuel. Pour définir l’adresse IP sur une adresse de réseau virtuel, l’ASE doit être déployé avec un ILB. Lorsque vous déployez votre ASE avec un ILB, vous devez indiquer :

-   votre propre domaine que vous utilisez lorsque vous créez vos applications ;
-   le certificat utilisé pour le protocole HTTPS ;
-   la gestion DNS pour votre domaine.

En retour, vous pouvez effectuer des tâches telles que :

-   héberger des applications intranet en toute sécurité dans le cloud, auquel vous accédez via un VPN de site à site ou Azure ExpressRoute ;
-   héberger des applications dans le cloud qui ne figurent pas dans les serveurs DNS publics ;
-   créer des applications principales isolées d’Internet auxquelles vos applications frontales peuvent s’intégrer en toute sécurité.

### <a name="disabled-functionality"></a>Fonctionnalités désactivées ###

Lorsque vous utilisez un ASE ILB, vous ne pouvez pas effectuer certaines opérations :

-   utiliser SSL basé sur IP ;
-   attribuer des adresses IP à des applications spécifiques ;
-   acheter et utiliser un certificat avec une application via le portail Azure. Vous pouvez obtenir des certificats directement auprès d’une autorité de certification et les utiliser avec vos applications. Vous ne pouvez pas les obtenir via le portail Azure.

## <a name="create-an-ilb-ase"></a>Créer un environnement ASE ILB ##

Pour créer un ILB ASE :

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Web + Mobile** > **App Service Environment**.

2. Sélectionnez votre abonnement.

3. Sélectionnez ou créez un groupe de ressources.

4. Sélectionnez ou créez un réseau virtuel.

5. Si vous sélectionnez un réseau virtuel existant, vous devez créer un sous-réseau pour accueillir l’ASE. Veillez à définir une taille de sous-réseau suffisamment grande pour s’adapter à toute croissance éventuelle à venir de votre environnement ASE. Nous recommandons la taille `/25`, qui comprend 128 adresses et peut gérer un ASE de taille maximale. La taille minimale que vous pouvez sélectionner est `/28`. Selon les besoins de l’infrastructure, cette taille peut être mise à l’échelle jusqu’à un maximum de 11 instances.

    * Allez au-delà du nombre maximal par défaut de 100 instances dans vos plans App Service.

    * Mettez à l’échelle jusqu’à près de 100 instances, mais avec une mise à l’échelle du serveur frontal plus rapide.

6. Sélectionnez **Réseau virtuel/Emplacement** > **Configuration du réseau virtuel**. Définissez le **Type d’adresse IP virtuelle** sur **Interne**.

7. Entrez un nom de domaine. Ce domaine est celui utilisé pour les applications créées dans cet ASE. Quelques restrictions s’appliquent. Ce ne peut pas être :

    * net   

    * azurewebsites.net

    * p.azurewebsites.net

    * &lt;asename&gt;.p.azurewebsites.net

   Le nom de domaine personnalisé utilisé pour les applications et le nom de domaine utilisé par votre ASE ne peut pas se chevaucher. Pour un ASE ILB dont le nom de domaine est _contoso.com_, vous ne pouvez pas utiliser pour vos applications des noms de domaine personnalisés tels que :

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Si vous connaissez les noms de domaine personnalisés de vos applications, choisissez pour l’ASE ILB un domaine qui n’est pas en conflit avec ces noms de domaine personnalisés. Dans cet exemple, vous pouvez utiliser quelque chose comme *contoso-internal.com* pour le domaine de votre ASE, car cela n’est pas en conflit avec les noms de domaines personnalisés qui se terminent par *. contoso.com*.

8. Sélectionnez **OK**, puis **Créer**.

    ![Création d’un environnement App Service][1]

Le panneau **Réseau virtuel** contient une option **Configuration du réseau virtuel**. Vous pouvez l’utiliser pour sélectionner une adresse IP virtuelle externe ou interne. La valeur par défaut est **Externe**. Si vous sélectionnez **Externe**, votre ASE utilise une adresse IP virtuelle accessible via Internet. Si vous sélectionnez **Interne**, votre ASE est configuré avec un ILB sur une adresse IP au sein de votre réseau virtuel.

La sélection de **Interne** a pour effet d’écarter la possibilité d’ajouter des adresses IP à votre ASE. Au lieu de cela, vous devez fournir le domaine de l’ASE. Dans un environnement ASE pourvu d’une adresse IP virtuelle externe, le nom de l’environnement ASE est utilisé dans le domaine pour les applications créées dans cet environnement ASE.

Si vous définissez **Type d’adresse IP virtuelle** sur **Interne**, le nom de votre environnement ASE n’est pas utilisé dans le domaine de l’environnement ASE. Vous spécifiez explicitement le domaine. Si votre domaine est *contoso.corp.net* et que vous créez une application dans cet ASE nommé *timereporting*, l’URL de cette application est timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Créer une application dans un ASE ILB ##

Pour créer une application dans un ASE ILB, procédez de la même façon que pour créer une application dans un ASE normalement.

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Web + Mobile** > **Web** ou **Mobile** ou **API App**.

2. Entrez le nom de l’application.

3. Sélectionnez l’abonnement.

4. Sélectionnez ou créez un groupe de ressources.

5. Sélectionnez ou créez un plan App Service. Si vous souhaitez créer un plan App Service, sélectionnez votre ASE en tant qu’emplacement. Sélectionnez le pool de workers dans lequel vous souhaitez créer votre plan App Service. Lorsque vous créez le plan App Service, sélectionnez votre ASE en tant qu’emplacement et pool de workers. Lorsque vous spécifiez le nom de l’application, le domaine sous le nom de votre application est remplacé par celui de votre ASE.

6. Sélectionnez **Créer**. Si vous voulez que l’application apparaisse sur votre tableau de bord, activez la  case à cocher **Épingler au tableau de bord**.

    ![Création de plan App Service][2]

    Sous **Nom de l’application**, le nom du domaine est mis à jour pour refléter le domaine de votre ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validation après la création de l’ASE ILB ##

Un ASE ILB est légèrement différent d’un ASE non-ILB. Comme signalé, vous devez gérer votre propre DNS. Vous devez également fournir votre propre certificat pour les connexions HTTPS.

Une fois votre ASE créé, le nom de domaine indique le domaine que vous avez spécifié. Un nouvel élément nommé **Certificat ILB** apparaît dans le menu **Paramètre**. L’ASE est créé avec un certificat ne spécifiant pas le domaine de l’ASE ILB. Si vous utilisez l’ASE avec ce certificat, votre navigateur vous indique qu’il n’est pas valide. Ce certificat facilite le test HTTPS, mais vous devez charger votre propre certificat qui est lié à votre domaine ASE ILB. Cette étape est nécessaire, que votre certificat soit auto-signé ou acquis auprès d’une autorité de certification.

![Nom de domaine de l’ASE ILB][3]

Votre ASE ILB a besoin d’un certificat SSL valide. Vous pouvez recourir à des autorités de certification internes, acheter un certificat à un émetteur externe, ou utiliser un certificat auto-signé. Quelle que soit la source du certificat SSL, les attributs de certificat suivants doivent être configurés correctement :

* **Objet** : cet attribut doit être défini sur *.votre-domaine-racine-ici.
* **Autre nom de l’objet** : cet attribut doit inclure à la fois **.votre-domaine-racine-ici* et **.scm.votre-domaine-racine-ici*. Les connexions SSL au site SCM/Kudu associé à chaque application utilisent une adresse sous la forme *nom-de-votre-application.scm.votre-domaine-racine-ici*.

Convertissez/enregistrez le certificat SSL en tant que fichier de format .pfx. Le fichier .pfx doit inclure tous les certificats racines et intermédiaires. Sécurisez-le avec un mot de passe.

Si vous souhaitez créer un certificat auto-signé, vous pouvez utiliser les commandes PowerShell ici. Veillez à utiliser le nom de domaine de votre ASE ILB à la place du domaine *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

Le certificat que ces commandes PowerShell génèrent est signalé par les navigateurs, car il n’a pas été créé par une autorité de certification figurant dans la chaîne d’approbation de votre navigateur. Pour obtenir un certificat auquel votre navigateur fait confiance, vous devez vous le procurer auprès d’une autorité de certification commerciale figurant dans la chaîne d’approbation de votre navigateur. 

![Définir un certificat ILB][4]

Pour charger vos propres certificats et tester l’accès :

1. Une fois l’ASE créé, accédez à son interface utilisateur. Sélectionnez **ASE** > **Paramètres** > **Certificat ILB**.

2. Pour définir le Certificat ILB, sélectionnez le fichier .pfx du certificat, puis entrez le mot de passe. Cette étape prend un certain temps. Un message s’affiche, indiquant qu’une opération de chargement est en cours.

3. Obtenez l’adresse de l’ILB pour votre ASE. Sélectionnez **ASE** > **Propriétés** > **Adresse IP virtuelle**.

4. Une fois l’ASE créé, créez une application web dans celui-ci.

5. Créez une machine virtuelle si vous n’en avez pas dans ce réseau virtuel.

    > [!NOTE] 
    > N’essayez pas de créer cette machine virtuelle dans le même sous-réseau que l’ASE, car cela échouera ou provoquera des problèmes.
    >
    >

6. Définissez le service DNS pour le domaine de votre environnement ASE. Vous pouvez utiliser un caractère générique avec votre domaine dans votre DNS. Pour effectuer des tests simples, modifiez le fichier hosts sur votre machine virtuelle afin de définir le nom de l’application web sur l’adresse IP de l’adresse IP virtuelle :

    a. Si le nom de domaine de votre ASE est _.ilbase.com_ et que vous créez l’application web nommée _mytestapp_, l’adresse de celle-ci est _mytestapp.ilbase.com_. Définissez ensuite _mytestapp.ilbase.com_ pour résoudre cette adresse en adresse de l’ILB. (Sur Windows, le fichier hosts se situe sous _C:\Windows\System32\drivers\etc\_).

    b. Pour tester la publication du déploiement web ou l’accès à la console avancée, créez un enregistrement pour _mytestapp.scm.ilbase.com_.

7. Utilisez un navigateur sur cette machine virtuelle et accédez à http://mytestapp.ilbase.com. (ou au nom quelconque de votre application web avec votre domaine).

8. Utilisez un navigateur sur cette machine virtuelle et accédez à https://mytestapp.ilbase.com. Si vous utilisez un certificat auto-signé, acceptez le manque de sécurité.

    L’adresse IP de votre ILB est répertoriée sous **Adresses IP**. Cette section répertorie également les adresses IP utilisées par l’adresse IP virtuelle externe et pour le trafic de gestion entrant.

    ![Adresse IP de l’ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Tâches Web, fonctions et l’environnement App Service ILB ##

Les fonctions et les tâches Web sont prises en charge sur un environnement App service ILB, mais pour que le portail interagisse avec eux, vous devez disposer d’un accès réseau vers le site SCM.  Cela signifie que votre navigateur doit être sur un ordinateur hôte qui se trouve dans ou connecté au réseau virtuel.  

Si vous utilisez Azure Functions sur un environnement App Service ILB, il se peut que vous rencontriez le message d’erreur suivant : « Nous ne pouvons pas récupérer vos fonctions pour l’instant. Veuillez réessayer plus tard. » Cette erreur se produit parce que l’interface utilisateur des fonctions exploite le site SCM via HTTPS et le certificat racine n’est pas dans la chaîne d’approbation du navigateur. Les tâches Web présente un problème similaire. Pour éviter ce problème, vous pouvez effectuer une des opérations suivantes :

- ajouter le certificat à votre magasin de certificats approuvé ; cela débloque Edge et Internet Explorer.
- Utiliser Chrome et accéder d’abord au site SCM, accepter le certificat non approuvé, puis accéder au portail.
- Utiliser un certificat commercial qui figure dans la chaîne d’approbation de votre navigateur.  Il s’agit de la meilleure option.  

## <a name="dns-configuration"></a>Configuration DNS ##

Lorsque vous utilisez une adresse IP virtuelle externe, le DNS est géré par Azure. Toute application créée dans votre environnement ASE est automatiquement ajoutée au service Azure DNS, qui est un service DNS public. Dans un environnement ASE ILB, vous devez gérer votre propre service DNS. Pour un domaine donné tel que _contoso.net_, vous devez créer des enregistrements DNS A dans votre service DNS, qui pointent vers votre adresse ILB pour :

- *.contoso.net
- *.scm.contoso.net

Si le domaine de votre ASE ILB est utilisé pour plusieurs éléments en dehors de cet ASE, il se peut que vous deviez gérer le DNS par nom d’application. Cette méthode est complexe, car vous devez ajouter chaque nouveau nom d’application dans votre DNS lors de la création de celui-ci. C’est pourquoi nous vous recommandons d’utiliser un domaine dédié.

## <a name="publish-with-an-ilb-ase"></a>Publier avec un ASE ILB ##

Pour chaque application créée, il existe deux points de terminaison. Dans un ASE ILB, vous avez *&lt;nom d’application>.&lt;domaine ASE ILB>* et *&lt;nom d’application>.scm.&lt;>*. 

Le nom du site SCM vous dirige vers la console Kudu nommée **Portail avancé** au sein du portail Azure. La console Kudu vous permet d’afficher des variables d’environnement, d’explorer le disque, d’utiliser une console, et bien plus encore. Pour plus d’informations, voir [Kudu console for Azure App Service (Console Kudu pour Azure App Service)][Kudu]. 

Dans l’App Service mutualisé et un ASE externe, il existe une authentification unique entre le portail Azure et la console Kudu. Pour l’ASE ILB, vous devez utiliser vos informations d’identification de publication pour vous connecter à la console Kudu.

Les systèmes d’intégration continue basés sur Internet, comme GitHub et Visual Studio Team Services ne fonctionnent pas avec un ASE ILB, car le point de terminaison de publication n’est pas accessible via Internet. À la place, vous devez utiliser un système d’intégration continue utilisant un modèle d’extraction, par exemple Dropbox.

Les points de terminaison de publication pour les applications d’un environnement ASE d’équilibreur de charge interne utilisent le domaine avec lequel l’environnement ASE d’équilibreur de charge interne a été créé. Ce domaine apparaît dans le profil de publication de l’application et sur le panneau du portail de l’application (**Vue d’ensemble** > **Bases** et également **Propriétés**). Si vous disposez d’un ASE ILB avec le sous-domaine *contoso.net* et d’une application nommée *mytest*, utilisez *mytest.contoso.net* pour le FTP et *mytest.scm.contoso.net* pour le déploiement web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Coupler un ASE ILB avec un dispositif WAF ##

Azure App Service applique de nombreuses mesures de sécurité pour protéger le système. Celles-ci permettent également de déterminer si une application a été piratée. La meilleure protection pour une application web consiste associer une plateforme d’hébergement telle qu’Azure App Service à un pare-feu d’applications web (WAF). Comme l’ASE ILB possède un point de terminaison d’application isolé du réseau, il convient parfaitement à cet usage.

Pour en savoir plus sur la configuration de votre ASE ILB avec un dispositif WAF, voir [Configuration d’un pare-feu d’applications web (WAF) pour un environnement App Service][ASEWAF]. Cet article explique comment utiliser une appliance virtuelle Barracuda avec votre ASE. Une autre option consiste à utiliser Azure Application Gateway. Azure Application Gateway utilise les règles de base OWASP pour sécuriser les applications placées derrière lui. Pour plus d’informations sur Azure Application Gateway, voir [Pare-feu d’applications web (WAF)][AppGW].

## <a name="get-started"></a>Prise en main ##

* Pour bien démarrer avec les ASE, voir [Présentation de l’environnement App Service Environment][Intro].
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
