---
title: Bien démarrer avec Azure Log Integration | Microsoft Docs
description: Découvrez comment installer le service Azure Log Integration et intégrer les journaux du Stockage Azure, les journaux d’audit Azure et les alertes Azure Security Center.
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 3e229c4db44fc3c8d16aa2bd0a014fb1acc64a5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration avec la journalisation Azure Diagnostics et le transfert des événements Windows

Azure Log Integration fournit aux clients une alternative quand un connecteur [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) n’est pas disponible auprès de leur fournisseur SIEM (Security Incident and Event Management). Azure Log Integration met les journaux Azure à la disposition de votre SIEM pour que vous puissiez créer un tableau de bord de sécurité unifié pour toutes vos ressources.

> [!NOTE]
> Pour plus d’informations sur Azure Monitor, consultez [Bien démarrer avec Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md). Pour plus d’informations sur l’état d’un connecteur Azure Monitor, contactez votre fournisseur SIEM.

> [!IMPORTANT]
> Si vous êtes principalement intéressé par la collecte des journaux de machine virtuelle, la plupart des fournisseurs SIEM intègrent cette option à leur solution. L’utilisation du connecteur du fournisseur SIEM doit toujours être l’alternative privilégiée.

Cet article vous aide à bien démarrer avec Azure Log Integration. Il traite principalement de l’installation du service Azure Log Integration et de son intégration à Azure Diagnostics. Le service Azure Log Integration peut alors collecter les informations du journal des événements Windows provenant du canal des événements de sécurité Windows à partir des machines virtuelles déployées dans l’infrastructure IaaS Azure. C’est l’équivalent du *transfert d’événements* que vous utilisez en local.

> [!NOTE]
> L’intégration de la sortie d’Azure Log Integration à un SIEM est effectuée par le SIEM lui-même. Pour plus d’informations, consultez [Intégrer Azure Log Integration à votre SIEM local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Le service Azure Log Integration s’exécute sur un ordinateur physique ou virtuel exécutant Windows Server 2008 R2 ou version ultérieure (de préférence Windows Server 2016 ou Windows Server 2012 R2).

Un ordinateur physique peut s’exécuter localement ou sur un site d’hébergement. Si vous choisissez d’exécuter le service Azure Log Integration sur une machine virtuelle, cette dernière peut être située localement ou dans un cloud public, comme Microsoft Azure.

La machine physique ou virtuelle qui exécute le service d’intégration des journaux Azure nécessite une connexion réseau avec le cloud public Azure. Cet article fournit des détails sur la configuration requise.

## <a name="prerequisites"></a>Prérequis


Au minimum, l’installation d’Azure Log Integration nécessite les éléments suivants :

* Un **abonnement Azure**. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/).
* Un **compte de stockage** utilisable pour la journalisation Azure Diagnostics. Vous pouvez utiliser un compte de stockage préconfiguré ou en créer un. Plus loin dans cet article, nous décrivons la configuration du compte de stockage.

  > [!NOTE]
  > Selon votre scénario, un compte de stockage n’est pas forcément nécessaire. Pour le scénario Azure Diagnostics abordé dans cet article, un compte de stockage est nécessaire.

