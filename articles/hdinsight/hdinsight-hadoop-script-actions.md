---
title: Développement d’une action de script avec HDInsight - Azure | Documents Microsoft
description: Découvrez comment personnaliser des clusters Hadoop avec une action de script. L’action de script permet d’installer des logiciels supplémentaires s’exécutant sur un cluster Hadoop ou de modifier la configuration des applications installées sur un cluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 836d68a8-8b21-4d69-8b61-281a7fe67f21
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: ac2a087bb0a9d8cac15dfea2448a9c42cee4a1f4
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Développer des scripts d’action de script pour des clusters HDInsight Windows
Découvrez comment écrire des scripts d’action de script pour HDInsight. Pour plus d’informations sur les scripts d’action de script, consultez [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md). Pour accéder au même article écrit pour les clusters HDInsight basés sur Linux, consultez [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md).



> [!IMPORTANT]
> Les étapes décrites dans ce document fonctionnent uniquement pour les clusters HDInsight Windows. HDInsight est uniquement disponible sur Windows pour les versions antérieures à HDInsight 3.4. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Pour plus d’informations sur l’utilisation des actions de script avec les clusters basés sur Linux, consultez [Développement d’action de script avec HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).
>
>



L’action de script permet d’installer des logiciels supplémentaires s’exécutant sur un cluster Hadoop ou de modifier la configuration des applications installées sur un cluster. Les actions de script sont des scripts qui s'exécutent sur des nœuds de cluster lors du déploiement des clusters HDInsight. Elles sont exécutées une fois la configuration de HDInsight terminée dans le cluster. Une action de script est exécutée avec les privilèges d'un compte d'administrateur système qui fournissent des droits d'accès complets aux nœuds du cluster. Chaque cluster peut recevoir une liste d'actions de script qui sont exécutées dans l'ordre spécifié.

> [!NOTE]
> Si vous obtenez le message d’erreur suivant :
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage : Le terme ’Save-HDIFile’ n’est pas reconnu comme le nom d’une applet de commande, une fonction, un fichier de script ou un programme exécutable. Vérifiez l’orthographe du nom ou, si un chemin d’accès a été inclus, vérifiez que le chemin d’accès est correct et réessayez.
> Cela signifie que vous n’avez pas inclus les méthodes d’assistance.  Consultez [Méthodes d’assistance pour les scripts personnalisés](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).
>
>

## <a name="sample-scripts"></a>Exemples de scripts
Pour créer des clusters HDInsight sur un système d’exploitation Windows, l’action de script est le script Azure PowerShell. Le script suivant est un exemple pour configurer les fichiers de configuration de site :

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Le script accepte quatre paramètres : le nom de fichier de la configuration, la propriété à modifier, la valeur à définir et une description. Par exemple : 

    hive-site.xml hive.metastore.client.socket.timeout 90

Ces paramètres définissent la valeur hive.metastore.client.socket.timeout sur 90 dans le fichier hive-site.xml.  La valeur par défaut est de 60 secondes.

Cet exemple de script est également disponible ici : [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight propose plusieurs scripts pour installer des composants supplémentaires sur les clusters HDInsight :

| NOM | Script |
| --- | --- |
| **Installation de Spark** |https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consultez [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]. |
| **Installation de R** |https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consultez [Installer et utiliser R sur les clusters HDInsight][hdinsight-r-scripts]. |
| **Installation de Solr** |https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md). |
| - **Installation de Giraph** |https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md). |

L’action de script peut être déployée à partir du portail Azure, d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight.  Pour plus d’informations, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script][hdinsight-cluster-customize].

> [!NOTE]
> Les exemples de scripts fonctionnent uniquement avec le cluster HDInsight version 3.1 ou version ultérieure. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Méthodes d’assistance pour les scripts personnalisés
L’action de script fournit des méthodes d’assistance que vous pouvez utiliser lors de l’écriture de scripts personnalisés. Ces méthodes sont définies dans [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1) et peuvent être incluses dans vos scripts à l’aide de l’exemple suivant :

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Voici les méthodes d’assistance fournies par ce script :

