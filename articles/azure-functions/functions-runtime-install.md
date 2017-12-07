---
title: "Installation du runtime d’Azure Functions | Microsoft Docs"
description: "Comment installer le runtime d’Azure Functions"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: b6078ba7b553773294ebbf11949f7d3b53f46f0c
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Installer la préversion du runtime d’Azure Functions

Suivez ces étapes pour installer la préversion du runtime d’Azure Functions :

1. Vérifiez que votre ordinateur respecte la configuration minimale requise.
1. Téléchargez le [programme d’installation de la préversion du runtime d’Azure Functions](https://aka.ms/azafr).
1. Installez la préversion du runtime Azure Functions.
1. Terminez la configuration de la préversion du runtime Azure Functions.

## <a name="prerequisites"></a>Prérequis

Avant d’installer la préversion du runtime d’Azure Functions, vous devez disposer des éléments suivants :

1. Un ordinateur exécutant Microsoft Windows Server 2016 ou Windows 10 Creators Update (édition Professionnelle ou Entreprise).
1. Une instance SQL Server en cours d’exécution au sein de votre réseau.  La version minimale requise est SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Installer la préversion du runtime d’Azure Functions

Le programme d’installation de la préversion du runtime Azure Functions vous guide lors de l’installation de ses rôles de gestion et de travail.  Il est possible d’installer le rôle de gestion et de travail sur le même ordinateur.  Toutefois, si vous ajoutez des fonctions, vous devez déployer davantage de rôles de travail sur des ordinateurs supplémentaires pour faire évoluer vos fonctions sur plusieurs rôles de travail.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Installer le rôle de gestion et de travail sur le même ordinateur

1. Exécutez le programme d’installation de la préversion du runtime d’Azure Functions.

    ![Programme d’installation de la préversion du runtime d’Azure Functions][1]

1. Cliquez sur **Suivant**.
1. Une fois que vous avez lu les conditions d’utilisation du **CLUF**, **cochez la case** pour accepter les conditions d’utilisation et cliquez sur **Suivant** pour passer à l’étape suivante.
1. À présent, sélectionnez les rôles que vous souhaitez installer sur cet ordinateur **Rôle de gestion Functions** et/ou **Rôle de travail Functions** et cliquez sur **Suivant**.

    ![Programme d’installation de la préversion du runtime d’Azure Functions - Sélection du rôle][3]

    > [!NOTE]
    > Vous pouvez installer le **rôle de travail Functions** sur de nombreuses autres machines. Pour cela, suivez ces instructions, puis sélectionnez uniquement **Rôle de travail Functions** dans le programme d’installation.

1. Cliquez sur **Suivant** pour que **l’Assistant Installation du runtime Azure Functions** démarre le processus d’installation sur votre ordinateur.
1. Une fois terminé, l’Assistant Installation lance l’outil de configuration du **runtime Azure Functions**.

    ![Programme d’installation de la préversion du runtime d’Azure Functions terminé][5]

    > [!NOTE]
    > Si vous effectuez l’installation sur **Windows 10** et que la fonctionnalité **Conteneur** n’avait pas été activée, le **programme d’installation du runtime Azure Functions** vous invite à redémarrer votre ordinateur pour terminer l’installation.

## <a name="configure-the-azure-functions-runtime"></a>Configurer le runtime d’Azure Functions

Pour terminer l’installation du runtime d’Azure Functions, vous devez terminer la configuration.

1. L’outil de configuration du **runtime Azure Functions** vous indique les rôles installés sur votre ordinateur.

    ![Outil de configuration de la préversion du runtime d’Azure Functions terminé][6]

1. Cliquez sur l’onglet **Base de données**, entrez les informations de connexion de votre instance SQL Server et cliquez sur **Appliquer**.  Cette opération est nécessaire pour que le runtime Azure Functions crée une base de données pour prendre en charge le runtime.
    
    ![Configuration de la base de données de la préversion du runtime d’Azure Functions][7]

1. Cliquez sur l’onglet **Informations d’identification**.  Dans cet écran, vous devez créer deux nouveaux jeux d’informations d’identification à utiliser avec un partage de fichiers pour l’hébergement de toutes vos Azure Functions.  Spécifiez les combinaisons de **nom d’utilisateur** et de **mot de passe** du **propriétaire du partage de fichiers** et de l’**utilisateur du partage de fichiers**, puis cliquez sur **Appliquer**.

    ![Informations d’identification de la préversion du runtime d’Azure Functions][8]

1. Cliquez sur l’onglet **Partage de fichiers**.  Dans cet écran, vous devez spécifier les détails de l’emplacement du **partage de fichiers**.  Celui-ci peut être créé pour vous ou vous pouvez utiliser un partage de fichier existant, puis cliquez sur **Appliquer**.  Si vous sélectionnez un nouvel emplacement de partage de fichiers, vous devez spécifier un répertoire que le runtime d’Azure Functions doit utiliser.
    
    ![Partage de fichiers de la version préliminaire du runtime d’Azure Functions][9]

1. Cliquez sur l’onglet **IIS**.  Cet onglet affiche les détails des sites web dans IIS, que l’outil de configuration du runtime Azure Functions va créer.  Cliquez sur **Appliquer** pour terminer.

    ![IIS pour la préversion du runtime d’Azure Functions][10]

1. Cliquez sur l’onglet **Services**.  Cet onglet affiche l’état des services dans votre outil de configuration du runtime Azure Functions.  Si après la configuration initiale, le **service d’activation hôte Azure Functions** ne fonctionne pas, cliquez sur **Démarrer le service**.

    ![Configuration de la préversion du runtime d’Azure Functions terminée][11]

1. Enfin, accédez au **portail du runtime Azure Functions** en tant que `https://<machinename>/`.

    ![Portail du runtime d’Azure Functions en préversion][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
