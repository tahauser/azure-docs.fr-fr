---
title: "Déploiement d’une machine virtuelle Azure avec Chef | Microsoft Docs"
description: "Découvrez comment utiliser Chef pour effectuer un déploiement et une configuration de machine virtuelle automatisés dans Microsoft Azure"
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: 9dabf666c633b59c7d1f9478b0e9cfe9d313e129
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisation du déploiement de machine virtuelle Azure avec Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef est un excellent outil d’automatisation et de fourniture des configurations d’état souhaitées.

Avec la dernière version d’API cloud, Chef assure l’intégration transparente avec Azure, ce qui vous donne la possibilité de configurer et de déployer les états de configuration via une commande unique.

Dans cet article, vous apprendrez à configurer votre environnement Chef pour configurer des machines virtuelles Azure et à créer une stratégie ou « Livre de recettes » avant de la déployer sur une machine virtuelle Azure.

Commençons !

## <a name="chef-basics"></a>Principes fondamentaux de Chef
Avant de commencer, [familiarisez-vous avec les principes fondamentaux de Chef](http://www.chef.io/chef). 

Le schéma ci-dessous illustre l’architecture de Chef de haut niveau.

![][2]

Chef présente trois principaux composants architecturaux : le serveur Chef, le client Chef (nœud) et la station de travail Chef.

Le serveur Chef est le point de gestion. Il existe deux options pour le serveur Chef : une solution hébergée ou une solution locale. Nous allons utiliser une solution hébergée.

Le client Chef (nœud) est l’agent qui se trouve sur les serveurs que vous gérez.

La station de travail Chef est la station de travail d’administration où nous créons des stratégies et exécutons des commandes de gestion. Nous exécutons la commande **knife** à partir de la station de travail Chef pour gérer l’infrastructure.

Les concepts de « Livres de recettes » et de « Recettes » existent aussi. Il s’agit des stratégies que nous définissons et qui s’appliquent aux serveurs.

## <a name="preparing-the-workstation"></a>Préparation de la station de travail
Tout d’abord, préparons la station de travail. J’utilise une station de travail Windows standard. Nous devons créer un répertoire pour stocker les fichiers de configuration et les livres de recettes.

Commencez par créer un répertoire appelé C:\chef.

Créez ensuite un second répertoire appelé c:\chef\cookbooks.

Nous devons maintenant télécharger le fichier de paramètres Azure pour que Chef puisse communiquer avec l’abonnement Azure.

Télécharger vos paramètres de publication à l’aide de la commande PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0). 

Enregistrez le fichier de paramètres de publications dans C:\chef.

