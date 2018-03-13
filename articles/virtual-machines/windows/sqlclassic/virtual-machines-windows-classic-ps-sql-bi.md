---
title: "Business Intelligence de SQL Server | Microsoft Docs"
description: "Cette rubrique utilise des ressources créées avec le modèle de déploiement Classic et décrit les fonctionnalités de Business Intelligence (BI) disponibles pour SQL Server exécutées dans les machines virtuelles Azure."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: asaxton
ms.openlocfilehash: a010e60df2d86d2b1cc923b427aa7d7452f58089
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Business Intelligence de SQL Server dans les machines virtuelles Azure
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

La galerie de machines virtuelles Microsoft Azure inclut des images qui contiennent des installations de SQL Server. Les éditions de SQL Server prises en charge dans les images de la galerie sont les mêmes fichiers d’installation que vous pouvez installer sur les ordinateurs locaux et sur les machines virtuelles. Cette rubrique résume les fonctionnalités de SQL Server Business Intelligence (BI) installées sur les images, et les étapes de configuration nécessaires après l’approvisionnement d’une machine virtuelle. Elle décrit également les topologies de déploiement prises en charge pour les fonctionnalités et les bonnes pratiques en matière de Business Intelligence (BI).

## <a name="license-considerations"></a>Remarques sur la licence
Il existe deux manières de mettre sous licence SQL Server dans Microsoft Azure Virtual Machines :

