---
title: "Ajouter des runbooks Azure Automation à des plans de récupération dans Azure Site Recovery | Microsoft Docs"
description: "Découvrez comment Azure Site Recovery peut vous aider à étendre des plans de récupération à l’aide d’Azure Automation. Découvrez comment effectuer des tâches complexes lors de la récupération sur Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 986c3b62426949f1e4c2009aabbfec2f1130f821
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Ajouter des runbooks Azure Automation à des plans de récupération
Cet article décrit comment Azure Site Recovery s’intègre avec Azure Automation pour vous aider à étendre vos plans de récupération. Des plans de récupération peuvent orchestrer la récupération de machines virtuelles protégées par Site Recovery. Les plans de récupération fonctionnent aussi bien pour la réplication sur un cloud secondaire que pour la réplication sur Azure. Ils aident également à rendre la récupération **toujours précise**, **répétable** et **automatisée**. Si vous basculez de vos machines virtuelles vers Azure, l’intégration avec Azure Automation étend vos plans de récupération. Vous pouvez l’utiliser pour exécuter des runbooks qui offrent des tâches d’automatisation puissantes.

Si vous ne connaissez pas Azure Automation, vous pouvez vous [inscrire](https://azure.microsoft.com/services/automation/) et [télécharger des exemples de scripts](https://azure.microsoft.com/documentation/scripts/). Pour plus d’informations et pour savoir comment orchestrer la récupération vers Azure à l’aide de [plans de récupération](./site-recovery-create-recovery-plans.md), voir [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/).

Cet article explique comment intégrer des runbooks Azure Automation dans vos plans de récupération. Nous utilisons des exemples pour automatiser des tâches de base qui nécessitaient auparavant une intervention manuelle. Nous décrivons également comment convertir une récupération multiétape en une action de récupération d’un simple clic.

## <a name="customize-the-recovery-plan"></a>Personnaliser un plan de récupération
1. Accédez au panneau des ressources de plan de récupération **Site Recovery**. Pour cet exemple, deux machines virtuelles ont été ajoutées au plan de récupération pour la récupération. Pour commencer l’ajout d’un runbook, cliquez sur l’onglet **Personnaliser**.

    ![Cliquer sur le bouton Personnaliser](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. Cliquez avec le bouton droit sur **Group 1: Start**, puis sélectionnez **Ajouter une action postérieure**.

    ![Cliquer avec le bouton droit sur Group 1: Start, puis ajouter une action postérieure](media/site-recovery-runbook-automation-new/customize-rp.png)

3. Cliquez sur **Choisir un script**.

4. Sur le panneau **Mettre à jour l'action**, nommez le script **Hello World**.

    ![Panneau Mettre à jour l'action](media/site-recovery-runbook-automation-new/update-rp.png)

5. Choisissez un nom de compte Automation.
    >[!NOTE]
    > Le compte Automation peut se trouver dans n’importe quelle région Azure. Le compte Automation doit être dans le même abonnement que le coffre Azure Site Recovery.

6. Dans votre compte Automation, sélectionnez un runbook. Ce runbook est le script qui s’exécute pendant l’exécution du plan de récupération après la récupération du premier groupe.

7. Pour enregistrer le script, cliquez sur **OK**. Le script est ajouté à **Group 1: Post-steps**.

    ![Action postérieure Group 1:Start](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>Considérations relatives à l’ajout d’un script

* Pour accéder aux options de **suppression d’une étape** ou de **mise à jour du script**, cliquez avec le bouton droit sur le script.
* Un script peut s’exécuter sur Azure pendant le basculement d’un ordinateur local vers Azure. Il peut également s’exécuter sur Azure en tant que script de site principal avant arrêt, durant une restauration automatique d’Azure sur un ordinateur local.
* Lorsqu’un script s’exécute, il injecte un contexte de plan de récupération. L’exemple suivant montre une variable de contexte :

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    Le tableau suivant répertorie le nom et la description de chaque variable dans le contexte.

    | **Nom de la variable** | **Description** |
    | --- | --- |
    | RecoveryPlanName |Nom du plan en cours d’exécution. Cette variable vous aide à prendre des mesures différentes selon le nom du plan de récupération. Vous pouvez également réutiliser le script. |
    | FailoverType |Indique si le basculement est un test, et s’il est ou non planifié. |
    | FailoverDirection |Spécifie si la récupération est opérée sur un site principal ou secondaire. |
    | GroupID |Identifie le numéro de groupe dans le plan de récupération lorsque celui-ci est en cours d'exécution. |
    | VmMap |Tableau de toutes les machines virtuelles dans le groupe. |
    | Clé VMMap |Clé unique (GUID) pour chaque machine virtuelle. Le cas échéant, elle est identique à l’ID VMM (Virtual Machine Manager) (VMM) Azure de la machine virtuelle. |
    | SubscriptionId |ID de l’abonnement Azure dans lequel la machine virtuelle a été créée. |
    | RoleName |Nom de la machine virtuelle Azure en cours de récupération. |
    | CloudServiceName |Nom du service cloud Azure sous lequel la machine virtuelle a été créée. |
    | ResourceGroupName|Nom du groupe de ressources Azure sous lequel la machine virtuelle a été créée. |
    | RecoveryPointId|Horodatage du moment de récupération de la machine virtuelle. |

* Veillez à ce le compte Automation dispose des modules suivants :
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

Les versions de tous les modules doivent être compatibles. Un méthode simple pour s’assurer que tous les modules sont compatibles consiste à utiliser les dernières versions de ceux-ci.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>Accéder à toutes les machines virtuelles de VmMap dans une boucle
Pour effectuer une boucle sur toutes les machines virtuelles de Microsoft VMMap, utilisez le code suivant :

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> Les valeurs de nom de groupe de ressources et de nom de rôle sont vides quant le script est une action antérieure pour un groupe de démarrage. Les valeurs sont renseignées uniquement si la machine virtuelle de ce groupe réussit le basculement. Le script est une action postérieure du groupe de démarrage.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>Utiliser le même runbook Automation avec plusieurs plans de récupération

Vous pouvez utiliser un même script dans plusieurs plans de récupération à l’aide de variables externes. Vous pouvez utiliser des [variables Azure Automation](../automation/automation-variables.md) pour stocker des paramètres que vous pouvez transmettre pour l’exécution d’un plan de récupération. En ajoutant le nom de plan de récupération en tant que préfixe à la variable, vous pouvez créer des variables individuelles pour chaque plan de récupération. Ensuite, utilisez les variables en tant que paramètres. Vous pouvez modifier un paramètre sans changer le script, mais en modifiant la façon dont le script fonctionne.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Utiliser une variable chaîne simple dans un script de runbook

Dans cet exemple, un script accepte l’entrée d’un groupe de sécurité réseau et l’applique aux machines virtuelles d’un plan de récupération.

Pour que le script détecte le plan de récupération en cours d’exécution, utilisez le contexte de plan de récupération :

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

Pour appliquer un groupe de sécurité réseau existant, vous devez connaître le nom du groupe de sécurité réseau et le nom du groupe de ressources du groupe de sécurité réseau. Utilisez ces variables en tant qu’entrées pour les scripts de plan de récupération. Pour ce faire, créez deux variables dans les ressources du compte Automation. Ajoutez le nom du plan de récupération pour lequel vous créez les paramètres en tant que préfixe au nom de la variable.

1. Créez une variable pour stocker le nom du groupe de sécurité réseau. Ajoutez un préfixe au nom de la variable en utilisant le nom du plan de récupération.

    ![Créer la variable du nom de groupe de sécurité réseau](media/site-recovery-runbook-automation-new/var1.png)

2. Créez une variable pour stocker le nom du groupe de ressources du groupe de sécurité réseau. Ajoutez un préfixe au nom de la variable en utilisant le nom du plan de récupération.

    ![Créer un nom de groupe de ressources de groupe de sécurité réseau](media/site-recovery-runbook-automation-new/var2.png)


3.  Dans le script, utilisez le code de référence suivant pour obtenir les valeurs variables :

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Utilisez les variables figurant dans le runbook pour appliquer le groupe de sécurité réseau à l’interface réseau de la machine virtuelle basculée :

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

Pour chaque plan de récupération, créez des variables indépendantes afin de pouvoir réutiliser le script. Ajoutez un préfixe en utilisant le nom du plan de récupération. Pour un script complet de bout en bout pour ce scénario, voir [Ajouter une adresse IP publique et un groupe de sécurité réseau aux machines virtuelles pendant le test de basculement d’un plan de récupération Site Recovery](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee).


### <a name="use-a-complex-variable-to-store-more-information"></a>Utiliser une variable complexe pour stocker plus d’informations

Imaginez un scénario dans le cadre duquel vous souhaitez disposer d’un script unique pour activer une adresse IP publique sur des machines virtuelles spécifiques. Dans un autre scénario, vous pourriez vouloir appliquer différents groupes de sécurité réseau à différentes machines virtuelles (au lieu de toutes). Vous pouvez créer un script réutilisable pour tout plan de récupération. Chaque plan de récupération peut avoir un nombre variable de machines virtuelles. Par exemple, une récupération SharePoint a deux serveurs frontaux. Une application métier n’a qu’un seul serveur frontal. Vous ne pouvez pas créer des variables distinctes pour chaque plan de récupération. 

Dans l’exemple suivant, nous utilisons une nouvelle technique et créons une [variable complexe](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396) dans les ressources du compte Azure Automation. Pour ce faire, spécifiez plusieurs valeurs. Vous devez utiliser Azure PowerShell pour suivre les étapes suivantes :

1. Dans PowerShell, connectez-vous à votre abonnement Azure :

    ```
    login-azurermaccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Pour stocker les paramètres, créez la variable complexe en utilisant le nom du plan de récupération :

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. Dans cette variable complexe, **VMDetails** est l’ID de machine virtuelle de la machine virtuelle protégée. Pour obtenir l’ID de machine virtuelle, dans le portail Azure, affichez les propriétés de la machine virtuelle. La capture d’écran suivante affiche une variable qui stocke les détails de deux machines virtuelles :

    ![Utilisez l’ID de machine virtuelle en tant que GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Utilisez cette variable dans votre runbook. Si le GUID de machine virtuelle indiqué figure dans le contexte du plan de récupération, appliquez le groupe de sécurité réseau à la machine virtuelle :

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. Dans votre runbook, parcourez les machines virtuelles du contexte du plan de récupération. Vérifiez l’existence de la machine virtuelle dans **$VMDetailsObj**. Si la variable existe, accédez à ses propriétés pour appliquer le groupe de sécurité réseau :

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Vous pouvez utiliser le même script pour différents plans de récupération. Entrez des paramètres différents en stockant la valeur correspondant à un plan de récupération dans différentes variables.

## <a name="sample-scripts"></a>Exemples de scripts

Pour déployer les exemples de scripts sur votre compte Automation, cliquez sur le bouton **Déployer sur Azure**.

[![Déploiement sur Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Pour un autre exemple, regardez la vidéo suivante. Elle montre comment récupérer une application WordPress à deux niveaux dans Azure :


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>Ressources supplémentaires
* [Documentation Automation](../automation/automation-create-runas-account.md)
* [Vue d’ensemble d’Azure Automation](http://msdn.microsoft.com/library/azure/dn643629.aspx "Vue d’ensemble d’Azure Automation")
* [Ressources de script pour les professionnels de l'informatique](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Ressources de script pour les professionnels de l'informatique")

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus](site-recovery-failover.md) sur l’exécution des basculements.
