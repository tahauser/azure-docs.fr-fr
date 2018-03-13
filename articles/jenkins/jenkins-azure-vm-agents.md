---
title: "Mettre à l’échelle des déploiements Jenkins avec des agents de machines virtuelles Azure."
description: "Dotez vos pipelines Jenkins d’une capacité supplémentaire en utilisant des machines virtuelles Azure avec le plug-in Jenkins Azure VM Agents."
services: multiple
documentationcenter: 
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 4d45ed14be499ed927f1433e134a029066146eea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Mettre à l’échelle vos déploiements Jenkins pour répondre à la demande avec des agents de machines virtuelles Azure

Ce didacticiel montre comment utiliser le [plug-in Jenkins Azure VM Agents](https://plugins.jenkins.io/azure-vm-agents) pour ajouter de la capacité à la demande avec des machines virtuelles Linux qui s’exécutent dans Azure.

Ce didacticiel présente les procédures suivantes :

> [!div class="checklist"]
> * Installer le plug-in Azure VM Agents
> * Configurer le plug-in pour créer des ressources dans votre abonnement Azure
> * Définir les ressources de calcul accessibles à chaque agent
> * Définir le système d’exploitation et les outils installés sur chaque agent
> * Créer un travail libre (freestyle) Jenkins
> * Exécuter le travail sur un agent de machine virtuelle Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure
* Un serveur maître Jenkins. Si vous n’en possédez pas, consultez le [guide de démarrage rapide](install-jenkins-solution-template.md) pour en configurer un dans Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Installer le plug-in Agents de machine virtuelle Azure

> [!TIP]
> Si vous avez déployé Jenkins dans Azure en utilisant le [modèle de solution](install-jenkins-solution-template.md), le plug-in Azure VM Agents est déjà installé.

1. Dans le tableau de bord Jenkins, sélectionnez **Manage Jenkins** (Gérer Jenkins), puis **Manage Plugins** (Gérer les plug-ins).
2. Sélectionnez l’onglet **Available** (Disponible), puis recherchez **Azure VM Agents**. Cochez la case en regard de l’entrée correspondant au plug-in, puis sélectionnez **Install without restart** (Installer sans redémarrage) au bas du tableau de bord.

## <a name="configure-the-azure-vm-agents-plugin"></a>Configurer le plug-in Azure VM Agents

1. Dans le tableau de bord Jenkins, sélectionnez **Manage Jenkins** (Gérer Jenkins), puis **Configure System** (Configurer le système).
2. Faites défiler l’écran jusqu’au bas de la page et recherchez la section **Cloud** où figure la liste déroulante **Add new cloud** (Ajouter un nouveau cloud), puis choisissez **Microsoft Azure VM Agents** (Agents de machines virtuelles Microsoft Azure).
3. Sélectionnez un principal de service existant dans la liste déroulante **Add** (Ajouter) de la section **Azure Credentials** (Informations d’identification Azure). Si aucun n’est répertorié, effectuez les étapes suivantes pour [créer un principal de service](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) pour votre compte Azure et l’ajouter à votre configuration Jenkins :   

    a. Sélectionnez **Add** (Ajouter) en regard de **Azure Credentials** (Informations d’identification Azure), puis choisissez **Jenkins**.   
    b. Dans la boîte de dialogue **Add Credentials** (Ajouter des informations d’identification), sélectionnez **Microsoft Azure Service Principal** (Principal du service Microsoft Azure) dans la liste déroulante **Kind** (Type).   
    c. Créez un principal du service Active Directory à partir d’Azure CLI ou de [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Entrez les informations d’identification du principal du service dans la boîte de dialogue **Add credentials** (Ajouter des informations d’identification). Si vous ne connaissez pas l’ID de votre abonnement Azure, vous pouvez le demander à partir de l’interface CLI :
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Le principal du service finalisé doit utiliser le champ `id` pour **Subscription ID** (ID d’abonnement), la valeur `appId` pour **Client ID** (ID client), `password` pour **Client Secret** (Clé secrète du client) et `tenant` pour **Tenant ID** (ID de locataire). Sélectionnez **Add** (Ajouter) pour ajouter le principal du service, puis configurez le plug-in de sorte qu’il utilise les informations d’identification nouvellement créées.

    ![Configurer le principal du service Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. Dans la section **Resource Group Name** (Nom du groupe de ressources), laissez **Create new** (Créer un nouveau) sélectionné, puis entrez `myJenkinsAgentGroup`.
5. Sélectionnez **Verify configuration** (Vérifier la configuration) pour vous connecter à Azure et tester les paramètres du profil.
6. Sélectionnez **Apply** (Appliquer) pour mettre à jour la configuration du plug-in.

## <a name="configure-agent-resources"></a>Configurer les ressources d’agent

Configurez un modèle qui servira à définir un agent de machine virtuelle Azure. Ce modèle définit les ressources de calcul dont dispose chaque agent au moment où il est créé.

1. Sélectionnez **Add** (Ajouter) en regarde de **Add Azure Virtual Machine Template** (Ajouter un modèle de machine virtuelle Azure).
2. Entrez `defaulttemplate` dans **Name** (Nom).
3. Entrez `ubuntu` dans **Label** (Étiquette).
4. Dans la zone de liste modifiable, sélectionnez la [région Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) souhaitée.
5. Sélectionnez une [taille de machine virtuelle](/azure/virtual-machines/linux/sizes) dans la liste déroulante située en dessous de **Virtual Machine Size** (Taille de la machine virtuelle). Une taille à usage général `Standard_DS1_v2` convient pour ce didacticiel.   
6. Conservez la valeur dans **Retention time** (Durée de rétention), à savoir `60`. Ce paramètre définit le délai d’attente de Jenkins (en minutes) au bout duquel il libère les agents inactifs. Spécifiez 0 si vous ne voulez pas que les agents inactifs soient retirés automatiquement.

   ![Configuration générale des machines virtuelles](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Configurer le système d’exploitation et les outils des agents

Dans la section **Image Configuration** (Configuration de l’image) de la configuration de plug-in, sélectionnez **Ubuntu 16.04 LTS**. Cochez les cases en regard de **Install Git (Latest)** [Installer Git (dernière version)], **Install Maven (V3.5.0)** [Installer Maven (V3.5.0)] et **Install Docker** (Installer Docker) pour installer ces outils sur les agents nouvellement créés.

![Configurer le système d’exploitation et les outils des machines virtuelles](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Sélectionnez **Add** (Ajouter) en regard de **Admin Credentials** (Informations d’identification administrateur), puis sélectionnez **Jenkins**. Entrez le nom d’utilisateur et le mot de passe utilisés pour se connecter aux agents en veillant à ce qu’ils soient conformes à la [stratégie en matière de noms d’utilisateur et de mots de passe](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) pour les comptes d’administration sur les machines virtuelles Azure.

Sélectionnez **Verify Template** (Vérifier le modèle) pour vérifier la configuration, puis choisissez **Save** (Enregistrer) pour enregistrer vos modifications et revenir au tableau de bord Jenkins.

## <a name="create-a-job-in-jenkins"></a>Créer un travail dans Jenkins

1. Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**. 
2. Entrez `demoproject1` en guise de nom et sélectionnez **Freestyle projet** (Projet libre), puis **OK**.
3. Sous l’onglet **General** (Général), choisissez **Restrict where project can be run** (Restreindre les emplacements d’exécution du projet), puis tapez `ubuntu` dans le champ **Label Expression** (Expression d’étiquette). Vous obtenez un message confirmant que l’étiquette est prise en charge par la configuration cloud créée à l’étape précédente. 
   ![Configurer un travail](./media/jenkins-azure-vm-agents/job-config.png)
4. Sous l’onglet **Source Code Management** (Gestion du code source), sélectionnez **Git**, puis entrez l’URL suivante dans le champ **Repository URL** (URL du référentiel) : `https://github.com/spring-projects/spring-petclinic.git`
5. Sous l’onglet **Build** (Générer), sélectionnez **Add build step** (Ajouter une étape de génération), puis **Invoke top-level Maven targets** (Appeler des cibles Maven de niveau supérieur). Entrez `package` dans le champ **Goals** (Objectifs).
6. Sélectionnez **Save** (Enregistrer) pour enregistrer la définition du travail.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Générer le nouveau travail sur un agent de machine virtuelle Azure

1. Revenez au tableau de bord Jenkins.
2. Sélectionnez le travail que vous avez créé à l'étape précédente, puis cliquez sur **Build now** (Générer maintenant). Une nouvelle build est mise en file d’attente, mais elle ne démarre pas tant qu’une machine virtuelle d’agent n’est pas créée dans votre abonnement Azure.
3. Une fois la génération terminée, accédez à la **sortie de la console**. Vous constatez que la génération a été effectuée à distance sur un agent Azure.

![Sortie de la console](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>étapes suivantes

> [!div class="nextstepaction"]
> [CI/CD vers Azure App Service](java-deploy-webapp-tutorial.md)
