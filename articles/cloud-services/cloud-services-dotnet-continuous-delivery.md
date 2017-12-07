---
title: Remise continue pour les services cloud avec TFS dans Azure | Microsoft Docs
description: "Découvrez comment configurer la livraison continue pour les applications cloud Azure. Exemples de code pour les instructions en ligne de commande MSBuild et les scripts PowerShell."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 470fda7722e6a22e50ed66a7bc193fc7c9f71536
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Remise continue pour Cloud Services dans Azure
Le processus décrit dans cet article vous explique comment configurer la remise continue pour les applications cloud Azure. Ce processus vous permet de créer automatiquement des packages et de les déployer dans Azure après chaque intégration du code. Le processus de génération du package décrit dans cet article est équivalent à la commande **Package** de Visual Studio et les étapes de la publication sont les mêmes que pour la commande **Publish** dans Visual Studio.
Cet article présente les méthodes que vous allez utiliser pour créer un serveur de builds avec des instructions de ligne de commande MSBuild et des scripts Windows PowerShell. Il explique également comment configurer les définitions Visual Studio Team Foundation Server - Team Build à utiliser avec les commandes MSBuild et les scripts PowerShell. Ce processus est personnalisable en fonction de votre environnement de génération et des environnements Azure cibles.

Vous pouvez également utiliser Visual Studio Team Services, une version de TFS hébergée sur Azure, pour effectuer ces opérations plus facilement. 

Avant de commencer, vous devez publier votre application à partir de Visual Studio.
Ceci vous assure que toutes les ressources sont disponibles et initialisées lorsque vous tentez d'automatiser le processus de publication.

## <a name="1-configure-the-build-server"></a>1 : Configuration du serveur de builds
Avant de pouvoir créer un package Azure à l'aide de MSBuild, vous devez installer les logiciels et les outils nécessaires sur le serveur de builds.

Visual Studio ne doit pas obligatoirement être installé sur le serveur de builds. Si vous souhaitez utiliser Team Foundation Build Service pour gérer votre serveur de builds, suivez les instructions de la documentation [Team Foundation Build Service][Team Foundation Build Service] .

