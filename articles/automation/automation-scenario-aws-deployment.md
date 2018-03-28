---
title: Automatisation du déploiement d’une machine virtuelle dans Amazon Web Services
description: Cet article explique comment utiliser Azure Automation pour automatiser la création d’une machine virtuelle Amazon Web Services
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 371510bf335bda2e2475e6ba3f5f74ee2117d9a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Scénario Azure Automation – Approvisionner une machine virtuelle AWS
Dans cet article, vous allez découvrir comment provisionner une machine virtuelle de votre abonnement AWS (Amazon Web Services) à l’aide d’Azure Automation et comment lui attribuer un nom spécifique (opération appelée « balisage » dans AWS).

## <a name="prerequisites"></a>Prérequis

Pour les besoins de cet article, vous devez disposer d’un compte Azure Automation et d’un abonnement AWS. Pour plus d’informations sur la création d’un compte Azure Automation et sur sa configuration avec les informations d’identification de votre abonnement AWS, consultez l’article [Authentification des Runbooks avec Amazon Web Services](automation-config-aws-account.md). Vous devez au préalable créer ou mettre à jour ce compte avec les informations d’identification de votre abonnement AWS, car vous ferez référence à ce compte dans les étapes suivantes.

## <a name="deploy-amazon-web-services-powershell-module"></a>Déployer le module PowerShell Amazon Web Services
Votre runbook de provisionnement de machine virtuelle utilise le module PowerShell AWS pour exécuter cette tâche. Pour ajouter le module à votre compte Automation qui est configuré avec les informations d’identification de votre abonnement AWS, procédez comme suit.  

