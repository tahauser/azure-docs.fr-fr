---
title: "Exécuter un serveur d’applications Java sur une machine virtuelle Azure Classic | Microsoft Docs"
description: "Ce didacticiel utilise des ressources créées avec le modèle de déploiement Classic, et montre comment créer une machine virtuelle Windows et la configurer pour exécuter un serveur d’applications Apache Tomcat."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: b166d2af56051b7d90eba0d50e1ea41f96c1109e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Exécution d’un serveur d’applications Java sur une machine virtuelle créée avec le modèle de déploiement classique.
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Vous trouverez [ici](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/) plus d’informations pour déployer une application web avec Java 8 et Tomcat selon un modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Azure permet d'utiliser une machine virtuelle pour obtenir des fonctionnalités de serveur. Par exemple, vous pouvez configurer une machine virtuelle exécutée sur Azure pour qu'elle héberge un serveur d'applications Java, comme Apache Tomcat.

Après avoir lu ce guide, vous comprendrez comment créer une machine virtuelle exécutée sur Azure et comment la configurer pour exécuter un serveur d’applications Java. Vous allez apprendre à effectuer les tâches suivantes :

* créer une machine virtuelle déjà dotée d’un kit de développement Java (JDK) ;
* se connecter à distance à votre machine virtuelle ;
* installer un serveur d’applications Java (Apache Tomcat) sur votre machine virtuelle ;
* créer un point de terminaison pour votre machine virtuelle ;
* ouvrir un port dans le pare-feu pour votre serveur d'applications.

À la fin de l’installation, Tomcat s’exécute sur une machine virtuelle.

