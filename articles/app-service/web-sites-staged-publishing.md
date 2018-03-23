---
title: Configurer des environnements intermédiaires pour les applications web dans Azure App Service | Microsoft Docs
description: Découvrez comment utiliser la publication intermédiaire pour les applications web dans Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 18f6ef3997ba60f588040f641ebe9e9aca8d091a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurer des environnements intermédiaires dans Azure App Service
<a name="Overview"></a>

Lorsque vous déployez votre application web, votre application web Linux, votre mobile backend ou votre API dans [App Service](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez cibler un autre emplacement de déploiement que l’emplacement de production par défaut lorsque vous exécutez le niveau de plan **Standard** ou **Premium** d’App Service. Les emplacements de déploiement sont en fait des applications dynamiques pourvues de leur propre nom d’hôte. Les éléments de contenu et de configuration des applications peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. Le déploiement de votre application sur un emplacement de déploiement présente les avantages suivants :

* Vous pouvez valider les modifications d’une application dans un emplacement de déploiement intermédiaire avant de l’échanger avec l’emplacement de production.
* Déployer d’abord une application vers un emplacement et la basculer ensuite en production garantit que toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Cela permet d’éliminer les temps d’arrêt lors du déploiement de l’application. La redirection du trafic est transparente et aucune demande n'est abandonnée durant les opérations de basculement. Ce flux de travail peut être entièrement automatisé en configurant [Échange automatique](#Auto-Swap) lorsqu’aucune validation n’est requise avant l’échange.
* Après basculement, la précédente application de production se retrouve dans l’emplacement de l’application précédemment intermédiaire. Si les modifications basculées en production ne vous conviennent pas, vous pouvez effectuer le même basculement afin de récupérer immédiatement le contenu du précédent site qui vous plaisait.

Chaque niveau de plan App Service prend en charge un nombre différent d’emplacements de déploiement. Pour connaître le nombre d’emplacements pris en charge par le plan de votre application, consultez la page [Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/).

* Si votre application dispose de plusieurs emplacements, vous ne pouvez pas changer de plan.
* La mise à l'échelle est uniquement disponible pour les emplacements de site de production.
* La gestion des ressources liées est uniquement prise en charge pour les emplacements de site de production. Dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) uniquement, vous pouvez éviter cet impact potentiel sur un emplacement de production en déplaçant temporairement l’emplacement (autre que de production) vers un autre niveau de plan App Service. Notez que l’emplacement autre que de production doit une fois encore partager le même niveau que l’emplacement de production avant que vous ne puissiez échanger les deux emplacements.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Ajouter un emplacement de déploiement
Pour que vous puissiez activer plusieurs emplacements de déploiement, l’application doit s’exécuter au niveau **Standard** ou **Premium**.

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez le [panneau de ressources](../azure-resource-manager/resource-group-portal.md#manage-resources) de votre application.
2. Choisissez l’option **Emplacements de déploiement**, puis cliquez sur **Ajouter un emplacement**.
   
    ![Add a new deployment slot][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Si l’application ne s’exécute pas au niveau **Standard** ou **Premium**, vous recevez un message indiquant les niveaux pris en charge pour l’activation de la publication intermédiaire. À ce stade, vous pouvez sélectionner **Mettre à niveau** et accéder à l’onglet **Mettre à l’échelle** de votre application avant de continuer.
   > 
   > 
3. Dans le panneau **Ajouter un emplacement**, nommez l’emplacement, puis indiquez si vous souhaitez cloner la configuration de l’application à partir d’un autre emplacement de déploiement. Cliquez sur la coche pour continuer.
   
    ![Source de configuration][ConfigurationSource1]
   
    La première fois que vous ajoutez un emplacement, vous avez uniquement deux choix : cloner la configuration à partir de l’emplacement par défaut en production ou ne rien cloner du tout.
    Après avoir créé plusieurs emplacements, vous pourrez cloner la configuration depuis un emplacement autre que l'emplacement de production :
   
    ![Sources de configuration][MultipleConfigurationSources]
4. Dans le panneau des ressources de votre application, cliquez sur **Emplacements de déploiement**, puis cliquez sur un emplacement de déploiement pour ouvrir le panneau des ressources correspondant. Comme pour toute autre application, celui-ci contient un ensemble de mesures et de paramètres de configuration. Le nom de l’emplacement est indiqué en haut du panneau pour vous rappeler que c’est l’emplacement de déploiement qui affiché.
   
    ![Titre de l'emplacement de déploiement][StagingTitle]
5. Cliquez sur l’URL de l’application dans le panneau de l’emplacement. Notez que l’emplacement de déploiement possède son propre nom d’hôte et qu’il s’agit d’une application dynamique. Pour limiter l’accès public à l’emplacement de déploiement, consultez la page [Application web App Service : bloquer l’accès web aux emplacements de déploiement autres que de production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Il n’existe pas de contenu après la création de l’emplacement de déploiement. Vous pouvez effectuer un déploiement sur l'emplacement d'une autre branche référentielle, ou d'un référentiel complètement différent. Vous pouvez également modifier la configuration de l'emplacement. Utilisez le profil de publication ou les informations d'identification associées à l'emplacement de déploiement pour les mises à jour de contenu.  Par exemple, vous pouvez [publier sur cet emplacement avec Git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Configuration d’emplacements de déploiement
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Par ailleurs, après un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement (éléments propres à un emplacement). Les listes suivantes présentent la configuration changée lorsque vous effectuez des basculements d'emplacements.

**Paramètres échangés**:

* Paramètres généraux, par exemple versions d’infrastructure, 32/64 bits, sockets web
* Paramètres d’application (peuvent être configurés pour respecter un emplacement)
* Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
* Mappages de gestionnaires
* Paramètres de surveillance et de diagnostics
* Contenu WebJobs

**Paramètres non échangés**:

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats SSL et liaisons
* Paramètres de mise à l'échelle
* Planificateurs WebJobs

Pour lier un paramètre d’application ou une chaîne de connexion à un emplacement (aucun échange), accédez au panneau **Paramètres de l’application** d’un emplacement, puis cochez la case **Paramètre d’emplacement** correspondant aux éléments de configuration à lier à cet emplacement. Notez que si vous marquez un élément de configuration comme propre à un emplacement, cet élément ne pourra pas être échangé entre tous les emplacements de déploiement associés à l’application.

![Paramètres d’emplacement][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Échanger des emplacements de déploiement 
Vous pouvez échanger des emplacements de déploiement dans la vue **Vue d’ensemble** ou **Emplacements de déploiement** du panneau de ressources de votre application.

> [!IMPORTANT]
> Avant de basculer une application à partir d’un emplacement de déploiement vers un emplacement de production, assurez-vous que tous les paramètres non propres à un emplacement sont configurés comme vous le souhaitez dans la cible de l’échange.
> 
> 

1. Pour échanger des emplacements de déploiement, cliquez sur le bouton **Échanger** dans la barre de commandes de l’application ou dans celle d’un emplacement de déploiement.
   
    ![Bouton Swap][SwapButtonBar]

2. Assurez-vous que la source et la cible de l’échange sont définies correctement. En règle générale, la cible de l’échange correspond à l’emplacement de production. Cliquez sur **OK** pour terminer l’opération. À l’issue de l’opération, les emplacements de déploiement ont été échangés.

    ![Échange effectué](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pour le type d’échange **Échange avec aperçu**, consultez [Échange avec aperçu (échange multiphase)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Échange avec aperçu (échange multiphase)

L’échange avec l’aperçu, ou échange multiphase, simplifie la validation des éléments de configuration spécifiques d’un emplacement, tels que les chaînes de connexion.
Pour les charges de travail stratégiques, il est souhaitable de vérifier que l’application se comporte comme prévu lorsque la configuration de l’emplacement de production est appliquée, et cette vérification doit être effectuée *avant* le basculement de l’application en production. C’est précisément ce que l’échange avec aperçu permet de faire.

> [!NOTE]
> L’échange avec l’aperçu n’est pas pris en charge dans les applications web sous Linux.

Lorsque vous utilisez l’option **Échange avec aperçu** (voir [Échanger des emplacements de déploiement](#Swap)), App Service :

- Laisse l’emplacement de destination inchangé, de sorte que la charge de travail existante dans cet emplacement (de production, par exemple) n’est pas affectée.
- Applique les éléments de configuration de l’emplacement de destination à l’emplacement source, y compris les chaînes de connexion spécifiques de l’emplacement et les paramètres de l’application.
- Redémarre les processus de travail dans l’emplacement source à l’aide des éléments de configuration mentionnés ci-dessus.
- Lorsque vous effectuez l’échange : transfère l’emplacement source préinitialisé dans l’emplacement de destination. L’emplacement de destination est transféré dans l’emplacement source, comme dans le cadre d’un échange manuel.
- Lorsque vous annulez l’échange : réapplique les éléments de configuration de l’emplacement source à l’emplacement source.

Vous pouvez prévisualiser le comportement précis de l’application avec la configuration de l’emplacement de destination. Une fois la validation terminée, vous effectuez l’échange dans le cadre d’une étape distincte. L’avantage de cette étape est que l’emplacement source est déjà initialisé avec la configuration souhaitée et que les clients ne seront pas confrontés à des temps d’arrêt.  

Des exemples pour les applets de commande Azure PowerShell disponibles pour l’échange multiphase figurent dans les applets de commande Azure PowerShell de la section des emplacements de déploiement.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurer l’échange automatique
L’échange automatique simplifie les scénarios d’opérations de développement impliquant un déploiement de l’application en continu sans démarrage à froid ni temps d’arrêt pour les clients finaux. Si un emplacement de déploiement est configuré pour l’échange automatique en production, chaque fois que vous envoyez une mise à jour de votre code par une transmission de type push vers cet emplacement, App Service échange automatiquement l’application en production après l’avoir initialisée dans l’emplacement.

> [!IMPORTANT]
> Lorsque vous activez l’échange automatique pour un emplacement, vérifiez que la configuration de l’emplacement est exactement celle que vous souhaitez pour l’emplacement cible (en général, l’emplacement de production).
> 
> 

> [!NOTE]
> L’échange automatique n’est pas pris en charge dans les applications web sous Linux.

La configuration de l’échange automatique pour un emplacement est facile. Pour ce faire, procédez comme suit :

1. Dans **Emplacements de déploiement**, sélectionnez un emplacement autre que de production et choisissez **Paramètres de l’application** dans le panneau de ressources de cet emplacement.  
   
    ![][Autoswap1]
2. Sélectionnez **Activé** dans **Échange automatique**, ainsi que l’emplacement cible souhaité dans **Emplacement d’échange automatique**, puis cliquez sur **Enregistrer** dans la barre de commandes. Assurez-vous que la configuration de l’emplacement est celle que vous souhaitez pour l’emplacement cible.
   
    Dans l’onglet **Notifications**, la mention **RÉUSSITE** clignote en vert une fois l’opération terminée.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Pour tester l’échange automatique avec votre application, commencez par sélectionner un emplacement cible autre que de production dans **Emplacement d’échange automatique** afin de vous familiariser avec la fonctionnalité.  
   > 
   > 
3. Exécutez une transmission de code de type push vers cet emplacement de déploiement. L’échange automatique se produit peu après, et la mise à jour est appliquée dans l’URL de votre emplacement cible.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Pour rétablir une application de production après un échange
Si vous identifiez des erreurs de production après un basculement d'emplacements, rétablissez ces deux emplacements comme ils étaient, en les intervertissant immédiatement.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Mise en route personnalisée avant la permutation
Certaines applications peuvent nécessiter des actions personnalisées de mise en route. L’élément de configuration `applicationInitialization` du fichier web.config vous permet de spécifier les actions d’initialisation personnalisées à exécuter avant la réception d’une demande. L'opération de permutation attendra la fin de cette mise en route personnalisée. Voici un exemple de fragment web.config.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Pour supprimer un emplacement de déploiement
Ouvrez le panneau d’un emplacement de déploiement, cliquez sur **Vue d’ensemble** (page par défaut), puis cliquez sur **Supprimer** dans la barre de commandes.  

![Supprimer un emplacement de déploiement][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Cmdlets Azure PowerShell pour les emplacements de déploiement
Azure PowerShell est un module qui fournit des applets de commande pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement des applications dans Azure App Service.

* Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell et sur l’authentification d’Azure PowerShell avec votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Créer une application web
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Créer un emplacement de déploiement
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-review-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initialiser un échange avec aperçu (échange multiphase) et appliquer la configuration de l’emplacement de destination à l’emplacement source
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Annuler un échange en attente (échange avec aperçu) et restaurer la configuration de l’emplacement source
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Échanger des emplacements de déploiement
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Supprimer un emplacement de déploiement
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Commandes de l’interface de ligne de commande Azure pour les emplacements de déploiement
L’interface de ligne de commande (CLI) Azure fournit des commandes interplateformes fonctionnant avec Azure, notamment la prise en charge de la gestion des emplacements de déploiement d’App Service.

* Pour obtenir des instructions sur l’installation et la configuration de l’interface de ligne de commande Azure, et notamment des informations sur la connexion de cette dernière à votre abonnement Azure, consultez la rubrique [Installation et configuration de l’interface de ligne de commande Azure](../cli-install-nodejs.md).
* Pour répertorier les commandes disponibles pour Azure App Service dans l’interface de ligne de commande Azure, appelez `azure site -h`.

> [!NOTE] 
> Pour connaître les commandes de l’interface [Azure CLI 2.0](https://github.com/Azure/azure-cli) pour les emplacements de déploiement, consultez [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

- - -
### <a name="azure-site-list"></a>azure site list
Pour plus d’informations sur les applications de l’abonnement actuel, appelez **azure site list**, comme dans l’exemple suivant.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>azure site create
Pour créer un emplacement de déploiement, appelez **azure site create** et spécifiez le nom d’une application et de l’emplacement à créer, comme dans l’exemple suivant.

`azure site create webappslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l'option **--git** , comme dans l'exemple suivant.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>azure site swap
Pour appliquer l’emplacement de déploiement mis à jour à l’application de production, utilisez la commande **azure site swap** pour effectuer un échange, comme dans l’exemple suivant. L’application de production ne subira ni temps d’arrêt, ni démarrage à froid.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>azure site delete
Pour supprimer un emplacement de déploiement dont vous n'avez plus besoin, utilisez la commande **azure site delete** , comme dans l'exemple suivant.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Visualisez une application web en action. [Essayez App Service](https://azure.microsoft.com/try/app-service/) dès maintenant et créez une première application temporaire. Aucune carte de crédit ni aucun engagement ne sont nécessaires.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
[Application web Azure App Service : bloquer l’accès web aux emplacements de déploiement hors production](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[Introduction à App Service sur Linux](../app-service/containers/app-service-linux-intro.md)
[Essai gratuit de Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

