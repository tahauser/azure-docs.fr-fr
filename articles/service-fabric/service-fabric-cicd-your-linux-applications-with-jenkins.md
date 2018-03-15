---
title: "Génération et intégration en continu pour vos applications Linux Azure Service Fabric à l’aide de Jenkins | Microsoft Docs"
description: "Génération et intégration en continu pour votre application Linux Service Fabric à l’aide de Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: saysa
ms.openlocfilehash: 80c52cfeab007030203b6af4bb220f1a847e9426
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Utiliser Jenkins pour générer et déployer vos applications Linux
Jenkins est un outil populaire pour l’intégration et le déploiement en continu de vos applications. Voici comment générer et déployer votre application Azure Service Fabric à l’aide de Jenkins.

## <a name="general-prerequisites"></a>Conditions préalables
- Git doit être installé en local. Vous pouvez installer la version appropriée de Git à partir de [la page de téléchargements de Git](https://git-scm.com/downloads), en fonction de votre système d’exploitation. Si vous débutez avec Git, vous trouverez plus d’informations à ce sujet dans la [documentation Git](https://git-scm.com/docs).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurer Jenkins à l’intérieur d’un cluster Service Fabric

Vous pouvez configurer Jenkins à l’intérieur ou en dehors d’un cluster Service Fabric. Les sections suivantes montrent comment le configurer dans un cluster lors de l’utilisation d’un compte de stockage Azure pour enregistrer l’état de l’instance de conteneur.

### <a name="prerequisites"></a>Prérequis
1. Avoir un cluster Linux Service Fabric de prêt. Un cluster Service Fabric créé à partir du portail Azure avec Docker doit être installé. Si vous exécutez le cluster en local, vérifiez si Docker est installé à l’aide de la commande ``docker info``. S’il n’est pas installé, installez-le en utilisant les commandes suivantes :

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Assurez-vous que le port 8081 est spécifié comme un point de terminaison personnalisé sur le cluster.
   >

2. Clonez l’application en procédant comme suit :
  ```sh
  git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
  cd service-fabric-java-getting-started/Services/JenkinsDocker/
  ```

3. Conservez l’état du conteneur Jenkins dans un partage de fichiers :
  * Créez un compte de stockage Azure dans la **même région** que votre cluster, avec un nom tel que ``sfjenkinsstorage1``.
  * Créez un **partage de fichiers** sous ce compte de stockage, avec un nom tel que ``sfjenkins``.
  * Cliquez sur **Connecter** pour le partage de fichiers et notez les valeurs affichées sous **Connexion à partir de Linux** ; la valeur devrait ressembler à ce qui suit :
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Pour que vous puissiez monter des partages cifs, le package cifs-utils doit être installé dans les nœuds de cluster.       
  >

4. Mettez à jour les valeurs d’espace réservé dans le script ```setupentrypoint.sh``` avec les détails azure-storage de l’étape 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Remplacez ``[REMOTE_FILE_SHARE_LOCATION]`` par la valeur ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` à partir de la sortie de la connexion à l'étape 3 ci-dessus.
  * Remplacez ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` par la valeur ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` à partir de l’étape 3 ci-dessus.

5. **Cluster sécurisé uniquement :** Pour configurer le déploiement d’applications sur un cluster sécurisé à partir de Jenkins, le certificat doit être accessible au sein du conteneur Jenkins. Sur les clusters Linux, les certificats (PEM) sont simplement copiés à partir du magasin spécifié par X509StoreName sur le conteneur. Dans ApplicationManifest, sous ContainerHostPolicies, ajoutez cette référence de certificat et mettez à jour la valeur de l’empreinte. La valeur de l’empreinte doit être celle d’un certificat qui se trouve sur le nœud.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > La valeur de l’empreinte doit être la même que celle du certificat utilisé pour se connecter au cluster sécurisé. 
  >

6. Connectez-vous au cluster et installez l’application de conteneur.

  **Cluster sécurisé**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **Cluster non sécurisé**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  Cela permet d’installer un conteneur Jenkins sur le cluster et de l’analyser à l’aide de Service Fabric Explorer.

    > [!NOTE]
    > Le téléchargement de l’image Jenkins sur le cluster peut prendre quelques minutes.
    >

### <a name="steps"></a>Étapes
1. Dans votre navigateur, accédez à ``http://PublicIPorFQDN:8081``. Vous obtenez le chemin d’accès du mot de passe administrateur initial requis pour vous connecter. 
2. Consultez Service Fabric Explorer pour déterminer le nœud sur lequel le conteneur Jenkins est en cours d’exécution. Connectez-vous via Secure Shell (SSH) à ce nœud.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Obtenez l’ID d’instance conteneur à l’aide de ``docker ps -a``.
4. Connectez-vous en SSH (Secure Shell) au conteneur et collez le chemin d’accès indiqué sur le portail Jenkins. Par exemple, si dans le portail le chemin d’accès indiqué est `PATH_TO_INITIAL_ADMIN_PASSWORD`, vous pouvez exécuter ce qui suit :

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. Sur la page de mise en route Jenkins, choisissez l’option Sélectionner les plug-ins à installer, cochez la case **Aucun**, puis cliquez sur Installer.
6. Créez un utilisateur ou sélectionnez cette option pour continuer en tant qu’administrateur.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurer Jenkins en dehors d’un cluster Service Fabric

Vous pouvez configurer Jenkins à l’intérieur ou en dehors d’un cluster Service Fabric. Les sections suivantes expliquent comment le configurer en dehors d’un cluster.

### <a name="prerequisites"></a>Prérequis
Docker doit être installé. Les commandes suivantes peuvent être utilisées pour installer Docker à partir du terminal :

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Désormais, lorsque vous exécutez ``docker info`` dans le terminal, vous devriez voir dans la sortie que le service Docker est en cours d’exécution.

### <a name="steps"></a>Étapes
  1. Extrayez l’image du conteneur Jenkins de Service Fabric : ``docker pull rapatchi/jenkins:v10``. Cette image est fournie avec le plug-in Jenkins de Service Fabric préinstallé.
  2. Exécutez l’image de conteneur :``docker run -itd -p 8080:8080 rapatchi/jenkins:v10``
  3. Récupérez l’ID de l’instance d’image du conteneur. Vous pouvez répertorier tous les conteneurs Docker avec la commande ``docker ps –a``
  4. Connectez-vous au portail Jenkins en procédant comme suit :

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Si l’ID du conteneur est 2d24a73b5964, utilisez 2d24.
    * Ce mot de passe est nécessaire pour vous connecter au tableau de bord Jenkins à partir du portail : ``http://<HOST-IP>:8080``
    * Après votre première connexion, vous pouvez créer votre propre compte d’utilisateur pour l’utiliser à l’avenir ou vous pouvez continuer à vous servir du compte d’administrateur. Après avoir créé un utilisateur, vous devez continuer ainsi.
  5. Configurez GitHub pour utiliser Jenkins, en suivant les étapes présentées dans [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Génération d’une clé SSH et ajout de celle-ci à l’agent SSH).
        * Suivez les instructions fournies par GitHub pour générer la clé SSH, puis l’ajouter au compte GitHub qui héberge le référentiel.
        * Exécutez les commandes mentionnées dans le lien ci-dessus dans le shell Jenkins Docker (et non sur l’hôte).
      * Pour vous connecter au shell Jenkins depuis votre hôte, utilisez les commandes suivantes :

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Vérifiez que le cluster ou la machine hébergeant l’image de conteneur Jenkins possède une adresse IP publique. Cela permet à l’instance Jenkins de recevoir des notifications de GitHub.


## <a name="create-and-configure-a-jenkins-job"></a>Créer et configurer un travail Jenkins

1. Créez un **élément** à partir du tableau de bord.
2. Entrez un nom d’élément (par exemple, **MyJob**). Sélectionnez **free-style project** (projet libre), puis cliquez sur **OK**.
3. Accédez à la page du travail, puis cliquez sur **Configurer**.

   a. Dans la section générale, cochez la case **GitHub project** (Projet GitHub), puis spécifiez l’URL de votre projet GitHub. Cette URL héberge l’application Java Service Fabric que vous souhaitez intégrer au flux d’intégration continue et de déploiement continu Jenkins (CI/CD) (par exemple, ``https://github.com/sayantancs/SFJenkins``).

   b. Sous la section **Gestion du code source**, sélectionnez **Git**. Spécifiez l’URL du référentiel qui héberge l’application Java Service Fabric que vous souhaitez intégrer au flux Jenkins CI/CD (par exemple, ``https://github.com/sayantancs/SFJenkins.git``). Vous pouvez également spécifier ici la branche à générer (par exemple, **/master**).
4. Configurez votre *GitHub* (qui héberge le référentiel) afin qu’il soit en mesure de communiquer avec Jenkins. Procédez comme suit :

   a. Accédez à la page de votre référentiel GitHub. Accédez à **Settings** > **Integrations and Services** (Paramètres -> Intégrations et services).

   b. Sélectionnez **Add Service** (Ajouter un service), tapez **Jenkins** et sélectionnez le **plug-in Jenkins-Github**.

   c. Entrez votre URL du webhook Jenkins (par défaut, il doit être ``http://<PublicIPorFQDN>:8081/github-webhook/``). Cliquez sur **Add/Update service** (Ajouter/Mettre à jour le service).

   d. Un événement de test est envoyé à votre instance Jenkins. Vous devez voir une coche verte à côté du webhook dans GitHub et votre projet sera généré.

   e. Dans la section **Build Triggers** (Générer des déclencheurs), sélectionnez l’option de création souhaitée. Pour cet exemple, vous souhaitez déclencher une génération chaque fois qu’un envoi a lieu vers le référentiel. Vous sélectionnez donc **GitHub hook trigger for GITScm polling** (Déclencher un hook GitHub pour l’interrogation GITScm). (Cette option s’appelait auparavant **Build when a change is pushed to GitHub** (Générer lorsqu’une modification est envoyée à GitHub).)

   f. **Section Build pour les applications Java :** Sous la **section Build** (Générer), dans la liste déroulante **Add build step** (Ajouter une étape de génération), sélectionnez l’option **Invoke Gradle Script** (Appeler le script Gradle). Dans le widget qui apparaît, ouvrez le menu Avancé, spécifiez le chemin d’accès à **Root build script** (Script de génération racine) pour votre application. Il récupère le Gradle de la génération à partir du chemin spécifié et fonctionne en conséquence. Si vous créez un projet nommé ``MyActor`` (à l’aide du plug-in Eclipse ou du générateur Yeoman), le script de compilation racine doit contenir ``${WORKSPACE}/MyActor``. Consultez la capture d’écran ci-dessous pour avoir une idée du rendu :

    ![Action de génération Jenkins de Service Fabric][build-step]

   g. **Section Build pour les applications .Net Core :** Sous la **section Build** (Générer), dans la liste déroulante **Add build step** (Ajouter une étape de génération), sélectionnez l’option **Execute Shell** (Exécuter l’interpréteur de commandes). Dans la zone de commande qui apparaît, le répertoire doit d'abord être remplacé par le chemin d'accès au fichier build.sh. Une fois le répertoire modifié, le script build.sh peut être exécuté et va générer l'application.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    Voici un exemple de commandes utilisées pour générer l'exemple [Counter Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) (Service de compteur) avec le nom de travail Jenkins CounterServiceApplication.

    ![Action de génération Jenkins de Service Fabric][build-step-dotnet]
  
   h. Dans la liste déroulante **Post-Build Actions** (Actions post-génération), sélectionnez **Deploy Service Fabric Project** (Déployer le projet Service Fabric). Ici, vous devez fournir des détails sur le cluster où l’application Service Fabric compilée par Jenkins est déployée. Pour trouver le chemin du certificat, vous pouvez faire écho à la variable d’environnement Certificates_JenkinsOnSF_Code_MyCert_PEM de l’écho à partir du conteneur. Ce chemin peut être utilisé pour les champs Client Key et Client Cert.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    Vous pouvez également donner des informations supplémentaires sur l’application utilisée pour déployer l’application. Consultez la capture d’écran ci-dessous pour avoir une idée du rendu :

    ![Action de génération Jenkins de Service Fabric][post-build-step]

      > [!NOTE]
      > Le cluster ici peut être identique à celui qui héberge l’application de conteneur Jenkins dans le cas où vous utilisez Service Fabric pour déployer l’image de conteneur Jenkins.
      >

## <a name="next-steps"></a>Étapes suivantes
GitHub et Jenkins sont maintenant configurés. Envisagez d’effectuer des exemples de modifications dans votre projet ``MyActor`` dans l’exemple de référentiel, https://github.com/sayantancs/SFJenkins. Envoyez vos modifications à la branche ``master`` distante (ou à n’importe quelle branche configurée pour être compatible). Cela déclenche le travail Jenkins, ``MyJob``, que vous avez configuré. Il extrait les modifications de GitHub, les génère et déploie l’application sur le point de terminaison du cluster spécifié dans les actions après génération.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