![Machine virtuelle exécutant Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Création d’une machine virtuelle
1. Connectez-vous au [Portail Azure](https://portal.azure.com).  
2. Cliquez sur **Créer une ressource**, sur **Calcul**, puis sur **Afficher tout** dans **Applications proposées**.
3. Cliquez sur **JDK**, puis sur **JDK 8** dans le volet **JDK**.  
   Les images de machine virtuelle prenant en charge **JDK 6** et **JDK 7** sont disponibles si vous ne pouvez pas exécuter certaines de vos applications héritées dans la version JDK 8.
4. Dans le volet JDK 8, sélectionnez **Classique**, puis cliquez sur **Créer**.
5. Sur le panneau **De base** :
   1. Entrez un nom pour la machine virtuelle.
   2. Entrez un nom pour l'administrateur dans le champ **Nom d'utilisateur** . Notez le nom et le mot de passe associé qui s’affichent dans le champ suivant. Vous en aurez besoin lorsque vous vous connecterez à distance à la machine virtuelle.
   3. Entrez un mot de passe dans le champ **Nouveau mot de passe**, puis entrez-le de nouveau dans le champ **Confirmer le mot de passe**. Il s’agit du mot de passe du compte Administrateur.
   4. Sélectionnez **l’Abonnement**approprié.
   5. Pour le **Groupe de ressources**, cliquez sur **Création** et entrez le nom d’un nouveau groupe de ressources. Vous pouvez également cliquer sur **Utiliser l’élément existant** et sélectionner l’un des groupes de ressources disponibles.
   6. Sélectionnez l’emplacement où réside la machine virtuelle, tel que **Sud du centre des États-Unis**.
6. Cliquez sur **Suivant**.
7. Dans le panneau **Taille d’image de machine virtuelle**, sélectionnez **A1 Standard** ou une autre image appropriée.
8. Cliquez sur **Sélectionner**.

9. Dans le panneau **Paramètres**, cliquez sur **OK**. Vous pouvez utiliser les valeurs par défaut fournies par Azure.  
10. Dans le panneau **Résumé**, cliquez sur **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Connexion distante à votre machine virtuelle
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Machines virtuelles (classiques)**. Si nécessaire, cliquez sur **Plus de services** dans le coin inférieur gauche sous les catégories de services. L’entrée **Machines virtuelles (classiques)** est répertoriée dans le groupe **Calcul**.
3. Cliquez sur le nom de la machine virtuelle à laquelle vous souhaitez vous connecter.
4. Une fois que la machine virtuelle a démarré, un menu situé dans la partie supérieure du volet autorise les connexions.
5. Cliquez sur **Connecter**.
6. Répondez aux invites pour vous connecter à la machine virtuelle. En général, vous enregistrez ou vous ouvrez le fichier .rdp qui contient les informations de connexion. Vous devrez peut-être copier le port url:port en tant que dernière partie de la première ligne du fichier .rdp et le coller dans une application de connexion distante.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Installation d'un serveur d'applications Java sur votre machine virtuelle
Vous pouvez copier un serveur d’applications Java sur votre machine virtuelle ou l’installer via un programme d’installation.

Ce didacticiel utilise Tomcat en tant que serveur d’applications Java pour l’installation.

1. Une fois connecté à votre machine virtuelle, ouvrez une session de navigateur sur [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Double-cliquez sur le lien **32-bit/64-bit Windows Service Installer**. Cette procédure installe Tomcat en tant que service Windows.
3. Lorsque vous y êtes invité, exécutez le programme d'installation.
4. Dans l'Assistant **Apache Tomcat Setup** , suivez les consignes pour installer Tomcat. Dans le cadre de ce didacticiel, vous pouvez valider les valeurs par défaut. Quand vous accédez à la boîte de dialogue **Completing the Apache Tomcat Setup Wizard**, vous pouvez éventuellement cocher **Run Apache Tomcat** pour démarrer Tomcat immédiatement. Cliquez sur **Terminer** pour terminer le processus d'installation de Tomcat.

## <a name="to-start-tomcat"></a>Démarrage de Tomcat

Vous pouvez démarrer manuellement Tomcat en ouvrant une invite de commandes sur votre machine virtuelle et en exécutant la commande **net&nbsp;start&nbsp;Tomcat8**.

Une fois Tomcat en cours d’exécution, vous pouvez y accéder en entrant l’URL <http://localhost:8080> dans le navigateur de la machine virtuelle.

Pour démarrer Tomcat depuis des machines externes, vous devez créer un point de terminaison et ouvrir un port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Création d'un point de terminaison pour votre machine virtuelle
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Cliquez sur **Machines virtuelles (classiques)**.
3. Cliquez sur le nom de la machine virtuelle exécutant votre serveur d'applications Java.
4. Cliquez sur **Endpoints**.
5. Cliquez sur **Add**.
6. Dans la boîte de dialogue **Add Endpoint** :
   1. Entrez un nom pour le point de terminaison, par exemple **HttpIn**.
   2. Sélectionnez **TCP** pour le protocole.
   3. Indiquez **80** pour le port public.
   4. Indiquez **8080** pour le port privé.
   5. Sélectionnez **Désactivé** pour l’adresse IP flottante.
   6. Laissez la liste de contrôle d’accès en l’état.
   7. Cliquez sur le bouton **OK** pour fermer la boîte de dialogue et créer le point de terminaison.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Ouverture d'un port dans le pare-feu pour votre machine virtuelle
1. Connectez-vous à votre machine virtuelle.
2. Cliquez sur le menu **Démarrage de Windows**.
3. Cliquez sur **Panneau de configuration**.
4. Cliquez sur **Système et sécurité**, sur **Pare-feu Windows**, puis sur **Paramètres avancés**.
5. Cliquez sur **Règles de trafic entrant**, puis sur **Nouvelle règle**.  
   ![Nouvelle règle de trafic entrant][NewIBRule]
6. Pour **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.  
   ![Porte de nouvelle règle de trafic entrant][NewRulePort]
7. Dans l’écran **Protocole et ports**, sélectionnez **TCP**, spécifiez **8080** comme **Port local spécifique**, puis cliquez sur **Suivant**.  
  ![Nouvelle règle de trafic entrant][NewRuleProtocol]
8. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.
   ![Action de nouvelle règle de trafic entrant][NewRuleAction]
9. Dans l’écran **Profil**, vérifiez que les options **Domaine**, **Privé** et **Public** sont sélectionnées, puis cliquez sur **Suivant**.
   ![Profil de nouvelle règle de trafic entrant][NewRuleProfile]
10. Dans l’écran **Name**, indiquez un nom pour la règle, par exemple **HttpIn** (il n’est pas nécessaire que ce nom corresponde à celui du point de terminaison), puis cliquez sur **Terminer**.  
    ![Nom de nouvelle règle de trafic entrant][NewRuleName]

À ce stade, votre site web Tomcat doit être visible à partir d’un navigateur externe. Dans la fenêtre d’adresse du navigateur, tapez une URL au format **http://*votre\_nom\_DNS*.cloudapp.net**, où ***votre\_nom\_DNS*** correspond au nom DNS que vous avez indiqué lors de la création de la machine virtuelle.

## <a name="application-lifecycle-considerations"></a>Considérations relatives au cycle de vie de l'application
* Vous pouvez créer votre propre archive web d’application (WAR) et l’ajouter au dossier **webapps** . Par exemple, créez un projet web dynamique Java Service Page (JSP) de base et exportez-le sous forme de fichier WAR. Ensuite, copiez le fichier WAR dans le dossier **webapps** Apache Tomcat sur la machine virtuelle, puis exécutez-le dans un navigateur.
* Par défaut, lorsque le service Tomcat est installé, il est configuré pour être démarré manuellement. Vous pouvez le configurer pour démarrer automatiquement en utilisant le composant logiciel enfichable Services. Démarrez-le en cliquant sur le menu **Démarrer de Windows**, **Outils d’administration**, puis **Services**. Double-cliquez sur le service **Apache Tomcat** et affectez au **Type de démarrage** la valeur **Automatique**.

    ![Configurer un service pour qu'il démarre automatiquement][service_automatic_startup]

    L’activation du démarrage automatique de Tomcat lui permet de redémarrer en même temps que la machine virtuelle (par exemple, après des mises à jour logicielles nécessitant un redémarrage).

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre davantage sur les autres services, tels qu’Azure Storage, Service Bus et la base de données SQL, à inclure dans vos applications Java. Afficher les informations disponibles dans le [Centre de développement Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
