---
title: Créer un serveur Jenkins sur Azure
description: Installez Jenkins sur une machine virtuelle Linux Azure à partir du modèle de solution Jenkins et générez un exemple d’application Java.
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: c9f86ab2536d3c598bb8c7084524395b41f18db0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Créer un serveur Jenkins sur une machine virtuelle Linux Azure à partir du portail Azure

Ce démarrage rapide montre comment installer [Jenkins](https://jenkins.io) sur une machine virtuelle Linux Ubuntu avec les outils et les plug-ins configurés pour fonctionner avec Azure. Lorsque vous avez terminé, vous disposez d’un serveur de Jenkins s’exécutant dans Azure qui génère un exemple d’application Java à partir de [Github](https://github.com).

## <a name="prerequisites"></a>Prérequis


* Abonnement Azure
* Accéder à SSH sur la ligne de commande de votre ordinateur (par exemple l’interpréteur de commandes Bash ou [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Créer la machine virtuelle Jenkins à partir du modèle de solution
Jenkins prend en charge un modèle où le serveur Jenkins délègue le travail à un ou plusieurs agents pour permettre une seule installation Jenkins visant à héberger un grand nombre de projets ou de fournir des différents environnements nécessaires pour les builds ou des tests. Les étapes décrites dans cette section vous guident dans l’installation et la configuration d’un serveur Jenkins sur Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

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
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
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