1. Sur le serveur de builds, installez [.NET Framework 4.5.2][.NET Framework 4.5.2], qui comprend MSBuild.
2. Installez la dernière version des [outils de création Azure pour .NET](https://azure.microsoft.com/develop/net/).
3. Installez les [bibliothèques Azure pour .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Copiez le fichier Microsoft.WebApplication.targets à partir d’une installation Visual Studio vers le serveur de builds.

   Sur un ordinateur doté de Visual Studio, ce fichier se trouve dans le répertoire C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Vous devez le copier dans le même répertoire sur le serveur de builds.
5. Installez les outils [Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2 : Génération d'un package à l'aide des commandes MSBuild
Cette section décrit la création d'une commande MSBuild qui génère un package Azure. Exécutez cette étape sur le serveur de builds pour vérifier que tout est correctement configuré et que la commande MSBuild fait ce que vous attendez d’elle. Vous pouvez ajouter cette ligne de commande aux scripts existants sur le serveur de builds. Vous pouvez aussi utiliser la ligne de commande dans une définition de build TFS, comme décrit dans la section suivante. Pour plus d’informations sur les paramètres de ligne de commande et MSBuild, consultez la page [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Si Visual Studio est installé sur le serveur de builds, localisez puis choisissez **Invite de commandes de Visual Studio** dans le dossier **Visual Studio Tools** sous Windows.

   Si Visual Studio n'est pas installé sur le serveur de builds, ouvrez une invite de commandes et assurez-vous que MSBuild.exe est bien accessible sur le chemin d'accès. MSBuild est installé avec .NET Framework dans %WINDIR%\\Microsoft.NET\\Framework\\*Version*. Par exemple, pour ajouter MSBuild.exe à la variable d'environnement PATH quand .NET Framework 4 est installé, tapez la commande suivante à l'invite de commandes :

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. À l'invite de commandes, accédez au dossier qui contient le fichier de projet Azure que vous voulez compiler.
3. Exécutez MSBuild avec l'option /target:Publish comme dans l'exemple suivant :

       MSBuild /target:Publish

   Cette option peut être abrégée en /t:Publish. L'option /t:Publish de MSBuild ne doit pas être confondue avec les commandes Publish disponibles dans Visual Studio lorsque le Kit de développement logiciel (SDK) Azure est installé. L'option /t:Publish ne génère que les packages Azure. Elle ne déploie pas les packages comme les commandes Publish de Visual Studio.

   Vous pouvez aussi spécifier le nom du projet comme paramètre MSBuild. S'il n'est pas spécifié, le répertoire actif est utilisé. Pour plus d’informations sur les options de ligne de commande MSBuild, consultez la page [Référence de la ligne de commande MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Recherchez la sortie. Par défaut, cette commande crée un répertoire en relation avec le dossier racine du projet, par exemple *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Lorsque vous générez un projet Azure, vous générez deux fichiers, le fichier de package et le fichier de configuration qui l'accompagne :

   * Project.cspkg
   * ServiceConfiguration.*TargetProfile*.cscfg

   Par défaut, tous les projets Azure comprennent un fichier de configuration de service (.cscfg file) pour les versions locales (débogage) et un autre pour les versions cloud (intermédiaire ou de production), mais vous pouvez ajouter ou supprimer les fichiers de configuration de service selon vos besoins. Lorsque vous générez un package dans Visual Studio, il vous est demandé quels fichiers de configuration de service vous voulez inclure avec le package.
5. Spécifiez le fichier de configuration de service Lorsque vous générez un package avec MSBuild, le fichier de configuration de service local est inclus par défaut. Pour inclure un autre fichier de configuration de service, définissez la propriété TargetProfile de la commande MSBuild, comme dans l'exemple suivant :

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Spécifiez l'emplacement de la sortie. Définissez le chemin d’accès avec l’option /p:PublishDir=*Directory*\\, en incluant la barre oblique inverse de fin, comme dans l’exemple suivant :

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   Une fois que vous avez conçu et testé une ligne de commande MSBuild appropriée pour générer vos projets et les combiner dans un package Azure, vous pouvez ajouter cette ligne de commande à vos scripts. Si votre serveur de builds utilise des scripts personnalisés, ce processus dépend des particularités de votre processus de génération personnalisé. Si vous utilisez TFS comme environnement de génération, vous pouvez suivre les instructions de l'étape qui suit pour ajouter la création du package Azure à votre processus.

## <a name="3-build-a-package-using-tfs-team-build"></a>3 : Génération d’un package avec TFS Team Build
Si Team Foundation Server (TFS) est configuré comme contrôleur de build et que le serveur de builds est configuré comme ordinateur de builds TFS, vous pouvez éventuellement configurer une compilation automatisée pour votre package Azure. Pour plus d’informations sur la configuration et l’utilisation de Team Foundation Server comme système de génération, consultez [Faire évoluer votre système de build][Scale out your build system]. En particulier, la procédure suivante suppose que vous avez configuré votre serveur de builds comme décrit dans [Déployer et configurer un serveur de builds][Deploy and configure a build server] et que vous avez créé un projet d’équipe, ainsi qu’un projet de service cloud dans le projet d’équipe.

Pour configurer TFS pour générer des packages Azure, procédez comme suit :

1. Dans Visual Studio sur votre ordinateur de développement, dans le menu Affichage, choisissez **Team Explorer**, ou choisissez Ctrl+\\, Ctrl+M. Dans la fenêtre Team Explorer, développez le nœud **Builds** ou choisissez la page **Builds**, puis choisissez **Nouvelle définition de build**.

   ![Option Nouvelle définition de build][0]
2. Choisissez l'onglet **Déclencheur** et spécifiez les conditions pour lesquelles le package doit être généré. Par exemple, spécifiez **Intégration continue** pour générer le package à chaque intégration de contrôle du code source.
3. Choisissez l’onglet **Paramètres de la source** et vérifiez que votre dossier de projet figure dans la colonne **Dossier du contrôle de code source** et que le statut est **Actif**.
4. Choisissez l'onglet **Valeurs par défaut des builds** , et sous Contrôleur de build, vérifiez le nom du serveur de builds.  De même, choisissez l'option **Copier la sortie de la génération dans le dossier de dépôt suivant** et spécifiez l'emplacement souhaité.
5. Choisissez l’onglet **Processus** . Sous l’onglet Processus, choisissez le modèle par défaut, sous **Build**, choisissez le projet s’il n’est pas déjà sélectionné et développez la section **Avancé** dans la section **Build** de la grille.
6. Choisissez **Arguments MSBuild**et définissez les arguments de ligne de commande MSBuild comme décrit à l'étape 2 plus haut. Par exemple, entrez **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** pour générer un package et copier les fichiers associés dans \\\\myserver\\drops\\ :

   ![Arguments MSBuild][2]

   > [!NOTE]
   > le fait de copier les fichiers dans un partage public simplifie le déploiement manuel des packages à partir de votre ordinateur de développement.
7. Testez le fonctionnement de votre processus en intégrant une modification à votre projet ou en ajoutant une build dans la file d'attente. Pour ajouter une nouvelle build dans la file d’attente, dans Explorateur d’équipes, cliquez avec le bouton droit sur **Toutes les définitions de build**, puis choisissez **Mettre la nouvelle build en file d’attente**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4 : Publication d'un package à l'aide d'un script PowerShell
Cette section décrit la création d'un script Windows PowerShell qui publie le résultat du package de l'application cloud dans Azure à l'aide de paramètres facultatifs. Ce script peut être appelé après l'étape de compilation dans votre automatisation de build personnalisée. Il peut également être appelé depuis les activités de workflow du modèle de processus dans Visual Studio TFS Team Build.

1. Installez les [applets de commande Azure PowerShell][Azure PowerShell cmdlets] (v0.6.1 ou version ultérieure).
   Pendant la phase de configuration des applets de commande, choisissez l’installation en tant que composant logiciel enfichable. Cette installation, officiellement prise en charge, remplace l’ancienne version proposée via CodePlex, même si les versions étaient numérotées 2.x.x.
2. Démarrez Azure PowerShell dans le menu ou la page Démarrer. Si vous démarrez de cette façon, les cmdlets Azure PowerShell sont chargées.
3. À l'invite de commandes PowerShell, vérifiez que les cmdlets PowerShell sont bien chargées en tapant la commande partielle `Get-Azure` et en appuyant sur la touche Tab pour compléter l'instruction.

   Si vous appuyez plusieurs fois sur la touche de tabulation, les différentes commandes Azure PowerShell doivent apparaître.
4. Vérifiez que vous pouvez vous connecter à votre abonnement Azure en important vos informations d'abonnement à partir du fichier .publishsettings.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Puis entrez la commande

   `Get-AzureSubscription`

   Ceci affiche les informations sur votre abonnement. Vérifiez que tout est correct.
5. Enregistrez le modèle de script fourni à la fin de cet article dans votre dossier de scripts sous c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.
6. Vérifiez la section des paramètres de ce script. Ajoutez des valeurs ou modifiez les valeurs par défaut. Ces valeurs peuvent de toute manière être ignorées en indiquant des paramètres explicites.
7. Assurez-vous que les comptes valides de service cloud et de stockage créés dans votre abonnement peuvent être utilisés par le script de publication. Le compte de stockage (stockage d'objets blob) est utilisé pour télécharger et stocker de façon temporaire le package de déploiement et le fichier de configuration pendant la création du déploiement.

   * Pour créer un service cloud, vous pouvez appeler ce script ou utiliser le [portail Azure](https://portal.azure.com). Le nom du service cloud sera utilisé comme préfixe dans le nom de domaine complet. Il doit donc être unique.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Pour créer un compte de stockage, vous pouvez appeler ce script ou utiliser le [portail Azure](https://portal.azure.com). Le nom du compte de stockage sera utilisé comme préfixe dans le nom de domaine complet. Il doit donc être unique. Vous pouvez essayer d'utiliser le même nom que le service cloud.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. Appelez ce script directement depuis Azure PowerShell ou ajoutez ce script à votre automatisation de build hôte afin qu'il arrive après la génération du package.

   > [!IMPORTANT]
   > le script supprime ou remplace toujours vos déploiements existants par défaut s'ils sont détectés. Ceci est nécessaire pour permettre la remise continue automatique là où il n'est pas possible de demander à l'utilisateur d'intervenir.
   >
   >

   **Exemple de scénario 1 :** déploiement continu d’un service dans l’environnement intermédiaire :

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Cette opération est normalement suivie d'un test de vérification et d'un échange d'adresses IP virtuelles. Cet échange d’adresses IP virtuelles peut se faire par le biais du [portail Azure](https://portal.azure.com) ou à l’aide de l’applet de commande Move-Deployment.

   **Exemple de scénario 2 :** déploiement continu d’un service de test dédié dans l’environnement de production

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Bureau à distance :**

   Si le Bureau à distance est activé dans votre projet Azure, vous devrez effectuer des opérations supplémentaires pour vous assurer que le bon certificat de service cloud est téléchargé dans tous les services cloud ciblés par ce script.

   Recherchez les valeurs d'empreinte numérique de certificat attendues pour vos rôles. Ces valeurs sont visibles dans la section Certificats du fichier de configuration de cloud (ServiceConfiguration.Cloud.cscfg). Elles sont également visibles dans la boîte de dialogue Configuration Bureau à distance de Visual Studio lorsque vous affichez les options et consultez le certificat sélectionné.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   Téléchargez les certificats Bureau à distance (opération de configuration unique) à l'aide du script de cmdlet suivant :

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Par exemple :

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Vous pouvez également exporter le fichier de certificat PFX avec une clé privée et charger les certificats sur chaque service cloud ciblé à l’aide du [portail Azure](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Mise à niveau du déploiement et suppression du déploiement -\> Nouveau déploiement**

   Le script exécute par défaut un déploiement de mise à niveau ($enableDeploymentUpgrade = 1) si aucun paramètre n'est transmis ou si la valeur 1 est transmise de manière explicite. Pour les instances uniques, ceci présente l'avantage de prendre moins de temps qu'un déploiement complet. Pour les instances qui ont besoin d'une haute disponibilité, ceci présente également l'avantage de laisser s'exécuter certaines instances pendant que d'autres sont mises à niveau (mise à niveau progressive du domaine). De plus, votre adresse IP virtuelle n'est pas supprimée.

   Le déploiement de mise à niveau peut être désactivé dans le script ($enableDeploymentUpgrade = 0) ou en transmettant le paramètre *-enableDeploymentUpgrade 0* , ce qui modifie le comportement du script : il supprime d'abord les déploiements existants, puis crée un nouveau déploiement.

   > [!IMPORTANT]
   > le script supprime ou remplace toujours vos déploiements existants par défaut s'ils sont détectés. Ceci est nécessaire pour permettre la remise continue automatique là où il n'est pas possible de demander à l'utilisateur ou à l'opérateur d'intervenir.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5 : Publication d’un package avec TFS Team Build
Cette étape facultative connecte TFS Team Build au script créé à l'étape 4, qui gère la publication du package généré dans Azure. Ceci implique de modifier le modèle de processus utilisé par votre définition de build afin qu'il exécute une activité Publish à la fin du workflow. Cette activité Publish exécute votre commande PowerShell en transmettant des paramètres à partir de la build. La sortie des cibles MSBuild et du script de publication sera intégré à la sortie de génération standard.

1. Modifiez la définition de build responsable du déploiement continu.
2. Sélectionnez l'onglet **Process** .
3. Suivez [ces instructions](http://msdn.microsoft.com/library/dd647551.aspx) pour ajouter un projet d’activité pour le modèle de processus de génération, télécharger le modèle par défaut, l’ajouter au projet et l’archiver. Fournissez au modèle de processus de génération un nouveau nom, tel qu’AzureBuildProcessTemplate.
4. Retournez dans l’onglet **Processus** et utilisez **Afficher les détails** pour afficher la liste des modèles de processus de génération disponibles. Choisissez le bouton **Nouveau...** et accédez au projet que vous venez d’ajouter et d’archiver. Localisez le modèle que vous venez de créer et choisissez **OK**.
5. Ouvrez le modèle de processus sélectionné pour le modifier. Vous pouvez l'ouvrir directement dans le concepteur de workflow ou dans l'éditeur XML pour modifier le XAML.
6. Ajoutez la liste de nouveaux arguments suivante, en tant que lignes distinctes dans l'onglet des arguments du concepteur de workflow. Tous ces arguments doivent avoir direction=In et type=String. Elles sont utilisées pour passer les paramètres de la définition de build vers le workflow, qui est ensuite utilisé pour appeler le script de publication.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Liste des arguments][3]

   Le code XAML correspondant ressemble à ceci :

       <Activity  _ />
         <x:Members>
           <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
           <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
           <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
           <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
           <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
           <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
           <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
           <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
           <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
           <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
           <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
           <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
           <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
           <x:Property Name="GetVersion" Type="InArgument(x:String)" />
           <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
           <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
           <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
           <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
           <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
           <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
           <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
           <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
           <x:Property Name="Environment" Type="InArgument(x:String)" />
           <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
           <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
           <x:Property Name="ServiceName" Type="InArgument(x:String)" />
         </x:Members>

         <this:Process.MSBuildArguments>
7. Ajoutez une nouvelle séquence à la fin de Exécuter sur l'agent :

   1. Commencez par ajouter une activité d'instruction If pour vérifier la présence d'un fichier de script valide. Définissez la valeur suivante pour la condition :

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. Dans le cas Then de l'instruction If, ajoutez une nouvelle activité Sequence. Définissez le nom d'affichage sur « Start publish »
   3. La séquence Start publish toujours sélectionnée, ajoutez la liste de nouvelles variables suivante, en tant que lignes distinctes dans l'onglet des variables du concepteur de workflow. Toutes les variables doivent comporter type =String et Scope=Start publish. Elles sont utilisées pour passer les paramètres de la définition de build vers le workflow, qui est ensuite utilisé pour appeler le script de publication.

      * SubscriptionDataFilePath, de type String
      * PublishScriptFilePath, de type String

        ![Nouvelles variables][4]
   4. Si vous utilisez TFS 2012 ou version antérieure, ajoutez une activité ConvertWorkspaceItem au début de la nouvelle séquence. Si vous utilisez TFS 2013 ou version ultérieure, ajoutez une activité GetLocalPath au début de la nouvelle séquence. Pour une activité ConvertWorkspaceItem, définissez les propriétés comme suit : Direction=ServerToLocal, DisplayName=’Convert publish script filename’, Input=’ PublishScriptLocation’, Result=’PublishScriptFilePath’, Workspace=’Workspace’. Pour une activité GetLocalPath, définissez la propriété IncomingPath sur « PublishScriptLocation » et le résultat sur « PublishScriptFilePath ». Cette activité convertit le chemin vers le script de publication des emplacements de serveur TFS (si applicable) en chemin d'accès vers le disque local.
   5. Si vous utilisez TFS 2012 ou version antérieure, ajoutez une autre activité ConvertWorkspaceItem au début de la nouvelle séquence. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Si vous utilisez TFS 2013 ou version ultérieure, ajoutez un autre GetLocalPath. IncomingPath='SubscriptionDataFileLocation' et Result='SubscriptionDataFilePath.'
   6. Ajoutez une activité InvokeProcess à la fin de la nouvelle partie Sequence.
      Cette activité appelle PowerShell.exe avec les arguments transmis dans la définition de build.

      + Arguments = String.Format(" -File ""{0}"" -serviceName {1}  -storageAccountName {2} -packageLocation ""{3}""  -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""  -selectedSubscription {6} -environment ""{7}""",  PublishScriptFilePath, ServiceName, StorageAccountName,  PackageLocation, CloudConfigLocation,  SubscriptionDataFilePath, SubscriptionName, Environment)
      + DisplayName = Execute publish script
      + FileName = "PowerShell" (inclure les guillemets)
      + OutputEncoding=  System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. Dans la zone de texte de la section **Handle Standard Output** d’InvokeProcess, définissez la valeur sur « data ». Cette variable permet de stocker les données de sortie standard.
   8. Ajoutez une activité WriteBuildMessage juste en dessous de la section **Handle Standard Output** . Définissez Importance = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' et Message='data'. Ceci permet que la sortie standard du script soit écrite dans la sortie de génération.
   9. Dans la zone de texte de la section **Handle Error Output** d’InvokeProcess, définissez la valeur sur « data ». Cette variable permet de stocker les données d'erreur standard.
   10. Ajoutez une activité WriteBuildError juste en dessous de la section **Handle Error Output** . Définissez Message='data'. Ceci permet d'écrire les erreurs standard du script dans la sortie d'erreur de génération.
   11. Corrigez toutes les erreurs, indiquées par des points d'exclamation bleus. Placez le pointeur sur un point d’exclamation pour obtenir une indication sur l’erreur. Enregistrez le workflow pour supprimer les erreurs.

   Le résultat final des activités du workflow de publication doit ressembler à ceci dans le concepteur :

   ![Activités de workflow][5]

   Le résultat final des activités du workflow de publication doit ressembler à ceci dans le XAML :

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Enregistrez le workflow de modèle de processus de génération et archivez ce fichier.
9. Modifiez la définition de build (fermez-la si elle est déjà ouverte) et sélectionnez le bouton **Nouveau** si le nouveau modèle n’apparaît pas encore dans la liste des modèles de processus.
10. Définissez les valeurs de la propriété du paramètre dans la section Misc comme suit :

    1. CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Cette valeur est dérivée de : ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Cette valeur est dérivée de : ($PublishDir)($ProjectName).cspkg*
    3. PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'
    4. ServiceName = ’mycloudservicename’ *Utilisez le nom du service cloud correspondant*
    5. Environment = 'Staging'
    6. StorageAccountName = ’mystorageaccountname’ *Utilisez le nom du compte de stockage correspondant*
    7. SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'
    8. SubscriptionName = 'default'

    ![Valeurs de propriétés des paramètres][6]
11. Enregistrez les modifications apportées à la définition de build.
12. Ajoutez une build à la file d'attente pour exécuter à la fois la génération et la publication du package. Si un déclencheur est défini sur Continuous Integration, vous exécuterez ce comportement à chaque intégration.

### <a name="publishcloudserviceps1-script-template"></a>Modèle de script PublishCloudService.ps1
```powershell
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Étapes suivantes
Pour activer le débogage à distance quand vous utilisez la remise continue, consultez [Activation du débogage distant lors de l’utilisation de la remise continue pour publier sur Azure](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
