---
title: Ressources de connexion dans Azure Automation
description: Les ressources de connexion dans Azure Automation contiennent les informations nécessaires pour se connecter à une application ou un service externe à partir d’un Runbook ou d’une configuration DSC. Cet article présente de façon détaillée les connexions et leur utilisation dans la création textuelle et graphique.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d319768f88a03f4736c3b69276cfb4bf82fd9100
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="connection-assets-in-azure-automation"></a>Ressources de connexion dans Azure Automation

Une ressource de connexion Automation contient les informations nécessaires pour se connecter à une application ou un service externe à partir d’un Runbook ou d’une configuration DSC. Cela peut inclure des informations requises pour l’authentification comme un nom d’utilisateur et un mot de passe en plus des informations de connexion telles qu’une URL ou un port. La valeur d’une connexion consiste à conserver toutes les propriétés de connexion à une application spécifique dans une seule ressource plutôt que de créer plusieurs variables. L’utilisateur peut modifier les valeurs pour une connexion à un seul endroit et vous pouvez transmettre le nom d’une connexion à un Runbook ou à une configuration DSC dans un seul paramètre. Les propriétés d’une connexion sont accessibles dans le Runbook ou la configuration DSC avec l’activité **Get-AutomationConnection** . 

Lorsque vous créez une connexion, vous devez spécifier un *type de connexion*. Le type de connexion est un modèle qui définit un ensemble de propriétés. La connexion définit des valeurs pour chaque propriété définie dans son type de connexion. Des types de connexion sont ajoutés à Azure Automation dans les modules d’intégration ou créés avec l’[API Azure Automation](http://msdn.microsoft.com/library/azure/mt163818.aspx) si le module d’intégration inclut un type de connexion et est importé dans votre compte Automation. Dans le cas contraire, vous devez créer un fichier de métadonnées pour indiquer le type de connexion Automation.  Pour plus d’informations à ce sujet, consultez [Modules d’intégration](automation-integration-modules.md).  

>[!NOTE]
>Les ressources sécurisées dans Azure Automation incluent les informations d'identification, les certificats, les connexions et les variables chiffrées. Ces ressources sont chiffrées et stockées dans Azure Automation à l’aide d’une clé unique générée pour chaque compte Automation. Cette clé est stockée dans Key Vault. Avant de stocker une ressource sécurisée, la clé est chargée à partir de Key Vault, puis utilisée pour chiffrer la ressource.

## <a name="windows-powershell-cmdlets"></a>Applets de commande Windows PowerShell

Les applets de commande du tableau suivant permettent de créer et de gérer les connexions Automation avec Windows PowerShell. Elles sont fournies dans le cadre du [module Azure PowerShell](/powershell/azure/overview) , utilisable dans les Runbooks Automation et les configurations DSC.

|Applet de commande|Description|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Récupère une connexion. Inclut une table de hachage avec les valeurs des champs de la connexion.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Crée une connexion.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Supprime une connexion existante.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Définit la valeur d’un champ particulier d’une connexion existante.|

## <a name="activities"></a>Activités

Les activités du tableau suivant sont utilisées pour accéder aux connexions dans un Runbook ou dans une configuration DSC.

|Activités|Description|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Obtient une connexion à utiliser. Renvoie une table de hachage avec les propriétés de la connexion.|

>[!NOTE] 
>Évitez d’utiliser des variables dans le paramètre –Name de **Get- AutomationConnection**, car cela complique la découverte des dépendances entre les runbooks ou les configurations DSC et les ressources de connexion au moment de la conception.

 
## <a name="python2-functions"></a>Fonctions Python2 
La fonction dans le tableau suivant est utilisée pour accéder aux connexions dans un runbook Python2. 

| Fonction | Description | 
|:---|:---| 
| automationassets.get_automation_connection | Récupère une connexion. Renvoie un dictionnaire avec les propriétés de la connexion. | 

> [!NOTE] 
> Vous devez importer le module « automationassets » en haut de votre runbook Python afin d’accéder aux fonctions des ressources.

## <a name="creating-a-new-connection"></a>Création d’une connexion

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Pour créer une connexion avec le portail Azure

1. À partir de votre compte Automation, cliquez sur la partie **Ressources** afin d’ouvrir le panneau **Ressources**.
2. Cliquez sur la partie **Connexions** afin d’ouvrir le panneau **Connexions**.
3. Cliquez sur **Ajouter une connexion** en haut du panneau.
4. Dans la liste déroulante **Type** , sélectionnez le type de connexion que vous souhaitez créer. Le formulaire présente les propriétés spécifiques à ce type
5. Remplissez le formulaire, puis cliquez sur **Créer** pour enregistrer la nouvelle connexion.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Pour créer une connexion avec Windows PowerShell

Créez une connexion avec Windows PowerShell à l’aide de l’applet de commande [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection). Cette applet de commande possède un paramètre nommé **ConnectionFieldValues** qui attend une [table de hachage](http://technet.microsoft.com/library/hh847780.aspx) définissant des valeurs pour chacune des propriétés définies par le type de connexion.

Si vous êtes familiarisé avec la fonction [Compte d’identification](automation-sec-configure-azure-runas-account.md) Automation qui permet d’authentifier les Runbooks à l’aide du principal de service, le script PowerShell (fourni comme alternative à la création du compte d’identification à partir du portail) crée une ressource de connexion à l’aide des exemples de commandes suivants.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 
```

Vous pouvez utiliser le script pour créer la ressource de connexion, car quand vous créez votre compte Automation, celui-ci inclut automatiquement plusieurs modules globaux par défaut, ainsi que le type de connexion **AzurServicePrincipal** pour créer la ressource de connexion **AzureRunAsConnection**.  Il est important de garder cela à l’esprit, car si vous essayez de créer une ressource de connexion pour vous connecter à un service ou à une application avec une autre méthode d’authentification, l’opération échoue, car le type de connexion n’est pas encore défini dans votre compte Automation.  Pour plus d’informations sur la façon de créer votre propre type de connexion pour votre module personnalisé à partir de la [galerie PowerShell](https://www.powershellgallery.com), consultez [Modules d’intégration](automation-integration-modules.md).
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Utilisation d’une connexion dans un Runbook ou une configuration DSC

Vous récupérez une connexion dans un Runbook ou une configuration DSC avec l’applet de commande **Get-AutomationConnection** .  Vous ne pouvez pas utiliser l’activité [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn).  Cette activité récupère les valeurs des différents champs dans la connexion et les retourne sous forme de [table de hachage](http://go.microsoft.com/fwlink/?LinkID=324844) , qui peut ensuite être utilisée avec les commandes appropriées dans le Runbook ou la configuration DSC.

### <a name="textual-runbook-sample"></a>Exemple de Runbook textuel

Les exemples de commandes suivants montrent comment utiliser le compte d’identification indiqué précédemment pour s’authentifier auprès des ressources Azure Resource Manager dans votre runbook.  Ils utilisent la ressource de connexion qui représente le compte d’identification, qui fait référence au principal du service basé sur un certificat, et non aux informations d’identification.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 
```

### <a name="graphical-runbook-samples"></a>Exemples de Runbook graphiques

Vous ajoutez une activité **Get-AutomationConnection** à un Runbook graphique en cliquant avec le bouton droit sur la connexion dans le volet Bibliothèque de l’éditeur graphique et en sélectionnant **Ajouter à la zone de dessin**.

![](media/automation-connections/connection-add-canvas.png)

L’image suivante montre un exemple d’utilisation d’une connexion dans un Runbook graphique.  Voici le même exemple que le précédent pour s’authentifier à l’aide du compte d’identification avec un runbook textuel.  Cet exemple utilise les données **Valeurs constantes** définies pour l’activité **Get RunAs Connection (Obtenir la connexion d’identification)**, qui utilise un objet de connexion pour l’authentification.  Un [lien pipeline](automation-graphical-authoring-intro.md#links-and-workflow) est utilisé ici, car le jeu de paramètres ServicePrincipalCertificate attend un seul objet.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exemple de Runbook Python2
L’exemple suivant montre comment s’authentifier à l’aide de la fonction de connexion d’identification dans un Runbook Python2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue la page relative aux [liens lors de la création de graphiques](automation-graphical-authoring-intro.md#links-and-workflow) pour comprendre comment diriger et contrôler le flux logique dans les runbooks.  

- Pour plus d’informations sur l’utilisation des modules PowerShell dans Azure Automation et les meilleures pratiques pour créer vos propres modules PowerShell afin de garantir que ceux-ci fonctionnent en tant que modules d’intégration au sein d’Azure Automation, consultez [Modules d’intégration](automation-integration-modules.md).  
