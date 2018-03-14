---
title: "Prise en main d’Azure Automation DSC | Microsoft Docs"
description: "Explication et exemples de tâches les plus courantes dans Azure Automation Desired State Configuration (DSC)"
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;gwallace
ms.openlocfilehash: ff23b4f5022597763728ccf301966c34c5423b34
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>Prise en main d’Azure Automation DSC
Cette rubrique explique comment effectuer les tâches les plus courantes avec Azure Automation Desired State Configuration (DSC), comme la création, l’importation et la compilation de configurations, l’intégration des ordinateurs à gérer et l’affichage des rapports. Pour une vue d’ensemble d’Azure Automation DSC, consultez [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview.md). Pour la documentation DSC, consultez l’article [Vue d’ensemble de la fonctionnalité Desired State Configuration de Windows PowerShell](https://msdn.microsoft.com/PowerShell/dsc/overview).

Cet article fournit des instructions détaillées sur l’utilisation d’Azure Automation DSC. Si vous souhaitez obtenir un exemple d’environnement qui est déjà configuré sans suivre les étapes décrites dans cet article, vous pouvez utiliser le modèle Resource Manager suivant : ce modèle configure un environnement Azure Automation DSC complet, comprenant une machine virtuelle Azure gérée par Azure Automation DSC.

## <a name="prerequisites"></a>Prérequis
Pour exécuter les exemples de cet article, vous devez disposer des éléments suivants :

* Un compte Azure Automation. Pour obtenir des instructions sur la création d’un compte d’identification Azure Automation, consultez [Authentifier des Runbooks avec un compte d’identification Azure](automation-sec-configure-azure-runas-account.md).
* Une machine virtuelle Azure Resource Manager (non classique) exécutant Windows Server 2008 R2 ou version ultérieure. Pour obtenir des instructions sur la création d’une machine virtuelle, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Création d’une configuration DSC
Vous créez une [configuration DSC](https://msdn.microsoft.com/powershell/dsc/configurations) simple qui garantit la présence ou l’absence de la fonctionnalité Windows **Serveur web** (IIS), selon le mode d’attribution des nœuds.

1. Démarrez Windows PowerShell ISE (ou n’importe quel éditeur de texte).
2. Saisissez le texte suivant :
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
   
            }
        }
   
        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
   
            }
        }
        }
    ```
3. Enregistrez le fichier sous le nom `TestConfig.ps1`.

Cette configuration appelle une ressource dans chaque bloc de nœuds, la [ressource WindowsFeature](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource), qui garantit la présence ou l’absence de la fonctionnalité **Serveur web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importation d’une configuration dans Azure Automation
Ensuite, vous importez la configuration dans le compte Automation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, sélectionnez **Configurations DSC** sous **Gestion de la configuration**.
4. Sur la page **Configurations DSC**, cliquez sur **+ Ajouter une configuration**.
5. Sur la page **Importer la configuration**, recherchez le fichier `TestConfig.ps1` sur votre ordinateur.
   
    ![Capture d’écran du panneau **Importer la configuration**](./media/automation-dsc-getting-started/AddConfig.png)
6. Cliquez sur **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Affichage d’une configuration dans Azure Automation
Après avoir importé une configuration, vous pouvez l’afficher dans le portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, sélectionnez **Configurations DSC** sous **Gestion de la configuration**.
4. Sur la page **Configurations DSC**, cliquez sur **TestConfig** (il s’agit du nom de la configuration que vous avez importée dans la procédure précédente).
5. Sur la page **Configuration de TestConfig**, cliquez sur **Afficher la source de configuration**.
   
    ![Capture d’écran du panneau Configuration de TestConfig](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    Dans le panneau **Source de configuration de TestConfig** qui s’affiche, vous accédez au code PowerShell de configuration.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilation d’une configuration dans Azure Automation
Avant de pouvoir appliquer un état souhaité à un nœud, vous devez compiler une configuration DSC définissant cet état dans une ou plusieurs configurations de nœuds (documents MOF) et la placer sur le serveur Pull Automation DSC. Pour obtenir une description plus détaillée de la compilation des configurations dans Azure Automation DSC, consultez [Compilation de configurations dans Azure Automation DSC](automation-dsc-compile.md). Pour plus d’informations sur la compilation des configurations, consultez [Configurations DSC](https://msdn.microsoft.com/PowerShell/DSC/configurations).

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Configurations DSC** sous **Gestion de la configuration**.
4. Sur la page **Configurations DSC**, cliquez sur **TestConfig** (le nom de la configuration que vous avez précédemment importée).
5. Sur la page **Configuration de TestConfig**, cliquez sur **Compiler**, puis sur **Oui**. Une tâche de compilation démarre.
   
    ![Capture d’écran de la page Configuration de TestConfig mettant en évidence le bouton de compilation](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Lorsque vous compilez une configuration dans Azure Automation, tous les fichiers MOF de configuration de nœuds créés sont automatiquement déployés sur le serveur Pull.
> 
> 

## <a name="viewing-a-compilation-job"></a>Affichage d’une tâche de compilation
Après avoir démarré une compilation, vous pouvez l’afficher sur la vignette **Travaux de compilation** du panneau **Configuration**. La mosaïque **Tâches de compilation** affiche les tâches en cours d’exécution, terminées et en échec. Lorsque vous ouvrez un volet de tâche de compilation, vous obtenez des informations sur cette tâche, notamment les erreurs ou les avertissements rencontrés, les paramètres d’entrée utilisés dans la configuration et les journaux de compilation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Configurations DSC** sous **Gestion de la configuration**.
4. Sur la page **Configurations DSC**, cliquez sur **TestConfig** (le nom de la configuration que vous avez précédemment importée).
5. Sous **Tâches de compilation**, sélectionnez la tâche de compilation que vous souhaitez afficher. Une page **Tâche de compilation** s’ouvre en indiquant la date de démarrage de la tâche de compilation.
   
    ![Capture d’écran de la page Tâche de compilation](./media/automation-dsc-getting-started/CompilationJob.png)
6. Cliquez sur n’importe quelle mosaïque de la page **Tâche de compilation** pour afficher des détails supplémentaires sur la tâche.

## <a name="viewing-node-configurations"></a>Affichage des configurations de nœuds
La réussite d’une tâche de compilation a pour effet de créer une ou plusieurs configurations de nœud. Une configuration de nœud est un document MOF déployé sur le serveur Pull et prêt à être extrait et appliqué par un ou plusieurs nœuds. Vous pouvez afficher les configurations de nœud de votre compte Automation dans le panneau **Configurations de nœuds DSC** . Une configuration de nœud possède un nom au format *NomConfiguration*.*NomNœud*.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, cliquez sur **Toutes les ressources** , puis sur le nom de votre compte Automation.
3. Dans le panneau **Compte Automation**, cliquez sur **Configurations de nœuds DSC**.
   
    ![Capture d’écran du panneau Configurations de nœuds DSC](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>Intégration d’une machine virtuelle Azure pour la gérer avec Azure Automation DSC
Azure Automation DSC vous permet de gérer vos machines virtuelles Azure (via les modèles de déploiement classique et Resource Manager), vos machines virtuelles locales, vos machines virtuelles Linux, vos machines virtuelles AWS et vos ordinateurs physiques en local. Dans cet article, vous allez n’intégrer que des machines virtuelles Azure Resource Manager. Pour plus d’informations sur l’intégration d’autres types d’ordinateur, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>Pour intégrer une machine virtuelle Azure Resource Manager pour la gérer via Azure Automation DSC, procédez comme suit.
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Nœuds DSC** sous **Gestion de la configuration**.
4. Sur la page **Nœuds DSC**, cliquez sur **Ajouter une machine virtuelle Azure**.
   
    ![Capture d’écran de la page Nœuds DSC mettant en évidence le bouton Ajouter des machines virtuelles Azure](./media/automation-dsc-getting-started/OnboardVM.png)
5. Sur la page Machines virtuelles, sélectionnez votre machine virtuelle. Sur la page **Ajouter des machines virtuelles Azure**, cliquez sur **Sélectionner les machines virtuelles à intégrer**.
6. Cliquez sur **Connecter**.
   
   > [!IMPORTANT]
   > Il doit s’agir d’une machine virtuelle Azure Resource Manager exécutant Windows Server 2008 R2 ou version ultérieure.
   > 
   > 
1. Sur la page **Inscription**, entrez le nom de la configuration de nœud que vous souhaitez appliquer à la machine virtuelle dans la zone **Nom de la configuration de nœuds**. Ce nom doit correspondre exactement au nom d’une configuration de nœud dans le compte Automation. Vous n’êtes pas obligé de fournir un nom à ce stade. Vous pouvez modifier la configuration de nœud assignée une fois l’intégration du nœud effectuée.
   Cochez la case **Redémarrer le nœud si nécessaire**, puis cliquez sur **OK**.
   
    ![Capture d’écran du panneau Inscription](./media/automation-dsc-getting-started/RegisterVM.png)
   
    La configuration de nœud spécifiée s’applique à la machine virtuelle selon les intervalles spécifiés par la **Fréquence du mode de configuration**. La machine virtuelle recherche les mises à jour de la configuration du nœud selon les intervalles spécifiés par la **Fréquence d’actualisation**. Pour plus d’informations sur la façon dont ces valeurs sont utilisées, consultez [Configuration du Gestionnaire de configuration local](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).
9. Dans le panneau **Ajouter des machines virtuelles Azure**, cliquez sur **Créer**.

Azure lance le processus d’intégration de la machine virtuelle. Lorsqu’il est terminé, la machine virtuelle s’affiche dans le panneau **Nœuds DSC** du compte Automation.

## <a name="viewing-the-list-of-dsc-nodes"></a>Affichage de la liste des nœuds DSC
Vous pouvez utiliser le panneau **Nœuds DSC** pour afficher la liste de tous les ordinateurs qui ont été intégrés afin d’être gérés dans votre compte Automation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Nœuds DSC**.

## <a name="viewing-reports-for-dsc-nodes"></a>Affichage des rapports de nœuds DSC
Chaque fois qu’azure Automation DSC effectue une vérification de cohérence sur un nœud géré, le nœud envoie un rapport d’état sur le serveur Pull. Vous pouvez consulter ces rapports sur la page correspondant à ce nœud.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Nœuds DSC**.
4. Dans la liste **Nœuds DSC**, sélectionnez le nœud que vous souhaitez afficher.
5. Sur la page **Nœud**, cliquez sur le rapport que vous souhaitez afficher sous **Rapports**.
   
    ![Capture d’écran du panneau Rapport](./media/automation-dsc-getting-started/NodeReport.png)

Le panneau d’un rapport vous permet d’accéder aux informations d’état suivantes pour vous permettre d’effectuer la vérification de cohérence correspondante :

* L’état du rapport : si le nœud est « Conforme », si la configuration a « Échoué » ou si le nœud est « Non conforme » (lorsque le nœud est en mode **applyandmonitor** et que l’ordinateur ne se trouve pas à l’état souhaité).
* L’heure de début de la vérification de cohérence.
* Le temps total d’exécution de la vérification de cohérence.
* Le type de vérification de cohérence.
* Toute erreur rencontrée, avec code d’erreur et message d’erreur. 
* Toutes les ressources DSC utilisées dans la configuration, ainsi que l’état de chaque ressource (si le nœud est à l’état souhaité pour cette ressource) : vous pouvez cliquer sur chaque ressource pour obtenir des informations plus détaillées sur cette ressource.
* Le nom, l’adresse IP et le mode de configuration du nœud.

Vous pouvez également cliquer sur **Afficher le rapport brut** pour afficher les données que le nœud envoie effectivement au serveur. Pour plus d’informations sur l’utilisation de ces données, consultez la page [Utilisation d’un serveur de rapports DSC](https://msdn.microsoft.com/powershell/dsc/reportserver).

Vous risquez de devoir attendre un certain temps après l’intégration d’un nœud pour pouvoir accéder au premier rapport. Vous devrez peut-être attendre jusqu’à 30 minutes pour obtenir le premier rapport après avoir effectué l’intégration.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Réaffectation d’un nœud à une configuration de nœud différent
Vous pouvez attribuer un nœud à une configuration de nœud différente de celle qui lui a été initialement affectée.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Nœuds DSC**.
4. Sur la page **Nœuds DSC**, cliquez sur le nom du nœud que vous souhaitez réaffecter.
5. Sur la page de ce nœud, cliquez sur **Assign node** (Attribuer le nœud).
   
    ![Capture d’écran du panneau Nœud mettant en évidence le bouton Attribuer un nœud](./media/automation-dsc-getting-started/AssignNode.png)
6. Sur la page **Affecter une configuration de nœuds**, sélectionnez la configuration de nœuds à laquelle vous souhaitez affecter le nœud, puis cliquez sur **OK**.
   
    ![Capture d’écran du panneau Attribuer une configuration de nœuds](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Annulation de l’inscription d’un nœud
Si vous ne souhaitez plus qu’un nœud soit géré par Azure Automation DSC, vous pouvez annuler son inscription.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sur la gauche, cliquez sur **Toutes les ressources**, puis sur le nom de votre compte Automation.
3. Sur la page **Compte Automation**, cliquez sur **Nœuds DSC**.
4. Sur la page **Nœuds DSC**, cliquez sur le nom du nœud dont vous souhaitez annuler l’inscription.
5. Sur la page de ce nœud, cliquez sur **Annuler l’inscription**.
   
    ![Capture d’écran du panneau Nœud mettant en évidence le bouton Annuler l’inscription](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Articles connexes
* [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview.md)
* [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md)
* [Vue d’ensemble d’Azure Automation DSC](https://msdn.microsoft.com/powershell/dsc/overview)
* [Applets de commande Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Tarification d’Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)

