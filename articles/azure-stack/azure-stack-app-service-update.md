---
title: Mettre à jour Azure App Service sur Azure Stack | Microsoft Docs
description: Instructions détaillées pour la mise à jour d’Azure App Service dans Azure Stack
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
ms.openlocfilehash: 151456bbb7f9331730e640e4bece3872c3c92f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="update-azure-app-service-on-azure-stack"></a>Mettre à jour Azure App Service sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!IMPORTANT]
> Appliquez la mise à jour 1802 à votre système Azure Stack intégré ou déployez le dernier kit de développement Azure Stack avant le déploiement d’Azure App Service.
>
>

En suivant les instructions de cet article, vous pouvez mettre à niveau le [fournisseur de ressources App Service](azure-stack-app-service-overview.md) déployé dans un environnement Azure Stack qui est connecté à Internet.

> [!IMPORTANT]
> Avant d’exécuter la mise à niveau, assurez-vous d’avoir déjà effectué le [déploiement d’Azure App Service sur le fournisseur de ressources Azure Stack](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

Au cours de ce processus, la mise à niveau va :

* Détecter le déploiement précédent d’App Service
* Préparer tous les packages de mise à jour et nouvelles versions de toutes les bibliothèques OSS à déployer
* Charger vers le stockage
* Mettre à niveau tous les rôles App Service (contrôleurs, gestion, frontal, publication et Worker)
* Mettre à jour les définitions de groupe identique d’App Service
* Mettre à jour le manifeste du fournisseur de ressources App Service

> [!IMPORTANT]
> Le programme d’installation d’App Service doit être exécuté sur une machine pouvant atteindre le point de terminaison Azure Stack Administrator Azure Resource Manager.
>
>

Pour mettre à niveau votre déploiement d’App Service sur Azure Stack, procédez comme suit :

1. Téléchargez le [Programme d’installation App Service](https://aka.ms/appsvcupdate1installer)

2. Exécutez appservice.exe en tant qu’administrateur

    ![Programme d’installation App Service][1]

3. Cliquez sur **Deploy App Service or upgrade to the latest version** (Déployer App Service ou effectuer une mise à niveau vers la dernière version).

4. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Assurez-vous que le point de terminaison Azure Stack Azure Resource Manager et les informations sur le locataire Active Directory sont corrects. Si vous avez utilisé les paramètres par défaut au cours du déploiement du Kit de développement Azure Stack, vous pouvez accepter les valeurs par défaut ici. Toutefois, si vous avez personnalisé les options lors du déploiement d’Azure Stack, vous devez modifier les valeurs dans cette fenêtre pour les faire correspondre. Par exemple, si vous utilisez le suffixe de domaine *mycloud.com*, vous devez modifier votre point de terminaison Azure Stack Azure Resource Manager sur *management.region.mycloud.com*. Après avoir confirmé vos informations, cliquez sur **suivant**.

    ![Informations sur Azure Stack Cloud][2]

7. Sur la page suivante :

   1. Cliquez sur le bouton **Se connecter** situé en regard de la zone **Abonnements Azure Stack**.
        * Si vous utilisez Azure Active Directory (Azure AD), entrez votre compte et mot de passe d’administrateur Azure AD que vous avez indiqués lors du déploiement d’Azure Stack. Cliquez sur **Se connecter**.
        * Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : *cloudadmin@azurestack.local*. Entrez votre mot de passe, puis cliquez sur **Se connecter**.
   2. Sélectionnez votre abonnement dans la zone **Abonnements Azure Stack**.
   3. Dans la zone **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si effectuez votre déploiement sur le Kit de développement Azure Stack.
   4. Si un déploiement App Service existant est détecté, le groupe de ressources et le compte de stockage sont renseignés et grisés.
   5. Cliquez sur **Suivant** pour passer en revue le résumé de la mise à niveau.

    ![Installation d’App Service détectée][3]

8. Sur la page de résumé :
   1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
   2. Si les configurations sont correctes, cochez la case.
   3. Pour lancer la mise à niveau, cliquez sur **Suivant**.

       ![Résumé de la mise à niveau d’App Service][4]

9. Page de progression de la mise à niveau :
    1. Suivez la progression de la mise à niveau. La durée de la mise à niveau d’App Service sur Azure Stack varie en fonction des nombre d’instances de rôle déployées.
    2. Une fois la mise à niveau terminée avec succès, cliquez sur **Quitter**.

        ![Progression de la mise à niveau d’App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-tools-paas-services.md).

* [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
