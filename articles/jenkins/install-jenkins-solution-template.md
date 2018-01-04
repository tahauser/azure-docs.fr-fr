---
title: "Créer un serveur Jenkins sur Azure"
description: "Installez Jenkins sur une machine virtuelle Linux Azure à partir du modèle de solution Jenkins et générez un exemple d’application Java."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 0cf24d8db845beb63b1b895f895da761488fd8ec
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Créer un serveur Jenkins sur une machine virtuelle Linux Azure à partir du portail Azure

Ce démarrage rapide montre comment installer [Jenkins](https://jenkins.io) sur une machine virtuelle Linux Ubuntu avec les outils et les plug-ins configurés pour fonctionner avec Azure. Lorsque vous avez terminé, vous disposez d’un serveur de Jenkins s’exécutant dans Azure qui génère un exemple d’application Java à partir de [Github](https://github.com).

## <a name="prerequisites"></a>configuration requise

* Abonnement Azure
* Accéder à SSH sur la ligne de commande de votre ordinateur (par exemple l’interpréteur de commandes Bash ou [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Créer la machine virtuelle Jenkins à partir du modèle de solution

Ouvrez l’[image de marketplace pour Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) dans votre navigateur web et cliquez sur **OBTENIR MAINTENANT** sur le côté gauche de la page. Passez en revue les détails de tarification et sélectionnez **Continuer**, puis sélectionnez **Créer** pour configurer le serveur Jenkins dans le portail Azure. 
   
![Boîte de dialogue du Portail Azure](./media/install-jenkins-solution-template/ap-create.png)

Dans l’onglet **Configurer les paramètres de base**, renseignez les champs suivants :

![Configurer les paramètres de base](./media/install-jenkins-solution-template/ap-basic.png)

* Utilisez **Jenkins** comme **Nom**.
* Entrez un **Nom d’utilisateur**. Le nom d’utilisateur doit répondre aux [exigences spécifiques](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Sélectionnez **Mot de passe** comme **Type d’authentification** et entrez un mot de passe. Le mot de passe doit comprendre au moins une majuscule, un chiffre et un caractère spécial.
* Utilisez **myJenkinsResourceGroup** pour le **Groupe de ressources**.
* Choisissez **Est des États-Unis** comme [Région Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) à partir de la liste déroulante **Emplacement**.

Sélectionnez **OK** pour accéder à l’onglet **Configurer des options supplémentaires**. Entrez un nom de domaine unique pour identifier le serveur Jenkins et sélectionnez **OK**.

![Configurer des options supplémentaires](./media/install-jenkins-solution-template/ap-addtional.png)  

 Une fois la validation réussie, sélectionnez de nouveau **OK** dans l’onglet **Résumé**. Enfin, sélectionnez **Acheter** pour créer la machine virtuelle Jenkins. Lorsque votre serveur est prêt, vous recevez une notification dans le portail Azure :   

![Notification Jenkins est prêt](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Se connecter à Jenkins

Accédez à votre machine virtuelle (par exemple, http://jenkins2517454.eastus.cloudapp.azure.com/) dans votre navigateur web. La console Jenkins n’est pas accessible via un protocole HTTP non sécurisé. Vous trouverez sur la page des informations pour accéder à la console Jenkins en toute sécurité à partir de votre ordinateur à l’aide d’un tunnel SSH.

![Déverrouiller Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configurez le tunnel à l’aide de la commande `ssh` sur la page à partir de la ligne de commande. Remplacez `username` par le nom d’utilisateur administrateur de la machine virtuelle choisi précédemment lors de la configuration de la machine virtuelle à partir du modèle de solution.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Une fois que vous avez démarré le tunnel, accédez à http://localhost:8080/ sur votre machine locale. 

Obtenez le mot de passe initial en exécutant la commande suivante dans la ligne de commande tout en étant connecté à la machine virtuelle Jenkins via SSH.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

Déverrouillez le tableau de bord Jenkins pour la première fois avec le mot de passe initial.

![Déverrouiller Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Sélectionnez **Installer les plug-ins suggérés** sur l’autre page, puis créez un utilisateur administrateur Jenkins permettant d’accéder au tableau de bord Jenkins.

![Jenkins est prêt à l’emploi](./media/install-jenkins-solution-template/jenkins-welcome.png)

Le serveur Jenkins est maintenant prêt à générer des codes.

## <a name="create-your-first-job"></a>Créer votre premier travail

Sélectionnez **Créer de nouveaux travaux** à partir de la console Jenkins, puis entrez le nom **mySampleApp**. Sélectionnez **Projet Freestyle**, puis sélectionnez **OK**.

![Créer un travail](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Sélectionnez l’onglet **Gestion du code source**, activez **Git**, puis entrez l’URL suivante dans le champ**URL du référentiel** : `https://github.com/spring-guides/gs-spring-boot.git`

![Définir le référentiel Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Sélectionnez l’onglet **Générer**, puis sélectionnez **Ajouter une étape de génération**, **Appel du script Gradle**. Sélectionnez **Utiliser Gradle Wrapper**, puis entrez `complete` dans l’**emplacement de Wrapper** et `build` pour **Tâches**.

![Utiliser le wrapper Gradle pour générer](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Sélectionnez **Avancé..** puis entrez `complete` dans le champ **Script build racine**. Sélectionnez **Enregistrer**.

![Définir les paramètres avancés dans l’étape de génération du wrapper Gradle](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Générer le code

Sélectionnez **Générer maintenant** pour compiler le code et combiner l’exemple d’application. Lorsque le build est terminé, sélectionnez le lien**Espace de travail** pour le projet.

![Accédez à l’espace de travail pour obtenir le fichier JAR du build](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Accédez à `complete/build/libs` et assurez-vous que le `gs-spring-boot-0.1.0.jar` peut vérifier que votre build a été généré avec succès. Votre serveur Jenkins est maintenant prêt à générer vos propres projets dans Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des machines virtuelles Azure en tant qu’agents Jenkins](jenkins-azure-vm-agents.md)
