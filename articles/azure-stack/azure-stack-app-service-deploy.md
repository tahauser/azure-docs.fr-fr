---
title: 'Déployer App Services : Azure Stack | Microsoft Docs'
description: Instructions détaillées pour le déploiement d’App Service dans Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 2d26aedf37727a4e3d687cdc6c748268d546f60f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Ajouter un fournisseur de ressources App Service à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!IMPORTANT]
> Appliquez la mise à jour 1802 à votre système intégré Azure Stack ou déployez le dernier kit de développement Azure Stack avant de déployer Azure App Service.
>
>

En tant qu’opérateur cloud Azure Stack, vous pouvez donner à vos utilisateurs la possibilité de créer des applications web et API. Pour ce faire, vous devez d’abord ajouter le [fournisseur de ressources App Service](azure-stack-app-service-overview.md) à votre déploiement Azure Stack comme décrit dans cet article. Après avoir installé le fournisseur de ressources App Service, vous pouvez l’inclure dans vos offres et vos plans. Les utilisateurs peuvent ensuite s’abonner pour obtenir le service et commencer à créer des applications.

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation, assurez-vous d’avoir suivi notre guide dans [Avant de commencer](azure-stack-app-service-before-you-get-started.md).
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

L’installation du fournisseur de ressources App Service dans votre environnement Azure Stack peut prendre au moins une heure en fonction du nombre d’instances de rôle que vous avez choisi de déployer. Au cours de ce processus, le programme d’installation :

* Crée un conteneur d’objets blob dans le compte de stockage Azure Stack spécifié.
* Crée une zone DNS et les entrées pour App Service.
* Inscrit le fournisseur de ressources App Service.
* Inscrit les éléments de la galerie App Service.

Pour déployer le fournisseur de ressources App Service, procédez comme suit :

1. Exécutez appservice.exe en tant qu’administrateur à partir d’un ordinateur pouvant atteindre le point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack.

2. Cliquez sur **Déployer App Service ou effectuer une mise à niveau vers la dernière version**.

    ![Programme d’installation App Service][1]

3. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

4. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

5. Vérifiez l’exactitude des informations de configuration du Cloud App Service. Si vous avez utilisé les paramètres par défaut au cours du déploiement du Kit de développement Azure Stack, vous pouvez accepter les valeurs par défaut ici. Toutefois, si vous avez personnalisé les options lors du déploiement d’Azure Stack, ou si vous effectuez le déploiement sur un système intégré, vous devez modifier les valeurs dans cette fenêtre pour le refléter. Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez remplacer votre point de terminaison Azure Resource Manager de locataire Azure Stack par management.&lt;région&gt;.mycloud.com. Après avoir confirmé vos informations, cliquez sur **suivant**.

    ![Programme d’installation App Service][2]

6. Sur la page suivante :
    1. Cliquez sur le bouton **Se connecter** situé en regard de la zone **Abonnements Azure Stack**.
        * Si vous utilisez Azure Active Directory (Azure AD), entrez votre compte et mot de passe d’administrateur Azure AD que vous avez indiqués lors du déploiement d’Azure Stack. Cliquez sur **Se connecter**.
        * Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis cliquez sur **Se connecter**.
    2. Dans la zone **Abonnements Azure Stack**, sélectionnez **Abonnement au fournisseur par défaut**.
    3. Dans la zone **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si effectuez votre déploiement sur le Kit de développement Azure Stack.

    ![Programme d’installation App Service][3]