| Méthode d'assistance | Description |
| --- | --- |
| **Save-HDIFile** |Télécharger un fichier à partir de l'URI spécifié vers un emplacement du disque local qui est associé au nœud de machine virtuelle Azure affecté au cluster. |
| **Expand-HDIZippedFile** |Décompresser un fichier zippé. |
| **Invoke-HDICmdScript** |Exécuter un script à partir de cmd.exe. |
| **HDILog d'écriture** |Écrire la sortie du script personnalisé utilisé pour une action de script. |
| **Get-Services** |Obtenir la liste des services s'exécutant sur l'ordinateur où s'exécute le script. |
| **Get-Service** |En utilisant le nom de service spécifique comme entrée, obtenir des informations détaillées pour un service spécifique (nom du service, ID de processus, état, etc.) sur l'ordinateur où s'exécute le script. |
| **Get-HDIServices** |Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script. |
| **Get-HDIService** |En utilisant le nom de service HDInsight spécifique comme entrée, obtenir des informations détaillées pour un service spécifique (nom du service, l'identificateur de processus, état, etc.) sur l'ordinateur où s'exécute le script. |
| **Get-ServicesRunning** |Obtenir la liste des services en cours d'exécution sur l'ordinateur où s'exécute le script. |
| **Get-ServiceRunning** |Vérifier si un service spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script. |
| **Get-HDIServicesRunning** |Obtenir la liste des services HDInsight en cours d'exécution sur l'ordinateur où s'exécute le script. |
| **Get-HDIServiceRunning** |Vérifier si un service HDInsight spécifique (par nom) est en cours d'exécution sur l'ordinateur où s'exécute le script. |
| **Get-HDIHadoopVersion** |Obtenir la version de Hadoop installée sur l'ordinateur où s'exécute le script. |
| **Test-IsHDIHeadNode** |Vérifier si l'ordinateur où s'exécute le script est un nœud principal. |
| **Test-IsActiveHDIHeadNode** |Vérifier si l'ordinateur où s'exécute le script est un nœud principal actif. |
| **Test-IsHDIDataNode** |Vérifier si l'ordinateur où s'exécute le script est un nœud de données. |
| **Edit-HDIConfigFile** |Modifier les fichiers de configuration hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml ou yarn-site.xml. |

## <a name="best-practices-for-script-development"></a>Meilleures pratiques relatives au développement de scripts
Quand vous développez un script personnalisé pour un cluster HDInsight, tenez compte des meilleures pratiques suivantes :

* Rechercher la version Hadoop

    Seul HDInsight 3.1 (Hadoop 2.4) ou version ultérieure prend en charge l'utilisation d'une action de script pour installer des composants personnalisés sur un cluster. Dans votre script personnalisé, vous devez utiliser la méthode d'assistance **Get-HDIHadoopVersion** pour vérifier la version de Hadoop avant d'effectuer d'autres tâches dans le script.
* Fournir des liens stables vers les ressources de script

    Les utilisateurs doivent s'assurer que tous les scripts et autres artefacts utilisés dans la personnalisation d'un cluster sont disponibles tout au long de la durée de vie du cluster et que les versions de ces fichiers ne changent pas au cours de cette période. Ces ressources sont requises si le réimageage des nœuds dans le cluster est requis. La meilleure pratique consiste à tout télécharger et à tout archiver dans un compte de stockage contrôlé par l'utilisateur. Ce compte peut être le compte de stockage par défaut ou les comptes de stockage supplémentaires spécifiés au moment du déploiement d'un cluster personnalisé.
    Dans les exemples de clusters Spark et R personnalisés fournis dans la documentation, par exemple, il existe une copie locale des ressources dans ce compte de stockage : https://hdiconfigactions.blob.core.windows.net/.
* S’assurer que le script de personnalisation du cluster est idempotent

    Vous devez tenir compte du réimageage des nœuds d'un cluster HDInsight au cours de la durée de vie du cluster. Le script de personnalisation du cluster est exécuté chaque fois qu'un cluster est réimagé. Ce script doit être conçu pour être idempotent afin que lors du réimageage, le script s’assure que le cluster retrouve l’état personnalisé qui était le sien juste après la première exécution du script lors de la création initiale du cluster. Par exemple, si un script personnalisé a installé une application à l’emplacement D:\AppLocation lors de sa première exécution, alors, à chaque exécution ultérieure, lors du réimageage, le script doit vérifier si l'application existe à l'emplacement D:\AppLocation avant de passer aux autres étapes du script.
* Installer des composants personnalisés dans l’emplacement optimal

    Lorsque les nœuds de cluster sont réimagés, le lecteur des ressources C:\ et le lecteur du système D:\ peuvent être reformatés, ce qui entraîne la perte des données et des applications qui avaient été installées sur ces lecteurs. Cette perte peut également se produire si un nœud de machine virtuelle (VM) Azure qui fait partie du cluster tombe en panne et est remplacé par un nouveau nœud. Vous pouvez installer les composants sur le lecteur D:\ ou dans l'emplacement C:\apps sur le cluster. Tous les autres emplacements sur le lecteur C:\ sont réservés. Indiquez l'emplacement d'installation des applications ou bibliothèques dans le script de personnalisation de cluster.
* Garantir la haute disponibilité de l’architecture du cluster

    HDInsight possède une architecture actif/passif pour la haute disponibilité, dans laquelle un nœud principal est en mode actif (où les services HDInsight sont en cours d'exécution) et l'autre nœud principal est en mode veille (dans lequel les services HDInsight ne sont pas en cours d’exécution). Les nœuds basculent entre les modes actif et passif si les services HDInsight sont interrompus. Si une action de script est utilisée pour installer les services sur les deux nœuds principaux pour la haute disponibilité, notez que le mécanisme de basculement HDInsight n’est pas en mesure de basculer automatiquement ces services installés par l'utilisateur. Ainsi, les services installés par l'utilisateur sur les nœuds principaux HDInsight qui sont censés être hautement disponibles doivent soit avoir leur propre mécanisme de basculement en mode actif/passif soit être en mode actif-actif.

    Une commande d’action de script HDInsight s’exécute sur les deux nœuds principaux si le rôle de nœud principal est spécifié en tant que valeur dans le paramètre *ClusterRoleCollection* . Par conséquent, lorsque vous concevez des scripts personnalisés, vérifiez qu'il est au fait de cette configuration. Vous devez vous assurer que des services identiques ne sont pas installés et démarrés sur les deux nœuds principaux pour éviter les conflits. Sachez, par ailleurs, que les données seront perdues lors des réimageages ; les logiciels installés à l'aide d'actions de script ne doivent donc pas être affectés par de tels événements. Les applications doivent être conçues pour fonctionner avec des données hautement disponibles qui sont distribuées sur de nombreux nœuds. Notez que jusqu'à 1/5 des nœuds d'un cluster peuvent être réimagés en même temps.
* Configurer les composants personnalisés pour utiliser le stockage d’objets blob Azure

    Les composants personnalisés que vous installez sur les nœuds de cluster peuvent être configurés par défaut pour utiliser le stockage HDFS (Hadoop Distributed File System). Vous devez modifier la configuration de façon à utiliser plutôt le stockage d'objets blob Azure. Sur un réimageage de cluster, le système de fichiers HDFS est formaté et vous perdez alors toutes les données qui y sont stockées. L'utilisation du stockage d'objets blob Azure au lieu du stockage HDFS garantit que vos données sont conservées.

## <a name="common-usage-patterns"></a>Modes d’utilisation courants
Cette section fournit des conseils sur l'implémentation de certains des modèles d'utilisation courants que vous pouvez rencontrer lors de l'écriture de votre propre script personnalisé.

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement
Dans le développement d'actions de script, il est souvent nécessaire de définir des variables d'environnement. Par exemple, la situation suivante correspond à un scénario très probable : vous téléchargez un fichier binaire à partir d'un site externe, l’installez sur le cluster et ajoutez l'emplacement où il est installé à votre variable d'environnement « PATH ». L'extrait de code suivant montre comment définir des variables d'environnement dans le script personnalisé.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Cette instruction définit la variable d'environnement **MDS_RUNNER_CUSTOM_CLUSTER** sur la valeur « true » et définit également l'étendue de cette variable à l'échelle de l'ordinateur. Dans certains cas, il est important que les variables d'environnement soient définies avec l’étendue appropriée (machine ou utilisateur). Cliquez [ici][1] pour obtenir plus d’informations sur la définition des variables d’environnement.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accès aux emplacements où sont stockés les scripts personnalisés
Les scripts utilisés pour personnaliser un cluster doivent être soit dans le compte de stockage par défaut pour le cluster soit dans un conteneur public en lecture seule sur un autre compte de stockage. Si votre script accède à des ressources situées ailleurs, celles-ci doivent se trouver dans un conteneur accessible publiquement (au moins public en lecture seule). Par exemple, vous souhaitez accéder à un fichier et l’enregistrer à l'aide de la commande HDI SaveFile.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Dans cet exemple, vous devez vous assurer que le conteneur « somecontainer » du compte de stockage « somestorageaccount » est accessible publiquement. Sinon, le script lève une exception « Introuvable » et échoue.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Transmettez les paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction
Pour transmettre plusieurs paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction, vous devez mettre en forme la valeur de chaîne pour qu’elle contienne tous les paramètres du script. Par exemple : 

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

or

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Lever une exception pour l’échec d’un déploiement de cluster
Si vous souhaitez être notifié précisément du fait que la personnalisation du cluster n’a pas réussi comme prévu, il est important qu’une exception soit levée et que la création du cluster échoue. Par exemple, vous souhaitez traiter un fichier s'il existe et gérer la situation d'erreur si le fichier n'existe pas. Cela garantira que le script s'arrête normalement et que l'état du cluster est correctement connu. L'extrait de code suivant fournit un exemple montrant comment y parvenir :

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Dans cet extrait, si le fichier n’existe pas, le script peut s’arrêter de manière appropriée après l’affichage du message d’erreur et le cluster est défini sur l’état En cours d’exécution, en supposant qu’il a terminé correctement le processus de personnalisation du cluster. Si vous souhaitez être averti précisément du fait que la personnalisation du cluster a échoué de manière inattendue en raison d’un fichier manquant, il est préférable de lever une exception et de faire échouer l’étape de personnalisation du cluster. Pour ce faire, vous devez plutôt utiliser l’extrait de code suivant.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Liste de vérification pour le déploiement d’une action de script
Voici les étapes à suivre avant de déployer des scripts :

1. Placez les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible aux nœuds du cluster lors du déploiement. Il peut s'agir de tout compte de stockage par défaut ou supplémentaire spécifié lors du déploiement du cluster, ou de tout conteneur de stockage accessible au public.
2. Ajoutez des contrôles dans les scripts pour vous assurer qu'ils s'exécutent de manière idempotente, ce qui permet d'exécuter le script plusieurs fois sur le même nœud.
3. Utilisez l’applet de commande Azure PowerShell **Write-Output** pour imprimer dans STDOUT et dans STDERR. N'utilisez pas **Write-Host**.
4. Utilisez un dossier de fichiers temporaires, tel que $env:TEMP, pour conserver le fichier téléchargé utilisé par les scripts, puis nettoyez-le au terme de l'exécution des scripts.
5. 5.Installez des logiciels personnalisés uniquement aux emplacements suivants : D:\ ou C:\apps. N'utilisez pas d'autres emplacements sur le lecteur C:, car ceux-ci sont réservés. Notez que l'installation de fichiers sur le lecteur C: à l’extérieur du dossier C:/apps peut entraîner des erreurs d'installation lors des opérations de réimageage du nœud.
6. En cas de modification des paramètres au niveau du système d'exploitation ou des fichiers de configuration du service Hadoop, vous pouvez redémarrer les services HDInsight pour qu'ils récupèrent des paramètres au niveau du système d'exploitation, tels que les variables d'environnement définies dans les scripts.

## <a name="debug-custom-scripts"></a>Déboguer des scripts personnalisés
Les journaux des erreurs de script et d'autres sorties sont stockés dans le compte de stockage par défaut que vous avez spécifié pour le cluster au moment de sa création. Les journaux sont stockés dans une table nommée *u<\cluster-name-fragment><\time-stamp>setuplog*. Il s'agit de journaux agrégés contenant des enregistrements de tous les nœuds (nœud principal et nœuds de travail) sur lesquels le script s'exécute dans le cluster.

Pour vérifier facilement les journaux, utilisez les outils HDInsight pour Visual Studio. Pour installer les outils, consultez [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Pour vérifier le journal à l’aide de Visual Studio**

1. Ouvrez Visual Studio.
2. Cliquez sur **Affichage**, puis sur **Explorateur de serveurs**.
3. Cliquez avec le bouton droit sur Azure, cliquez sur Se connecter à **Abonnements Microsoft Azure**, puis entrez vos informations d’identification.
4. Développez successivement **Stockage**, le compte de stockage Azure utilisé comme système de fichiers par défaut, **Tables**, puis double-cliquez sur le nom de la table.

Vous pouvez également accéder à distance aux nœuds de cluster pour voir les informations STDOUT et STDERR des scripts personnalisés. Les journaux de chaque nœud sont spécifiques à celui-ci et sont enregistrés dans **C:\HDInsightLogs\DeploymentAgent.log**. Ces fichiers journaux enregistrent tous les résultats du script personnalisé. Un extrait de journal pour une action de script Spark ressemble à ceci :

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Dans ce journal, il apparaît clairement que l'action de script Spark a été exécutée sur la machine virtuelle nommée HEADNODE0 et qu'aucune exception n'a été générée pendant l'exécution.

En cas d'échec de l'exécution, la sortie décrivant cet échec est également contenue dans ce fichier journal. Les informations fournies dans ces journaux doivent être utiles lors du débogage des problèmes de script qui peuvent survenir.

## <a name="see-also"></a>Voir aussi
* [Personnaliser des clusters HDInsight à l’aide d’une action de script][hdinsight-cluster-customize]
* [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]
* [Installer et utiliser R sur les clusters HDInsight][hdinsight-r-scripts]
* [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md)
* [Installez et utilisez Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
