---
title: Ressources de certificats dans Azure Automation | Microsoft Docs
description: "Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’y accéder par des Runbooks ou des configurations DSC pour s’authentifier auprès des ressources Azure et tierces.  Cet article présente les certificats et leur utilisation dans la création textuelle et graphique."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 55ad7d4b2643b448801f41aea95f3505d9fcd78f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="certificate-assets-in-azure-automation"></a>Ressources de certificats dans Azure Automation

Les certificats peuvent être stockés en toute sécurité dans Azure Automation afin d’être accessibles par des Runbooks ou des configurations DSC à l’aide de l’activité **Get-AzureRmAutomationCertificate** pour les ressources Azure Resource Manager. Cette méthode vous permet de créer des Runbooks et des configurations DSC qui utilisent des certificats pour l’authentification ou les ajoute aux ressources Azure ou tierces.

> [!NOTE] 
> Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l'aide d'une clé unique, générée pour chaque compte Automation. Cette clé est chiffrée par un certificat principal et stockée dans Azure Automation. Avant de stocker une ressource sécurisée, la clé du compte Automation est déchiffrée à l'aide du certificat principal, puis utilisée pour chiffrer la ressource.
> 

## <a name="azurerm-powershell-cmdlets"></a>Applets de commande AzureRM PowerShell
Pour AzureRM, les applets de commande du tableau suivant sont utilisées pour créer et gérer les ressources d’informations d’identification Automation avec Windows PowerShell.  Elles sont fournies avec le [module AzureRM.Automation](/powershell/azure/overview), utilisable dans les runbooks Automation et les configurations DSC.

|Applets de commande|Description|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Récupère des informations sur un certificat à utiliser dans un Runbook ou dans une configuration DSC. Vous pouvez uniquement récupérer le certificat lui-même à partir de l’activité Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Crée un nouveau certificat dans Azure Automation.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Supprime un certificat dans Azure Automation.|Crée un nouveau certificat dans Azure Automation.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|Définit les propriétés d’un certificat existant, y compris le téléchargement du fichier de certificat et le mot de passe d’un fichier .pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Charge un certificat de service pour le service cloud spécifié.|

## <a name="activities"></a>Activités
Les activités du tableau suivant sont utilisées pour accéder aux certificats dans un Runbook et dans des configurations DSC.

| Activités | Description |
|:---|:---|
|Get-AutomationCertificate|Obtient un certificat à utiliser dans un Runbook ou dans une configuration DSC. Retourne un objet [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.x509certificate2.aspx).|

> [!NOTE] 
> Évitez d’utiliser des variables dans le paramètre –Name de **Get-AutomationCertificate** dans un Runbook ou une configuration DSC, car cela peut compliquer la découverte de dépendances entre les Runbooks ou la configuration DSC et les variables Automation au moment de la conception.

## <a name="python2-functions"></a>Fonctions Python2

La fonction dans le tableau suivant est utilisée pour accéder aux certificats dans un runbook Python2.

| Fonction | Description |
|:---|:---|
| automationassets.get_automation_certificate | Récupère des informations sur les ressources d’un certificat. |

> [!NOTE]
> Vous devez importer le module **automationassets** au début de votre runbook Python afin d’accéder aux fonctions des ressources.


## <a name="creating-a-new-certificate"></a>Création d’un certificat

Lorsque vous créez un certificat, vous téléchargez un fichier .cer ou .pfx dans Azure Automation. Si vous marquez le certificat comme exportable, vous pouvez également le transférer du magasin de certificats Azure Automation. S’il n’est pas exportable, il peut uniquement être utilisé pour la signature dans le Runbook ou la configuration DSC.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Pour créer un certificat avec le portail Azure

1. À partir de votre compte Automation, cliquez sur le panneau **Ressources** afin d’ouvrir le panneau **Ressources**.
1. Cliquez sur le panneau **Certificats** pour ouvrir le panneau **Certificats**.
1. Cliquez sur **Ajouter un certificat** en haut du panneau.
2. Tapez un nom pour le certificat dans la zone **Nom** .
2. Cliquez sur **Sélectionner un fichier** sous **Charger un fichier de certificat** pour rechercher un fichier .cer ou .pfx.  Si vous sélectionnez un fichier .pfx, spécifiez un mot de passe et s’il est autorisé à être exporté.
1. Cliquez sur **Créer** pour enregistrer la nouvelle ressource de certificat.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Pour créer un certificat avec Windows PowerShell

L’exemple suivant démontre la création d’un certificat Automation et sont marquage comme exportable. Cette opération importe un fichier pfx existant.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Utilisation d’un certificat

Vous devez utiliser l’activité **Get-AutomationCertificate** pour utiliser un certificat. Vous ne pouvez pas utiliser l’applet de commande [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) dans la mesure où elle retourne des informations sur la ressource de certificat, mais pas le certificat lui-même.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel

L’exemple de code suivant montre comment ajouter un certificat à un service cloud dans un Runbook. Dans cet exemple, le mot de passe est récupéré à partir d’une variable Automation chiffrée.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Exemple de Runbook graphique

Pour ajouter une commande **Get-AutomationCerticificate** à un Runbook graphique, vous devez cliquer avec le bouton droit sur le certificat dans le volet Bibliothèque de l’éditeur graphique et sélectionner l’option **Ajouter à la zone de dessin**.

![Ajouter un certificat à la zone de dessin](media/automation-certificates/automation-certificate-add-to-canvas.png)

L’image suivante montre un exemple d’utilisation d’un certificat dans un Runbook graphique.  Il s’agit du même exemple que celui affiché plus haut pour ajouter un certificat à un service cloud à partir d’un Runbook textuel.

![Exemple de création graphique ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemple de Python2
L’exemple suivant montre comment accéder aux certificats dans les runbooks Python2.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>étapes suivantes

- Pour en savoir plus sur l’utilisation des liens pour contrôler le flux logique d’activités que votre runbook est conçu pour effectuer, consultez [Liens de création graphique](automation-graphical-authoring-intro.md#links-and-workflow). 
