---
title: "Configuration d'Apache Tomcat sur une machine virtuelle Linux | Microsoft Docs"
description: "Apprenez à configurer Apache Tomcat7 avec une machine virtuelle Azure exécutant Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: f3bd3167c9a879a876774e5d91fbb10fd340c6a8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Configurer Tomcat7 sur une machine virtuelle Linux avec Azure
Apache Tomcat (ou simplement Tomcat, également appelé Jakarta Tomcat précédemment) est un serveur web open source et un conteneur de servlet développé par Apache Software Foundation (ASF). Tomcat implémente le servlet Java et les spécifications Java Server Pages (JSP) de Sun Microsystems. Tomcat fournit un environnement de serveur web HTTP Java pur dans lequel exécuter le code Java. Dans la configuration la plus simple, Tomcat s’exécute dans un processus de système d’exploitation unique. Ce processus exécute une machine virtuelle Java (JVM). Chaque requête HTTP d’un navigateur vers Tomcat est traitée comme un thread séparé dans le processus de Tomcat.  

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Azure Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article explique comment utiliser le modèle de déploiement classique. Pour la plupart des nouveaux déploiements, nous recommandons d’utiliser le modèle Resource Manager. Pour utiliser un modèle Resource Manager afin de déployer une machine virtuelle Ubuntu avec Open JDK et Tomcat, lisez [cet article](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Dans cet article, vous installerez Tomcat7 sur une image Linux et le déploierez dans Azure.  

Vous apprendrez à effectuer les opérations suivantes :  

* Création d’une machine virtuelle dans Azure.
* Préparation de la machine virtuelle pour Tomcat7.
* Installation de Tomcat7.

Nous partons du principe que vous possédez déjà un abonnement Azure.  Si ce n’est pas le cas, vous pouvez vous inscrire sur [le site web Azure](https://azure.microsoft.com/) pour obtenir une évaluation gratuite. Si vous disposez d’un abonnement MSDN, consultez la page présentant les [tarifs préférentiels Microsoft Azure : avantages MSDN, MPN et BizSpark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Pour en savoir plus sur Azure, consultez [Présentation d’Azure](https://azure.microsoft.com/overview/what-is-azure/).

Cet article suppose que vous avez des connaissances de base relatives à Tomcat et Linux.  

## <a name="phase-1-create-an-image"></a>Phase 1 : Création d’une image
Lors de cette phase, vous allez créer une machine virtuelle à l’aide d’une image Linux dans Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Étape 1 : Générer une clé d’authentification SSH
SSH est un outil important pour les administrateurs système. Toutefois, une configuration de la sécurité d’accès basée sur un mot de passe déterminé par l’homme n’est pas recommandée. Les utilisateurs malveillants peuvent s’introduire dans votre système si vous disposez d’un nom d’utilisateur et d’un mot de passe faibles.

Toutefois, il existe un moyen de laisser l’accès à distance ouvert. Vous n’aurez donc pas à vous soucier des mots de passe. Cette méthode se compose d’une authentification avec chiffrement asymétrique. La clé privée de l’utilisateur est celle qui accorde l’authentification. Vous pouvez même verrouiller le compte de l’utilisateur pour interdire l’authentification par mot de passe.

Un autre avantage de cette méthode est que vous n’avez pas besoin de mots de passe différents pour vous connecter à des serveurs différents. Vous pouvez vous authentifier à l’aide de la clé privée personnelle sur tous les serveurs, ce qui vous évite d’avoir à mémoriser plusieurs mots de passe.



Pour générer la clé d’authentification SSH, procédez comme suit.

1. Téléchargez et installez PuTTYgen à partir de l’emplacement suivant : [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Exécutez Puttygen.exe.
3. Cliquez sur **Generate** pour générer les clés. Dans le processus, vous pouvez augmenter le caractère aléatoire en déplaçant la souris sur la zone vide dans la fenêtre.  
   ![Capture d’écran du générateur de clé puTTY montrant le bouton permettant de générer une nouvelle clé][1]
4. Après le processus de génération, Puttygen.exe affiche votre nouvelle clé publique.  
   ![Capture d’écran du générateur de clé puTTY montrant la nouvelle clé publique et le bouton permettant d'enregistrer une clé privée][2]
5. Sélectionnez et copiez la clé publique et enregistrez-la dans un fichier nommé publicKey.pem. Ne cliquez pas sur **Save public key**, car le format de fichier de la clé publique enregistrée est différent de la clé publique que nous voulons.
6. Cliquez sur **Save private key** et enregistrez-la dans un fichier nommé privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Étape 2 : Créer l’image dans le portail Azure
1. Dans le [portail](https://portal.azure.com/), cliquez sur **Créer une ressource** dans la barre des tâches pour créer une image. Choisissez ensuite l’image Linux selon vos besoins. L’exemple suivant utilise l’image Ubuntu 14.04.
![Capture d’écran du portail montrant le bouton Nouveau][3]

2. Pour **Nom d’hôte** spécifiez le nom de l’URL que les clients Internet utiliseront pour accéder à cette machine virtuelle. Définissez la dernière partie du nom DNS, par exemple, tomcatdemo. Azure génère alors l’URL sous la forme tomcatdemo.cloudapp.net.  

3. Pour **Clé d’authentification SSH**, copiez la valeur de clé à partir du fichier publicKey.pem qui contient la clé publique générée par PuTTYgen.  
![Zone de la clé d’authentification SSH dans le portail][4]

4. Configurez d’autres paramètres selon les besoins, puis cliquez sur **Créer**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Phase 2 : Préparation de la machine virtuelle pour Tomcat7
Lors de cette phase, vous allez configurer un point de terminaison pour le trafic Tomcat, puis vous connecter à votre nouvelle machine virtuelle.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Étape 2 : Ouverture du port HTTP pour autoriser l’accès web
Les points de terminaison dans Azure se composent d’un protocole TCP ou UDP et d’un port public et privé. Le port privé est celui que le service écoute sur la machine virtuelle. Le port public est celui que le service cloud Azure écoute en externe pour le trafic Internet entrant.  

Le port TCP 8080 est le numéro de port par défaut sur lequel Tomcat écoute. Si ce port est ouvert avec un point de terminaison Azure, vous pouvez (vous et d’autres clients Internet) accéder aux pages Tomcat.  

1. Dans le portail, cliquez sur **Parcourir** > **Machines virtuelles**, puis cliquez sur la machine virtuelle que vous avez créée.  
   ![Capture d’écran de l’annuaire des machines virtuelles][5]
2. Pour ajouter un point de terminaison à votre machine virtuelle, cliquez sur la zone **Points de terminaison** .
   ![Capture d’écran montrant la zone Points de terminaison][6]
3. Cliquez sur **Add**.  

   1. Entrez un nom pour le point de terminaison dans **Point de terminaison**, puis tapez 80 dans **Port public**.  

      Si vous affectez le port 80, vous n’avez pas besoin d’inclure le numéro de port dans l’URL utilisée pour accéder à Tomcat. Par exemple, http://tomcatdemo.cloudapp.net.    

      Si vous affectez une autre valeur pour le port, par exemple 81, vous devez ajouter le numéro de port à l’URL pour accéder à Tomcat. Par exemple, http://tomcatdemo.cloudapp.net:81/.
   2. Tapez 8080 dans **Port privé**. Par défaut, Tomcat écoute sur le port TCP 8080. Si vous avez modifié le port d’écoute par défaut de Tomcat, vous devez mettre à jour **Port privé** pour qu’il s’agisse du même que le port d’écoute Tomcat.  
      ![Capture d’écran de l’interface utilisateur montrant la commande Ajouter, Port public et Port privé][7]
4. Cliquez sur **OK** pour ajouter le point de terminaison à votre machine virtuelle.

### <a name="step-2-connect-to-the-image-you-created"></a>Étape 2 : Connexion à l’image que vous avez créée
Vous pouvez choisir n’importe quel outil SSH pour vous connecter à votre machine virtuelle. Dans cet exemple, nous utilisons PuTTY.  

1. Obtenez le nom DNS de votre machine virtuelle à partir du portail.
    1. Cliquez sur **Parcourir** > **Machines virtuelles**.
    2. Sélectionnez le nom de votre machine virtuelle, puis cliquez sur **Propriétés**.
    3. Dans la mosaïque **Propriétés**, consultez la zone **Nom de domaine** pour obtenir le nom DNS.  

2. Obtenez le numéro de port pour les connexions SSH à partir de la zone **SSH**.  
![Capture d’écran montrant le numéro de port de connexion SSH][8]

3. Téléchargez [PuTTY](http://www.putty.org/).  

4. Après le téléchargement, cliquez sur le fichier exécutable Putty.exe. Dans la configuration PuTTY, configurez les options de base avec le nom d’hôte et le numéro de port obtenus à partir des propriétés de votre machine virtuelle.   
![Capture d’écran montrant les options de nom d'hôte et de port de la configuration PuTTY][9]

5. Dans le volet gauche, cliquez sur **Connexion** > **SSH** > **Auth**, puis cliquez sur **Parcourir** pour spécifier l’emplacement du fichier privateKey.ppk. Le fichier privateKey.ppk contient la clé privée générée précédemment par PuTTYgen dans la section « Phase 1 : Création d'une image » de cet article.  
![Capture d’écran montrant la hiérarchie du répertoire Connexion et un bouton Parcourir][10]

6. Cliquez sur **Ouvrir**. Une boîte de message peut s’afficher. Si vous avez configuré le nom DNS et le numéro de port correctement, cliquez sur **Oui**.
![Capture d’écran montrant la notification][11]

7. Vous êtes invité à saisir votre nom d'utilisateur.  
![Capture d’écran montrant où entrer le nom d’utilisateur][12]

8. Entrez le nom d’utilisateur que vous avez utilisé pour créer la machine virtuelle dans la section « Phase 1 : Création d'une image » plus haut dans cet article. Vous verrez quelque chose comme suit :   
![Capture d’écran montrant la confirmation de l’authentification][13]

## <a name="phase-3-install-software"></a>Phase 3 : Installation du logiciel
Dans cette phase, vous installez l’environnement d’exécution Java, Tomcat7 et d’autres composants de Tomcat7.  

### <a name="java-runtime-environment"></a>Environnement d’exécution Java
Tomcat est écrit en Java. Il existe deux types de Kits de développement Java (JDK), OpenJDK et JDK Oracle. Vous pouvez choisir celui qui vous intéresse.  

> [!NOTE]
> Les deux JDK ont pratiquement le même code pour les classes dans l’API Java, mais le code de la machine virtuelle est différent. OpenJDK utilise généralement des bibliothèques libres tandis que celles de JDK Oracle sont fermées. Le JDK Oracle a plus de classes et quelques correctifs et il est plus stable qu’OpenJDK.

#### <a name="install-openjdk"></a>Installer OpenJDK  

Utilisez la commande suivante pour télécharger OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Pour créer un répertoire contenant les fichiers JDK :  

        sudo mkdir /usr/lib/jvm  
* Pour extraire les fichiers JDK dans le répertoire /usr/lib/jvm :  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Installer JDK Oracle


Utilisez la commande suivante pour télécharger JDK Oracle à partir du site Web d’Oracle.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Pour créer un répertoire contenant les fichiers JDK :  

        sudo mkdir /usr/lib/jvm  
* Pour extraire les fichiers JDK dans le répertoire /usr/lib/jvm :  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Pour définir le JDK Oracle comme machine virtuelle Java par défaut :  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Confirmer que l’installation de Java a réussi
Vous pouvez utiliser une commande semblable à la suivante pour vérifier si l’environnement d’exécution Java est correctement installé :  

    java -version  

Si vous avez installé OpenJDK, vous devez voir un message similaire au suivant : ![Successful OpenJDK installation message][14] (Message d'installation réussie d'OpenJDK)

Si vous avez installé JDK Oracle, vous devez voir un message similaire au suivant : ![Successful Oracle JDK installation message][15] (Message d'installation réussie de JDK Oracle)

### <a name="install-tomcat7"></a>Installer Tomcat7
Utilisez la commande suivante pour installer Tomcat7.  

    sudo apt-get install tomcat7  

Si vous n’utilisez pas Tomcat7, utilisez la variation appropriée de cette commande.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Confirmer que l’installation de Tomcat7 a réussi
Pour vérifier que Tomcat7 est correctement installé, recherchez le nom DNS de votre serveur Tomcat. Dans cet article, l’exemple d'URL est http://tomcatexample.cloudapp.net/. Si vous voyez un message similaire à celui-ci, Tomcat7 est correctement installé.
![Message d’installation réussie de Tomcat7][16]

### <a name="install-other-tomcat7-components"></a>Installer d’autres composants de Tomcat7
Il existe d’autres composants facultatifs de Tomcat que vous pouvez installer.  

Utilisez la commande **sudo apt-cache search tomcat7** pour afficher tous les composants disponibles. Utilisez les commandes suivantes pour installer certains composants utiles.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Phase 4 : Configuration de Tomcat7
Dans cette phase, vous administrez Tomcat.

### <a name="start-and-stop-tomcat7"></a>Démarrage et arrêt de Tomcat7
Le serveur de Tomcat7 démarre automatiquement lorsque vous l’installez. Vous pouvez également le lancer avec la commande suivante :   

    sudo /etc/init.d/tomcat7 start

Pour arrêter Tomcat7 :

    sudo /etc/init.d/tomcat7 stop

Pour afficher l’état de Tomcat7 :

    sudo /etc/init.d/tomcat7 status

Pour redémarrer les services de Tomcat : 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administration de Tomcat7
Vous pouvez modifier le fichier de configuration utilisateur de Tomcat pour configurer vos informations d’identification d’administration. Utilisez la commande suivante :  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Voici un exemple :   
![Capture d’écran montrant la sortie de la commande sudo vi][17]  

> [!NOTE]
> Créez un mot de passe fort pour le nom d’utilisateur admin.  

Après avoir modifié ce fichier, vous devez redémarrer les services Tomcat7 avec la commande suivante pour vous assurer que les modifications prennent effet :  

    sudo /etc/init.d/tomcat7 restart  

Ouvrez votre navigateur et entrez l’URL **http://<your tomcat server DNS name>/manager/html**. Pour l’exemple de cet article, l’URL est http://tomcatexample.cloudapp.net/manager/html.  

Une fois connecté, vous devez voir quelque chose de similaire à ce qui suit :   
![Capture d’écran de Tomcat Web Application Manager][18]

## <a name="common-issues"></a>Problèmes courants
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Impossible d’accéder à une machine virtuelle avec Tomcat et Moodle à partir d’Internet
#### <a name="symptom"></a>Symptôme  
  Tomcat est en cours d’exécution, mais vous ne voyez pas la page par défaut Tomcat avec votre navigateur.
#### <a name="possible-root-cause"></a>Cause principale possible   

  * Le port d’écoute Tomcat n’est pas identique au port privé du point de terminaison de votre machine virtuelle pour le trafic Tomcat.  

     Vérifiez vos paramètres de points de terminaison de port public et de port privé et assurez-vous que le port privé est identique au port d’écoute Tomcat. Consultez la section « Phase 1 : Création d’une image » de cet article pour obtenir des instructions sur la configuration des points de terminaison pour votre machine virtuelle.  

     Pour déterminer le port d’écoute de Tomcat, ouvrez /etc/httpd/conf/httpd.conf (version Red Hat) ou /etc/tomcat7/server.xml (version Debian). Par défaut, le port d’écoute de Tomcat est 8080. Voici un exemple :   

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Si vous utilisez un ordinateur virtuel comme Debian ou Ubuntu et que vous souhaitez modifier la valeur par défaut de port d’écoute de Tomcat (par exemple 8081), vous devez également ouvrir le port pour le système d’exploitation. Tout d’abord, ouvrez le profil :  

        sudo vi /etc/default/tomcat7  

     Supprimez ensuite le commentaire de la dernière ligne, puis remplacez « non » par « oui ».  

        AUTHBIND=yes
  2. Le pare-feu a désactivé le port d’écoute Tomcat.

     Vous pouvez uniquement afficher la page Tomcat par défaut à partir de l’hôte local. Le problème est probablement dû au fait que le port écouté par Tomcat est bloqué par le pare-feu. Vous pouvez utiliser l’outil w3m pour parcourir la page web. Les commandes suivantes installent w3m et accèdent à la page par défaut Tomcat :  


        sudo yum  install w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Solution

  * Si le port d’écoute Tomcat n’est pas identique au port privé du point de terminaison pour le trafic sur la machine virtuelle, vous devez modifier le port privé pour qu’il soit identique au port d’écoute de Tomcat.   
  2. Si le problème est dû à firewall/iptables, ajoutez les lignes suivantes à /etc/sysconfig/iptables. La deuxième ligne est nécessaire uniquement pour le trafic https :  

      -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT

      -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

     > [!IMPORTANT]
     > Assurez-vous que les lignes précédentes sont positionnées au-dessus de toutes les lignes qui limiteraient globalement l’accès, par exemple : -A INPUT -j REJECT --reject-with icmp-host-prohibited



Pour recharger les iptables, exécutez la commande suivante :

    service iptables restart

Cela a été testé sur CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Autorisation refusée quand vous chargez des fichiers de projet vers /var/lib/tomcat7/webapps/
#### <a name="symptom"></a>Symptôme
  Quand vous utilisez un client SFTP (par exemple, FileZilla) pour vous connecter à votre machine virtuelle et que vous accédez à /var/lib/tomcat7/webapps/ pour publier votre site, un message d’erreur semblable au suivant s’affiche :  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Cause principale possible
  Vous n’êtes pas autorisé à accéder au dossier /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Solution  
  Vous devez obtenir l’autorisation à partir du compte racine. Vous pouvez modifier le propriétaire par défaut de ce dossier (la racine) et choisir le nom d’utilisateur que vous avez utilisé lors de l’approvisionnement de la machine. Voici un exemple avec le nom de compte azureuser :  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Utilisez l’option -R pour appliquer aussi les autorisations pour tous les fichiers contenus dans un répertoire.  

  Cette commande fonctionne également pour les répertoires. L’option -R modifie les autorisations pour tous les fichiers et répertoires contenus dans le répertoire. Voici un exemple :   

     sudo chown -R username:group directory  

  Cette commande modifie le propriétaire (utilisateur et groupe) pour tous les fichiers et répertoires contenus dans le répertoire.  

  La commande suivante modifie uniquement l’autorisation du répertoire. Les fichiers et dossiers dans le répertoire restent intacts.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
