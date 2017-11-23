---
title: Sauvegarde de votre application dans Azure
description: "Apprenez à créer des sauvegardes de vos applications Web dans Azure App Service."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 435370a8758d439a5fcce2e04efd11b4aaaf0357
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2017
---
# <a name="back-up-your-app-in-azure"></a>Sauvegarde de votre application dans Azure
La fonctionnalité de sauvegarde et de restauration [d’Azure App Service](app-service-web-overview.md) vous permet de créer facilement des sauvegardes d’applications manuelles ou planifiées. Vous pouvez restaurer l’application d’après la capture instantanée d’un état précédent en remplaçant l’application existante ou en restaurant sur une autre application. 

Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Éléments sauvegardés
App Service peut sauvegarder les informations suivantes dans un compte de stockage Azure et un conteneur pour l’utilisation desquels votre application a été configurée. 

* la configuration d’une application ;
* le contenu d’un fichier ;
* la base de données connectée à votre application.

Les solutions de base de données suivantes sont prises en charge par la fonctionnalité de sauvegarde : 
   - [Base de données SQL](https://azure.microsoft.com/en-us/services/sql-database/)
   - [Azure Database pour MySQL (Version préliminaire)](https://azure.microsoft.com/en-us/services/mysql)
   - [Azure Database pour PostgreSQL (Version préliminaire)](https://azure.microsoft.com/en-us/services/postgres)
   - [MySQL dans l’application](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Chaque sauvegarde représente une copie hors connexion complète de votre application et non une mise à jour incrémentielle.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Exigences et restrictions
* La fonctionnalité de sauvegarde et de restauration implique que le plan App Service soit de type **Standard** ou **Premium**. Pour plus d'informations sur la mise à l’échelle de votre plan App Service en vue d'utiliser un niveau plus élevé, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md).  
  Le niveau **Premium** permet un plus grand nombre de sauvegardes quotidiennes que le niveau **Standard**.
* Vous avez besoin d’un compte de stockage Azure et d’un conteneur dans le même abonnement que l’application que vous souhaitez sauvegarder. Pour plus d'informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) situés en bas de cet article.
* Les sauvegardes peuvent contenir jusqu’à 10 Go de contenu d’applications et de bases de données. Une erreur se produit si la taille de la sauvegarde dépasse cette limite.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Création d’une sauvegarde manuelle
1. Dans le [portail Azure](https://portal.azure.com), accédez à la page de votre application, puis sélectionnez **Sauvegardes**. La page **Sauvegardes** s’affiche.
   
    ![Page Sauvegardes][ChooseBackupsPage]
   
   > [!NOTE]
   > Si le message suivant s’affiche, cliquez dessus pour mettre à niveau votre plan App Service avant de pouvoir poursuivre les sauvegardes.
   > Pour plus d’informations, consultez [Faire monter en puissance une application web dans Azure](web-sites-scale.md).  
   > ![Sélection d'un compte de stockage](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. Dans la page **Sauvegarde**, cliquez sur **Configurer**.
![Cliquer sur Configurer](./media/web-sites-backup/ClickConfigure1.png)
3. Dans la page **Configuration de la sauvegarde**, cliquez sur **Stockage : Non configuré** pour configurer un compte de stockage.
   
    ![Sélection d'un compte de stockage][ChooseStorageAccount]
4. Choisissez la destination de sauvegarde en sélectionnant un **Compte de stockage** et un **Conteneur**. Ce compte de stockage doit relever du même abonnement que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un compte de stockage ou un conteneur dans les pages respectives. Quand vous avez terminé, cliquez sur **Sélectionner**.
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. Dans la page **Configuration de la sauvegarde** toujours ouverte, vous pouvez configurer **Base de données de sauvegarde**, sélectionner les bases de données que vous souhaitez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquer sur **OK**.  
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Pour qu’une base de données apparaisse dans cette liste, sa chaîne de connexion doit figurer dans la section **Chaînes de connexion** de la page **Paramètres d’application** de votre application.
   > 
   > 
6. Dans la page **Configuration de la sauvegarde**, cliquez sur **Enregistrer**.    
7. Dans la page **Sauvegardes**, cliquez sur **Sauvegarde**.
   
    ![Bouton Backup Now][BackUpNow]
   
    Un message de progression s’affiche au cours du processus de sauvegarde.

Une fois le compte de stockage et le conteneur configurés, vous pouvez lancer une sauvegarde manuelle à tout moment.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configuration de sauvegardes automatisées
1. Dans la page **Configuration de la sauvegarde**, **activez** la **Sauvegarde planifiée**. 
   
    ![Sélection d'un compte de stockage](./media/web-sites-backup/05ScheduleBackup1.png)
2. Les options de planification de la sauvegarde apparaissent : **activez** la **Sauvegarde planifiée**, puis configurez la planification de sauvegarde comme vous le souhaitez et cliquez sur **OK**.
   
    ![Activation des sauvegardes automatisées][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurer des sauvegardes partielles
Parfois, vous ne souhaitez pas sauvegarder tout le contenu de votre application. Voici quelques exemples :

* Vous [configurez une sauvegarde hebdomadaire](web-sites-backup.md#configure-automated-backups) de votre application qui contient du contenu statique qui ne change jamais, comme des anciens billets de blog ou des images.
* Votre application contient plus de 10 Go de données (quantité maximale que vous pouvez sauvegarder à la fois).
* Vous ne souhaitez pas sauvegarder les fichiers journaux.

Les sauvegardes partielles vous permettent de choisir exactement les fichiers à sauvegarder.

### <a name="exclude-files-from-your-backup"></a>Exclusion de fichiers de votre sauvegarde
Supposons que vous avez une application qui contient des fichiers journaux et des images statiques créés à un moment donné et qui ne seront jamais modifiés. Dans ce cas, vous pouvez exclure ces fichiers et dossiers du stockage lors de vos sauvegardes futures. Pour exclure des fichiers et dossiers de vos sauvegardes, créez un fichier `_backup.filter` dans le dossier `D:\home\site\wwwroot` de votre application. Spécifiez la liste des fichiers et dossiers à exclure de ce fichier. 

L’utilisation de Kudu permet d’accéder facilement à vos fichiers. Cliquez sur le paramètre **Outils avancés -> Accéder** de votre application web pour accéder aux Kudu.

![Utilisation du portail par Kudu][kudu-portal]

Identifiez les dossiers que vous souhaitez exclure de vos sauvegardes.  Par exemple, si vous souhaitez exclure les fichiers et dossiers en surbrillance.

![Dossier images][ImagesFolder]

Créez un fichier sous le nom `_backup.filter` et placez la liste précédente dans le fichier, mais supprimez `D:\home`. Listez un répertoire ou fichier par ligne. Par conséquent, le contenu du fichier doit ressembler à ce qui suit :
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Téléchargez le fichier `_backup.filter` vers le répertoire `D:\home\site\wwwroot\` de votre site en utilisant [ftp](app-service-deploy-ftp.md) ou toute autre méthode. Si vous le souhaitez, vous pouvez créer le fichier directement à l’aide de Kudu `DebugConsole` et y insérer le contenu.

Exécutez des sauvegardes comme vous le faites normalement, [manuellement](#create-a-manual-backup) ou [automatiquement](#configure-automated-backups). Maintenant, tous les fichiers et dossiers spécifiés dans `_backup.filter` sont exclus des futures sauvegardes planifiées ou lancées manuellement. 

> [!NOTE]
> Pour restaurer les sauvegardes partielles de votre site, procédez de la même façon que pour [restaurer une sauvegarde régulière](web-sites-restore.md). Le processus de restauration fait ce qu’il faut.
> 
> Lorsqu'une sauvegarde complète est restaurée, tout le contenu sur le site est remplacé par tout ce qui se trouve dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé. Mais lorsqu'une sauvegarde partielle est restaurée, tout contenu qui se trouve dans l'un des répertoires exclus, ou n'importe quel fichier exclu, est conservé tel quel.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Mode de stockage des sauvegardes
Dès que vous avez effectué une ou plusieurs sauvegardes de votre application, celles-ci apparaissent dans la page **Conteneurs** de votre compte de stockage et dans votre application. Dans le compte de stockage, chaque sauvegarde se compose d’un fichier `.zip` et d’un fichier `.xml` contenant respectivement les données sauvegardées et un manifeste du contenu du fichier `.zip`. Vous pouvez décompresser et parcourir ces fichiers si vous souhaitez accéder à vos sauvegardes sans réellement effectuer une restauration d'application.

La sauvegarde de base de données pour l'application est stockée dans la racine du fichier .zip. Pour une base de données SQL, il s'agit d'un fichier BACPAC (pas d'extension de fichier) qui peut être importé. Pour créer une base de données SQL en fonction de l’exportation de BACPAC, consultez [Importer un fichier BACPAC pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Toute modification apportée aux fichiers de votre conteneur **websitebackups** peut invalider la sauvegarde et la rendre impossible à restaurer.
> 
> 

## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des sauvegardes à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Pour obtenir des exemples, consultez :

- [Exemples d’interface de ligne de commande Azure](app-service-cli-samples.md)
- [Exemples Azure PowerShell](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, consultez [Restauration d’une application dans Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