* **Deux systèmes** : 
  * Une machine qui exécute le service Azure Log Integration. Cette machine collecte toutes les informations de journal importées par la suite dans votre SIEM. Ce système :
    * Peut être local ou hébergé dans Microsoft Azure.  
    * Doit exécuter une version x64 de Windows Server 2008 R2 SP1 ou version ultérieure, et doit avoir Microsoft .NET 4.5.1 installé. Pour déterminer la version du .NET installée, consultez [Déterminer les versions du .NET Framework installées](https://msdn.microsoft.com/library/hh925568).  
    * Doit être connecté à un compte de stockage Azure utilisé pour la journalisation Azure Diagnostics. Plus loin dans cet article, nous décrivons la confirmation de la connectivité.
  * Une machine à surveiller. Il s’agit d’une machine virtuelle s’exécutant comme une [machine virtuelle Azure](../virtual-machines/virtual-machines-windows-overview.md). Les informations de journalisation de cette machine sont envoyées à la machine du service Azure Log Integration.

Pour une démonstration rapide de la création d’une machine virtuelle à l’aide du portail Azure, regardez la vidéo ci-dessous :<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Pendant le test, vous pouvez utiliser n’importe quel système qui respecte la configuration minimale requise du système d’exploitation. Pour un environnement de production, la charge peut vous obliger à planifier une montée en puissance ou une augmentation de la taille des instances.

Vous pouvez exécuter plusieurs instances du service Azure Log Integration. Toutefois, vous pouvez exécuter une seule instance du service par machine physique ou virtuelle. Par ailleurs, vous pouvez équilibrer la charge des comptes de stockage Azure Diagnostics pour les diagnostics Microsoft Azure. Le nombre d’abonnements à fournir aux instances dépend de votre capacité.

> [!NOTE]
> Actuellement, nous n’avons pas de recommandation spécifique sur l’augmentation de la taille des instances des machines Azure Log Integration (autrement dit, les machines qui exécutent le service Azure Log Integration), ou pour les abonnements ou les comptes de stockage. Les décisions de mise à l’échelle doivent être basées sur les observations de vos performances dans chacun de ces domaines.

Pour améliorer les performances, vous pouvez aussi monter en puissance le service Azure Log Integration. Les métriques de performance suivantes peuvent vous aider à dimensionner les machines sur lesquelles vous exécutez le service Azure Log Integration :

* Sur une machine à 8 processeurs (cœurs), une seule instance d’Azure Log Integration peut traiter environ 24 millions d’événements par jour (environ 1 million d’événements par heure).
* Sur une machine à 4 processeurs (cœurs), une seule instance d’Azure Log Integration peut traiter environ 1,5 million d’événements par jour (environ 62 500 événements par heure).

## <a name="install-azure-log-integration"></a>Installer Azure Log Integration

Pour installer Azure Log Integration, téléchargez le fichier d’installation d’[Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324). Exécutez le processus d’installation. Choisissez s’il faut fournir des informations de télémétrie à Microsoft.

Le service Azure Log Integration collecte les données de télémétrie à partir de la machine sur laquelle il est installé.  

Les données de télémétrie collectées comprennent les éléments suivants :

* Les exceptions qui se produisent pendant l’exécution d’Azure Log Integration.
* Les métriques sur le nombre de requêtes et d’événements traités.
* Les statistiques sur les options de ligne de commande Azlog.exe utilisées. 

> [!NOTE]
> Nous vous conseillons d’autoriser Microsoft à collecter les données de télémétrie. Vous pouvez désactiver la collecte de données de télémétrie en décochant la case **Autoriser Microsoft à collecter les données de télémétrie**.
>

![Capture d’écran du volet d’installation, avec la case des données de télémétrie cochée](./media/security-azure-log-integration-get-started/telemetry.png)


Le processus d’installation est traité dans la vidéo suivante :<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Étapes post-installation et de validation

Une fois l’installation de base effectuée, vous êtes prêt à passer aux étapes post-installation et de validation :

1. Ouvrez PowerShell ISE en tant qu’administrateur. Ensuite, accédez à C:\Program Files\Microsoft Azure Log Integration.
2. Importez les applets de commande Azure Log Integration. Pour importer les applets de commande, exécutez le script `LoadAzlogModule.ps1`. Entrez `.\LoadAzlogModule.ps1`, puis appuyez sur Entrée (notez l’utilisation de **.\\** dans cette commande). Vous devez obtenir quelque chose comme ce qui apparaît dans la figure suivante :

  ![Capture d’écran de la sortie de la commande LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Ensuite, configurez Azure Log Integration pour utiliser un environnement Azure spécifique. Un *environnement Azure* est le type de centre de données cloud Azure que vous voulez utiliser. Bien qu’il existe plusieurs environnements Azure, actuellement, les options pertinentes sont **AzureCloud** ou **AzureUSGovernment**. Exécutez PowerShell comme administrateur et vérifiez que vous vous trouvez dans C:\Program Files\Microsoft Azure Log Integration\. Ensuite, exécutez cette commande :

  `Set-AzlogAzureEnvironment -Name AzureCloud` (pour **AzureCloud**)
  
  Si vous voulez utiliser le cloud Azure US Government, utilisez **AzureUSGovernment** pour la variable **-Name**. Actuellement, les autres clouds Azure ne sont pas pris en charge.  

  > [!NOTE]
  > Vous ne recevez pas de confirmation quand la commande aboutit. 

4. Pour pouvoir surveiller un système, vous avez besoin du nom du compte de stockage utilisé pour Azure Diagnostics. Dans le portail Azure, accédez à **Machines virtuelles**. Recherchez la machine virtuelle que vous surveillez. Dans la section **Propriétés**, sélectionnez **Paramètres de diagnostic**.  Ensuite, sélectionnez **Agent**. Notez le nom du compte de stockage spécifié. Vous avez besoin de ce nom de compte pour une étape ultérieure.

  ![Capture d’écran du volet Paramètres des diagnostics Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Capture d’écran du bouton Activer le monitoring d’invités](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Si le monitoring n’a pas été activé à la création de la machine virtuelle, vous pouvez l’activer comme indiqué dans l’image précédente.

5. Ensuite, revenez à la machine Azure Log Integration. Vérifiez que vous êtes connecté au compte de stockage à partir du système sur lequel Azure Log Integration est installé. L’ordinateur qui exécute le service Azure Log Integration doit accéder au compte de stockage pour récupérer les informations journalisées par Azure Diagnostics sur chacun des systèmes surveillés. Pour vérifier la connectivité : 
  1. [Téléchargez l’Explorateur Stockage Azure](http://storageexplorer.com/).
  2. Exécutez l’installation.
  3. Une fois l’installation terminée, sélectionnez **Suivant**. Laissez cochée la case **Lancer l’Explorateur Stockage Microsoft Azure**.  
  4. Connectez-vous à Azure.
  5. Vérifiez que vous pouvez voir le compte de stockage que vous avez configuré pour Azure Diagnostics : 

    ![Capture d’écran des comptes de stockage dans l’Explorateur Stockage](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Certaines options s’affichent sous les comptes de stockage. Sous **Tables**, vous devez voir une table nommée **WADWindowsEventLogsTable**.

  Si le monitoring n’a pas été activé à la création de la machine virtuelle, vous pouvez l’activer, comme indiqué précédemment.


## <a name="integrate-azure-diagnostics-logging"></a>Intégrer la journalisation Azure Diagnostics

Dans cette étape, vous configurez la machine exécutant le service Azure Log Integration pour qu’elle se connecte au compte de stockage qui contient les fichiers journaux.

Pour effectuer cette étape, vous avez besoin de quelques éléments :  
* **FriendlyNameForSource** : Il s’agit d’un nom convivial que vous pouvez appliquer au compte de stockage configuré sur la machine virtuelle pour stocker les informations d’Azure Diagnostics.
* **StorageAccountName** : Il s’agit du nom du compte de stockage que vous avez spécifié quand vous avez configuré Azure Diagnostics.  
* **StorageKey** : Il s’agit de la clé de stockage pour le compte de stockage où les informations Azure Diagnostics sont stockées pour cette machine virtuelle.  

Pour obtenir la clé de stockage, suivez ces étapes :
1. Accédez au [portail Azure](http://portal.azure.com).
2. Dans le volet de navigation, sélectionnez **Tous les services**.
3. Dans la zone **Filtre**, entrez **Stockage**. Ensuite, sélectionnez **Comptes de stockage**.

  ![Capture d’écran qui montre les comptes de stockage dans Tous les services](./media/security-azure-log-integration-get-started/filter.png)

4. Une liste des comptes de stockage s’affiche. Double-cliquez sur le compte que vous avez affecté au stockage de journal.

  ![Capture d’écran qui montre la liste des comptes de stockage](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Sous **Paramètres**, sélectionnez **Clés d’accès**.

  ![Capture d’écran qui montre l’option Clés d’accès dans le menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Copiez la valeur de **key1** et enregistrez-la dans un emplacement sécurisé auquel vous pouvez accéder pour l’étape suivante.
7. Sur le serveur où vous avez installé Azure Log Integration, ouvrez une fenêtre d’invite de commandes comme administrateur. (Ouvrez une fenêtre d’invite de commandes comme administrateur et non PowerShell).
8. Accédez à C:\Program Files\Microsoft Azure Log Integration.
9. Exécutez cette commande : `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Exemple :
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Si vous voulez que l’ID d’abonnement s’affiche dans le fichier XML de l’événement, ajoutez l’ID d’abonnement au nom convivial :

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Exemple : 
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Attendez 60 minutes, puis consultez les événements tirés du compte de stockage. Pour afficher les événements, dans Azure Log Integration, sélectionnez **Observateur d’événements** > **Journaux Windows** > **Événements transférés**.

La vidéo suivante illustre les étapes précédentes :<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Si les données ne s’affichent pas dans le dossier Événements transférés
Si les données ne s’affichent pas dans le dossier Événements transférés au bout d’une heure, suivez ces étapes :

1. Examinez la machine qui exécute le service Azure Log Integration. Vérifiez qu’elle peut accéder à Azure. Pour tester la connectivité, dans un navigateur, essayez d’accéder au [portail Azure](http://portal.azure.com).
2. Vérifiez que le compte d’utilisateur AzLog a un accès en écriture au dossier users\Azlog.
  1. Ouvrez l’Explorateur de fichiers.
  2. Accédez à C:\users.
  3. Cliquez avec le bouton droit sur C:\users\Azlog.
  4. Sélectionnez **Sécurité**.
  5. Sélectionnez **NT Service\Azlog**. Vérifiez les autorisations du compte. Si le compte n’apparaît pas sous cet onglet ou si les autorisations appropriées ne sont pas affichées, vous pouvez accorder les autorisations de compte sous cet onglet.
3. Quand vous exécutez la commande `Azlog source list`, vérifiez que le compte de stockage qui a été ajouté dans la commande `Azlog source add` est répertorié dans la sortie.
4. Pour vérifier si des erreurs sont signalées par le service Azure Log Integration, accédez à **Observateur d’événements** > **Journaux Windows** > **Application**.

Si vous rencontrez des problèmes pendant l’installation et la configuration, vous pouvez créer une [demande de support](../azure-supportability/how-to-create-azure-support-request.md). Pour le service, sélectionnez **Intégration du journal**.

Une autre option de support est le [Forum MSDN Azure Log Integration](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Dans le forum MSDN, la communauté peut fournir du support en répondant aux questions et en partageant des conseils et astuces sur la façon d’utiliser au mieux Azure Log Integration. L’équipe Azure Log Integration surveille également ce forum. Elle apporte son aide chaque fois qu’elle le peut.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Log Integration, consultez les articles suivants :

* [Azure Log Integration pour les journaux Azure](https://www.microsoft.com/download/details.aspx?id=53324). Le Centre de téléchargement donne des informations, la configuration système requise et des instructions d’installation pour Azure Log Integration.
* [Présentation d’Azure Log Integration](security-azure-log-integration-overview.md). Cet article présente Azure Log Integration, ses principales fonctionnalités et son fonctionnement.
* [Étapes de configuration de partenaires](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Ce billet de blog vous montre comment configurer Azure Log Integration pour fonctionner avec les solutions de partenaire Splunk, HP ArcSight et IBM QRadar. Il présente nos recommandations sur la configuration des composants SIEM. Contactez votre fournisseur SIEM pour plus d’informations.
* [Questions fréquentes (FAQ) sur Azure Log Integration](security-azure-log-integration-faq.md). Ce FAQ répond aux questions courantes sur Azure Log Integration.
* [Intégration des alertes Azure Security Center à Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). Cet article vous explique comment synchroniser les alertes Security Center et les événements de sécurité de machine virtuelle qui sont collectés par Azure Diagnostics et les journaux d’activité Azure. Vous synchronisez les journaux à l’aide de votre solution Azure Log Analytics ou SIEM.
* [Nouvelles fonctionnalités d’Azure Diagnostics et des journaux d’audit Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Ce billet de blog présente les journaux d’audit Azure et d’autres fonctionnalités pour vous permettre de mieux connaître les opérations de vos ressources Azure.
