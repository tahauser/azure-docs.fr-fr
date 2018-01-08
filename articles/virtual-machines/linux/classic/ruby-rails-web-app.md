---
title: "Hébergement d’un Ruby sur le site web de Rails sur une machine virtuelle Linux | Microsoft Docs"
description: "Configuration et hébergement d'un Ruby sur le site web de Rails dans Azure en utilisant une machine virtuelle Linux."
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fb6ded1dcba2ac0f78fc6f1f4f7de9238cd752bd
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Application Web Ruby on Rails sur une machine virtuelle Azure
Ce didacticiel explique comment héberger un site web Ruby on Rails sur Azure à l'aide d'une machine virtuelle Linux.  

Ce didacticiel a été validé à l’aide d’Ubuntu Server 14.04 LTS. Si vous utilisez une distribution Linux différente, vous devrez peut-être modifier les étapes pour installer Rails.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Création d’une machine virtuelle Azure
Commencez par créer une machine virtuelle Azure avec une image Linux.

Pour créer la machine virtuelle, vous pouvez utiliser le portail Azure ou l’interface de ligne de commande (CLI) Azure.

### <a name="azure-portal"></a>Portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau**, puis tapez « Ubuntu Server 14.04 » dans la zone de recherche. Cliquez sur l’entrée renvoyée par la recherche. Pour le déploiement du modèle, sélectionnez **Classique**, puis cliquez sur « Créer ».
3. Dans le panneau des éléments de base, saisissez des valeurs pour les champs requis : nom (de la machine virtuelle), nom d’utilisateur, type d’authentification et les informations d’identification correspondantes, abonnement Azure, groupe de ressources et emplacement.

   ![Create a new Ubuntu Image](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Une fois que la machine virtuelle est configurée, cliquez sur son nom, puis cliquez sur **Points de terminaison** dans la catégorie **Paramètres**. Recherchez le point de terminaison SSH répertorié sous **Autonome**.

   ![Point de terminaison par défaut.](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Interface de ligne de commande Azure
Suivez les étapes de [Create a Virtual Machine Running Linux (Création d’une machine virtuelle exécutant Linux)][vm-instructions].

Une fois que la machine virtuelle est configurée, vous pouvez obtenir le point de terminaison SSH en exécutant la commande suivante :

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>installation de Ruby sur Rails
1. Utilisez le protocole SSH pour vous connecter à la machine virtuelle.
2. Depuis la session SSH, utilisez les commandes suivantes pour installer Ruby sur la machine virtuelle :

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    L’installation peut prendre quelques minutes. Lorsqu’elle est terminée, utilisez la commande suivante pour vérifier que Ruby est installé :

        ruby -v

3. Utilisez la commande suivante pour installer Rails :

        sudo gem install rails --no-rdoc --no-ri -V

    Utilisez les indicateurs --no-rdoc et--no-ri pour ignorer l’installation de la documentation, ce qui est plus rapide.
    L’exécution de cette commande peut être très longue ; l’ajout de -V permet d’afficher des informations sur la progression de l’installation.

## <a name="create-and-run-an-app"></a>Création et exécution d’une application
Alors que vous êtes toujours connecté via SSH, exécutez les commandes suivantes :

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

La [nouvelle](http://guides.rubyonrails.org/command_line.html#rails-new) commande crée une nouvelle application Rails. La commande [server](http://guides.rubyonrails.org/command_line.html#rails-server) démarre le serveur web WEBrick qui est fourni avec Rails. (À des fins de production, vous souhaiterez probablement utiliser un autre serveur, par exemple Unicorn ou Passenger.)

Le résultat ressemble à ce qui suit.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Ajout d’un point de terminaison
1. Accédez au [portail Azure] [https://portal.azure.com] et sélectionnez votre machine virtuelle.

2. Sélectionnez **Points de terminaison** dans les **Paramètres** sur le bord gauche de la page.

3. Cliquez sur **Ajouter** en haut de la page.

4. Dans la boîte de dialogue **Ajouter point de terminaison**, entrez les informations suivantes :

   * **Nom** : HTTP
   * **Protocole**: TCP
   * **Port public** : 80
   * **Port privé** : 3000
   * **Adresse PI flottante** : Désactivée
   * **Liste de contrôle d’accès - ordre** : 1001, ou une autre valeur qui viendra définir la priorité de cette règle d’accès.
   * **Liste de contrôle d’accès - nom** : allowHTTP
   * **Liste de contrôle d’accès - Action** : autoriser
   * **Liste de contrôle d’accès - sous-réseau distant** : 1.0.0.0/16

     Ce point de terminaison a un port public de 80 pour acheminer le trafic vers le port privé 3000, où le serveur Rails écoute. La règle de la liste de contrôle de l’accès permet un trafic public sur le port 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Cliquez sur OK pour enregistrer le point de terminaison.

6. Un message doit apparaître indiquant **Enregistrement du point de terminaison de la machine virtuelle**. Une fois que ce message disparaît, le point de terminaison est actif. Vous pouvez désormais tester votre application en accédant au nom DNS de votre machine virtuelle. Le site web qui apparaît doit être semblable à l'illustration ci-dessous :

    ![page rails par défaut][default-rails-cloud]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez effectué la plupart des étapes manuellement. Dans un environnement de production, vous écririez votre application sur un ordinateur de développement et la déploieriez sur la machine virtuelle Azure. En outre, la plupart des environnements de production hébergent l'application Rails en association avec un autre processus serveur tel que Apache ou NginX, qui traite l'acheminement des requêtes vers plusieurs instances de l'application Rails et l'envoi des ressources statiques. Pour plus d’informations, consultez http://rubyonrails.org/deploy/.

Pour en savoir plus sur Ruby on Rails, consultez les [guides Ruby on Rails][rails-guides].

Pour utiliser des services Azure depuis votre application Ruby, consultez :

* [Utilisation du stockage d’objets blob à partir de Ruby][blobs]
* [Utilisation du stockage de tables à partir de Ruby][tables]
* [Distribution de contenu à bande passante élevée avec le CDN][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