4. Vous avez maintenant la possibilité d’effectuer le déploiement sur un réseau virtuel existant conformément à la configuration dans les étapes mentionnées [ici](azure-stack-app-service-before-you-get-started.md#virtual-network), ou d’autoriser le programme d’installation App Service à créer un réseau virtuel et des sous-réseaux associés.
    1. Sélectionnez **Créer un réseau virtuel avec les paramètres par défaut**, acceptez les valeurs par défaut, puis cliquez sur **Suivant** ; ou
    2. Sélectionnez **Utiliser un réseau virtuel et des sous-réseaux existants**.
        1. Sélectionnez le **Groupe de ressources** qui contient votre réseau virtuel.
        2. Choisissez le nom de **Réseau virtuel** sur lequel vous voulez effectuer le déploiement.
        3. Sélectionnez les valeurs **Sous-réseau** appropriées pour chacun des sous-réseaux de rôle nécessaires.
        4. Cliquez sur **Suivant**

    ![Programme d’installation App Service][4]

7. Entrez les informations du partage de fichiers, puis cliquez sur **Suivant**. L’adresse du partage de fichiers doit utiliser le nom de domaine complet ou l’adresse IP de votre serveur de fichiers. Par exemple, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.

   > [!NOTE]
   > Le programme d’installation tente de tester la connectivité au partage de fichiers avant de continuer.  Toutefois, si vous avez choisi d’effectuer le déploiement sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter au partage de fichiers, auquel cas un avertissement s’affiche, vous demandant si vous souhaitez continuer.  Vérifiez les informations du partage de fichiers et continuez si elles sont correctes.
   >
   >

   ![Programme d’installation App Service][7]

8. Sur la page suivante :
    1. Dans la zone **ID d’application d’identité**, entrez le GUID de l’application que vous utilisez pour l’identité (à partir d’Azure AD).
    2. Dans la zone **fichier de certificat d’application d’identité**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    3. Dans la zone **Mot de passe du certificat d’application d’identité**, entrez le mot de passe du certificat. Ce mot de passe est celui que vous avez noté lorsque vous avez utilisé le script pour créer les certificats.
    4. Dans la zone **fichier de certificat racine Azure Resource Manager**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    5. Cliquez sur **Suivant**.

    ![Programme d’installation App Service][9]

9. Pour chacune des trois zones de fichier de certificat, cliquez sur **Parcourir** et accédez au fichier de certificat approprié. Vous devez fournir le mot de passe pour chaque certificat. Ces certificats sont ceux que vous avez créés lors de l’étape [Créer les certificats requis](azure-stack-app-service-before-you-get-started.md#get-certificates). Cliquez sur **Suivant** après avoir entré toutes les informations.

    | Box | Exemple de nom de fichier de certificat |
    | --- | --- |
    | **Fichier de certificat SSL par défaut d’App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL d’API App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL Publisher App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si vous avez utilisé un suffixe de domaine différent lorsque vous avez créé les certificats, vos noms de fichier n’utilisent pas *local.AzureStack.external*. Utilisez alors vos informations de domaine personnalisées.

    ![Programme d’installation App Service][10]

10. Entrez les détails de SQL Server pour l’instance de serveur utilisée pour héberger les bases de données du fournisseur de ressources App Service, puis cliquez sur **Suivant**. Le programme d’installation valide les propriétés de connexion SQL.

    > [!NOTE]
    > Le programme d’installation tente de tester la connectivité au serveur SQL Server avant de continuer.  Toutefois, si vous avez choisi d’effectuer le déploiement sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter au serveur SQL Server, auquel cas un avertissement s’affiche, vous demandant si vous souhaitez continuer.  Vérifiez les informations SQL Server et continuez si elles sont correctes.
    >
    >

    ![Programme d’installation App Service][11]

11. Passez en revue de l’instance de rôle et les options de la référence SKU. Les valeurs par défaut sont remplies avec le nombre minimal d’instance et la référence (SKU) minimale pour chaque rôle dans un déploiement ASDK. Un résumé des exigences en termes de processeur virtuel et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, cliquez sur **Suivant**.

    > [!NOTE]
    > Pour les déploiements de production, suivez le guide dans [Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack](azure-stack-app-service-capacity-planning.md).
    >
    >

    | Rôle | Nombre minimal d’instances | Nombre minimal de références (SKU) | Notes |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Gère et maintient l’intégrité du Cloud App Service. |
    | gestion | 1 | Standard_A2 - (2 processeurs virtuels, 3 584 Mo) | Gère les points de terminaison App Service Azure Resource Manager et d’API, les extensions du portail (admin, locataire, portail Functions) et du service des données. Pour prendre en charge le basculement, augmenter les instances recommandées à 2. |
    | Publisher | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Publie du contenu via FTP et un déploiement web. |
    | FrontEnd | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Achemine les demandes vers les applications App Service. |
    | Worker partagé | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Héberge les applications web ou d’API et Azure Functions. Il peut être nécessaire d’ajouter plus d’instances. En tant qu’opérateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un processeur virtuel. |

    ![Programme d’installation App Service][13]

    > [!NOTE]
    > **Windows Server 2016 Core n’est pas une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack.  N’utilisez pas d’images d’évaluation pour les déploiements de production.**

12. Dans la zone **Sélectionner l’image de plateforme**, choisissez votre image de machine virtuelle Windows Server 2016 de déploiement parmi les images disponibles dans le fournisseur de ressources de calcul pour le cloud App Service. Cliquez sur **Suivant**.

13. Sur la page suivante :
     1. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle du rôle Worker.
     2. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle des autres rôles.
     3. Cliquez sur **Suivant**.

    ![Programme d’installation App Service][15]    

14. Sur la page de résumé :
    1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
    2. Si les configurations sont correctes, cochez la case.
    3. Cliquez sur **Suivant** pour commencer le déploiement.

    ![Programme d’installation App Service][16]

15. Sur la page suivante :
    1. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack prend environ 60 minutes avec les sélections par défaut.
    2. Une fois le programme d’installation terminé avec succès, cliquez sur **Quitter**.

    ![Programme d’installation App Service][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Valider l’installation App Service sur Azure Stack

1. Dans le portail administrateur d’Azure Stack, allez dans **Administration - App Service**.

2. Dans la vue d’ensemble, sous les statuts, vérifiez que le **Statut** affiche **Tous les rôles sont prêts**.

    ![Gestion d’App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Tester App Service sur Azure Stack

Après avoir déployé et inscrit le fournisseur de ressources App Service, testez-le pour vous assurer que les utilisateurs peuvent déployer des applications web et d’API.

> [!NOTE]
> Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Ensuite, vous devez avoir un abonnement locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
Vous *devez* disposer d’un abonnement locataire pour créer des applications qui utilisent App Service sur Azure Stack. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail d’administration sont liées à l’administration du fournisseur de ressources App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références.
>
Pour créer des applications web, d’API et Azure Functions, vous devez utiliser le portail du locataire et disposer d’un abonnement locataire.

1. Dans le portail du locataire Azure Stack, cliquez sur **Nouveau** > **Web + Mobile** > **Application web**.

2. Dans le panneau **Application web**, tapez un nom dans la zone **Application web**.

3. Sous **Groupe de ressources**, cliquez sur **Nouveau**. Tapez un nom dans la zone **Groupe de ressources**.

4. Cliquez sur **Plan App Service/Emplacement** > **Créer**.

5. Dans le panneau **Plan App Service**, tapez un nom dans la zone **Plan App Service**.

6. Cliquez sur **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

7. En moins d’une minute, une vignette pour la nouvelle application web s’affiche dans le tableau de bord. Cliquez sur la vignette.

8. Dans le panneau **Application web**, cliquez sur **Parcourir** pour afficher le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail du locataire Azure Stack, cliquez sur **+**, accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire comme SQL ou MySQL.

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

3. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

- [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