1. Ouvrez votre navigateur web et accédez à [PowerShell Gallery](http://www.powershellgallery.com/packages/AWSPowerShell/), puis cliquez sur le bouton **Deploy to Azure Automation** (Déployer sur Azure Automation).<br><br> ![Importation du module PS AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Vous accédez alors à la page de connexion Azure. Après vous être authentifié, vous êtes dirigé vers le portail Azure dans la page suivante :<br><br> ![Page Importer le module](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Sélectionnez le compte Automation à utiliser et cliquez sur **OK** pour lancer le déploiement.

   > [!NOTE]
   > Les applets de commande sont importées en même temps qu’un module PowerShell dans Azure Automation, et ces activités n’apparaissent pas tant que l’importation du module et l’extraction des applets de commande ne sont pas terminées. Ce processus peut prendre plusieurs minutes.  
   > <br>

1. Dans le portail Azure, ouvrez votre compte Automation référencé à l’étape 3.
2. Cliquez sur la mosaïque **Ressources** et, dans le volet **Ressources**, sélectionnez la mosaïque **Modules**.
3. Dans la page **Modules**, le module **AWSPowerShell** apparaît dans la liste.

## <a name="create-aws-deploy-vm-runbook"></a>Créer un runbook de machine virtuelle de déploiement AWS
Une fois le module PowerShell AWS déployé, vous pouvez créer un runbook pour automatiser le provisionnement d’une machine virtuelle dans AWS au moyen d’un script PowerShell. La procédure ci-dessous montre comment tirer parti d’un script PowerShell natif dans Azure Automation.  

> [!NOTE]
> Pour plus d’options et d’informations concernant ce script, visitez [PowerShell Gallery](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Téléchargez le script PowerShell New-AwsVM à partir de PowerShell Gallery en ouvrant une session PowerShell et en tapant ce qui suit :<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. Dans le portail Azure, ouvrez votre compte Automation et sélectionnez **Runbooks** dans la section **Automatisation de processus**, sur la gauche.  
3. Dans la page **Runbooks**, sélectionnez **Ajouter un Runbook**.
4. Dans le volet **Ajouter un Runbook**, sélectionnez **Création rapide** (Créer un Runbook).
5. Dans le volet de propriétés du **Runbook**, saisissez un nom dans la zone Nom de votre runbook et, dans la liste déroulante **Type de Runbook**, sélectionnez **PowerShell**. Cliquez ensuite sur **Créer**.<br><br> ![Volet Créer un Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Lorsque la page Modifier le Runbook PowerShell apparaît, copiez et collez le script PowerShell dans le canevas de création de runbook.<br><br> ![Script PowerShell de runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Notez les points suivants au moment d’utiliser l’exemple de script PowerShell :
    > 
    > * Le runbook contient un certain nombre de valeurs de paramètres par défaut. Évaluez toutes les valeurs par défaut et procédez aux mises à jour nécessaires.
    > * Si vous avez stocké vos informations d’identification AWS comme ressource d’informations d’identification sous un autre nom que **AWScred**, vous devez mettre à jour en conséquence la ligne 57 du script.  
    > * Quand vous utilisez les commandes de l’interface de ligne de commande AWS dans PowerShell, plus particulièrement avec cet exemple de runbook, vous devez spécifier la région AWS. Sinon, les applets de commande échouent. Pour plus d’informations, consultez la rubrique AWS [Specify AWS Region](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Spécifier la région AWS) dans le document AWS Tools for PowerShell (Outils AWS pour PowerShell).  
    >

7. Pour récupérer une liste de noms d’images à partir de votre abonnement AWS, lancez PowerShell ISE et importez le module PowerShell AWS. Authentifiez-vous auprès d’AWS en remplaçant **Get-AutomationPSCredential** dans votre environnement ISE par **AWScred = Get-Credential**. Vous êtes alors invité à entrer vos informations d’identification. Vous pouvez fournir votre ID de clé d’accès (**Access Key ID**) comme nom d’utilisateur et la clé d’accès secrète (**Secret Access Key**) comme mot de passe. Prenons l'exemple suivant :  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    Voici les résultats qui sont retournés :<br><br>
   ![Obtenir des images AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copiez et collez celui relatif aux noms d’images dans une variable Automation référencée dans le runbook par **$InstanceType**. Sachant que cet exemple s’appuie sur l’abonnement hiérarchisé AWS gratuit, utilisez **t2.micro** dans votre exemple de runbook.  
9. Enregistrez le runbook, puis cliquez sur **Publier** pour publier le runbook, puis sur **Oui** quand vous y êtes invité.

### <a name="testing-the-aws-vm-runbook"></a>Test du runbook de machine virtuelle AWS
Avant de procéder au test du runbook, vous devez vérifier plusieurs points. Plus précisément :  

* Une ressource permettant de s’authentifier auprès d’AWS nommée **AWScred** a été créée ou le script a été mis à jour pour référencer le nom de votre ressource d’informations d’identification.    
* Le module PowerShell AWS a été importé dans Azure Automation  
* Un runbook a été créé et les valeurs de paramètre ont été vérifiées et mises à jour lorsque c’était nécessaire  
* Les options **Journaliser les enregistrements détaillés** et éventuellement **Journaliser les informations de progression** sous le paramètre de runbook **Journalisation et suivi** ont été définies sur **Activé**.<br><br> ![Journalisation et suivi de runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Votre objectif étant de démarrer le runbook, cliquez sur **Démarrer**, puis sur **OK** à l’ouverture du volet Démarrer le runbook.
2. Dans le volet Démarrer le Runbook, fournissez un nom de machine virtuelle (**VMname**). Acceptez les valeurs par défaut des autres paramètres que vous avez préconfigurés plus tôt dans le script. Cliquez sur **OK** pour démarrer la tâche du runbook.<br><br> ![Démarrer un runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Fermez ce panneau.
4. Vous pouvez examiner la progression de la tâche et consulter les **flux** de sortie en sélectionnant la vignette **Tous les journaux** dans la page de tâche du runbook.<br><br> ![Sortie de flux](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Pour vérifier que la machine virtuelle est en cours d’approvisionnement, connectez-vous à la console de gestion AWS si ce n’est pas déjà fait.<br><br> ![Machine virtuelle déployée par la console AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

