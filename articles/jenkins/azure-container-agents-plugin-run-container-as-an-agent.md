---
title: "Générer un projet dans Azure à l’aide de Jenkins et d’Azure Container Instances"
description: "Découvrez comment utiliser le plug-in Azure Container Agent pour Jenkins afin de générer un projet dans Azure avec Azure Container Instances."
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/28/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 557b21340a0ba4e5381d7505b14a172aa3478b84
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Générer un projet dans Azure à l’aide de Jenkins et d’Azure Container Instances

Azure Container Instances vous permet de travailler sans avoir à approvisionner des machines virtuelles ou à adopter un service de niveau supérieur. Azure Container Instances fournit une facturation à la seconde en fonction de la capacité demandée, ce qui en fait une solution intéressante pour les charges de travail temporaires tels qu’une build.

Vous allez apprendre à effectuer les actions suivantes :
> [!div class="checklist"]
> * Installer et configurer un serveur Jenkins sur Azure
> * Installer et configurer le plug-in Azure Container Agents pour Jenkins
> * Utiliser Azure Container Instances pour générer l’[exemple d’application Spring PetClinic](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>configuration requise

- **Abonnement Azure** : pour en savoir plus sur les options d’achat d’Azure, consultez [Comment acheter Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI 2.0 ou Azure Cloud Shell** : installez l’un des produits suivants dans lequel entrer des commandes Azure :

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) : permet d’exécuter des commandes Azure à partir d’une commande ou une fenêtre de terminal.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) : expérience shell basée sur un navigateur. Cloud Shell permet d’accéder à une expérience de ligne de commande basée sur navigateur avec les tâches de gestion Azure à l’esprit.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Installer un serveur Jenkins sur Azure à l’aide de l’image Jenkins Marketplace

Jenkins prend en charge un modèle où le serveur Jenkins délègue le travail à un ou plusieurs agents pour permettre une seule installation Jenkins visant à héberger un grand nombre de projets ou de fournir des différents environnements nécessaires pour les builds ou des tests. Les étapes décrites dans cette section vous guident dans l’installation et la configuration d’un serveur Jenkins sur Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Se connecter au serveur Jenkins qui s’exécute sur Azure

Une fois Jenkins installé sur Azure, vous devez vous connecter à Jenkins. Les étapes suivantes vous guident dans l’établissement d’une connexion SSH à la machine virtuelle Jenkins qui s’exécute sur Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Mettre à jour le DNS Jenkins

Jenkins doit connaître sa propre URL lors de la création des liens qui pointent vers lui-même. Par exemple, l’URL doit être utilisé lorsque Jenkins envoie des messages électroniques contenant des liens directs pour générer des résultats. 

Cette section vous guide dans le processus de création de l’URL Jenkins.

1. Dans votre navigateur, accédez au tableau de bord Jenkins à `http://localhost:8080`.

