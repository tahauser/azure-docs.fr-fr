---
title: Utiliser une image de la Place de marché Microsoft Azure pour créer une machine virtuelle Terraform pour Linux avec Managed Service Identity
description: Utilisez une image de la Place de marché afin de créer une machine virtuelle Terraform pour Linux avec Managed Service Identity et la gestion de l’état à distance pour déployer facilement des ressources sur Azure.
keywords: terraform, devops, MSI, machine virtuelle, état à distance, azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Utiliser une image de la Place de marché Microsoft Azure pour créer une machine virtuelle Terraform pour Linux avec Managed Service Identity

Cet article explique comment utiliser une [image de la Place de marché Terraform](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) pour créer une machine virtuelle `Ubuntu Linux VM (16.04 LTS)` avec la dernière version de [Terraform](https://www.terraform.io/intro/index.html) installée et configurée à l’aide de [MSI (Managed Service Identity)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Cette image configure également un backend distant pour activer la gestion de [l’état à distance](https://www.terraform.io/docs/state/remote.html) à l’aide de Terraform. En utilisant l’image de la Place de marché Terraform, vous êtes prêt à démarrer avec Terraform sur Azure en quelques minutes, sans même avoir besoin d’installer Terraform et configurer l’authentification manuellement. 

Cette image de machine virtuelle Terraform ne génère pas de frais logiciels. Vous payez uniquement les frais d’utilisation matérielle Azure en fonction de la taille de la machine virtuelle provisionnée. Pour plus de détails sur le calcul des frais, consultez la page [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir créer une machine virtuelle Terraform pour Linux, vous devez avoir un abonnement Azure. Si vous n’en avez pas déjà un, voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>Créer une machine virtuelle Terraform 

Voici les étapes à suivre pour créer une instance de machine virtuelle Terraform pour Linux. 

1. Accédez à la liste [Créer une ressource](https://ms.portal.azure.com/#create/hub) sur le portail Azure.
2. Recherchez `Terraform` dans la barre de recherche `Search the Marketplace`. Sélectionnez le modèle `Terraform`. Sélectionnez le bouton **Créer** dans l’onglet des détails Terraform en bas à droite.
![texte de remplacement](media\terraformmsi.png)
3. Les sections suivantes décrivent les entrées pour chacune des étapes de l’Assistant (**énumérées à droite**) à suivre pour créer la machine virtuelle Terraform pour Linux.  Voici les entrées nécessaires à la configuration de chaque étape

## <a name="details-in-create-terraform-tab"></a>Détails de l’onglet Créer Terraform

Voici les informations à renseigner dans l’onglet Créer Terraform.

a. **Paramètres de base**
    
* **Name** (Nom) : nom de votre machine virtuelle Terraform.
* **User Name**(Nom d’utilisateur) : premier ID de connexion du compte.
* **Password**(Mot de passe) : premier mot de passe du compte (vous pouvez utiliser une clé publique SSH au lieu d’un mot de passe).
* **Subscription**(Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine. Vous devez disposer des privilèges de création de ressources pour cet abonnement.
* **Resource Group** (Groupe de ressources) : créez un groupe ou utilisez un groupe existant.
* **Location**(Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau.

b. **Paramètres supplémentaires**

* Taille : taille de la machine virtuelle.
* Type de disque de machine virtuelle : choisissez SSD ou HDD

c. **Récapitulatif Terraform**

* Vérifiez que toutes les informations que vous avez saisies sont correctes. 

d. **Acheter**

* Pour démarrer le provisionnement, cliquez sur Acheter. Les conditions de la transaction vous sont communiquées via un lien. La machine virtuelle ne génère pas de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l’étape de définition de la taille.

L’image de la machine virtuelle Terraform effectue les étapes suivantes

* Crée une machine virtuelle à laquelle le système assigne une identité basée sur l’image d’Ubuntu 16.04 LTS
* Installe l’extension MSI sur la machine virtuelle pour permettre l’envoi de jetons OAuth pour les ressources Azure
* Assigne des autorisations RBAC à l’identité gérée pour octroyer des droits de propriétaire sur le groupe de ressources
* Crée un dossier de modèle Terraform (tfTemplate)
* Préconfigure l’état à distance de Terraform avec le backend Azure

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Comment utiliser et configurer la machine virtuelle Terraform pour Linux

Une fois la machine virtuelle créée, vous pouvez vous y connecter avec SSH. Utilisez les informations d’identification de compte que vous avez définies dans la section Paramètres de base de l’étape 3 de l’interface de l’interpréteur de commandes texte. Sur Windows, vous pouvez télécharger un outil client SSH tel que [Putty](http://www.putty.org/)

Après vous être connecté à la machine virtuelle à l’aide de `SSH`, vous devez accorder les autorisations de collaborateur sur tout l’abonnement au service MSI installé sur la machine virtuelle. Les autorisations de collaborateur permettent au service MSI sur la machine virtuelle d’utiliser Terraform pour créer des ressources en dehors du groupe de ressources de la machine virtuelle. Vous pouvez facilement effectuer cette opération en exécutant un script une seule fois. Voici la commande à exécuter.

`. ~/tfEnv.sh`

Le script précédent utilise la méthode de [connexion interactive avec AZ CLI v 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) pour s’authentifier sur Azure et assigner les autorisations de collaborateur sur tout l’abonnement au service MSI installé sur la machine virtuelle. 

 Un backend d’état à distance Terraform est créé sur la machine virtuelle. Pour l’activer dans votre déploiement Terraform, vous devez copier le fichier remoteState.tf du répertoire tfTemplate à la racine des scripts Terraform.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Découvrez-en plus sur la gestion de l’état à distance [ici](https://www.terraform.io/docs/state/remote.html). La clé d’accès de stockage est exposée dans ce fichier et doit être vérifiée avec soin dans le contrôle de code source.  

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à créer une machine virtuelle Terraform pour Linux sur Azure. Pour en savoir plus sur Terraform sur Azure, consultez les ressources supplémentaires suivantes. 

 [Hub Terraform sur Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Documentation Terraform Azure Provider](http://aka.ms/terraform)  
 [Source Terraform Azure Provider](http://aka.ms/tfgit)  
 [Modules Terraform Azure](http://aka.ms/tfmodules)
 

