1. Via les avantages License Mobility inclus dans la Software Assurance. Pour plus d’informations, consultez [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Payez un tarif horaire pour Azure Virtual Machines avec SQL Server installé. Consultez la section « SQL Server » dans [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)

Pour plus d’informations sur les licences et les tarifs actuellement pratiqués, consultez [FAQ concernant les licences Virtual Machines](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Images de SQL Server disponibles dans la galerie de machines virtuelles Azure
La galerie de machines virtuelles Microsoft Azure inclut plusieurs images qui contiennent Microsoft SQL Server. Le logiciel installé sur les images de machine virtuelle varie selon la version du système d’exploitation et la version de SQL Server. La liste des images disponibles dans la galerie de machines virtuelles Azure change souvent.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Image SQL dans la galerie de machines virtuelles Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Le script PowerShell suivant renvoie la liste des images Azure dont le nom ImageName contient « SQL Server » :

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Pour plus d’informations sur les éditions et les fonctionnalités prises en charge par SQL Server, consultez les rubriques suivantes :

* [Éditions de SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Fonctionnalités prises en charge par les éditions de SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Fonctionnalités d’aide à la décision installées sur les images de la galerie de machines virtuelles SQL Server
Le tableau suivant récapitule les fonctionnalités Business Intelligence installées sur les images de la galerie de machines virtuelles Microsoft Azure pour SQL Server :

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Fonctionnalité BI de SQL Server | Installé sur l’image de la galerie | Notes |
| --- | --- | --- |
| **Mode natif de Reporting Services** |OUI |Installé, mais nécessite une configuration, notamment de l’URL du Gestionnaire de rapports. Consultez la section [Configurer Reporting Services](#configure-reporting-services). |
| **Mode SharePoint de Reporting Services** |Non  |L’image de la galerie de machines virtuelles Microsoft Azure ne comprend pas les fichiers SharePoint ou d’installation SharePoint. <sup>1</sup> |
| **Mode multidimensionnel et d’exploration de données Analysis Services (OLAP)** |OUI |Installé et configuré comme instance par défaut de Analysis Services |
| **Mode tabulaire Analysis Services** |Non  |Pris en charge dans les images SQL Server 2012, 2014 et 2016, mais pas installé par défaut. Installez une autre instance d’Analysis Services. Consultez la section Installation d’autres services et fonctionnalités SQL Server dans cette rubrique. |
| **Analysis Services Power Pivot pour SharePoint** |Non  |L’image de la galerie de machines virtuelles Microsoft Azure ne comprend pas les fichiers SharePoint ou d’installation SharePoint. <sup>1</sup> |

<sup>1</sup> Pour plus d’informations sur SharePoint et les machines virtuelles Azure, consultez [Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) et [Déploiement de SharePoint dans Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Exécutez la commande PowerShell suivante pour obtenir la liste des services installés dont le nom contient « SQL ».

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Instructions générales et meilleures pratiques
* La taille minimale recommandée d’une machine virtuelle est la taille **A3** quand vous utilisez SQL Server Enterprise Edition. La taille de machine virtuelle **A4** est recommandée pour les déploiements SQL Server BI d’Analysis Services et de Reporting Services.
  
    Pour plus d’informations sur les tailles des machines virtuelles actuelles, consultez [Tailles de machines virtuelles pour Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* La meilleure pratique de gestion du disque consiste à stocker les fichiers de données, de journal et de sauvegarde sur des lecteurs autres que **C**: et **D**:. Par exemple, créez des disques de données **E**: et **F**:.
  
  * La stratégie de mise en cache du lecteur **C**: par défaut n’est pas optimale pour l’utilisation de données.
  * Nous vous déconseillons d’utiliser le lecteur **D**: qui est un disque temporaire principalement utilisé pour le fichier d’échange. Nous vous déconseillons d’utiliser le lecteur **D**: qui n’est pas persistant et n’est pas stocké dans le stockage d’objets blob. Les tâches de gestion telles qu’une simple modification de la taille d’une machine virtuelle réinitialisent le lecteur **D**:. Nous vous **DÉCONSEILLONS** d’utiliser le lecteur **D**: pour les fichiers de base de données, y compris tempdb.
    
    Pour plus d’informations sur la création et l’attachement de disques, consultez la page [Procédure d’attachement d’un disque de données à une machine virtuelle](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Arrêtez ou désinstallez les services que vous ne prévoyez pas d’utiliser. Par exemple, si la machine virtuelle est uniquement utilisée pour Reporting Services, arrêtez ou désinstallez Analysis Services et SQL Server Integration Services. L’image suivante est un exemple de services qui sont démarrés par défaut.
  
    ![Services SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Le moteur de base de données SQL Server est requis dans les scénarios BI pris en charge. Dans une topologie de machine virtuelle avec un seul serveur, le moteur de base de données doit être exécuté sur la même machine virtuelle.
  
    Pour plus d’informations, consultez les rubriques suivantes : [Désinstaller Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) et [Procédure : désinstaller une instance d’Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Vérifiez les nouvelles mises à jour importantes sur **Windows Update** . Les images de machines virtuelles Microsoft Azure sont souvent actualisées ; toutefois, des mises à jour importantes peuvent être disponibles sur **Windows Update** après la dernière actualisation de l’image de machine virtuelle.

## <a name="example-deployment-topologies"></a>Exemples de topologies de déploiement
Voici des exemples de déploiements qui utilisent Microsoft Azure Virtual Machines. Les topologies décrites dans ces schémas ne représentent qu’une partie des topologies possibles avec les fonctionnalités BI de SQL Server et avec Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Une seule machine virtuelle
Analysis Services, Reporting Services, le moteur de base de données SQL Server et les sources de données sont sur une seule machine virtuelle.

![scénario BI iass avec 1 machine virtuelle](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Deux machines virtuelles
* Analysis Services, Reporting Services et le moteur de base de données SQL Server sont sur une seule machine virtuelle. Ce déploiement inclut les bases de données du serveur de rapports.
* Les sources de données sont sur une deuxième machine virtuelle. La deuxième machine virtuelle inclut le moteur de base de données SQL Server comme source de données.

![scénario BI iaas avec 2 machines virtuelles](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Topologie mixte Azure - données sur Base de données SQL Azure
* Analysis Services, Reporting Services et le moteur de base de données SQL Server sont sur une seule machine virtuelle. Ce déploiement inclut les bases de données du serveur de rapports.
* La source de données est la Base de données SQL Azure.

![machine virtuelle de scénarios BI iaas et AzureSQL comme source de données](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Topologie hybride – données locales
* Dans cet exemple de déploiement, Analysis Services, Reporting Services et le moteur de base de données SQL Server s’exécutent sur une seule machine virtuelle. La machine virtuelle héberge les bases de données du serveur de rapports. La machine virtuelle est jointe à un domaine local via le réseau virtuel Azure ou une autre solution de tunneling VPN.
* La source de données est locale.

![machine virtuelle de scénarios BI iaas et sources de données locales](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuration du mode natif de Reporting Services
L’image de la galerie de machines virtuelles pour SQL Server inclut l’installation du mode natif de Reporting Services, mais le serveur de rapports n’est pas configuré. Les étapes de cette section permettent de configurer le serveur de rapports Reporting Services. Pour plus d’informations sur la configuration du mode natif de Reporting Services, consultez la page [Installer le serveur de rapports Reporting Services en mode natif](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Pour obtenir un contenu semblable qui utilise des scripts Windows PowerShell pour configurer le serveur de rapports, consultez la page [Utilisation de PowerShell pour créer une machine virtuelle Azure avec un serveur de rapports en mode natif](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Se connecter à la machine virtuelle et démarrer le Gestionnaire de configuration Reporting Services
Il existe deux flux de travail courants pour la connexion à une machine virtuelle Azure :

* Pour vous connecter, cliquez sur le nom de la machine virtuelle, puis cliquez sur **Connecter**. Une connexion Bureau à distance s’ouvre et le nom de l’ordinateur est automatiquement renseigné.
  
    ![se connecter à une machine virtuelle azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Connectez-vous à la machine virtuelle avec une connexion Bureau à distance Windows. Dans l’interface utilisateur du Bureau à distance :
  
  1. Tapez le **nom du service cloud** comme nom d’ordinateur.
  2. Appuyez sur la touche deux-points (:), puis entrez le numéro de port public qui est configuré pour le point de terminaison Bureau à distance TCP.
     
      Myservice.cloudapp.net:63133
     
      Pour plus d’informations, consultez [Présentation d’un service cloud](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Démarrer le Gestionnaire de configuration de Reporting Services**

Dans **Windows Server 2012/2016** :

1. Dans l’écran de **démarrage**, tapez **Reporting Services** pour afficher une liste d’applications.
2. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.

Dans **Windows Server 2008 R2**:

1. Cliquez sur **Démarrer**, puis sur **Tous les programmes**.
2. Cliquez sur **Microsoft SQL Server 2016**.
3. Cliquez sur **Outils de configuration**.
4. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.

Ou :

1. Cliquez sur **Start**.
2. Dans la boîte de dialogue **Rechercher les programmes et fichiers**, tapez **reporting services**. Si la machine virtuelle exécute Windows Server 2012, tapez **reporting services** à l’écran de démarrage de Windows Server 2012.
3. Cliquez avec le bouton droit sur **Gestionnaire de configuration de Reporting Services**, puis cliquez sur **Exécuter en tant qu’administrateur**.
   
    ![recherche du Gestionnaire de configuration de ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurer Reporting Services
**Compte de service et URL du service Web :**

1. Vérifiez que **Nom du serveur** est le nom du serveur local et cliquez sur **Connecter**.
2. Notez que **Nom de la base de données du serveur de rapports**est vide. La base de données est créée une fois la configuration terminée.
3. Vérifiez que l’**État de Report Server** est **Démarré**. Pour vérifier le service dans le Gestionnaire de Windows Server, consultez le Service Windows **SQL Server Reporting Services** .
4. Cliquez sur **Compte de service** et modifiez le compte si nécessaire. Si la machine virtuelle est utilisée dans un environnement non joint à un domaine, le compte **ReportServer** intégré est suffisant. Pour plus d’informations sur le compte de service, consultez la page [Compte de service](https://msdn.microsoft.com/library/ms189964.aspx).
5. Cliquez sur l’ **URL du service Web** dans le volet gauche.
6. Cliquez sur **Appliquer** pour configurer les valeurs par défaut.
7. Notez les **URL du service Web Report Server**. Le port TCP par défaut est 80 et fait partie de l’URL. Dans une étape ultérieure, vous créerez un point de terminaison de machine virtuelle Microsoft Azure pour le port.
8. Dans le volet **Résultats** , vérifiez que les actions ont été effectuées.

**Base de données :**

1. Dans le volet gauche, cliquez sur **Base de données** .
2. Cliquez sur **Changer la base de données**.
3. Vérifiez que **Créer une nouvelle base de données de serveur de rapports** est sélectionné, puis cliquez sur Suivant.
4. Vérifiez le **Nom du serveur** et cliquez sur **Tester la connexion**.
5. Si le résultat est **Le test de la connexion a réussi**, cliquez sur **OK**, puis sur **Suivant**.
6. Notez que le nom de la base de données est **ReportServer** et que le **Mode du serveur de rapports** est **Natif**, puis cliquez sur **Suivant**.
7. Cliquez sur **Suivant** on the **Suivant** .
8. Cliquez sur **Suivant** on the **Suivant** .
9. Cliquez sur **Suivant** on the **Suivant** .

**URL du portail web ou URL du Gestionnaire de rapports pour 2012 et 2014 :**

1. Cliquez sur **URL du portail web** ou sur **URL du Gestionnaire de rapports** pour 2014 et 2012, dans le volet gauche.
2. Cliquez sur **Appliquer**.
3. Dans le volet **Résultats** , vérifiez que les actions ont été effectuées.
4. Cliquez sur **Quitter**.

Pour plus d’informations sur les autorisations du serveur de rapports, consultez [Octroi d’autorisations sur un serveur de rapports en mode natif](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Accéder au Gestionnaire de rapports local
Pour vérifier la configuration, accédez au Gestionnaire de rapports sur la machine virtuelle.

1. Sur la machine virtuelle, démarrez Internet Explorer avec des privilèges d’administrateur.
2. Accédez à http://localhost/reports sur la machine virtuelle.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Se connecter au portail web distant ou au Gestionnaire de rapports pour 2014 et 2012
Si vous souhaitez vous connecter au portail web ou au Gestionnaire de rapports pour 2014 et 2012, sur la machine virtuelle d’un ordinateur distant, créez un point de terminaison TCP de machine virtuelle. Par défaut, le serveur de rapports écoute les requêtes HTTP sur le **port 80**. Si vous configurez les URL du serveur de rapports pour utiliser un autre port, vous devez spécifier ce numéro de port dans les instructions ci-après.

1. Créez un point de terminaison pour la machine virtuelle avec le port TCP 80. Pour plus d’informations, consultez la section [Points de terminaison de la machine virtuelle et ports de pare-feu](#virtual-machine-endpoints-and-firewall-ports) dans ce document.
2. Ouvrez le port 80 dans le pare-feu de la machine virtuelle.
3. Accédez au portail web ou au Gestionnaire de rapports en utilisant le **nom DNS** de la machine virtuelle Azure comme nom de serveur dans l’URL. Par exemple : 
   
    **Serveur de rapports** : http://uebi.cloudapp.net/reportserver  **Portail web** : http://uebi.cloudapp.net/reports
   
    [Configurer un pare-feu pour accéder au serveur de rapports](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Pour créer et publier des rapports sur la machine virtuelle Azure
Le tableau suivant résume certaines des options disponibles pour publier des rapports existants à partir d’un ordinateur local vers le serveur de rapports hébergé sur la machine virtuelle Microsoft Azure :

* **Générateur de rapports**: la machine virtuelle inclut la version en un clic du Générateur de rapports Microsoft SQL Server 2014 et 2012. Pour démarrer le Générateur de rapports la première fois sur la machine virtuelle avec SQL 2016 :
  
  1. Démarrez votre navigateur avec des privilèges administratifs.
  2. Accédez au portail web sur la machine virtuelle, puis sélectionnez l’icône **Télécharger** dans le coin supérieur droit.
  3. Sélectionnez **Générateur de rapports**.
     
     Pour plus d’informations, consultez [Démarrer le Générateur de rapports](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools** : machines virtuelles : SQL Server Data Tools est installé sur la machine virtuelle et peut être utilisé pour créer des **projets de serveur de rapports** et des rapports sur la machine virtuelle. SQL Server Data Tools peut publier les rapports vers le serveur de rapports sur la machine virtuelle.
* **SQL Server Data Tools : distant** : sur votre ordinateur local, créez un projet Reporting Services contenant des rapports Reporting Services dans SQL Server Data Tools. Configurez le projet pour la connexion à l’URL du service web.
  
    ![propriétés de projet ssdt pour un projet SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Créez un disque dur .VHD qui contient des rapports, puis téléchargez et attachez le lecteur.
  
  1. Créez un disque dur .VHD sur votre ordinateur local qui contient vos rapports.
  2. Créez et installez un certificat de gestion.
  3. Téléchargez le fichier VHD sur Azure à l’aide de l’applet de commande Add-AzureVHD en procédant de la manière décrite dans [Créer et charger un disque dur virtuel Windows Server dans Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Attachez le disque à la machine virtuelle.

## <a name="install-other-sql-server-services-and-features"></a>Installation d’autres services et fonctionnalités SQL Server
Pour installer les services supplémentaires de SQL Server, tels que Analysis Services en mode tabulaire, exécutez l’Assistant Installation de SQL Server. Les fichiers d’installation sont sur le disque local de la machine virtuelle.

1. Cliquez sur **Démarrer**, puis sur **Tous les programmes**.
2. Cliquez sur **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** ou **Microsoft SQL Server 2012**, puis sur **Outils de configuration**.
3. Cliquez sur **Centre d’installation SQL Server**.

Vous pouvez également exécuter C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> La première fois que vous exécutez l’installation de SQL Server, davantage de fichiers d’installation peuvent être téléchargés et nécessiter un redémarrage de la machine virtuelle et un redémarrage de l’installation de SQL Server.
> 
> Si vous devez personnaliser de façon répétée l’image sélectionnée à partir de la machine virtuelle Microsoft Azure, envisagez de créer votre propre image SQL Server. La fonctionnalité d’Analysis Services SysPrep a été activée avec SQL Server 2012 SP1 CU2. Pour plus d’informations, consultez [Considérations relatives à l’installation de SQL Server à l’aide de SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) et [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Pour installer Analysis Services en mode tabulaire
Les étapes de cette section **résument** l’installation d’Analysis Services en mode tabulaire. Pour plus d’informations, consultez les rubriques suivantes : 

* [Installer Analysis Services en mode tabulaire](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modélisation tabulaire (didacticiel Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Pour installer Analysis Services en mode tabulaire :**

1. Dans l’Assistant Installation de SQL Server, cliquez sur **Installation** dans le volet gauche, puis cliquez sur **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**.
   
   * Si vous voyez **Rechercher un dossier**, accédez à c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full, puis cliquez sur **OK**.
2. Dans la page des mises à jour du produit, cliquez sur **Suivant** .
3. Dans la page **Type d’installation**, sélectionnez **Effectuer une nouvelle installation de SQL Server**, puis cliquez sur **Suivant**.
4. Dans la page **Rôle d’installation**, cliquez sur **Installation de fonctionnalités SQL Server**.
5. Dans la page **Sélection des composants**, cliquez sur **Analysis Services**.
6. Dans la page **Configuration de l’instance**, entrez un nom descriptif, tel que **Tabulaire** dans les zones de texte **Instance nommée** et **ID d’instance**.
7. Dans la page **Configuration d’Analysis Services**, sélectionnez **Mode Tabulaire**. Ajoutez l’utilisateur actuel à la liste des autorisations d’administrateur.
8. Terminez et fermez l’Assistant Installation de SQL Server.

## <a name="analysis-services-configuration"></a>Configuration d’Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Accès distant au serveur Analysis Services
Le serveur Analysis Services prend en charge uniquement l’authentification Windows. Pour accéder à Analysis Services à distance des applications clientes telles que SQL Server Management Studio ou SQL Server Data Tools, la machine virtuelle doit être jointe à votre domaine local à l’aide d’Azure Virtual Network. Pour plus d’informations, consultez [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

Une **instance par défaut** d’Analysis Services écoute sur le port TCP **2383**. Ouvrez le port dans le pare-feu des machines virtuelles. Une instance en cluster nommée d’Analysis Services écoute également sur le port **2383**.

Pour une **instance nommée** d’Analysis Services, le service SQL Server Browser est nécessaire pour gérer l’accès au port. La configuration par défaut de SQL Server Browser est le port **2382**.

Dans le pare-feu des machines virtuelles, ouvrez le port **2382** et créez un port d’instance nommée Analysis Services statique.

1. Pour vérifier les ports qui sont déjà utilisés sur la machine virtuelle et connaître le processus qui les utilise, exécutez la commande suivante avec des privilèges d’administration :
   
        netstat /ao
2. Utilisez SQL Server Management Studio pour créer un port d’instance nommée Analysis Services statique en mettant à jour la valeur « Port » dans les propriétés générales de l’instance AS tabulaire. Pour plus d’informations, consultez la section « Utiliser un port fixe pour une instance par défaut ou nommée d’Analysis Services » dans [Configurer le pare-feu Windows pour autoriser l’accès à Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Redémarrez l’instance tabulaire du service Analysis Services.

Pour plus d’informations, consultez la section **Points de terminaison de la machine virtuelle et ports de pare-feu** dans ce document.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Points de terminaison de la machine virtuelle et ports de pare-feu
Cette section résume les points de terminaison de machine virtuelle Microsoft Azure à créer et les ports à ouvrir dans les pare-feu de machine virtuelle. Le tableau suivant récapitule les ports **TCP** pour lesquels créer des points de terminaison et les ports à ouvrir dans le pare-feu des machines virtuelles.

* Si vous utilisez une seule machine virtuelle et si les deux affirmations suivantes sont vraies, vous n’avez pas besoin de créer des points de terminaison de machine virtuelle, ni d’ouvrir les ports dans le pare-feu sur la machine virtuelle.
  
  * Vous ne vous connectez pas à distance aux fonctionnalités SQL Server sur la machine virtuelle. L’établissement d’une connexion Bureau à distance vers la machine virtuelle et l’accès aux fonctionnalités de SQL Server localement sur la machine virtuelle ne sont pas considérés comme une connexion distante aux fonctionnalités de SQL Server.
  * Vous ne joignez pas la machine virtuelle à un domaine local via le réseau virtuel Azure ou via une autre solution de tunneling VPN.
* Si la machine virtuelle n’est pas jointe à un domaine mais que vous souhaitez vous connecter à distance aux fonctionnalités SQL Server sur la machine virtuelle : 
  
  * Ouvrez les ports dans le pare-feu sur la machine virtuelle.
  * Créez des points de terminaison de machine virtuelle pour les ports notés (*).
* Si la machine virtuelle est jointe à un domaine avec un tunnel VPN tel que le réseau virtuel Azure, les points de terminaison ne sont pas requis. Ouvrez toutefois les ports dans le pare-feu sur la machine virtuelle.
  
  | Port | type | Description |
  | --- | --- | --- |
  | **80** |TCP |Accès distant au serveur de rapports (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Nécessaire lorsque la machine virtuelle est jointe à un domaine. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Instance par défaut et instance en cluster nommée de SQL Server Analysis Services. |
  | **Définie par l’utilisateur** |TCP |Créez une instance nommée Analysis Services statique pour un numéro de port que vous choisissez, puis débloquez le numéro de port dans le pare-feu. |

Pour plus d’informations sur la création de points de terminaison, consultez les rubriques suivantes :

* Créer des points de terminaison :[Comment configurer des points de terminaison sur une machine virtuelle](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server : Consultez la section « Procédure de configuration complète pour la connexion à la machine virtuelle en utilisant SSMS (SQL Server Management Studio) sur un autre ordinateur » dans [Approvisionnement d’une machine virtuelle SQL Server sur Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Le schéma suivant montre les ports à ouvrir dans le pare-feu de la machine virtuelle pour autoriser l’accès à distance aux fonctionnalités et aux composants de la machine virtuelle.

![ports à ouvrir pour des applications BI dans des machines virtuelles Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Ressources
* Consultez la stratégie de prise en charge des logiciels serveurs Microsoft utilisée dans l’environnement de la machine virtuelle Azure. La rubrique suivante résume la prise en charge de fonctionnalités telles que BitLocker, le clustering de basculement et l’équilibrage de la charge réseau. [Prise en charge des logiciels serveur Microsoft pour Azure Virtual Machines](http://support.microsoft.com/kb/2721672).
* [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Configuration d'une machine virtuelle SQL Server sur Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Association d’un disque de données à une machine virtuelle](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrating a Database to SQL Server on an Azure VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Déterminer le mode serveur d’une instance Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modélisation multidimensionnelle (didacticiel Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centre de documentation](https://azure.microsoft.com/documentation/)
* [Utilisation de Power BI dans un environnement hybride](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Soumettre des commentaires et des informations de contact via Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Contenu de la communauté
* [Gestion de base de données SQL Azure avec PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