1. Sélectionnez **Manage Jenkins (Gérer Jenkins)**.

    ![Gérer les options Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Sélectionnez **Configure System (Configurer le système)**.

    ![Gérer l’option des plug-ins Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Sous **Jenkins Location (Emplacement de Jenkins)**, entrez l’URL de votre serveur Jenkins.

1. Sélectionnez **Enregistrer**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Mettre à jour Jenkins pour autoriser le protocole JNLP (Java Network Launch Protocol)

L’agent Jenkins se connecte au serveur Jenkins avec le protocole JNLP (Java Network Launch Protocol). Cette section explique comment spécifier un port pour les agents JNLP à utiliser lors de la communication avec le serveur Jenkins.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins (Gérer Jenkins)**.

    ![Gérer les options Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Sélectionnez **Configure Global Security (Configurer la sécurité globale)**.

    ![Configurer la sécurité globale dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. Sous **Agents (Agents)**, sélectionnez **Fixed (Fixe)**, puis entrez un port. La capture d’écran montre une valeur de port de 12345 comme exemple. Vous devez spécifier un port adapté à votre environnement.

    ![Mettre à jour les paramètres de sécurité globale Jenkins pour autoriser le protocole JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Sélectionnez **Enregistrer**.

1. À l’aide d’Azure CLI 2.0 ou de Cloud Shell, entrez la commande suivante pour créer une règle de trafic entrant pour votre groupe de sécurité réseau Jenkins :

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Créer un principal de service Azure et l’ajouter aux informations d’identification Jenkins

Un principal de service Azure est nécessaire pour les déploiements sur Azure. Les étapes suivantes vous guident tout au long du processus de création d’un principal de service (si vous n’en avez pas) et de mise à jour de Jenkins avec votre principal de service.

1. Si vous avez déjà un principal de service (et connaissez son ID d’abonnement, son client, son ID d’application et son mot de passe), vous pouvez ignorer cette étape. Si vous devez créer un principal de sécurité, consultez l’article [Créer un principal de service Azure avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Lors de la création du principal, notez les valeurs de l’ID d’abonnement, du client, de l’ID d’application et du mot de passe.

1. Sélectionnez **Credentials (Informations d’identification)**.

    ![Gérer l’option des informations d’identification dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. Sous **Credentials (Informations d’identification)**, sélectionnez **System (Système)**.

    ![Gérer l’option des informations d’identification du système dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Sélectionnez **global credentials (unrestricted) (Informations d’identification globales (sans restriction))**.

    ![Gérer l’option des informations d’identification du système global dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Sélectionnez **Adding some credentials (Ajout d’informations d’identification)**.

    ![Ajouter des informations d’identification dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Dans la liste déroulante **Kind (Type)** liste déroulante, sélectionnez **Microsoft Azure Service Principal (Principal de Service Microsoft Azure)** pour que la page affiche des champs permettant d’ajouter un principal de service. Ensuite, indiquez les valeurs demandées comme suit :

    - **Scope (Étendue)** : sélectionnez l’option pour **Global (Jenkins, nœuds, éléments, tous les éléments enfants, etc.)**.
    - **Subscription ID (ID d’abonnement)** : utilisez l’ID d’abonnement Azure que vous avez spécifié lors de l’exécution`az account set`.
    - **Client ID (ID du client)** : utilisez la valeur `appId` renvoyée par `az ad sp create-for-rbac`.
    - **Client Secret (Secret du client)** : utilisez la valeur `password` renvoyée par `az ad sp create-for-rbac`.
    - **Tenant ID (ID de l’abonné)** : utilisez la valeur `tenant` renvoyée par `az ad sp create-for-rbac`.
    - **Azure Environment (Environnement Azure)** : sélectionnez `Azure`.
    - **ID (ID)** : entrez `myTestSp`. Cette valeur sera réutilisée plus loin dans ce didacticiel.
    - **Description (Description)** : (facultatif) entrez une description pour ce principal.

    ![Spécifier les nouvelles propriétés du principal de service dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Une fois que vous avez entré les informations définissant le principal, vous pouvez sélectionner **Verify Service Principal (Vérifier le principal de service)** pour vous assurer que tout fonctionne correctement. Si votre principal de service Microsoft Azure est défini correctement, un message indiquant que la vérification a abouti s’affiche sous le champ **Description (Description)**.

1. Lorsque vous avez terminé, sélectionnez **OK (OK)** pour ajouter le principal dans Jenkins. Le tableau de bord Jenkins affiche le nouveau principal ajouté, dans la page **Global Credentials (Informations d’identification globales)**.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Créer un groupe de ressources Azure pour vos instances Azure Container Instances

Les instances Azure Container Instances doivent être placées dans un groupe de ressources Azure. Un groupe de ressources Azure est un conteneur réunissant les ressources associées d’une solution Azure.

À l’aide d’Azure CLI 2.0 ou de Cloud Shell, entrez la commande suivante pour créer un groupe de ressources appelé `JenkinsAciResourceGroup` dans l’emplacement `eastus` :

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Lorsque vous avez terminé, la commande `az group create` affiche des résultats similaires à ceux de l’exemple suivant :

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Installer le plug-in Azure Container Agents pour Jenkins

Si vous avez déjà installé le plug-in Azure Container Agents, vous pouvez ignorer cette section.

Une fois que vous avez créé pour votre agent Azure pour votre agent Jenkins, les étapes suivantes montrent comment installer le plug-in Azure Container Agents :

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins (Gérer Jenkins)**.

    ![Gérer les options Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Select **Manage Plugins (Gérer les plug-ins)**.

    ![Gérer l’option des plug-ins Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Sélectionnez **Available (Disponible)**.

    ![Afficher l’option des plug-ins Jenkins disponibles dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Entrez `Azure Container Agents` dans la zone de texte **Filter (Filtre)**. (La liste est filtrée au fur et à mesure de la saisie.)

    ![Filtrer les plug-ins Jenkins disponibles dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Activez la case à cocher en regard du plug-in **Azure Container Agents** et de l’une des options d’installation. Pour les besoins de cette démonstration, j’ai sélectionné l’option **Install without restart (Installer sans redémarrer)**.

    ![Installer des plug-ins Azure Container Agents à partir du tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Après avoir sélectionné l’option d’installation du ou des plug-ins souhaités, le tableau de bord Jenkins affiche une page qui décrit l’état de ce que vous installez.

    ![État de l’installation des plug-ins Azure Container Agents à partir du tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Pour revenir à la page principale du tableau de bord Jenkins, sélectionnez **Go back to the top page (Revenir à la première page)**.

## <a name="configure-the-azure-container-agents-plugin"></a>Configurer le plug-in Azure Container Agents

Une fois le plug-in Azure Container Agents installé, cette section vous explique comment le configurer dans le tableau de bord Jenkins.

1. Dans le tableau de bord Jenkins, cliquez sur **Manage Jenkins (Gérer Jenkins)**.

    ![Gérer les options Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Sélectionnez **Configure System (Configurer le système)**.

    ![Gérer l’option des plug-ins Jenkins dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Localisez la section **Cloud (Cloud)** en bas de la page, et dans la liste déroulante **Add a new cloud (Ajouter un nouveau cloud)**, sélectionnez **Azure Container Instance (Instance de conteneur Azure)**.

    ![Ajouter un nouveau fournisseur de cloud à partir du tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. Dans la section **Azure Container Instance (Instance de conteneur Azure)**, spécifiez les valeurs suivantes :

    - **Cloud name (Nom du cloud)** : (facultatif car ce nom est généré automatiquement.) Spécifiez le nom de cette instance. 
    - **Azure Credential (Informations d’identification Azure)** : sélectionnez la flèche déroulante, puis l’entrée `myTestSp` correspondant au principal du service Azure créé précédemment.
    - **Resource Group (Groupe de ressources)** : sélectionnez la flèche déroulante puis l’entrée `JenkinsAciResourceGroup` correspondant au principal du service Azure créé précédemment.

    ![Définition des propriétés de l’instance de conteneur Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Sélectionnez la flèche déroulante **Add Container Template (Ajouter un modèle de conteneur)** puis **Aci Container Template (Modèle de conteneur Aci)**.

1. Dans la section **Aci Container Template (Modèle de conteneur Aci)**, spécifiez les valeurs suivantes :

    - **Name (Nom)** : entrez `ACI-container`.
    - **Labels (Libellés)** : entrez `ACI-container`.
    - **Docker Image (Image Docker)** : entrez `cloudbees/jnlp-slave-with-java-build-tools`

    ![Définition des propriétés de l’image de l’instance de conteneur Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Sélectionnez **Advanced (Avancé)**.

1. Sélectionnez la flèche déroulante **Retention Strategy (Stratégie de rétention)** puis **Container Idle Retention Strategy (Stratégie de rétention en cas d’inactivité du conteneur)**. En sélectionnant cette option, Jenkins maintient l’agent en activité tant qu’aucun nouveau travail n’est exécuté sur l’agent et que durée d’inactivité spécifiée est écoulée.

    ![Définition des propriétés de rétention de l’instance de conteneur Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Sélectionnez **Enregistrer**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Créer le travail de l’application Spring PetClinic dans Jenkins

Les étapes suivantes vous guident tout au long de la création d’un travail Jenkins, en tant que projet libre, pour générer l’application Spring PetClinic.

1. Dans le tableau de bord Jenkins, cliquez sur **New Item (Nouvel élément)**.

    ![Option de menu New Item (Nouvel élément) dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Entrez `myPetClinicProject` comme nom et sélectionnez **Freestyle projet (Projet libre)**.

    ![Nouveau projet libre dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Sélectionnez **OK**.

1. Dans l’onglet **General (Général)**, spécifiez les paramètres suivants :

    - **Restrict where project can be run (Restreindre le cadre d’exécution du projet)** : sélectionnez cette option.
    - **Label Expression (Expression de libellé)** : entrez `ACI-container`. Lorsque vous sortez de ce champ, un message confirme que la configuration cloud créée à l’étape précédente prend en charge le libellé.

    ![Paramètres généraux d’un nouveau projet libre dans le tableau de bord Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Sélectionnez l’onglet **Source Code Management (Gestion du code source)** et spécifiez les valeurs suivantes :

    - **Source Code Management (Gestion du code source)** : sélectionnez **Git (Git)**.
    - **Repository URL (URL du référentiel)** : entrez l’URL suivante pour le référentiel GitHub de l’application exemple Spring PetClinic : `https://github.com/spring-projects/spring-petclinic.git`.

1. Sélectionnez l’onglet **Build (Générer)** et effectuez les tâches suivantes :

    - Sélectionnez la flèche déroulante **Add build step (Ajouter une étape de génération)**, puis sélectionnez **Invoke top-level Maven targets (Appeler des cibles Maven de premier niveau)**.

    - Dans **Goals (Objectifs)**, entrez `package`.

1. Sélectionnez **Save (Enregistrer)** pour enregistrer la définition du nouveau projet.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Générer le travail de l’application Spring PetClinic dans Jenkins

Il est temps de générer votre projet ! Cette section explique comment générer un projet à partir du tableau de bord Jenkins.

1. Dans le tableau de bord Jenkins, sélectionnez `myPetClinicProject`.

    ![Sélectionnez le projet à générer dans le tableau de bord Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Sélectionnez **Build now (Générer maintenant)**. 

    ![Générez le projet dans le tableau de bord Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Lorsque vous lancez une génération dans Jenkins, celle-ci est mise en attente. Si vous utilisez un agent Azure Container Agent, celui-ci doit être démarré et mis en ligne pour que la génération puisse s’exécuter. Un message vous indique le nom de l’agent et précise que la génération est en attente. (Ce processus prend environ cinq minutes, mais n’intervient que lors de la première utilisation de l’agent pour une génération. Les générations suivantes sont beaucoup plus rapides car l’agent est déjà en ligne.)

    ![La génération est mise en attente jusqu’à ce que l’agent soit créé et mis en ligne.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Une barre de progression s’affiche et indique que la génération est en cours :

    ![La génération ne commence qu’au moment où la barre de progression est visible.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Lorsque celle-ci disparaît, sélectionnez la flèche en regard du numéro de build. Dans le menu contextuel, sélectionnez **Console output (Sortie de console)**.

    ![Cliquez sur l’option de menu Console Log (Journal de console) pour afficher les informations de la build.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Les informations du journal de console concernant le processus de génération. Pour afficher toutes les informations de la build (y compris celle de la génération en cours à distance sur l’agent Azure que vous avez créé), sélectionnez **Full log (Journal complet)**.

    ![Cliquez sur le lien Full log (Journal complet) pour afficher des informations plus détaillées sur la build.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Le journal complet affiche des informations plus exhaustives sur la build :

    ![Le journal complet affiche des informations plus exhaustives sur la build.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Pour afficher la disposition de la build, faites défiler le journal jusqu’en bas.

    ![La disposition de la build s’affiche en bas du journal de génération.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Nettoyage des ressources Azure

Dans ce didacticiel, vous avez créé les ressources contenues dans deux groupes de ressources Azure : 
    - `JenkinsResourceGroup` : contient les ressources Azure du serveur Jenkins.
    - `JenkinsAciResourceGroup` : contient les ressources Azure de l’agent Jenkins.
    
Si vous n’avez plus besoin d’utiliser les ressources d’un groupe de ressources Azure, vous pouvez supprimer ce dernier à l’aide de la commande `az group delete` (en remplaçant l’espace réservé &lt;resourceGroup> par le nom du groupe de ressources à supprimer) :

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>étapes suivantes
> [!div class="nextstepaction"]
> [Visitez le Hub Jenkins sur Azure pour découvrir les derniers articles et exemples](https://docs.microsoft.com/azure/jenkins/)