## <a name="creating-a-managed-chef-account"></a>Création d’un compte Chef géré
Inscrivez-vous à un compte Chef hébergé [ici](https://manage.chef.io/signup).

Pendant le processus d’inscription, vous devrez créer une organisation.

![][3]

Une fois que votre organisation est créée, téléchargez le starter kit.

![][4]

> [!NOTE]
> Si vous recevez une invite pour vous prévenir que vos clés seront réinitialisées, continuez, étant donné que nous ne disposons pas encore d’une infrastructure configurée.
> 
> 

Le fichier zip du starter kit comprend vos clés et fichiers de configuration d’organisation.

## <a name="configuring-the-chef-workstation"></a>Configuration de la station de travail Chef
Extrayez le contenu du fichier chef-starter.zip vers C:\chef.

Copiez tous les fichiers sous chef-starter\chef-repo\.chef vers votre répertoire C:\chef.

Votre répertoire doit ressembler à ce qui suit.

![][5]

Vous devez maintenant avoir quatre fichiers, y compris le fichier de publication Azure dans la racine de c:\chef.

Les fichiers PEM contiennent votre organisation et les clés privées d’administration pour la communication, tandis que le fichier knife.rb contient votre configuration de couteau. Il convient de modifier le fichier knife.rb.

Ouvrez le fichier dans l’éditeur de votre choix et modifiez « cookbook_path » en supprimant /.../ du chemin d’accès afin qu’il apparaisse comme ci-dessous.

    cookbook_path  ["#{current_dir}/cookbooks"]

Ajoutez également la ligne suivante, pour refléter le nom de votre fichier de paramètres de publication Azure.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Votre fichier knife.rb doit maintenant ressembler à ce qui suit.

![][6]

Ces lignes garantissent que le fichier knife effectue ses références dans notre répertoire de livres de recettes dans c:\chef\cookbooks et utilise également notre fichier de paramètres de publication Azure pendant les opérations d’Azure.

## <a name="installing-the-chef-development-kit"></a>Installation du kit de développement Chef
Ensuite, [téléchargez et installez](http://downloads.getchef.com/chef-dk/windows) le ChefDK (Kit de développement Chef) pour configurer votre station de travail Chef.

![][7]

Installez-le dans l’emplacement par défaut de c:\opscode. Cette opération prendra environ 10 minutes.

Vérifiez que votre variable PATH comprend les entrées de C:\opscode\chefdk\bin;C:\opscode\chefdk\embedded\bin;C:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

S’ils sont absents, assurez-vous de les ajouter !

*NOTEZ QUE L’ORDRE DU CHEMIN D’ACCÈS EST IMPORTANT !* Si vos chemins opscode ne sont pas dans l’ordre adéquat, cela créera des problèmes.

Redémarrez votre station de travail avant de continuer.

Ensuite, nous allons installer l’extension Knife Azure. Cela fournit le « plug-in Azure » à Knife.

Exécutez la commande ci-dessous.

    chef gem install knife-azure ––pre

> [!NOTE]
> L’argument –pre garantit que vous recevez la dernière version RC du plug-in Knife Azure, qui permet d’accéder à la dernière série d’API.
> 
> 

Il est probable que plusieurs dépendances soient également installées en même temps.

![][8]

Pour vous assurer que tout est configuré correctement, exécutez la commande suivante.

    knife azure image list

Si tout est configuré correctement, vous verrez une liste d’images Azure disponibles défiler.

Félicitations ! La station de travail est configurée !

## <a name="creating-a-cookbook"></a>Création d’un livre de recettes
Dans Chef, un livre de recettes permet de définir un ensemble de commandes que vous souhaitez exécuter sur votre client managé. La création d’un livre de recettes est très simple et nous utilisons la commande **chef generate cookbook** pour générer le modèle de livre de recettes. Nous appellerons notre livre de recettes webserver, étant donné que je souhaite disposer d’une stratégie qui déploie IIS automatiquement.

Dans le répertoire C:\Chef, exécutez la commande suivante.

    chef generate cookbook webserver

Cela génère un ensemble de fichiers dans le répertoire C:\Chef\cookbooks\webserver. Nous devons maintenant définir le jeu de commandes que le client Chef doit exécuter sur la machine virtuelle managée.

Les commandes sont stockées dans le fichier default.rb. Dans ce fichier, je vais définir un ensemble de commandes qui installe les services IIS, démarre IIS et copie un fichier de modèle dans le dossier wwwroot.

Modifiez le fichier C:\chef\cookbooks\webserver\recipes\default.rb et ajoutez les lignes suivantes.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Enregistrez le fichier une fois que vous avez terminé.

## <a name="creating-a-template"></a>Création d’un modèle
Comme nous l’avons mentionné précédemment, nous devons générer un fichier de modèle qui sera utilisé comme la page default.html.

Exécutez la commande suivante pour générer le modèle.

    chef generate template webserver Default.htm

Maintenant, accédez au fichier C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Modifiez le fichier en y ajoutant le code html simple « Hello World », puis enregistrez-le.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Téléchargez le livre de recettes sur le serveur Chef
Dans cette étape, nous allons faire une copie du livre de recettes que nous avons créé sur l’ordinateur local et le télécharger vers le serveur hébergé Chef. Une fois téléchargé, le livre de recettes apparaît sous l’onglet **Stratégie** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Déployer une machine virtuelle avec Knife Azure
Nous allons maintenant déployer une machine virtuelle Azure et appliquerons le livre de recettes « Webserver » qui installera la page web par défaut et le service web IIS.

Pour ce faire, utilisez la commande **knife azure server create** .

Un exemple de la commande apparaît ci-dessous.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Les paramètres sont clairs. Remplacez vos variables particulières et exécutez.

> [!NOTE]
> À l’aide de la ligne de commande, nous automatisons également les règles de filtrage du réseau de point de terminaison à l’aide du paramètre –tcp-endpoints. Nous avons ouvert les ports 80 et 3389 pour fournir l’accès à notre page Web et à la session RDP.
> 
> 

Après avoir exécuté la commande, naviguez sur le portail Azure et vous verrez votre machine commencer à se configurer.

![][13]

L’invite de commande apparaît ci-dessous.

![][10]

Une fois le déploiement terminé, nous devrions être en mesure de nous connecter au service Web sur le port 80 que nous avions ouvert lorsque nous avons configuré la machine virtuelle avec la commande Knife Azure. Étant donné que cette machine virtuelle est la seule machine virtuelle dans notre service de cloud, nous allons la connecter avec l’URL du service cloud.

![][11]

Comme vous pouvez le voir, le code html est créatif.

N’oubliez pas que nous pouvons également nous connecter via une session RDP à partir du portail Azure via le port 3389.

Nous espérons que cela a été utile ! Démarrez votre voyage vers l’Infrastructure en tant que code avec Azure dès aujourd’hui !

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->
