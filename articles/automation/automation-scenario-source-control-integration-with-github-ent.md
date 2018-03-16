---
title: "Intégration du contrôle de code source avec GitHub Enterprise | Microsoft Docs"
description: "Décrit de manière détaillée comment configurer l’intégration avec GitHub Enterprise pour le contrôle de code source des runbooks Automation."
services: automation
documentationCenter: 
authors: georgewallace
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: 2944b62cb3dc6146573041533d56d45b6cc87f18
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Scénario Azure Automation - Intégration du contrôle de code source Automation avec GitHub Enterprise

Automation prend actuellement en charge l’intégration du contrôle de code source, qui vous permet d’associer des runbooks dans votre compte Automation à un référentiel de contrôle de code source GitHub. Toutefois, les clients qui ont déployé [GitHub Enterprise](https://enterprise.github.com/home) pour prendre en charge leurs pratiques DevOps, souhaitent également l’utiliser pour gérer le cycle de vie des runbooks développés afin d’automatiser les processus d’entreprise et les opérations de gestion des services.  

Dans ce scénario, vous disposez d’un ordinateur Windows dans votre centre de données, configuré en tant que Runbook Worker hybride, sur lequel les modules Azure Resource Manager et les outils Git sont installés. L’ordinateur Worker hybride inclut un clone du référentiel Git local. Lorsque le runbook est exécuté sur le Worker hybride, le répertoire Git est synchronisé et le contenu du fichier de runbook est importé dans le compte Automation.

Cet article décrit comment procéder à cette configuration dans votre environnement Azure Automation. Commencez par configurer Automation avec les informations d’identification de sécurité, les runbooks nécessaires pour prendre en charge ce scénario et le déploiement d’un Runbook Worker hybride dans votre centre de données pour exécuter les runbooks et accéder à votre dépôt GitHub Enterprise afin de synchroniser des runbooks avec votre compte Automation.  


## <a name="getting-the-scenario"></a>Obtention du scénario

Ce scénario se compose de deux runbooks PowerShell que vous pouvez importer directement depuis la [galerie de runbooks](automation-runbook-gallery.md) dans le portail Azure ou télécharger depuis [PowerShell Gallery](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Description| 
--------|------------|
Export-RunAsCertificateToHybridWorker | Ce runbook exporte un certificat d’identification (RunAs) d’un compte Automation vers un Worker hybride afin que les runbooks sur le Worker puissent s’authentifier auprès d’Azure pour pouvoir importer les runbooks dans le compte Automation.| 
Sync-LocalGitFolderToAutomationAccount | Ce runbook synchronise le dossier Git local sur l’ordinateur hybride, puis importe les fichiers de runbook (*.ps1) dans le compte Automation.|

### <a name="credentials"></a>Informations d'identification

Informations d'identification | Description|
-----------|------------|
GitHRWCredential | La ressource d’informations d’identification que vous créez inclut le nom d’utilisateur et le mot de passe d’un utilisateur disposant d’autorisations sur le Worker hybride.|

## <a name="installing-and-configuring-this-scenario"></a>Installation et configuration de ce scénario

### <a name="prerequisites"></a>Prérequis


1. Le runbook Sync-LocalGitFolderToAutomationAccount s’authentifie à l’aide du [Compte d’identification Azure](automation-sec-configure-azure-runas-account.md). 

2. Un espace de travail Microsoft Operations Management Suite (OMS) avec la solution Azure Automation activée et configurée est également requis. Si aucun n’est associé au compte Automation utilisé pour installer et configurer ce scénario, il est créé et configuré pour vous lorsque vous exécutez le script **New-OnPremiseHybridWorker.ps1** à partir du Runbook Worker hybride.        

    > [!NOTE]
    > Actuellement, seules les régions suivantes prennent en charge l’intégration d’Automation avec OMS : **Sud-Est de l’Australie**, **Est des États-Unis 2**, **Asie du Sud-Est** et **Europe de l’Ouest**. 

3. Un ordinateur pouvant servir de Runbook Worker hybride dédié, qui héberge également le logiciel GitHub et tient à jour les fichiers de runbook (*runbook*.ps1) dans un répertoire source sur le système de fichiers à synchroniser entre GitHub et votre compte Automation.

### <a name="import-and-publish-the-runbooks"></a>Importer et publier des runbooks

Pour importer les runbooks *Export-RunAsCertificateToHybridWorker* et *Sync-LocalGitFolderToAutomationAccount* à partir de la galerie de runbooks de votre compte Automation dans le portail Azure, suivez les procédures décrites dans [Importer un runbook de la galerie de runbooks](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publiez les runbooks une fois qu’ils ont été correctement importés dans votre compte Automation.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Déployer et configurer un Runbook Worker hybride

Si un Runbook Worker hybride n’est pas déjà déployé dans votre centre de données, vous devez consulter la configuration requise et suivre les étapes d’installation automatisée en utilisant la procédure décrite dans [Runbooks Worker hybride Azure Automation - Automatiser l’installation et la configuration](automation-hybrid-runbook-worker.md#automated-deployment). Une fois le Worker hybride correctement installé sur un ordinateur, procédez comme suit pour effectuer sa configuration afin de prendre en charge ce scénario.

1. Connectez-vous à l’ordinateur hébergeant le rôle Runbook Worker hybride avec un compte disposant des droits d’administrateur local et créez un répertoire pour stocker les fichiers de runbook Git. Clonez le dépôt Git interne dans le répertoire.
2. Si vous n’avez pas déjà créé un compte d’identification ou que vous souhaitez en créer un spécialement conçu à cet effet, dans le portail Azure, accédez aux comptes Automation, sélectionnez votre compte Automation, puis créez une [ressource d’informations d’identification](automation-credentials.md) contenant le nom d’utilisateur et le mot de passe d’un utilisateur disposant d’autorisations sur le Worker hybride.  
3. Dans votre compte Automation, [modifiez le runbook](automation-edit-textual-runbook.md) **Export-RunAsCertificateToHybridWorker** et remplacez la valeur de la variable *$Password* par un mot de passe fort.  Une fois la valeur modifiée, cliquez sur **Publier** pour que la version brouillon du runbook soit publiée. 
5. Démarrez le runbook **Export-RunAsCertificateToHybridWorker**, dans le panneau **Démarrer le runbook**, sous l’option **Paramètres d’exécution**, sélectionnez l’option **Worker hybride**, puis sélectionnez dans la liste déroulante le groupe Worker hybride que vous avez créé précédemment pour ce scénario.  

    Un certificat est exporté vers le Worker hybride pour que les runbooks sur le Worker puissent s’authentifier auprès d’Azure à l’aide de la connexion d’identification, afin de gérer les ressources Azure (et plus particulièrement pour ce scénario, l’importation de runbooks dans le compte Automation).

4. Dans votre compte Automation, sélectionnez le groupe Worker hybride créé précédemment et [spécifiez un compte d’identification](automation-hrw-run-runbooks.md#runas-account) pour le groupe Worker hybride, puis choisissez la ressource d’informations d’identification que vous avez créée. Cela garantit que le runbook de synchronisation peut exécuter des commandes Git. 
5. Démarrez le runbook **Sync-LocalGitFolderToAutomationAccount**, fournissez les valeurs de paramètre d’entrée suivantes requises puis, dans le panneau **Démarrer le runbook**, sous l’option **Paramètres d’exécution**, sélectionnez l’option **Worker hybride**, puis sélectionnez dans la liste déroulante le groupe Worker hybride que vous avez créé précédemment pour ce scénario :
    * *ResourceGroupName* : nom du groupe de ressources associé à votre compte Automation
    * *AutomationAccountName* : nom de votre compte Automation
    * *GitPath* : dossier ou fichier local sur le Runbook Worker hybride dans lequel Git est configuré pour extraire les dernières modifications

    Le dossier Git local est alors synchronisé sur l’ordinateur Worker hybride, puis les fichiers .ps1 sont importés du répertoire source dans le compte Automation.

7. Affichez les détails d’un résumé de travail du runbook en le sélectionnant dans le panneau **Runbooks** de votre compte Automation. Sélectionnez ensuite la vignette **Travaux**. Vérifiez qu’il a abouti en sélectionnant la vignette **Tous les journaux** et en examinant le flux de journal détaillé.  

## <a name="next-steps"></a>Étapes suivantes

-  Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
-  Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
