---
description: Fichier Include
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 552e93e9bd1b17c73fb1638fbae2ac30b051c261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
1. Dans votre navigateur, ouvrez [l’image de Place de marché Azure pour Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Sélectionnez **OBTENIR MAINTENANT**.

    ![Sélectionnez OBTENIR MAINTENANT pour démarrer le processus d’installation de l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Après avoir consulté les détails de la tarification et les conditions, sélectionnez **Continuer**.

    ![Tarification et conditions de l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Sélectionnez **Créer** pour configurer le serveur Jenkins dans le portail Azure. 

    ![Installez l’image de Place de marché Jenkins.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Dans l’onglet **Basics (De base)**, spécifiez les valeurs suivantes :

    - **Name (Nom)** : entrez `Jenkins`.
    - **User name (Nom d’utilisateur)** : entrez le nom d’utilisateur à utiliser pour la connexion à la machine virtuelle sur laquelle Jenkins est exécuté. Le nom d’utilisateur doit répondre aux [exigences spécifiques](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - **Authentication type** (Type d’authentification) : Sélectionnez **Clé publique SSH**.
    - **SSH public key** (Clé publique SSH) : Copiez-collez une clé publique RSA au format ligne unique (commençant par `ssh-rsa`) ou au format PEM multiligne. Vous pouvez générer des clés SSH à l’aide de ssh-keygen sur Linux et macOS, ou à l’aide de PuTTYGen sur Windows. Pour plus d’informations sur les clés SSH et sur Azure, consultez l’article [Utilisation de clés SSH avec Windows sur Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Subscription (Abonnement)** : sélectionnez l’abonnement Azure dans lequel installer Jenkins.
    - **Resource group (Groupe de ressources)** : sélectionnez **Create new (Nouveau)** et entrez le nom du groupe de ressources servant de conteneur logique à la collection de ressources qui composent l’installation Jenkins.
    - **Location (Emplacement)** : sélectionnez **East US (États-Unis de l’Est)**.

    ![Entrez les informations d’authentification et de groupe de ressources de Jenkins dans l’onglet Basics (De base).](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Sélectionnez **OK** pour accéder à l’onglet **Additional Settings** (Paramètres supplémentaires). 

1. Dans l’onglet **Additional Settings** (Paramètres supplémentaires), spécifiez les valeurs suivantes :

    - **Size (Taille)** : sélectionnez l’option de dimensionnement appropriée pour votre machine virtuelle Jenkins.
    - **VM disk type (Type de disque de machine virtuelle)** : sélectionnez HDD (disque dur) ou SSD (disque SSD) pour indiquer le type de disque de stockage autorisé pour la machine virtuelle Jenkins.
    - **Virtual network** (Réseau virtuel) : (facultatif) Sélectionnez **Virtual network** pour modifier les paramètres par défaut.
    - **Subnets** (Sous-réseaux) : Sélectionnez **Sous-réseaux**, vérifiez les informations, puis sélectionnez **OK**.
    - **Public IP Address (Adresse IP publique)** : le nom de l’adresse IP est par défaut le nom Jenkins spécifié dans la page précédente suivi du suffixe -IP. Vous pouvez sélectionner l’option pour modifier cette valeur par défaut.
    - **Domain name label (Étiquette du nom de domaine)** : spécifiez la valeur de l’URL complète d’accès à la machine virtuelle Jenkins.
    - **Jenkins release type** (Type de version Jenkins) : Sélectionnez le type de version souhaité parmi les options `LTS`, `Weekly build` et `Azure Verified`. Les options `LTS` et `Weekly build` sont expliquées dans l’article [Jenkins LTS Release Line](https://jenkins.io/download/lts/). L’option `Azure Verified` fait référence à une [version LTS de Jenkins](https://jenkins.io/download/lts/) qui a été vérifiée en vue de son exécution dans Azure. 

    ![Entrez les paramètres de la machine virtuelle pour Jenkins dans l’onglet Settings (Paramètres).](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Sélectionnez **OK** pour accéder à l’onglet **Integration Settings** (Paramètres d’intégration).

1. Dans l’onglet **Integration Settings** (Paramètres d’intégration), spécifiez les valeurs suivantes :

    - **Service Principal** (Principal du service) : Le principal du service est ajouté dans Jenkins en tant qu’informations d’identification pour l’authentification Azure. `Auto` signifie que le principal sera créé avec MSI (Managed Service Identity). `Manual` signifie que vous devez créer le principal. 
        - **Application ID** (ID d’application) et **Secret** : Si vous sélectionnez l’option `Manual` pour l’option **Service Principal** (Principal du service), vous devez spécifier les valeurs `Application ID` et `Secret` de votre principal du service. Lors de la [création d’un principal de service](/cli/azure/create-an-azure-service-principal-azure-cli), le rôle par défaut est celui de **Contributeur**, ce qui suffit pour utiliser les ressources Azure.
    - **Enable Cloud Agents** (Activer les agents cloud) : Spécifiez le modèle cloud par défaut pour les agents dans lesquels `ACI` référence Azure Container Instance, et `VM` référence des machines virtuelles. Vous pouvez également spécifier `No` si vous ne souhaitez pas activer d’agent cloud.

1. Sélectionnez **OK** pour accéder à l’onglet **Summary (Résumé)**.

1. Lorsque l’onglet **Summary (Résumé)** s’affiche, les informations saisies sont validées. Lorsque le message **Validation passed** (Validation réussie) s’affiche (en haut de l’onglet), cliquez sur **OK**. 

    ![L’onglet Summary (Résumé) affiche et valide les options sélectionnées.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Lorsque l’onglet **Create (Créer)** s’affiche, sélectionnez **Create (Créer)** pour créer la machine virtuelle Jenkins. Lorsque votre serveur est prêt, une notification s’affiche dans le portail Azure.

    ![Notification Jenkins est prêt.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)