---
title: Installation de Tâches de bases de données élastiques | Microsoft Docs
description: Passez en revue l'installation de la fonctionnalité de tâche élastique.
services: sql-database
manager: craigg
author: ddove
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 7e5258e0ccb88c8e2f3fe3277f69444ae5424cd0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="installing-elastic-database-jobs-overview"></a>Vue d’ensemble de l’installation de Tâches de bases de données élastiques
Une [**tâche de base de données élastique**](sql-database-elastic-jobs-overview.md) peut être installée à l’aide de PowerShell ou du portail Azure. Vous pouvez y accéder pour créer et gérer des tâches à l’aide de l’API PowerShell uniquement si vous installez le package PowerShell. En outre, les API PowerShell fournissent, à ce stade, beaucoup plus de fonctionnalités que le portail.

Si vous avez déjà installé **Tâche de base de données élastique** via le portail à partir d’un **pool élastique** existant, la dernière version préliminaire de Powershell inclut des scripts pour mettre à niveau votre installation existante. Il est vivement recommandé de mettre à niveau votre installation vers la dernière version des composants de **Tâches de bases de données élastiques** pour tirer parti des nouvelles fonctionnalités exposées via l'API PowerShell.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Pour un essai gratuit, consultez [Version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Installez la dernière version via [Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
* [utilitaire de ligne de commande NuGet](https://nuget.org/nuget.exe) est utilisé pour installer le package Tâches de bases de données élastiques. Pour plus d’informations, consultez http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Téléchargez et importez le package Tâches de bases de données élastiques PowerShell
1. Lancez la fenêtre de commande Microsoft Azure PowerShell et accédez au répertoire où vous avez téléchargé l’utilitaire de ligne de commande NuGet (nuget.exe).
2. Téléchargez et importez le package **Tâches de bases de données élastiques** dans le répertoire actuel à l'aide de la commande suivante :
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    Les fichiers **Tâche de base de données élastique** sont placés dans un dossier du répertoire local nommé **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** où *x.x.xxxx.x* correspond au numéro de version. Les applets de commande PowerShell (y compris les .dll clients requis) se trouvent dans le sous-répertoire **tools\ElasticDatabaseJobs** et les scripts PowerShell d’installation, de mise à niveau et de désinstallation résident également dans le sous-répertoire **tools**.
3. Accédez au sous-répertoire tools sous le dossier Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x en tapant cd tools, par exemple :
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Exécutez le script .\InstallElasticDatabaseJobsCmdlets.ps1 pour copier le répertoire ElasticDatabaseJobs dans $home\Documents\WindowsPowerShell\Modules. Ceci importera automatiquement le module à utiliser, par exemple :
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Installez les composants de Tâches de bases de données élastiques à l'aide de PowerShell
1. Lancez une fenêtre de commande Microsoft Azure PowerShell et accédez au sous-répertoire \tools sous le dossier Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x : tapez cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Exécutez le script PowerShell .\InstallElasticDatabaseJobs.ps1 et fournissez des valeurs pour ses variables requises. Ce script crée les composants décrits dans [Composants et tarification de Tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md#components-and-pricing) , ainsi que la configuration du service cloud Azure, pour utiliser correctement les composants dépendants.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Lorsque vous exécutez cette commande, une fenêtre s’ouvre dans laquelle vous devez entrer un **nom d’utilisateur** et un **mot de passe**. Il ne s'agit pas de vos informations d'identification Azure. Entrez le nom d'utilisateur et le mot de passe qui seront les informations d'identification d'administrateur que vous souhaitez créer pour le nouveau serveur.

Les paramètres fournis dans cet exemple d'appel peuvent être remplacés par les paramètres souhaités. La liste suivante vous fournit plus d'informations sur le comportement de chaque paramètre :

<table style="width:100%">
  <tr>
    <th>Paramètre</th>
    <th>Description</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Fournit le nom du groupe de ressources Azure créé pour contenir les composants Azure nouvellement créés. Ce paramètre est défini par défaut sur la valeur « __ElasticDatabaseJob ». Il n'est pas recommandé de modifier cette valeur.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Fournit l'emplacement Azure à utiliser pour les composants Azure nouvellement créés. Ce paramètre est défini par défaut sur  l'emplacement du centre des États-Unis.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fournit le nombre de travaux de service à installer. Ce paramètre est défini par défaut sur la valeur 1. Un nombre plus élevé de travaux peut permettre de faire évoluer le service et de fournir une haute disponibilité. Il est recommandé d'utiliser la valeur « 2 » pour les déploiements qui nécessitent une haute disponibilité du service.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Fournit la taille de machine virtuelle pour une utilisation dans le service cloud. Ce paramètre est défini par défaut sur la valeur A0. Les valeurs de paramètres A0/A1/A2/A3 sont acceptées, ce qui amène le rôle de travail à utiliser, respectivement, une très petite/petite/moyenne/grande taille. Pour plus d’informations sur les tailles de rôle de travail, consultez [Composants et tarification des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Fournit l'objectif de niveau de service d’une édition standard. Ce paramètre est défini par défaut sur la valeur S0. Les valeurs de paramètre S0/S1/S2/S3/S4/S6/S9/S12 sont acceptées, ce qui amène la base de données Microsoft Azure SQL à utiliser l’objectif de niveau de service (SLO) correspondant. Pour plus d’informations sur les objectifs de niveau de service SQL Database, consultez [Composants et tarification des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Fournit le nom d'utilisateur administrateur du serveur de base de données SQL Azure nouvellement créé. S’il n’est pas spécifié, une fenêtre d'informations d'identification PowerShell apparaît et vous demande de fournir les informations d'identification.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Fournit le mot de passe administrateur du serveur de base de données SQL Azure nouvellement créé. S’il n’est pas fourni, une fenêtre d'informations d'identification PowerShell apparaît et vous demande de fournir les informations d'identification.</td>
</tr>
</table>

Pour les systèmes qui ciblent un très grand nombre de tâches s’exécutant en parallèle sur de nombreuses bases de données, il est vivement recommandé de spécifier des paramètres tels que : - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Mettez à jour une installation de composants de Tâches de bases de données élastiques existante à l'aide de PowerShell
**Tâches de bases de données élastiques** peut être mis à jour dans une installation existante pour assurer la mise à l'échelle et la haute disponibilité. Ce processus permet d’effectuer ultérieurement des mises à niveau du code de service sans avoir à supprimer et recréer la base de données de contrôle. Il peut également être utilisé sur la même version pour modifier la taille de la machine virtuelle du service ou le nombre de travaux de serveur.

Pour mettre à jour la taille de la machine virtuelle d’une installation, exécutez le script suivant via les paramètres mis à jour avec les valeurs de votre choix.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Paramètre</th>
  <th>Description</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Identifie le nom du groupe de ressources Azure utilisé lorsque les composants de Tâches de bases de données élastiques ont été initialement installés. Ce paramètre est défini par défaut sur la valeur « __ElasticDatabaseJob ». Puisqu’il n'est pas recommandé de modifier cette valeur, vous ne devriez pas spécifier ce paramètre.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Fournit le nombre de travaux de service à installer.  Ce paramètre est défini par défaut sur la valeur 1.  Un nombre plus élevé de travaux peut permettre de faire évoluer le service et de fournir une haute disponibilité.  Il est recommandé d'utiliser la valeur « 2 » pour les déploiements qui nécessitent une haute disponibilité du service.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Fournit la taille de machine virtuelle pour une utilisation dans le service cloud. Ce paramètre est défini par défaut sur la valeur A0. Les valeurs de paramètres A0/A1/A2/A3 sont acceptées, ce qui amène le rôle de travail à utiliser, respectivement, une très petite/petite/moyenne/grande taille. Pour plus d’informations sur les tailles de rôle de travail, consultez [Composants et tarification des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Installez les composants de Tâches de bases de données élastiques à l'aide du portail
Une fois que vous avez [créé un pool élastique](sql-database-elastic-pool-manage-portal.md), vous pouvez installer les composants de **Tâches de bases de données élastiques** pour activer l'exécution des tâches d'administration dans chaque base de données du pool élastique. Contrairement aux API PowerShell de **Tâches de bases de données élastiques** , l'interface du portail ne peut être exécuté que sur un pool existant.

**Durée estimée :** 10 minutes.

1. Dans la vue du tableau de bord du pool élastique via le [portail Azure](https://portal.azure.com/#), cliquez sur **Créer une tâche**.
2. Si vous créez une tâche pour la première fois, vous devez installer **Tâche de base de données élastique** en cliquant sur **CONDITIONS D’UTILISATION DE LA VERSION PREVIEW**.
3. Acceptez les termes en cliquant sur la case à cocher.
4. Dans la vue « Installer les services », cliquez sur **JOB CREDENTIALS**.
   
    ![Installation des serveurs][1]
5. Tapez un nom d'utilisateur et un mot de passe d'administrateur de base de données. Dans le cadre de l'installation, un nouveau serveur de base de données SQL Azure est créé. Sur ce nouveau serveur, une nouvelle base de données, appelée base de données de contrôle, est créée et utilisée pour contenir les métadonnées des tâches de bases de données élastiques. Le nom d'utilisateur et le mot de passe créés ici sont utilisés pour se connecter à la base de données de contrôle. Une information d'identification distincte est utilisée pour l'exécution du script sur les bases de données du pool.
   
    ![Créer le nom d'utilisateur et le mot de passe][2]
6. Cliquez sur le bouton OK. Les composants sont créés pour vous en quelques minutes dans un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md). Le nouveau groupe de ressources est épinglé au panneau de démarrage, comme illustré ci-dessous. Les tâches de bases de données élastiques (Service Cloud, Base de données SQL, Service Bus et Storage) sont toutes créées dans le groupe.
   
    ![groupe de ressources dans le panneau de démarrage][3]
7. Si vous tentez de créer ou de gérer une tâche pendant l’installation de tâches de bases de données élastiques, le message suivant apparaît lorsque vous fournissez les **informations d'identification** .
   
    ![Déploiement en cours][4]

Si la désinstallation est nécessaire, supprimez le groupe de ressources. Consultez [Désinstaller les composants de Tâches de bases de données élastiques](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Étapes suivantes
Vérifiez que les informations d’identification disposant des droits appropriés pour l’exécution du script sont créées sur chaque base de données du groupe. Pour plus d’informations, consultez [Sécurisation de votre base de données SQL](sql-database-manage-logins.md).
Pour plus d’informations, consultez [Création et gestion de Tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md) pour commencer.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
